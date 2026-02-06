# **AWS Infrastructure for Private Strapi CMS Deployment**

This project automates the provisioning of a secure and scalable AWS infrastructure using Terraform to host a **Strapi CMS** application inside a private network architecture.

The infrastructure follows security best practices by isolating application resources from direct internet exposure.

---

# **Architecture Overview**

The architecture is designed with **security-first principles** using a custom VPC and controlled traffic flow.

### **Network Design**

The infrastructure uses a custom VPC with segmented subnets:

* **VPC**

  * CIDR Block: `10.0.0.0/16`
  * Provides isolated networking environment

* **Public Subnet**

  * Hosts:

    * Application Load Balancer (ALB)
    * NAT Gateway
  * Allows controlled internet-facing access

* **Private Subnet**

  * Hosts:

    * EC2 instance running Strapi CMS
  * No direct internet access
  * Traffic only allowed via ALB

---

# **Traffic Flow**

```
Internet
   ↓
Application Load Balancer (Port 80)
   ↓
Private EC2 Instance (Port 1337)
   ↓
NAT Gateway (Outbound only)
   ↓
Internet
```

---

# **Key Infrastructure Components**

## **VPC**

A custom Virtual Private Cloud providing network isolation.

## **Internet Gateway**

Enables internet connectivity for public subnet resources.

## **NAT Gateway**

Allows the private EC2 instance to:

* Download Docker images
* Perform system updates
* Access external APIs

Without exposing it publicly.

## **Application Load Balancer (ALB)**

* Public-facing entry point
* Routes HTTP traffic (Port 80)
* Forwards requests to Strapi (Port 1337)

## **EC2 Instance (Private)**

* Runs Strapi CMS inside Docker
* No public IP assigned
* Accessible only via ALB

---

# **Security Architecture**

Security is enforced at multiple layers.

## **Zero Public Exposure**

* EC2 instance is placed in a private subnet
* No direct internet access

## **Security Groups**

### **ALB Security Group**

* Allows inbound traffic on Port 80 from anywhere (`0.0.0.0/0`)
* Allows outbound traffic to EC2

### **EC2 Security Group**

* Allows inbound traffic on Port 1337 only from ALB security group
* Allows SSH (Port 22) only from authorized IP
* Allows outbound internet via NAT Gateway

---

# **Infrastructure as Code (Terraform)**

All resources are provisioned using Terraform.

## **Environment Configuration**

Environment-specific differences such as:

* Instance type
* Key pair name
* Region
* CIDR ranges

Are managed using:

```
terraform.tfvars
```

This allows easy separation between:

* Development
* Production

---

# **Automation (User Data Script)**

The EC2 instance automatically configures itself on boot using a user data script.

The script performs:

1. System update
2. Docker installation
3. Docker service startup
4. Pulling official `strapi/strapi` image
5. Running Strapi container on port 1337

No manual server configuration required.

---

# **Project Structure**

```
├── provider.tf        # AWS provider configuration
├── network.tf         # VPC, Subnets, IGW, NAT Gateway, Route Tables
├── security.tf        # Security Groups (ALB & EC2)
├── compute.tf         # EC2, ALB, Target Groups, Listeners
├── variables.tf       # Variable definitions
├── terraform.tfvars   # Environment configuration
└── README.md          # Project documentation
```

---

# **Deployment Instructions**

## **1. Initialize Terraform**

```bash
terraform init
```

Downloads required AWS provider plugins.

---

## **2. Validate Configuration**

```bash
terraform validate
```

Ensures syntax and structure are correct.

---

## **3. Review Execution Plan**

```bash
terraform plan
```

Displays all resources to be created (VPC, Subnets, ALB, EC2, etc.).

---

## **4. Apply Infrastructure**

```bash
terraform apply
```

Creates the complete infrastructure in AWS.

---

# **Post-Deployment**

After successful deployment:

1. Navigate to AWS Console
2. Go to EC2 → Load Balancers
3. Copy the ALB DNS name
4. Access the application:

```
http://<ALB-DNS>
```

Strapi CMS will be accessible through the load balancer.

---

# **Cost Notice**

⚠ NAT Gateway incurs charges.

After testing, run:

```bash
terraform destroy
```

To avoid ongoing costs.

---

# **Learning Outcomes**

This project demonstrates:

* Secure VPC architecture design
* Private subnet deployment
* NAT Gateway configuration
* Load balancer routing
* Security group isolation
* Infrastructure as Code (Terraform)
* Automated application deployment using user data


