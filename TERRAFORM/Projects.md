<h1> Hands-on Terraform Projects for Interviews</h1>
<h2>Basic Projects</h2>

### **1. Deploy an EC2 Instance on AWS**  
✅ **Objective:** Provision an EC2 instance with a specific instance type and AMI in AWS.  

✅ **Steps:**  
1. **Define Provider** (AWS credentials & region).  
2. **Create Security Group** (Allow SSH & HTTP traffic).  
3. **Launch EC2 Instance** (Specify AMI & instance type).  

✅ **Terraform Code:**  
```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  tags = {
    Name = "Terraform-EC2"
  }
}
```

✅ **Expected Outcome:**  
- A new EC2 instance is launched in **us-east-1**.  
- Instance type is **t2.micro** with a specified AMI.  

---

### **2. Deploy a VPC with Public and Private Subnets**  
✅ **Objective:** Create a **custom VPC** with one public and one private subnet.  

✅ **Steps:**  
1. **Create a VPC** (Define CIDR block).  
2. **Add a Public Subnet** (Attach an Internet Gateway).  
3. **Add a Private Subnet** (Without direct internet access).  
4. **Attach a Route Table** (Route public subnet to the internet).  

✅ **Terraform Code:**  
```hcl
resource "aws_vpc" "my_vpc" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "public_subnet" {
  vpc_id                  = aws_vpc.my_vpc.id
  cidr_block              = "10.0.1.0/24"
  map_public_ip_on_launch = true
}

resource "aws_subnet" "private_subnet" {
  vpc_id     = aws_vpc.my_vpc.id
  cidr_block = "10.0.2.0/24"
}
```

✅ **Expected Outcome:**  
- A VPC with a **10.0.0.0/16** CIDR block.  
- **Public subnet** has internet access, **private subnet** does not.  

---

### **3. Provision an S3 Bucket with Versioning Enabled**  
✅ **Objective:** Create an S3 bucket with versioning for backup and recovery.  

✅ **Steps:**  
1. **Create an S3 Bucket** (Define name and region).  
2. **Enable Versioning** (Preserve previous versions of objects).  

✅ **Terraform Code:**  
```hcl
resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-terraform-bucket"
}

resource "aws_s3_bucket_versioning" "versioning_example" {
  bucket = aws_s3_bucket.my_bucket.id
  versioning_configuration {
    status = "Enabled"
  }
}
```

✅ **Expected Outcome:**  
- S3 bucket named **"my-terraform-bucket"** is created.  
- Versioning is enabled, allowing object recovery.  

---


<h2>Intermediate Projects</h2>

### **4. Deploy a Multi-Tier Application Using Terraform (Frontend, Backend, Database)**  
✅ **Objective:** Provision a **multi-tier application** with a **Frontend (Web), Backend (App), and Database (RDS)** using Terraform.  

✅ **Steps:**  
1. **Create a VPC** with public and private subnets.  
2. **Deploy a Frontend (EC2 + ALB)** in a public subnet.  
3. **Deploy a Backend (EC2)** in a private subnet.  
4. **Provision an RDS Database** (MySQL/PostgreSQL) in a private subnet.  
5. **Use Security Groups** to restrict access.  

✅ **Terraform Code (Snippet for RDS Instance):**  
```hcl
resource "aws_db_instance" "db" {
  engine            = "mysql"
  instance_class    = "db.t3.micro"
  allocated_storage = 20
  username         = "admin"
  password         = "password"
  vpc_security_group_ids = [aws_security_group.db_sg.id]
  db_subnet_group_name   = aws_db_subnet_group.my_db_subnet_group.name
}
```

✅ **Expected Outcome:**  
- A **frontend EC2 instance** running a web app.  
- A **backend EC2 instance** processing app logic.  
- A **database instance (RDS)** storing application data.  
- Secure **network segmentation** between components.  

---

### **5. Automate Infrastructure Provisioning with Terraform Modules**  
✅ **Objective:** Improve reusability and automation by using **Terraform modules** for repeatable infrastructure components.  

✅ **Steps:**  
1. **Create a module structure** (`network`, `compute`, `database`).  
2. **Define reusable Terraform modules** for VPC, EC2, and RDS.  
3. **Call modules in the root Terraform configuration.**  

✅ **Terraform Code (Module Usage):**  
```hcl
module "vpc" {
  source   = "./modules/network"
  vpc_cidr = "10.0.0.0/16"
}

module "ec2" {
  source        = "./modules/compute"
  instance_type = "t2.micro"
}

module "db" {
  source         = "./modules/database"
  db_engine      = "mysql"
  instance_class = "db.t3.micro"
}
```

✅ **Expected Outcome:**  
- Simplified infrastructure management.  
- **Reusable** Terraform configurations.  
- Easier **team collaboration** on infrastructure.  

---

### **6. Terraform with Ansible: Provision and Configure Servers**  
✅ **Objective:** Use Terraform to provision EC2 instances and **Ansible** to configure them (install packages, set up applications).  

