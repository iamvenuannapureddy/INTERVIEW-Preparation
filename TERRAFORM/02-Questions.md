<h1>Terraform Interview Questions</h1>
<h2>Beginner Level Questions</h2>

### **1. What is Terraform, and why do we use it?**  
**Terraform** is an Infrastructure as Code (IaC) tool that allows users to define, provision, and manage cloud infrastructure in a **declarative** way.  

✅ **Why use it?**  
- Automates infrastructure deployment  
- Supports **multi-cloud** (AWS, Azure, GCP)  
- Manages infrastructure efficiently with **state tracking**  

---

### **2. How does Terraform differ from Ansible and CloudFormation?**  

| Feature         | Terraform       | Ansible         | CloudFormation  |
|---------------|----------------|----------------|----------------|
| **Type**      | IaC (Infrastructure as Code) | Configuration Management | IaC (AWS Only) |
| **Language**  | HCL (HashiCorp Configuration Language) | YAML & Python | JSON/YAML |
| **State Mgmt** | Yes (Tracks State) | No state tracking | Yes (AWS Manages State) |
| **Multi-Cloud** | ✅ Yes | ✅ Yes | ❌ AWS Only |

---

### **3. What are Terraform Providers, and How Do They Work?**  
Providers are plugins that **interact with cloud services** (AWS, Azure, GCP, Kubernetes).  

✅ **Example: AWS Provider**  
```hcl
provider "aws" {
  region = "us-east-1"
}
```

---

### **4. What is the Purpose of the `.terraform` Directory?**  
The `.terraform` directory stores:  
✅ **Downloaded provider plugins**  
✅ **Backend configuration files**  
✅ **Module caches**  

Created when `terraform init` is run.

---

### **5. What are the Basic Terraform CLI Commands?**  

| Command             | Purpose |
|---------------------|---------|
| `terraform init`    | Initializes Terraform & downloads providers |
| `terraform plan`    | Shows what changes Terraform will apply |
| `terraform apply`   | Creates/modifies resources |
| `terraform destroy` | Deletes all managed resources |
| `terraform fmt`     | Formats Terraform code |
| `terraform validate` | Checks for syntax errors |
| `terraform state`   | Manages Terraform state |

---

### **6. What is Terraform State, and Why is it Required?**  
Terraform **state** keeps track of existing resources and their configurations.  

✅ **Why needed?**  
- Detects changes  
- Prevents unintended modifications  
- Enables remote collaboration  

Example **state file (`terraform.tfstate`)**:  
```json
{
  "resources": [
    {
      "type": "aws_instance",
      "name": "web",
      "instances": [
        { "attributes": { "id": "i-1234567890" } }
      ]
    }
  ]
}
```

---

### **7. What are Terraform Backends?**  
Backends store the **Terraform state file**.  

✅ **Types of Backends:**  
- **Local (default)** – Stores state on local disk.  
- **Remote (S3, Azure Storage, Terraform Cloud)** – Enables team collaboration and state locking.  

**Example: S3 Backend with Locking**  
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

### **8. How Do You Define a Variable in Terraform?**  

✅ **Define Variable (`variables.tf`)**  
```hcl
variable "instance_type" {
  default = "t2.micro"
}
```

✅ **Use Variable (`main.tf`)**  
```hcl
resource "aws_instance" "web" {
  instance_type = var.instance_type
}
```

✅ **Pass Variable via CLI**  
```sh
terraform apply -var="instance_type=t3.micro"
```

---

### **9. What is the Difference Between `terraform plan` and `terraform apply`?**  

| Command            | Purpose |
|--------------------|---------|
| `terraform plan`   | **Previews** changes before applying |
| `terraform apply`  | **Executes** changes to provision resources |

✅ **Example Usage:**  
```sh
terraform plan   # Show planned changes
terraform apply  # Apply changes
```

---

