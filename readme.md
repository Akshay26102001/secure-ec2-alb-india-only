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

🙋‍♂️ Author
Akshay Pratap Upadhyay
