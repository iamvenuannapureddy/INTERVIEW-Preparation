<h1>A. Terraform Basics</h1>

### **1. What is Terraform? Why use it?**  
**Terraform** is an open-source Infrastructure as Code (IaC) tool developed by HashiCorp that allows users to **provision, manage, and automate** infrastructure using declarative configuration files.  

#### **Why use Terraform?**  
- **Multi-cloud support** – Works with AWS, Azure, GCP, Kubernetes, and more.  
- **Immutable infrastructure** – Ensures consistency by replacing rather than modifying resources.  
- **Declarative approach** – Users define the desired state, and Terraform manages it.  
- **State management** – Maintains a record of infrastructure changes.  
- **Automation & scalability** – Ideal for DevOps, CI/CD, and cloud automation.  

---

### **2. Difference Between Terraform and Other IaC Tools**  

| Feature           | Terraform | CloudFormation | Ansible | Pulumi |
|------------------|-----------|---------------|---------|--------|
| **Approach**     | Declarative | Declarative | Imperative & Declarative | Declarative |
| **Cloud Support** | Multi-cloud | AWS only | Multi-cloud | Multi-cloud |
| **State Management** | Yes (Remote & Local) | Yes (Managed by AWS) | No state tracking | No state tracking |
| **Provisioning** | Infrastructure | Infrastructure | Configuration management | Infrastructure |
| **Language** | HCL (HashiCorp Configuration Language) | JSON/YAML | YAML/Ansible Playbooks | Python, Go, TypeScript |

---

### **3. How does Terraform Work? (Declarative vs. Imperative)**  
- **Declarative (Terraform, CloudFormation, Pulumi):**  
  - Define the desired **end state**, and the tool handles execution.  
  - Example: "I want an EC2 instance running."  

- **Imperative (Ansible, Bash Scripts):**  
  - Define the exact **steps** to reach a state.  
  - Example: "Run this command to start an EC2 instance."  

Terraform uses a **declarative approach**, making infrastructure predictable, repeatable, and version-controlled.  

---

### **4. Terraform CLI Commands**  
| Command | Purpose |
|---------|---------|
| `terraform init` | Initializes a new or existing Terraform configuration. |
| `terraform plan` | Shows what changes Terraform will apply. |
| `terraform apply` | Applies the changes and provisions infrastructure. |
| `terraform destroy` | Destroys all resources defined in the configuration. |
| `terraform fmt` | Formats Terraform code to maintain best practices. |
| `terraform validate` | Validates the syntax of Terraform files. |
| `terraform state` | Manages and inspects Terraform's state file. |

---

### **5. Terraform Workflow (Write, Plan, Apply)**  
Terraform follows a **three-step workflow**:  

1. **Write** – Define resources in `.tf` files using HCL (e.g., EC2, VPC).  
2. **Plan** – Run `terraform plan` to preview infrastructure changes.  
3. **Apply** – Run `terraform apply` to create or update infrastructure.  

**Bonus Step:** If needed, `terraform destroy` can be used to delete resources.

---

### **6. Providers and Modules**  
#### **Providers:**  
- Terraform **providers** are plugins that interact with cloud platforms and services.  
- Examples: AWS, Azure, GCP, Kubernetes.  
- Defined using:  
  ```hcl
  provider "aws" {
    region = "us-east-1"
  }
  ```

#### **Modules:**  
- **Reusable Terraform configurations** that help manage infrastructure in a structured way.  
- Example of a module:  
  ```hcl
  module "network" {
    source  = "./network-module"
    vpc_cidr = "10.0.0.0/16"
  }
  ```
- **Benefits:** Reusability, maintainability, and easier team collaboration.

  <h1>B. Terraform Configuration</h1>
  ### **1. Understanding `.tf` Files and Their Structure**  
Terraform uses `.tf` files written in **HCL (HashiCorp Configuration Language)** to define infrastructure.  