### **10. What is `terraform.tfstate`, and Where Should It Be Stored?**  
`terraform.tfstate` is the **Terraform state file** that tracks managed infrastructure.  

✅ **Storage Options:**  
- **Local storage** (default) – `terraform.tfstate` in working directory  
- **Remote storage** (best practice) – S3, Terraform Cloud, GCS  

✅ **Example: Store in S3**  
```hcl
terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "prod/terraform.tfstate"
    region = "us-east-1"
  }
}
```

---

<h2>Intermediate Level Questions</h2>

### **1. How Do You Manage Multiple Environments in Terraform?**  
✅ **Use Separate Environment Folders**  
```
/terraform
  /environments
    /dev
      main.tf
      variables.tf
    /prod
      main.tf
      variables.tf
```

✅ **Use Terraform Workspaces**  
```sh
terraform workspace new dev
terraform workspace select dev
```
```hcl
resource "aws_s3_bucket" "example" {
  bucket = "my-bucket-${terraform.workspace}"
}
```

✅ **Use Remote State for Shared Environments**  
```hcl
terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "env/${terraform.workspace}/terraform.tfstate"
  }
}
```

---

### **2. Explain Terraform Modules and How They Help in Reusability**  
**Modules** allow **code reuse** by packaging Terraform resources into reusable components.  

✅ **Example Module Structure**  
```
/modules
  /vpc
    main.tf
    variables.tf
    outputs.tf
```
✅ **Using a Module in Root Configuration**  
```hcl
module "vpc" {
  source   = "./modules/vpc"
  vpc_cidr = "10.0.0.0/16"
}
```
**Benefits:**  
- **Reusability**  
- **Simplifies code maintenance**  
- **Reduces duplication**  

---

### **3. What is the Difference Between `count` and `for_each`?**  

| Feature   | `count` | `for_each` |
|-----------|--------|-----------|
| **Type**  | Index-based list | Key-value pairs |
| **Use Case** | Simple lists | Maps and sets |
| **Limitations** | No dynamic indexing | Cannot reference index numbers |

✅ **Example: Using `count`**  
```hcl
resource "aws_instance" "web" {
  count         = 3
  instance_type = "t2.micro"
}
```

✅ **Example: Using `for_each`**  
```hcl
resource "aws_instance" "web" {
  for_each = { dev = "t2.micro", prod = "t3.micro" }
  instance_type = each.value
}
```

---

### **4. How Do You Handle Secrets in Terraform?**  
**Never hardcode secrets in Terraform!**  

✅ **Use Environment Variables**  
```sh
export TF_VAR_db_password="mypassword"
```

✅ **Use AWS Secrets Manager**  
```hcl
data "aws_secretsmanager_secret_version" "db_password" {
  secret_id = "db-password"
}

output "db_password" {
  value = nonsensitive(data.aws_secretsmanager_secret_version.db_password.secret_string)
}
```

✅ **Use `sensitive` Attribute**  
```hcl
variable "db_password" {
  type      = string
  sensitive = true
}
```

---

### **5. What is Terraform Workspace, and How Does It Help?**  
Workspaces **separate environments** using a single Terraform configuration.  

✅ **Commands**  
```sh
terraform workspace new dev
terraform workspace select dev
```

✅ **Usage in Configuration**  
```hcl
resource "aws_s3_bucket" "example" {
  bucket = "my-bucket-${terraform.workspace}"
}
```
**Use Cases:**  
- Separate **dev, staging, and prod**  
- Multi-tenant environments  

---

### **6. Explain the Importance of Terraform State Locking**  
**State locking** prevents multiple users from modifying the state simultaneously.  

✅ **Use S3 + DynamoDB for State Locking**  
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
**Benefits:**  
- Prevents **concurrent modifications**  
- Ensures **state consistency**  

---

### **7. How Do You Use Data Sources in Terraform?**  
Data sources **fetch existing resources** without creating them.  