✅ **Steps:**  
1. **Terraform provisions EC2 instances** and outputs IP addresses.  
2. **Ansible connects to EC2** and installs/configures software.  
3. **Terraform and Ansible integrate** using `null_resource` and `local-exec`.  

✅ **Terraform Code (Run Ansible after EC2 Provisioning):**  
```hcl
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}

resource "null_resource" "ansible_provision" {
  provisioner "local-exec" {
    command = "ansible-playbook -i '${aws_instance.web.public_ip},' ansible/playbook.yml"
  }
}
```

✅ **Ansible Playbook (`playbook.yml`):**  
```yaml
- hosts: all
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present
```

✅ **Expected Outcome:**  
- Terraform **creates EC2 instances**.  
- Ansible **configures them (e.g., installs Apache, sets up apps)**.  
- **Infrastructure is automated** from provisioning to configuration.  

---

<h2>Advanced Projects</h2>

### **7. CI/CD Pipeline Using Jenkins, Terraform, and AWS**  
✅ **Objective:** Automate infrastructure deployment using **Jenkins, Terraform, and AWS** in a CI/CD pipeline.  

✅ **Steps:**  
1. **Set up a Jenkins pipeline** on an EC2 instance.  
2. **Configure Terraform in Jenkins** to deploy infrastructure.  
3. **Use Terraform to provision AWS resources** (e.g., EC2, S3, RDS).  
4. **Trigger automatic deployments** when code changes in Git.  

✅ **Jenkins Pipeline (`Jenkinsfile`)**  
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

✅ **Expected Outcome:**  
- **Infrastructure is automatically provisioned** when code is updated.  
- **Jenkins integrates with Terraform** for continuous deployment.  
- **Fully automated AWS resource management.**  

---

### **8. Deploy EKS (Elastic Kubernetes Service) Using Terraform**  
✅ **Objective:** Provision an **AWS EKS cluster** using Terraform for Kubernetes workloads.  

✅ **Steps:**  
1. **Create a VPC, subnets, and security groups** for EKS.  
2. **Deploy the EKS cluster** using Terraform.  
3. **Configure IAM roles and attach worker nodes.**  
4. **Deploy applications using Kubernetes manifests.**  

✅ **Terraform Code (EKS Cluster)**  
```hcl
resource "aws_eks_cluster" "eks" {
  name     = "my-cluster"
  role_arn = aws_iam_role.eks_role.arn

  vpc_config {
    subnet_ids = [aws_subnet.public.id, aws_subnet.private.id]
  }
}
```

✅ **Expected Outcome:**  
- A **fully functional EKS cluster** on AWS.  
- **Automated scaling and networking** for Kubernetes.  
- **Ready to deploy microservices applications.**  

---

### **9. Terraform with Vault for Secrets Management**  
✅ **Objective:** Use **HashiCorp Vault** with Terraform to securely store and retrieve secrets (e.g., DB passwords, API keys).  

✅ **Steps:**  
1. **Deploy HashiCorp Vault** on an AWS EC2 instance.  
2. **Store secrets (DB passwords, IAM keys) in Vault.**  
3. **Use Terraform to fetch and use secrets.**  

✅ **Terraform Code (Fetch Secrets from Vault)**  
```hcl
provider "vault" {
  address = "http://vault.example.com:8200"
}

data "vault_generic_secret" "db_password" {
  path = "secret/db"
}

resource "aws_db_instance" "db" {
  engine  = "mysql"
  username = "admin"
  password = data.vault_generic_secret.db_password.data["password"]
}
```

✅ **Expected Outcome:**  
- **Terraform securely retrieves secrets from Vault.**  
- **No hardcoded secrets in Terraform code.**  
- **Enhanced security and compliance.**  

---

### **10. Using Terraform to Provision Infrastructure Across Multiple Cloud Providers (AWS + Azure + GCP)**  
✅ **Objective:** Use Terraform to **provision resources across AWS, Azure, and GCP** in a unified infrastructure-as-code approach.  

✅ **Steps:**  
1. **Define multi-cloud providers (AWS, Azure, GCP).**  
2. **Deploy infrastructure (EC2 on AWS, VM on Azure, Compute Engine on GCP).**  
3. **Use Terraform remote state** to track resources across clouds.  

✅ **Terraform Code (Multi-Cloud Deployment)**  
```hcl
provider "aws" {
  region = "us-east-1"
}

provider "azurerm" {
  features {}
}

provider "google" {
  project = "my-gcp-project"
  region  = "us-central1"
}

resource "aws_instance" "aws_vm" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}

resource "azurerm_virtual_machine" "azure_vm" {
  name                = "azure-vm"
  location            = "East US"
  resource_group_name = "my-resource-group"
}

resource "google_compute_instance" "gcp_vm" {
  name         = "gcp-vm"
  machine_type = "n1-standard-1"
}
```

✅ **Expected Outcome:**  
- Terraform **deploys resources across AWS, Azure, and GCP.**  
- **Unified infrastructure management** with a single codebase.  
- **Seamless hybrid and multi-cloud architecture.**  

---

