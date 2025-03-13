<h1>5. Terraform Mock Interview Questions</h1>

### **1. Your Terraform state is corrupted; how do you recover it?**  
✅ **Steps to recover:**  
1. **Check if a backup exists** (`terraform.tfstate.backup`).  
   ```sh
   mv terraform.tfstate.backup terraform.tfstate
   ```
2. **Verify the integrity of the state file** using `terraform state list`.  
3. **Manually inspect and fix the state file** if necessary.  
4. **Restore the remote state** (if using S3, Terraform Cloud, etc.).  
5. **Recreate the state using `terraform import`** if needed.  
   ```sh
   terraform import aws_instance.example i-1234567890
   ```
✅ **Example:**  
- If an **EC2 instance is missing from the state**, re-import it using `terraform import`.  

---

### **2. You need to provision the same infrastructure for different teams with slight variations. How do you handle it?**  
✅ **Solution:** Use **Terraform modules** with **input variables** to customize configurations.  

✅ **Example:**  
Define a **module for an EC2 instance** (`modules/ec2/main.tf`).  
```hcl
resource "aws_instance" "web" {
  ami           = var.ami
  instance_type = var.instance_type
  tags = {
    Name = var.team_name
  }
}
```
Use the module for **different teams**:  
```hcl
module "team1_ec2" {
  source         = "./modules/ec2"
  ami            = "ami-123456"
  instance_type  = "t2.micro"
  team_name      = "Team1"
}

module "team2_ec2" {
  source         = "./modules/ec2"
  ami            = "ami-654321"
  instance_type  = "t3.micro"
  team_name      = "Team2"
}
```
✅ **Outcome:**  
- **Reusability** → Avoid code duplication.  
- **Scalability** → Easily create infrastructure for new teams.  

---

### **3. How would you refactor a large Terraform codebase to make it modular and maintainable?**  
✅ **Solution:**  
- Break the Terraform code into **modules**.  
- Separate **environments (dev, prod, staging)**.  
- Store Terraform **state remotely**.  
- Implement **version control (Git)** for tracking changes.  

✅ **Example: Terraform Project Structure**  
```
/terraform
  /modules
    /network
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
✅ **Outcome:**  
- Easier **code management**.  
- Improved **collaboration** across teams.  
- Faster **deployment and debugging**.  

---

### **4. Your Terraform `apply` failed halfway; what steps do you take?**  
✅ **Troubleshooting Steps:**  
1. **Check error logs** (`TF_LOG=DEBUG terraform apply`).  
2. **Run `terraform plan`** to check the current state.  
3. **Check Terraform state integrity** using `terraform state list`.  
4. **Manually remove failed resources** from state if needed.  
   ```sh
   terraform state rm aws_instance.failed_instance
   ```
5. **Re-run Terraform apply** to complete deployment.  

✅ **Example:**  
If an EC2 instance fails due to missing IAM permissions, **fix the policy and reapply**:  
```hcl
resource "aws_iam_policy" "fix_policy" {
  name   = "EC2Permissions"
  policy = jsonencode({
    Statement = [{
      Effect   = "Allow"
      Action   = "ec2:*"
      Resource = "*"
    }]
  })
}
```
✅ **Outcome:**  
- Prevents **manual intervention**.  
- Ensures **consistent infrastructure deployment**.  

---

### **5. How would you enforce compliance using Terraform in a corporate environment?**  
✅ **Solution:** Use **Terraform Sentinel** (Policy-as-Code) and enforce best practices.  

✅ **Example: Enforce S3 Encryption Policy**  
```hcl
import "tfplan"

main = rule {
  all tfplan.resources.aws_s3_bucket as _, bucket {
    bucket.applied.server_side_encryption_configuration != null
  }
}
```
✅ **Additional Measures:**  
- **Restrict manual changes** → Use IAM policies.  
- **Enable remote state locking** (S3 + DynamoDB).  
- **Code reviews before applying Terraform changes**.  

✅ **Outcome:**  
- **Prevents misconfigurations**.  
- **Ensures security and compliance** across teams.  

---

### **6. How would you implement Terraform in a production CI/CD pipeline?**  
✅ **Solution:**  
- Use **Jenkins, GitHub Actions, or GitLab CI/CD**.  
- Implement **automated plan and apply** steps.  
- Store **Terraform state remotely (S3, Terraform Cloud)**.  

✅ **Example: GitHub Actions Terraform Pipeline**  
```yaml
name: Terraform Deployment

on:
  push:
    branches:
      - main

jobs:
  terraform:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Terraform Init
        run: terraform init
      - name: Terraform Plan
        run: terraform plan
      - name: Terraform Apply
        run: terraform apply -auto-approve
```
✅ **Outcome:**  
- **Automated infrastructure provisioning**.  
- **No manual intervention required**.  
- **Faster and more reliable deployments**.  

---

### **7. A team is manually modifying infrastructure that Terraform manages. What issues could arise, and how would you address them?**  
✅ **Potential Issues:**  
- Terraform **drift** (inconsistent infrastructure).  
- State file **does not match reality**.  
- Risk of **accidental overwrites**.  

✅ **Solution:**  
1. **Detect Drift** using `terraform plan`.  
2. **Revert Unapproved Changes** using `terraform apply`.  
3. **Enforce IAM Policies** to **restrict manual changes**.  
4. **Use Remote State Locking** (S3 + DynamoDB) to **prevent conflicts**.  

✅ **Example: Enforce IAM Policy to Prevent Manual Changes**  
```hcl
resource "aws_iam_policy" "deny_manual_changes" {
  policy = jsonencode({
    Statement = [{
      Effect   = "Deny"
      Action   = ["ec2:*"]
      Resource = "*"
    }]
  })
}
```
✅ **Outcome:**  
- Ensures **Terraform is the single source of truth**.  
- Prevents **drift and unintended changes**.  
- **Improves infrastructure stability and security**.  

---

These **scenario-based Terraform solutions** help handle real-world infrastructure challenges! 🚀 Let me know if you need more details!
