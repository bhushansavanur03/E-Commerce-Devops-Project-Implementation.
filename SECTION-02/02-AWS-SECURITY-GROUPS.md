## Configuring AWS Security Group to Access the Application

After successfully running the E-Commerce application using Docker Compose, I tried accessing it through my web browser using the public IP address of my EC2 instance. However, I was initially unable to access the application because the required inbound traffic was not allowed by the AWS Security Group.

To resolve this issue, I logged in to the AWS Management Console and navigated to the EC2 Security Groups section.

I selected the Security Group associated with my EC2 instance and opened the **Inbound Rules** section. Since my E-Commerce application was running on port `8080`, I added a new inbound rule to allow incoming TCP traffic on port 8080.

The rule was configured with the following details:

| Configuration | Value |
|---|---|
| Type | Custom TCP |
| Protocol | TCP |
| Port Range | 8080 |
| Source | My IP / As configured |

After adding the rule, I saved the changes and accessed the application using the following URL:

```text
http://<EC2-Public-IP>:8080
```

After configuring the Security Group, I was successfully able to access and view my E-Commerce application in the web browser using the HTTP protocol.

### Learning Outcome

Through this activity, I understood how AWS Security Groups control incoming network traffic to EC2 instances.
I also learned that running an application successfully on an EC2 instance is not enough; the required network ports must also be allowed to access it externally.

This activity gave me practical experience in configuring inbound rules and accessing a containerized application running on an AWS EC2 instance through a web browser.
