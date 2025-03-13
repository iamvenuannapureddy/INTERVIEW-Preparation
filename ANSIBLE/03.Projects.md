<h1></h1>
<h2>Basic Projects</h2>

### **1. Automate Apache/Nginx Installation on Linux Servers**  
✅ **Objective:** Automate the installation and configuration of **Apache/Nginx** on Linux servers using Ansible.  

✅ **Steps:**  
1. Define the target **hosts** in the inventory file.  
2. Create a playbook to install and start **Apache/Nginx**.  
3. Ensure **idempotency** (re-run without duplicate installs).  

✅ **Example Ansible Playbook (`install_web.yml`)**  
```yaml
- name: Install and Configure Apache/Nginx
  hosts: web
  become: yes
  tasks:
    - name: Install Apache/Nginx
      apt:
        name: apache2  # Use 'nginx' for Nginx
        state: present

    - name: Start Web Server
      service:
        name: apache2
        state: started
        enabled: yes
```

✅ **Outcome:**  
- Apache/Nginx is installed and running on all target servers.  
- The service starts automatically on boot.  

---

### **2. Deploy a Simple Application Using Ansible Playbook**  
✅ **Objective:** Deploy a static website or application files to a web server using Ansible.  

✅ **Steps:**  
1. Install **Apache/Nginx** (if not already installed).  
2. Copy application files (`index.html`) to the web root directory.  
3. Restart the web server to apply changes.  

✅ **Example Ansible Playbook (`deploy_app.yml`)**  
```yaml
- name: Deploy Web Application
  hosts: web
  become: yes
  tasks:
    - name: Ensure Apache is Installed
      apt:
        name: apache2
        state: present

    - name: Copy Web Files
      copy:
        src: index.html
        dest: /var/www/html/index.html

    - name: Restart Apache
      service:
        name: apache2
        state: restarted
```

✅ **Outcome:**  
- A simple web application is deployed on the target servers.  
- Users can access the **index.html page via a web browser**.  

---

### **3. Configure and Manage System Users and Groups**  
✅ **Objective:** Automate user and group management using Ansible.  

✅ **Steps:**  
1. Define **users and groups** in a playbook.  
2. Create users, assign groups, and set SSH keys.  
3. Ensure **idempotency** (only create users if they don’t exist).  

✅ **Example Ansible Playbook (`manage_users.yml`)**  
```yaml
- name: Manage System Users and Groups
  hosts: all
  become: yes
  tasks:
    - name: Create User Group
      group:
        name: developers
        state: present

    - name: Create Users and Assign Group
      user:
        name: "{{ item }}"
        groups: developers
        shell: /bin/bash
        state: present
      loop:
        - alice
        - bob

    - name: Set SSH Key for Alice
      authorized_key:
        user: alice
        key: "ssh-rsa AAAAB3Nza..."
```

✅ **Outcome:**  
- Users **alice** and **bob** are created and assigned to the `developers` group.  
- SSH key authentication is set for secure access.  

---

<h2>Intermediate Projects</h2>

### **4. Use Ansible Vault to Manage Secrets in Playbooks**  
✅ **Objective:** Securely store and use sensitive data (passwords, API keys) in Ansible Playbooks.  

✅ **Steps:**  
1. **Encrypt secrets** using **Ansible Vault**.  
2. **Use encrypted variables** in playbooks.  
3. **Run playbook securely** with the vault password.  

✅ **Encrypt a Secrets File (`secrets.yml`)**  
```sh
ansible-vault encrypt secrets.yml
```

✅ **Example Encrypted Variables (`secrets.yml`)**  
```yaml
db_password: "my_secure_password"
```

✅ **Use Vault Variables in a Playbook (`use_secrets.yml`)**  
```yaml
- name: Use Vault Secrets
  hosts: db
  become: yes
  vars_files:
    - secrets.yml
  tasks:
    - name: Configure Database
      mysql_user:
        name: admin
        password: "{{ db_password }}"
```

✅ **Run the Playbook with Vault:**  
```sh
ansible-playbook use_secrets.yml --ask-vault-pass
```

✅ **Outcome:**  
- **Passwords and sensitive data remain encrypted**.  
- **Only authorized users with the vault password can decrypt and use secrets**.  

---

### **5. Deploy a Multi-Node Web Application with Ansible Roles**  
✅ **Objective:** Use **Ansible Roles** to deploy a **multi-tier web application** (Frontend, Backend, Database).  

✅ **Steps:**  
1. **Create roles** for Web, App, and Database servers.  
2. **Use handlers, templates, and variables** for configuration.  
3. **Deploy the complete application across multiple nodes**.  

✅ **Project Structure:**  
```
/ansible-multi-tier-app
  /roles
    /web
      /tasks/main.yml
    /app
      /tasks/main.yml
    /db
      /tasks/main.yml
  /inventory.ini
  /site.yml
```

✅ **Example Role Playbook (`roles/web/tasks/main.yml`)**  
```yaml
- name: Install Nginx
  apt:
    name: nginx
    state: present

- name: Deploy Web App
  copy:
    src: index.html
    dest: /var/www/html/index.html
```

✅ **Run the Playbook:**  
```sh
ansible-playbook -i inventory.ini site.yml
```

✅ **Outcome:**  
- **Modular deployment** of frontend, backend, and database.  
- **Roles ensure reusability and scalability**.  

---

### **6. Ansible Dynamic Inventory for AWS (EC2, S3, RDS)**  
✅ **Objective:** Automate **inventory management** for AWS infrastructure.  

