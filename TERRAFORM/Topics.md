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

---

Would you like more details on any of these topics? 🚀
