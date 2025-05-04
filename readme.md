# 🇮🇳 Secure EC2 Web Server on AWS (India Only Access)

This project uses **Terraform** to deploy a complete AWS infrastructure in the `ap-south-1` (Mumbai) region. It provisions an **EC2 instance** behind an **Application Load Balancer (ALB)**, and restricts public access to **India only** using **AWS WAFv2**.

---

## 🧰 Features

- VPC with two public subnets across Availability Zones
- Internet Gateway and route table setup
- Security groups for ALB and EC2
- Launches EC2 instance with Apache web server
- Application Load Balancer (ALB)
- Target Group and Listener
- **WAFv2 Web ACL** restricting traffic to Indian IPs only
- Fully automated via Terraform

---

## 🗂️ Project Structure

├── main.tf # Terraform configuration
└── README.md # Project documentation


---

## 🚀 How to Use

### 1. Prerequisites

- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
- [Terraform](https://www.terraform.io/downloads)
- An existing EC2 Key Pair named `personal` in the `ap-south-1` region

---

### 2. Deployment

```bash
# Initialize Terraform
terraform init

# Review the execution plan
terraform plan

# Apply the changes
terraform apply

🌐 Output
After deployment:

Your EC2 web server will be accessible only from Indian IPs

Access the public DNS of the ALB (output in terminal)

The page shows:

<h1>Hello Akshay Upadhyay Welcome to EC22</h1>


🔐 WAF Rule
This rule allows traffic only from India based on geolocation:

geo_match_statement {
  country_codes = ["IN"]
  }

All other traffic is blocked by default.

🧹 Cleanup
To delete all resources:
---terraform destroy

📄 License
This project is licensed under the MIT License.






🙋‍♂️ If Your are using AWS console use below steps

✅ Step-by-Step AWS Console Guide
STEP 1: Create a VPC
Go to VPC Console → https://console.aws.amazon.com/vpc/

Click "Create VPC"

Choose "VPC only"

Set:

Name: india_vpc

IPv4 CIDR block: 10.0.0.0/16

Click "Create VPC"

STEP 2: Create Two Public Subnets
Repeat for each subnet:

Subnet A

Go to Subnets → Create subnet

Select india_vpc

Subnet name: public_subnet_a

Availability Zone: ap-south-1a

CIDR block: 10.0.1.0/24

Enable auto-assign public IPv4 (optional: do this after creation)

Save

Subnet B

Same steps, but:

Name: public_subnet_b

AZ: ap-south-1b

CIDR: 10.0.2.0/24

⚙️ Enable "Auto-assign public IPv4 address" for both subnets via Edit subnet settings.

STEP 3: Create and Attach Internet Gateway
Go to Internet Gateways → Click "Create internet gateway"

Name: igw

Click Create

Select the IGW → Actions → Attach to VPC → Select india_vpc

STEP 4: Create Route Table & Associate
Go to Route Tables → Create route table

Name: public_rt

VPC: india_vpc

After creation → Routes tab → Edit routes

Add Route:

Destination: 0.0.0.0/0

Target: Internet Gateway → select igw

Click Save

Associate with subnets:

Go to Subnet Associations tab → Edit subnet associations

Select both public_subnet_a and public_subnet_b

STEP 5: Create Security Groups
A. ALB Security Group (alb-sg)
Go to EC2 Console → Security Groups → Create

Name: alb-sg

VPC: india_vpc

Inbound:

Type: HTTP, Port 80, Source: 0.0.0.0/0

Outbound: allow all

B. EC2 Security Group (ec2-sg)
Same steps as above, but:

Name: ec2-sg

Inbound:

Type: HTTP, Port 80, Source: Security Group → alb-sg

Outbound: allow all

STEP 6: Launch EC2 Instance
Go to EC2 Console → Instances → Launch instance

Name: WebServer

AMI: Choose Amazon Linux 2

Instance type: t2.micro

Key pair: Select or create personal

Network:

VPC: india_vpc

Subnet: public_subnet_a

Auto-assign public IP: Enable

Security group: Select ec2-sg

Advanced → User data:

bash
Copy
Edit
#!/bin/bash
yum update -y
yum install -y httpd
echo "<h1>Hello Akshay Upadhyay Welcome to EC22</h1>" > /var/www/html/index.html
systemctl start httpd
systemctl enable httpd
Launch

STEP 7: Create Application Load Balancer
Go to EC2 Console → Load Balancers → Create Load Balancer

Choose Application Load Balancer

Name: india-alb

Scheme: Internet-facing

IP type: IPv4

Network:

VPC: india_vpc

Subnets: public_subnet_a, public_subnet_b

Security group: alb-sg

Listeners:

HTTP → forward to target group (create below)

STEP 8: Create Target Group
Type: Instance

Name: india-tg

Protocol: HTTP

Port: 80

VPC: india_vpc

Health checks:

Path: /index.html

After creation → Register targets → Add EC2 instance

STEP 9: Connect Target Group to ALB
Go back to Load Balancer setup

Add default action → Forward to india-tg

Review & Create

STEP 10: Create WAF ACL to Allow Only India
Go to WAF & Shield Console → Web ACLs → Create

Name: india-only-acl

Scope: Regional

Region: ap-south-1

Add rule:

Name: AllowIndia

Type: Geo match

Country codes: IN

Action: Allow

Default Action: Block

Add CloudWatch metrics if desired

Create

STEP 11: Associate WAF ACL with ALB
After WAF is created → Go to Web ACLs

Choose india-only-acl → Associations tab → Add association

Resource type: Application Load Balancer

Choose: india-alb

Confirm

✅ DONE!

You now have a fully working setup using AWS Console matching your Terraform setup, including:

2 Subnets

1 EC2 with Apache

1 ALB forwarding traffic

WAF allowing only India