✅ **Steps:**  
1. **Use AWS Dynamic Inventory Plugin** to fetch EC2 instances.  
2. **Run playbooks dynamically** based on real-time cloud infrastructure.  
3. **Avoid manual inventory file updates**.  

✅ **Install the AWS Collection:**  
```sh
ansible-galaxy collection install amazon.aws
```

✅ **Create AWS Dynamic Inventory File (`aws_ec2.yml`)**  
```yaml
plugin: amazon.aws.aws_ec2
regions:
  - us-east-1
keyed_groups:
  - key: tags.Name
    prefix: tag_
```

✅ **Run Playbook with AWS Inventory:**  
```sh
ansible-playbook -i aws_ec2.yml playbook.yml
```

✅ **Outcome:**  
- **Automatically discovers AWS EC2 instances**.  
- **No need to manually update the inventory file**.  
- **Works with dynamic cloud infrastructure (Auto Scaling, new instances, etc.)**.  

---

<h2>Advanced Projects</h2>

### **7. CI/CD Pipeline Using Ansible and Jenkins**  
✅ **Objective:** Automate application deployment using **Jenkins and Ansible** in a CI/CD pipeline.  

✅ **Steps:**  
1. **Jenkins pulls the latest code** from GitHub/GitLab.  
2. **Jenkins triggers Ansible** to deploy the application.  
3. **Ansible installs dependencies** and restarts services.  

✅ **Example Jenkins Pipeline (`Jenkinsfile`)**  
```groovy
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/myrepo/app.git'
            }
        }
        stage('Deploy with Ansible') {
            steps {
                sh 'ansible-playbook -i inventory deploy.yml'
            }
        }
    }
}
```

✅ **Example Ansible Playbook (`deploy.yml`)**  
```yaml
- name: Deploy Web Application
  hosts: web
  become: yes
  tasks:
    - name: Copy Application Files
      copy:
        src: app/
        dest: /var/www/html/
    - name: Restart Web Server
      service:
        name: apache2
        state: restarted
```

✅ **Outcome:**  
- **Automated deployments** triggered by **code commits**.  
- **Improves CI/CD workflow** with **Ansible and Jenkins**.  

---

### **8. Deploy Kubernetes Cluster Using Ansible**  
✅ **Objective:** Automate **Kubernetes cluster setup** using Ansible.  

✅ **Steps:**  
1. **Install Kubernetes components** (Kubeadm, Kubelet, Kubectl).  
2. **Initialize the Kubernetes Master Node**.  
3. **Join Worker Nodes to the Cluster**.  

✅ **Example Ansible Playbook (`k8s_setup.yml`)**  
```yaml
- name: Install Kubernetes
  hosts: all
  become: yes
  tasks:
    - name: Install dependencies
      apt:
        name: "{{ item }}"
        state: present
      loop:
        - docker.io
        - kubelet
        - kubeadm
        - kubectl

    - name: Initialize Kubernetes Master
      command: kubeadm init
      when: "'master' in group_names"

    - name: Join Worker Nodes
      command: kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
      when: "'worker' in group_names"
```

✅ **Outcome:**  
- **Automates Kubernetes deployment** across multiple nodes.  
- **Ensures a scalable and repeatable cluster setup**.  

---

### **9. Automate Infrastructure Provisioning with Ansible and Terraform**  
✅ **Objective:** Use **Terraform** for infrastructure provisioning and **Ansible** for configuration management.  

✅ **Steps:**  
1. **Terraform creates AWS infrastructure** (EC2, RDS, Security Groups).  
2. **Ansible installs and configures applications** on provisioned infrastructure.  

✅ **Example Terraform Code (`main.tf`)**  
```hcl
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}
output "instance_ip" {
  value = aws_instance.web.public_ip
}
```

✅ **Example Ansible Playbook (`setup.yml`)**  
```yaml
- name: Configure Web Server
  hosts: all
  become: yes
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present
```

✅ **Run Terraform & Ansible:**  
```sh
terraform apply -auto-approve
ansible-playbook -i inventory setup.yml
```

✅ **Outcome:**  
- **End-to-end automated infrastructure setup and configuration**.  
- **Seamless integration between Terraform and Ansible**.  

---

### **10. Ansible with AWS: Provision and Configure EC2, S3, RDS**  
✅ **Objective:** Use Ansible to provision and configure AWS services (EC2, S3, RDS).  

✅ **Steps:**  
1. **Create an EC2 instance** using the `amazon.aws.ec2_instance` module.  
2. **Create an S3 bucket** for object storage.  
3. **Set up an RDS database**.  

✅ **Example Ansible Playbook (`aws_setup.yml`)**  
```yaml
- name: Provision AWS Resources
  hosts: localhost
  tasks:
    - name: Create EC2 Instance
      amazon.aws.ec2_instance:
        name: "Ansible-Managed-EC2"
        instance_type: "t2.micro"
        image_id: "ami-123456"
        region: "us-east-1"

    - name: Create S3 Bucket
      amazon.aws.s3_bucket:
        name: "ansible-managed-bucket"
        state: present

    - name: Create RDS Database
      amazon.aws.rds_instance:
        db_instance_identifier: ansible-db
        engine: mysql
        db_instance_class: db.t3.micro
        allocated_storage: 20
        username: admin
        password: "SecurePassword123"
```

✅ **Outcome:**  
- **Automates AWS resource provisioning** using Ansible.  
- **Deploys EC2, S3, and RDS instances without manual intervention**.  

---