✅ **Example: Fetching an Existing VPC**  
```hcl
data "aws_vpc" "existing_vpc" {
  id = "vpc-123456"
}

resource "aws_subnet" "my_subnet" {
  vpc_id = data.aws_vpc.existing_vpc.id
  cidr_block = "10.0.1.0/24"
}
```
**Why Use Data Sources?**  
- Avoid duplication  
- Retrieve dynamic values  

---

### **8. Can You Explain Terraform Remote State?**  
Remote state **stores Terraform state files in a shared backend** for team collaboration.  

✅ **Example: Storing State in S3**  
```hcl
terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "prod/terraform.tfstate"
    region = "us-east-1"
  }
}
```

✅ **Referencing Remote State in Another Project**  
```hcl
data "terraform_remote_state" "vpc" {
  backend = "s3"
  config = {
    bucket = "my-terraform-state"
    key    = "prod/terraform.tfstate"
    region = "us-east-1"
  }
}

output "vpc_id" {
  value = data.terraform_remote_state.vpc.outputs.vpc_id
}
```

---

### **9. What Are Dynamic Blocks in Terraform?**  
Dynamic blocks **create multiple nested configurations dynamically**.  

✅ **Example: Dynamic Security Group Rules**  
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
✅ **Variable (`variables.tf`)**  
```hcl
variable "ports" {
  default = [22, 80, 443]
}
```

**Why Use Dynamic Blocks?**  
- Reduces redundant code  
- Increases flexibility  

---

### **10. What is the Difference Between `null_resource` and `local-exec` in Terraform?**  

| Feature | `null_resource` | `local-exec` |
|---------|---------------|-------------|
| **Purpose** | Trigger actions without creating a resource | Run local machine commands |
| **Use Case** | Dependent actions | Local shell execution |
| **Best For** | Triggers for provisioners | Local scripts |

✅ **Example: `null_resource` with Provisioner**  
```hcl
resource "null_resource" "example" {
  provisioner "local-exec" {
    command = "echo 'Terraform executed'"
  }
}
```

✅ **Example: `local-exec` in an EC2 Instance**  
```hcl
resource "aws_instance" "example" {
  provisioner "local-exec" {
    command = "echo ${self.public_ip} > ip.txt"
  }
}
```

---

<h2>Advanced Level Questions</h2>

### **1. How Do You Integrate Terraform into a CI/CD Pipeline?**  
✅ **Use Terraform in Jenkins, GitHub Actions, GitLab CI/CD**  