#### **Common `.tf` Files:**
- **`main.tf`** – Defines resources (e.g., EC2, VPC, RDS).  
- **`variables.tf`** – Declares input variables.  
- **`outputs.tf`** – Defines outputs (e.g., instance IPs).  
- **`terraform.tfvars`** – Stores variable values.  
- **`provider.tf`** – Configures cloud providers (AWS, Azure, GCP).  

---

### **2. Variables, Outputs, and Locals**  

#### **Variables (`variables.tf`)**  
Used to parameterize configurations.  
```hcl
variable "region" {
  default = "us-east-1"
  type    = string
}
```

#### **Outputs (`outputs.tf`)**  
Used to display resource attributes after execution.  
```hcl
output "instance_ip" {
  value = aws_instance.my_instance.public_ip
}
```

#### **Locals (`locals`)**  
Used for defining reusable expressions inside Terraform.  
```hcl
locals {
  common_tags = {
    Project = "Terraform Demo"
    Owner   = "DevOps Team"
  }
}
```
Usage:
```hcl
resource "aws_instance" "my_instance" {
  tags = local.common_tags
}
```

---

### **3. Data Sources and How to Use Them**  
Data sources allow Terraform to **fetch existing resources** without creating them.  

Example: Get details of an existing AWS VPC.  
```hcl
data "aws_vpc" "existing_vpc" {
  id = "vpc-123456"
}

resource "aws_subnet" "my_subnet" {
  vpc_id = data.aws_vpc.existing_vpc.id
  cidr_block = "10.0.1.0/24"
}
```
**Why use data sources?**  
- Avoid duplication  
- Integrate existing infrastructure  
- Retrieve dynamic values  

---

### **4. Terraform State (Local and Remote State)**  

#### **Local State (`terraform.tfstate`)**  
- Stored in the local machine by default.  
- Example:
  ```json
  {
    "version": 4,
    "terraform_version": "1.2.0",
    "resources": [...]
  }
  ```

#### **Remote State**  
Stored in cloud storage (S3, GCS, etc.) for **team collaboration and state locking**.  

Example: S3 backend  
```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-lock"
  }
}
```

**Why use Remote State?**  
✅ Collaboration  
✅ State versioning  
✅ State locking (DynamoDB)  

---

### **5. Backends (S3 + DynamoDB, Consul, etc.)**  

Terraform **backends** store the Terraform state.  

#### **Common Backends:**
| Backend  | Features |
|----------|----------|
| **S3 + DynamoDB** | Best for AWS, supports state locking |
| **Consul** | Distributed key-value store for large teams |
| **Terraform Cloud** | HashiCorp-managed remote state |
| **Azure Storage** | Similar to S3, used in Azure |

Example: **Using Consul Backend**  
```hcl
terraform {
  backend "consul" {
    address = "consul.example.com:8500"
    path    = "terraform/state"
  }
}
```

---

### **6. Terraform Modules (Reusable Infrastructure)**  
Terraform **modules** help organize and reuse code.  

#### **Example Module Structure**  
```
/modules
  /network
    main.tf
    variables.tf
    outputs.tf
  /compute
    main.tf
    variables.tf
    outputs.tf
```

#### **Using a Module in Root Configuration**
```hcl
module "vpc" {
  source = "./modules/network"
  vpc_cidr = "10.0.0.0/16"
}
```

**Why use modules?**  
✅ Code reusability  
✅ Scalability  
✅ Easier maintenance  

---



  <h1>C. Terraform Advanced Concepts</h1>

### **1. State Locking and Security**  
**State locking** prevents multiple users from modifying the Terraform state at the same time.  

- **Why is it important?**  
  ✅ Prevents corruption of `terraform.tfstate`  
  ✅ Ensures consistency in infrastructure  

- **How to enable state locking?**  
  - Use **DynamoDB with S3 backend**  
  ```hcl
  terraform {
    backend "s3" {
      bucket         = "my-terraform-state"
      key            = "prod/terraform.tfstate"
      region         = "us-east-1"
      dynamodb_table = "terraform-lock"
    }
  }
  ```

