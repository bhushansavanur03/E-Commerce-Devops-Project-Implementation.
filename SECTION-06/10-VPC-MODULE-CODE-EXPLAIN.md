# Code Explanation: Terraform VPC Module

The VPC module provisions the networking infrastructure required for deploying an Amazon EKS cluster. It creates a VPC, public and private subnets, Internet Gateway, NAT Gateways, Elastic IPs, route tables, and subnet associations.

## 1. Creating a VPC

```hcl
resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = {
    Name                                        = "${var.cluster_name}-vpc"
    "kubernetes.io/cluster/${var.cluster_name}" = "shared"
  }
}
```

**Explanation:**
- `aws_vpc` creates an Amazon Virtual Private Cloud.
- `cidr_block` defines the IP address range using the input variable `var.vpc_cidr`.
- `enable_dns_hostnames = true` enables DNS hostnames for supported resources in the VPC.
- `enable_dns_support = true` enables DNS resolution through Amazon-provided DNS.
- `Name` assigns a descriptive name to the VPC.
- The Kubernetes cluster tag identifies the VPC as shared with the specified EKS cluster.

## 2. Creating Private Subnets

```hcl
resource "aws_subnet" "private" {
  count             = length(var.private_subnet_cidrs)
  vpc_id            = aws_vpc.main.id
  cidr_block        = var.private_subnet_cidrs[count.index]
  availability_zone = var.availability_zones[count.index]

  tags = {
    Name                                        = "${var.cluster_name}-private-${count.index + 1}"
    "kubernetes.io/cluster/${var.cluster_name}" = "shared"
    "kubernetes.io/role/internal-elb"           = "1"
  }
}
```

**Explanation:**
- `aws_subnet` creates subnets inside the specified VPC.
- `count` creates multiple private subnets based on the number of CIDR blocks provided.
- `length()` calculates the number of elements in the private subnet CIDR list.
- `vpc_id` associates each subnet with the previously created VPC.
- `cidr_block` assigns an individual IP address range to each subnet.
- `availability_zone` distributes subnets across the specified Availability Zones.
- `count.index` identifies the current subnet being created.
- `kubernetes.io/role/internal-elb` identifies the subnet for internal Kubernetes load balancer discovery.

**Purpose:** Private subnets are intended to host resources that should not receive direct inbound internet traffic, such as EKS worker nodes.

## 3. Creating Public Subnets

```hcl
resource "aws_subnet" "public" {
  count             = length(var.public_subnet_cidrs)
  vpc_id            = aws_vpc.main.id
  cidr_block        = var.public_subnet_cidrs[count.index]
  availability_zone = var.availability_zones[count.index]

  map_public_ip_on_launch = true

  tags = {
    Name                                        = "${var.cluster_name}-public-${count.index + 1}"
    "kubernetes.io/cluster/${var.cluster_name}" = "shared"
    "kubernetes.io/role/elb"                    = "1"
  }
}
```

**Explanation:**
- Creates multiple public subnets based on `var.public_subnet_cidrs`.
- Each subnet receives its own CIDR block and Availability Zone.
- `map_public_ip_on_launch = true` automatically assigns public IPv4 addresses to supported instances launched in these subnets.
- `kubernetes.io/role/elb` identifies the subnet for public Kubernetes load balancer discovery.
- The cluster tag associates the subnet with the EKS cluster.

**Purpose:** Public subnets provide network connectivity for internet-facing resources, such as public load balancers and NAT Gateways.

## 4. Creating an Internet Gateway

```hcl
resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "${var.cluster_name}-igw"
  }
}
```

**Explanation:**
- `aws_internet_gateway` creates an Internet Gateway.
- `vpc_id` attaches the gateway to the previously created VPC.
- The `Name` tag identifies the Internet Gateway.

**Purpose:** An Internet Gateway enables communication between the VPC and the internet when the appropriate routing and security configurations are in place.

## 5. Creating NAT Gateways and Elastic IPs

### Elastic IP Configuration

```hcl
resource "aws_eip" "nat" {
  count  = length(var.public_subnet_cidrs)
  domain = "vpc"

  tags = {
    Name = "${var.cluster_name}-nat-${count.index + 1}"
  }
}
```