#### **GitHub Actions Example**
```yaml
name: Terraform Pipeline
on: push
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
✅ **Best Practices:**  
- Store **state in a remote backend**  
- Use **Terraform Cloud for collaboration**  
- Implement **plan approvals** in CI/CD  

---

### **2. What Are Some Ways to Optimize Terraform Performance?**  

✅ **Use Remote State Storage**  
```hcl
terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "prod/terraform.tfstate"
    region = "us-east-1"
  }
}
```
✅ **Enable Parallelism**  
```sh
terraform apply -parallelism=10
```
✅ **Use `for_each` Instead of `count`** (avoids resource recreation)  
✅ **Minimize Data Sources** (reduces API calls)  

---

### **3. Explain Terraform Drift and How to Detect and Fix It**  

✅ **What is Terraform Drift?**  
- When infrastructure changes **outside Terraform** (e.g., manual changes in AWS Console).  

✅ **Detect Drift**  
```sh
terraform plan
```
✅ **Fix Drift**  
- **Reapply Terraform to match state**  
```sh
terraform apply
```
- **Manually import unmanaged resources**  
```sh
terraform import aws_instance.example i-12345678
```
- **Use `terraform state rm` for unwanted resources**  

---

### **4. How Do You Rollback a Terraform Deployment?**  

✅ **Best Practice: Use Version Control**  
- Keep `.tf` files in Git and revert if needed.  

✅ **Revert to Previous State Using State History**  
```sh
terraform state list
terraform state show <resource>
```
✅ **Manually Restore a Backup (`terraform.tfstate.backup`)**  
```sh
mv terraform.tfstate.backup terraform.tfstate
```
✅ **Destroy and Redeploy if Needed**  
```sh
terraform destroy -target=aws_instance.example
terraform apply
```

---

### **5. What Happens If Your Terraform State File Is Lost?**  

✅ **If Using Remote Backend (Best Practice)**  
- Retrieve state from S3, Terraform Cloud, etc.  

✅ **If Using Local State (Risky)**  
- If no backup, **infrastructure is unmanaged** by Terraform.  
- Manually **import resources** to recreate state.  
```sh
terraform import aws_instance.example i-12345678
```
- **Rebuild from scratch** if necessary.  

---

### **6. How Do You Manage Terraform Code for Multiple Teams?**  

✅ **Use Remote State Storage for Collaboration**  
```hcl
terraform {
  backend "s3" {
    bucket = "team-terraform-state"
    key    = "team1/prod.tfstate"
  }
}
```
✅ **Use Workspaces for Multi-Tenant Environments**  
```sh
terraform workspace new team1
terraform workspace select team1
```
✅ **Use Role-Based Access Control (RBAC)**  
- Restrict **who can apply changes** (Terraform Cloud or IAM Policies).  

✅ **Implement GitOps Workflow**  
- Store Terraform configurations in Git repositories.  
- Enforce reviews via pull requests.  

---

### **7. How Does Terraform Interact with Kubernetes?**  

✅ **Terraform Manages Kubernetes Clusters & Resources**  

#### **Deploy EKS (AWS Kubernetes Service)**
```hcl
resource "aws_eks_cluster" "eks" {
  name = "my-cluster"
}
```
#### **Deploy Kubernetes Resources Using Terraform**
```hcl
provider "kubernetes" {
  config_path = "~/.kube/config"
}

resource "kubernetes_deployment" "nginx" {
  metadata {
    name = "nginx"
  }
  spec {
    replicas = 3
  }
}
```
✅ **Why Use Terraform for Kubernetes?**  
- Manages **cloud and cluster resources together**  
- Integrates with **CI/CD pipelines**  

---

### **8. Explain the Difference Between `terraform import` and `terraform state mv`**  

✅ **`terraform import`**  
- Brings an **existing resource** into Terraform state.  
```sh
terraform import aws_instance.example i-12345678
```

✅ **`terraform state mv`**  
- Moves a resource **within Terraform state**.  
```sh
terraform state mv aws_instance.old aws_instance.new
```

| Command | Purpose |
|---------|---------|
| `import` | Adds external resources to Terraform |
| `state mv` | Renames/moves resources within Terraform |

---

### **9. How Do You Test Terraform Code?**  

✅ **Validate Syntax**  
```sh
terraform validate
```
✅ **Format Code**  
```sh
terraform fmt
```
✅ **Run a Dry-Run Plan**  
```sh
terraform plan
```
✅ **Use `tflint` for Best Practices**  
```sh
tflint
```
✅ **Unit Testing with `terratest` (Go-based)**  
```go
package test

func TestTerraformExample(t *testing.T) {
    terraformOptions := &terraform.Options{
        TerraformDir: "../terraform",
    }
    terraform.InitAndApply(t, terraformOptions)
}
```

---

### **10. What is Sentinel in Terraform Enterprise?**  

✅ **Sentinel is a Policy-as-Code Framework**  
- Enforces security and compliance in Terraform Enterprise.  

✅ **Example: Enforce Tagging Policy**  
```hcl
import "tfplan"

main = rule {
  all tfplan.resources.aws_instance as _, instances {
    all instances as instance {
      "Environment" in instance.applied.tags
    }
  }
}
```
✅ **Benefits of Sentinel:**  
- Prevents **misconfigurations**  
- Enforces **compliance** in teams  
- Automates **security policies**  

---