- **Security best practices**  
  ✅ Encrypt state files using S3 encryption  
  ✅ Use IAM policies to restrict access  
  ✅ Store secrets in **HashiCorp Vault** or **AWS Secrets Manager**  

---

### **2. Dynamic Blocks and `for_each` vs `count`**  

#### **Dynamic Blocks** (Used to create multiple nested blocks dynamically)  
Example: Adding multiple security group rules dynamically  
```hcl
resource "aws_security_group" "example" {
  dynamic "ingress" {
    for_each = var.ports
    content {
      from_port   = ingress.value
      to_port     = ingress.value
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    }
  }
}
```

#### **`for_each` vs `count`**  
| Feature | `count` | `for_each` |
|---------|--------|-----------|
| **Type** | Uses index numbers | Uses key-value pairs |
| **Best for** | Simple lists | Maps and complex objects |
| **Example** | `count = length(var.instances)` | `for_each = var.instances_map` |

Example: Using `for_each` with EC2 instances  
```hcl
resource "aws_instance" "example" {
  for_each = var.instances
  ami      = each.value.ami
  instance_type = each.value.type
}
```

---

### **3. Workspaces and Their Usage**  
Workspaces allow **environment separation** using a single Terraform configuration.  

- **Default workspace**: `default`  
- **Create a new workspace**  
  ```sh
  terraform workspace new dev
  ```
- **Switch workspaces**  
  ```sh
  terraform workspace select dev
  ```

- **Use workspace in configurations**  
  ```hcl
  resource "aws_s3_bucket" "example" {
    bucket = "my-bucket-${terraform.workspace}"
  }
  ```

**Use cases:**  
✅ Separate **dev**, **staging**, and **prod** environments  
✅ Multi-tenant infrastructure  

---

### **4. Sensitive Variables and Secrets Management**  

- **Mark variables as sensitive**  
  ```hcl
  variable "password" {
    type      = string
    sensitive = true
  }
  ```
- **Use AWS Secrets Manager or HashiCorp Vault**  
  Example: Fetching secrets dynamically  
  ```hcl
  data "aws_secretsmanager_secret_version" "db_password" {
    secret_id = "db-password"
  }

  output "password" {
    value = nonsensitive(data.aws_secretsmanager_secret_version.db_password.secret_string)
  }
  ```
- **Security best practices**  
  ✅ **Never** hardcode secrets  
  ✅ Store state files securely (S3, Vault)  
  ✅ Limit access with IAM policies  

---

### **5. Terraform Cloud and Terraform Enterprise**  

| Feature | Terraform Cloud | Terraform Enterprise |
|---------|----------------|----------------------|
| **Usage** | Managed service | Self-hosted solution |
| **State Storage** | Remote backend | Local or external backend |
| **Collaboration** | Free for teams | Designed for large enterprises |
| **Security** | Encrypted state storage | Advanced access controls |
| **Policy Enforcement** | Sentinel | Sentinel & custom plugins |

Example: Using Terraform Cloud  
```hcl
terraform {
  cloud {
    organization = "my-org"
    workspaces {
      name = "prod"
    }
  }
}
```
**Why use Terraform Cloud?**  
✅ Centralized state management  
✅ Team collaboration  
✅ Secure execution environment  

---

### **6. Debugging Terraform Issues**  

- **Enable debug logs**  
  ```sh
  export TF_LOG=DEBUG
  terraform apply
  ```
- **Validate syntax**  
  ```sh
  terraform validate
  ```
- **Format code to avoid syntax issues**  
  ```sh
  terraform fmt
  ```
- **Check plan before applying changes**  
  ```sh
  terraform plan
  ```
- **Inspect state file**  
  ```sh
  terraform state list
  ```
- **Manually remove broken resources**  
  ```sh
  terraform state rm aws_instance.example
  ```

**Common errors and solutions:**  
| Error | Solution |
|-------|---------|
| `Error acquiring the state lock` | Ensure DynamoDB lock is enabled |
| `Invalid provider version` | Run `terraform init -upgrade` |
| `Resource already exists` | Use `terraform import` |