**Explanation:**
- `aws_eip` provisions Elastic IP addresses.
- `count` creates one Elastic IP for each public subnet.
- `domain = "vpc"` associates the Elastic IP with a VPC.
- These Elastic IPs provide static public IP addresses for NAT Gateways.

### NAT Gateway Configuration

```hcl
resource "aws_nat_gateway" "main" {
  count         = length(var.public_subnet_cidrs)
  allocation_id = aws_eip.nat[count.index].id
  subnet_id     = aws_subnet.public[count.index].id

  tags = {
    Name = "${var.cluster_name}-nat-${count.index + 1}"
  }
}
```

**Explanation:**
- `aws_nat_gateway` creates NAT Gateways in public subnets.
- `count` creates one NAT Gateway for each public subnet.
- `allocation_id` associates each NAT Gateway with its corresponding Elastic IP.
- `subnet_id` places the NAT Gateway inside the corresponding public subnet.

**Purpose:** NAT Gateways allow resources in private subnets to initiate outbound internet connections without requiring public IP addresses.

For example, an EKS worker node in a private subnet can download software updates or container images through a NAT Gateway.

## 6. Creating Route Tables

Route tables determine where network traffic from a subnet should be directed.

### Public Route Table

```hcl
resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.main.id
  }

  tags = {
    Name = "${var.cluster_name}-public"
  }
}
```

**Explanation:**
- Creates a public route table inside the VPC.
- `0.0.0.0/0` represents all IPv4 destinations that do not match a more specific route.
- `gateway_id` directs this traffic to the Internet Gateway.

**Purpose:** Allows associated public subnets to communicate with the internet, subject to security and network access rules.

### Private Route Tables

```hcl
resource "aws_route_table" "private" {
  count  = length(var.private_subnet_cidrs)
  vpc_id = aws_vpc.main.id

  route {
    cidr_block     = "0.0.0.0/0"
    nat_gateway_id = aws_nat_gateway.main[count.index].id
  }

  tags = {
    Name = "${var.cluster_name}-private-${count.index + 1}"
  }
}
```

**Explanation:**
- Creates separate private route tables based on the number of private subnet CIDR blocks.
- `0.0.0.0/0` represents outbound IPv4 traffic to destinations outside the VPC's more specific routes.
- `nat_gateway_id` directs this traffic through the corresponding NAT Gateway.

**Purpose:** Allows private subnet resources to access the internet for outbound connections without being directly exposed to inbound internet traffic.

## 7. Associating Route Tables with Subnets

Route tables must be associated with subnets for their routes to apply.

### Private Subnet Associations

```hcl
resource "aws_route_table_association" "private" {
  count          = length(var.private_subnet_cidrs)
  subnet_id      = aws_subnet.private[count.index].id
  route_table_id = aws_route_table.private[count.index].id
}
```

**Explanation:**
- Creates an association for each private subnet.
- `subnet_id` references the corresponding private subnet.
- `route_table_id` associates that subnet with its corresponding private route table.

### Public Subnet Associations

```hcl
resource "aws_route_table_association" "public" {
  count          = length(var.public_subnet_cidrs)
  subnet_id      = aws_subnet.public[count.index].id
  route_table_id = aws_route_table.public.id
}
```

**Explanation:**
- Creates an association for each public subnet.
- `subnet_id` references the corresponding public subnet.
- `route_table_id` associates all public subnets with the shared public route table.

## Final VPC Module Architecture

The VPC module establishes the following network architecture:

- One VPC.
- Multiple public subnets.
- Multiple private subnets.
- One Internet Gateway.
- One Elastic IP and NAT Gateway per public subnet.
- One shared public route table.
- Separate private route tables.
- Route table associations for public and private subnets.

### Expected Network Traffic Flow

**Public subnet:**

Public subnet resource → Public Route Table → Internet Gateway → Internet

**Private subnet:**

Private subnet resource → Private Route Table → NAT Gateway → Internet Gateway → Internet

### Outcome

The VPC module provides the networking foundation for the EKS infrastructure. It separates public-facing resources from private workloads while allowing private resources to establish outbound internet connectivity through NAT Gateways.
