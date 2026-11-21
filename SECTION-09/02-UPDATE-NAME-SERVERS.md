# Update Nameservers for Domain

## 1. Overview

This guide explains how to configure a domain purchased from GoDaddy to use Amazon Route 53 for DNS management.

The objective is to connect a domain (for example, `abhishekveeramalla.shop`) to an AWS Application Load Balancer (ALB) using Route 53.

After configuration, users can access the application using the custom domain instead of the ALB's default DNS hostname.

## 2. Create a Public Hosted Zone in Route 53

This step was completed in the previous section.

1. Open the AWS Management Console.
2. Navigate to Amazon Route 53.
3. Select **Hosted zones**.
4. Click **Create hosted zone**.
5. Enter your domain name, for example:

   `abhishekveeramalla.shop`

6. Select **Public Hosted Zone**.
7. Click **Create hosted zone**.

After creation, Route 53 automatically generates NS and SOA records.

Copy the four nameservers listed in the NS record.

## 3. Update Nameservers in GoDaddy

The next step is to delegate DNS management from GoDaddy to Amazon Route 53.

### Steps

1. Log in to your GoDaddy account.
2. Navigate to **My Products**.
3. Find your domain, for example, `abhishekveeramalla.shop`.
4. Open **DNS** or **Manage DNS**.
5. Locate the **Nameservers** section.
6. Click **Change**.
7. Select **Enter my own nameservers (advanced)**.
8. Enter the four nameservers provided by Route 53.
9. Save the changes.

**Important:** Enter all four Route 53 nameservers exactly as displayed in the hosted zone. Do not enter the GoDaddy nameservers.

Once saved, Route 53 becomes the authoritative DNS provider after the nameserver delegation takes effect.

## 4. Create an Alias A Record in Route 53

After updating the nameservers, configure a DNS record to route traffic to your AWS Application Load Balancer.

### Steps

1. Open the Route 53 console.
2. Select **Hosted zones**.
3. Open the hosted zone for `abhishekveeramalla.shop`.
4. Click **Create record**.
5. Configure the following values:

| Field | Value |
|---|---|
| Record name | `www` |
| Record type | A |
| Alias | Enabled |
| Route traffic to | Alias to Application and Classic Load Balancer |
| Region | Your ALB's AWS region |
| Load Balancer | Select your ALB |

6. Click **Create records**.

This creates the DNS record:

`www.abhishekveeramalla.shop`

It points to the selected AWS Application Load Balancer.

### Optional: Configure the Root Domain

If you also want users to access the application using:

`abhishekveeramalla.shop`

Create another Alias A record with:

- Record name: Leave blank.
- Record type: A.
- Alias: Enabled.
- Alias target: Your Application Load Balancer.

This ensures both the root domain and the `www` subdomain can reach the application.

## 5. Verify the DNS Configuration

DNS changes may take time to propagate. The duration depends on DNS caching and the previous nameserver TTL values. It can take up to 24–48 hours in some cases, but often completes sooner.

### Step 1: Check the Nameserver Configuration

Run:

```bash
dig NS abhishekveeramalla.shop
```

This displays the authoritative nameservers for the domain.

They should match the four nameservers provided by Route 53.

### Step 2: Check the DNS Record

Run:

```bash
dig www.abhishekveeramalla.shop
```

This checks whether the `www` domain resolves correctly.

### Step 3: Check the ALB DNS Name

You can also check the ALB endpoint:

```bash
kubectl get ingress -n robot-shop
```

The `ADDRESS` field should display the ALB DNS hostname after successful provisioning.

### Step 4: Test the Application

Open a browser and navigate to:

`http://www.abhishekveeramalla.shop`

If HTTPS is configured on the ALB with a valid TLS certificate, test:

`https://www.abhishekveeramalla.shop`

The application should load through the ALB and Kubernetes Ingress routing configuration.

## 6. Expected Architecture

```text
             Internet User
                   |
                   v
     www.abhishekveeramalla.shop
                   |
                   v
              GoDaddy
          (Domain Registrar)
                   |
                   v
              Route 53
          (DNS Management)
                   |
                   v
       AWS Application Load Balancer

                   |
                   v
          Kubernetes Ingress
                   |
                   v
          Kubernetes Services
                   |
                   v
            Application Pods
```

## 7. Summary

The domain purchased from GoDaddy is configured to use Route 53 for DNS management. An Alias A record connects the custom domain to the AWS Application Load Balancer.

**Quick Revision:**

1. Create a Public Hosted Zone in Route 53.
2. Replace GoDaddy nameservers with the four Route 53 nameservers.
3. Create an Alias A record pointing to the ALB.
4. Verify DNS resolution and test application access through the custom domain.