---


<h1>D. Terraform Integration & Best Practices</h1>

### **1. Using Terraform with AWS, Azure, GCP**  

#### **AWS Example: Provision an EC2 Instance**  
```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}
```

#### **Azure Example: Create a Virtual Machine**  
```hcl
provider "azurerm" {
  features {}
}

resource "azurerm_virtual_machine" "vm" {
  name                = "example-vm"
  location            = "East US"
  resource_group_name = "my-resource-group"
}
```

#### **GCP Example: Deploy a Compute Instance**  
```hcl
provider "google" {
  project = "my-gcp-project"
  region  = "us-central1"
}

resource "google_compute_instance" "vm" {
  name         = "example-instance"
  machine_type = "n1-standard-1"
}
```

---

### **2. CI/CD Pipeline with Terraform (Jenkins, GitHub Actions, GitLab CI/CD)**  

#### **Jenkins Pipeline for Terraform**
```groovy
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/myrepo/terraform.git'
            }
        }
        stage('Terraform Init') {
            steps {
                sh 'terraform init'
            }
        }
        stage('Terraform Plan') {
            steps {
                sh 'terraform plan'
            }
        }
        stage('Terraform Apply') {
            steps {
                sh 'terraform apply -auto-approve'
            }
        }
    }
}
```

#### **GitHub Actions Terraform Workflow**
```yaml
name: Terraform CI/CD

on:
  push:
    branches:
      - main

jobs:
  terraform:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v1
      - name: Terraform Init
        run: terraform init
      - name: Terraform Plan
        run: terraform plan
      - name: Terraform Apply
        run: terraform apply -auto-approve
```

---

### **3. Terraform with Kubernetes (EKS, AKS, GKE)**  

#### **EKS (AWS Kubernetes)**
```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_eks_cluster" "eks" {
  name = "my-eks-cluster"
  role_arn = aws_iam_role.eks.arn
}
```

#### **AKS (Azure Kubernetes Service)**
```hcl
resource "azurerm_kubernetes_cluster" "aks" {
  name                = "my-aks-cluster"
  location            = "East US"
  resource_group_name = "my-resource-group"
  dns_prefix          = "myaks"
}
```

#### **GKE (Google Kubernetes Engine)**
```hcl
resource "google_container_cluster" "gke" {
  name     = "my-gke-cluster"
  location = "us-central1"
}
```

---

### **4. Terraform Best Practices**  

#### **A. DRY (Don't Repeat Yourself)**
- Use **modules** to avoid duplication.
- Example: Create a reusable module for an EC2 instance.
```hcl
module "ec2" {
  source = "./modules/ec2"
  instance_type = "t2.micro"
}
```

---

#### **B. Structuring Terraform Projects**
```
/terraform-project
  /modules
    /vpc
      main.tf
      variables.tf
      outputs.tf
    /compute
      main.tf
      variables.tf
      outputs.tf
  /environments
    /dev
      main.tf
    /prod
      main.tf
  terraform.tfvars
  backend.tf
```
- Use `modules` for reusable infrastructure.
- Separate `environments` for dev, staging, prod.

---

#### **C. Using Environment-Specific Workspaces**
- Create and switch between workspaces:
```sh
terraform workspace new dev
terraform workspace select dev
```
- Reference workspace in configuration:
```hcl
resource "aws_s3_bucket" "example" {
  bucket = "my-bucket-${terraform.workspace}"
}
```

---

#### **D. Using Remote State Locking**
- Prevents concurrent changes to the state file.
- Example: **S3 Backend with DynamoDB Locking**
```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-lock"
  }
}
```

---

#### **E. Managing Secrets Securely**
- Use environment variables:
```sh
export TF_VAR_db_password="my-secure-password"
```
- Use AWS Secrets Manager:
```hcl
data "aws_secretsmanager_secret_version" "db_password" {
  secret_id = "db-password"
}
```
- Mark sensitive variables:
```hcl
variable "db_password" {
  type      = string
  sensitive = true
}
```

---



