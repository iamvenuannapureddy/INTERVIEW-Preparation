<h1> Core Topics to Cover</h1>
<h2>A. Ansible Basics</h2>

### **1. What is Ansible? Why use it?**  
✅ **Ansible** is an open-source **automation tool** used for **configuration management, application deployment, and orchestration**.  

✅ **Why use Ansible?**  
- **Agentless** – No need to install agents on managed nodes.  
- **Idempotent** – Ensures tasks only change when necessary.  
- **Declarative** – Define the desired state, and Ansible ensures it.  
- **Multi-platform** – Supports Linux, Windows, Cloud, and Kubernetes.  
- **Easy to learn** – Uses **YAML-based playbooks**.  

---

### **2. Difference between Ansible and Other Configuration Management Tools**  

| Feature        | Ansible | Puppet | Chef | Terraform |
|--------------|---------|--------|------|-----------|
| **Type**     | Configuration Management | Configuration Management | Configuration Management | Infrastructure as Code (IaC) |
| **Agentless** | ✅ Yes | ❌ Requires Agent | ❌ Requires Agent | ✅ Yes |
| **Language** | YAML (Playbooks) | DSL (Puppet Language) | Ruby | HCL (HashiCorp Config Language) |
| **Push/Pull** | Push-based | Pull-based | Pull-based | Push-based |
| **Use Case** | App deployment, Config Mgmt, Orchestration | Config Mgmt | Config Mgmt | Infra Provisioning |

✅ **Key Takeaway:**  
- **Use Ansible** for **config management & automation**.  
- **Use Terraform** for **infrastructure provisioning**.  
- **Use Puppet/Chef** for **long-term configuration management at scale**.  

---

### **3. Ansible Architecture (Control Node, Managed Nodes, Inventory)**  

✅ **Control Node:**  
- The system where **Ansible runs** and executes playbooks.  
- Communicates with managed nodes via **SSH (Linux)** or **WinRM (Windows)**.  

✅ **Managed Nodes:**  
- The target systems **Ansible configures** (Linux, Windows, Cloud, Containers).  

✅ **Inventory:**  
- A file that **defines managed nodes** (IP addresses, hostnames, groups).  

✅ **Architecture Flow:**  
1. **Control Node** runs a playbook.  
2. **Sends SSH/WinRM commands** to **Managed Nodes**.  
3. Executes tasks **in order** and ensures **desired state**.  

---

### **4. Ansible Installation & Setup**  

✅ **Install Ansible on Linux (Ubuntu/Debian)**  
```sh
sudo apt update
sudo apt install ansible -y
```

✅ **Install Ansible on RHEL/CentOS**  
```sh
sudo yum install epel-release -y
sudo yum install ansible -y
```

✅ **Verify Installation**  
```sh
ansible --version
```

✅ **Check Connection to a Managed Node**  
```sh
ansible all -m ping -i inventory
```

---

### **5. Ansible Configuration File (`ansible.cfg`)**  
✅ **Located at:**  
- `/etc/ansible/ansible.cfg` (Global config)  
- `~/.ansible.cfg` (User-specific config)  

✅ **Example `ansible.cfg` file**  
```ini
[defaults]
inventory = ./inventory
remote_user = ubuntu
host_key_checking = False
log_path = /var/log/ansible.log
```

✅ **Purpose:**  
- Defines **inventory location**, logging, SSH settings, etc.  
- Can be customized per **project or user**.  

---

### **6. Inventory and Host Files**  

✅ **Inventory File (`inventory`)**  
Defines target **managed nodes** using IPs, hostnames, and groups.  

✅ **Example Inventory File (`hosts`)**  
```ini
[web]
192.168.1.10
192.168.1.11

[db]
db1.example.com
db2.example.com

[all:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/id_rsa
```

✅ **Dynamic Inventory (AWS, Azure, GCP)**  
- Fetches **real-time inventory** from cloud services.  
- Example: AWS EC2 instances using `aws_ec2` plugin.  

---

<h2>B. Ansible Playbooks and Modules</h2>

### **1. Understanding Playbooks (.yml Files)**  
✅ **Ansible Playbooks** are **YAML-based configuration files** used to define automation tasks.  

✅ **Basic Playbook Example (`playbook.yml`)**  
```yaml
- name: Install and Start Nginx
  hosts: web
  become: yes
  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
    - name: Start Nginx Service
      service:
        name: nginx
        state: started
```
✅ **Key Components:**  
- **`name`** → Describes the task.  
- **`hosts`** → Specifies target servers.  
- **`tasks`** → List of automation steps.  
- **`become`** → Runs as sudo/root.  

---

### **2. Ansible Modules (Core Modules: `command`, `shell`, `copy`, `service`, `package`, etc.)**  

✅ **Commonly Used Ansible Modules:**  

| Module  | Purpose |
|---------|---------|
| `command` | Runs system commands (safe, doesn’t use shell features) |
| `shell` | Runs commands using shell (supports pipes, redirections) |
| `copy` | Copies files from control node to managed nodes |
| `template` | Uses Jinja2 to generate dynamic configuration files |
| `service` | Manages system services (start, stop, restart) |
| `package` | Installs/updates packages (generic for apt, yum, dnf) |

✅ **Example: Using the `copy` Module to Transfer a File**  
```yaml
- name: Copy an index.html file
  copy:
    src: index.html
    dest: /var/www/html/index.html
```

✅ **Example: Using `command` vs `shell`**  
```yaml
- name: Run a simple command
  command: ls -l /var/www

- name: Run a shell command with pipe
  shell: "cat /etc/passwd | grep root"
```

---

### **3. Ansible Tasks & Handlers**  

✅ **Tasks:** Individual automation steps executed in a playbook.  
✅ **Handlers:** Special tasks triggered when notified by other tasks.  

✅ **Example: Using Handlers to Restart Nginx**  
```yaml
- name: Install Nginx
  apt:
    name: nginx
    state: present
  notify: Restart Nginx

- name: Start Nginx
  service:
    name: nginx
    state: started

handlers:
  - name: Restart Nginx
    service:
      name: nginx
      state: restarted
```
✅ **How it Works?**  
- If **Nginx is installed**, it **notifies** the handler to restart the service.  
- Handlers **run only if notified** and execute at the **end of the playbook**.  

---

### **4. Conditionals (`when` Statement)**  

✅ **Conditionals allow tasks to execute only when a condition is met.**  

✅ **Example: Install Nginx only on Ubuntu**  
```yaml
- name: Install Nginx on Ubuntu
  apt:
    name: nginx
    state: present
  when: ansible_os_family == "Debian"
```

✅ **Example: Restart Service Only if a File Exists**  
```yaml
- name: Restart service if config file is present
  service:
    name: nginx
    state: restarted
  when: ansible_facts['distribution'] == "Ubuntu"
```

---

### **5. Loops (`with_items`, `loop`)**  

✅ **Loops allow repeated execution of a task for multiple items.**  

✅ **Example: Installing Multiple Packages**  
```yaml
- name: Install multiple packages
  apt:
    name: "{{ item }}"
    state: present
  loop:
    - nginx
    - git
    - unzip
```

✅ **Example: Creating Multiple Users**  
```yaml
- name: Create multiple users
  user:
    name: "{{ item }}"
    state: present
  with_items:
    - alice
    - bob
    - charlie
```

✅ **Loop Types:**  
| Loop Method  | Usage Example |
|-------------|--------------|
| `loop` | Newer, preferred method (`loop: [package1, package2]`) |
| `with_items` | Older method, still widely used (`with_items: [user1, user2]`) |
| `with_fileglob` | Loops over multiple files in a directory |

---

### **6. Templates (`Jinja2 Templates`)**  

✅ **Jinja2 Templates** are used to create **dynamic configuration files**.  
✅ **Variables inside templates** are enclosed in `{{ }}`.  

✅ **Example: Nginx Configuration Template (`nginx.conf.j2`)**  
```jinja
server {
    listen 80;
    server_name {{ server_name }};
    location / {
        root {{ document_root }};
        index index.html;
    }
}
```

✅ **Playbook to Deploy Template**  
```yaml
- name: Deploy Nginx config from template
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: Restart Nginx
```

✅ **Why Use Templates?**  
- Allows **dynamic configuration generation**.  
- Reduces **hardcoding of values**.  
- Uses **variables from inventory or playbooks**.  

---

<h2>C. Ansible Advanced Concepts</h2>

### **1. Roles and Role-Based Access**  
✅ **Ansible Roles** help **organize** and **reuse** automation tasks by structuring playbooks into modular components.  

✅ **Role Directory Structure:**  
```
my_role/
  ├── tasks/main.yml
  ├── handlers/main.yml
  ├── templates/
  ├── files/
  ├── vars/main.yml
  ├── defaults/main.yml
  ├── meta/main.yml
```

✅ **Example: Using a Role in a Playbook (`site.yml`)**  
```yaml
- name: Deploy Web Server
  hosts: web
  roles:
    - webserver
```

✅ **Benefits:**  
- **Code reusability** → Share across multiple playbooks.  
- **Better organization** → Modular and structured approach.  

---

### **2. Ansible Vault (Managing Secrets Securely)**  
✅ **Ansible Vault** encrypts sensitive data (passwords, API keys).  

✅ **Encrypt a File:**  
```sh
ansible-vault encrypt secrets.yml
```

✅ **Decrypt a File:**  
```sh
ansible-vault decrypt secrets.yml
```

✅ **Use an Encrypted Variable in a Playbook**  
```yaml
- name: Use Vault Secrets
  debug:
    msg: "Database password is {{ vault_db_password }}"
```

✅ **Best Practices:**  
- **Never store plain-text secrets in Git.**  
- **Use vault with `--ask-vault-pass` for security.**  

---

### **3. Ansible Dynamic Inventory**  
✅ **Dynamic Inventory** fetches real-time host information from cloud providers (AWS, Azure, GCP).  

✅ **Example: AWS Dynamic Inventory (`aws_ec2.yaml`)**  
```yaml
plugin: amazon.aws.aws_ec2
regions:
  - us-east-1
keyed_groups:
  - key: tags.Name
    prefix: tag_
```

✅ **Run Playbook with Dynamic Inventory:**  
```sh
ansible-playbook -i aws_ec2.yaml playbook.yml
```

✅ **Benefits:**  
- No need for a **static inventory file**.  
- Fetches **real-time** instance details.  

---

### **4. Using Ansible with Terraform**  
✅ **Terraform provisions infrastructure**, while **Ansible configures** it.  

✅ **Example: Terraform Creates EC2, Ansible Configures It**  

✅ **Terraform Code (`main.tf`)**  
```hcl
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}
output "instance_ip" {
  value = aws_instance.web.public_ip
}
```

✅ **Ansible Playbook (`setup.yml`)**  
```yaml
- name: Configure Web Server
  hosts: all
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present
```

✅ **Run Ansible After Terraform:**  
```sh
terraform apply -auto-approve
ansible-playbook -i inventory setup.yml
```

✅ **Why Use Both?**  
- Terraform → **Infrastructure Provisioning**  
- Ansible → **Configuration Management**  

---

### **5. Ansible Tower/AWX (GUI & Automation)**  
✅ **Ansible Tower** (Paid) & **AWX** (Open-source) provide a **GUI** for Ansible.  

✅ **Features:**  
- Role-Based Access Control (RBAC).  
- Job scheduling and monitoring.  
- Web-based UI for playbook execution.  

✅ **Install AWX (Docker-Compose Method)**  
```sh
git clone https://github.com/ansible/awx.git
cd awx/installer
ansible-playbook -i inventory install.yml
```

✅ **Why Use Ansible Tower/AWX?**  
- **Centralized automation** → Manage all playbooks via GUI.  
- **Better security** → Uses role-based access.  

---

### **6. Ansible Tags**  
✅ **Tags** allow running **specific tasks** in a playbook instead of executing everything.  

✅ **Example Playbook with Tags:**  
```yaml
- name: Install Web Server
  hosts: web
  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
      tags: install

    - name: Restart Nginx
      service:
        name: nginx
        state: restarted
      tags: restart
```

✅ **Run Playbook with a Specific Tag:**  
```sh
ansible-playbook playbook.yml --tags "install"
```

✅ **Why Use Tags?**  
- Speeds up playbook execution.  
- Runs only **necessary tasks**.  

---

### **7. Ansible Callback Plugins**  
✅ **Callback Plugins** allow **customizing** playbook output and logging.  

✅ **Example: Enable the Profile Tasks Plugin (`ansible.cfg`)**  
```ini
[defaults]
callbacks_enabled = profile_tasks
```

✅ **Built-in Callbacks:**  
- `log_plays` → Logs playbook runs.  
- `yaml` → Formats output in YAML.  
- `json` → Outputs logs in JSON format.  

✅ **Custom Callback Plugin Example:**  
```python
from ansible.plugins.callback import CallbackBase

class CallbackModule(CallbackBase):
    def v2_runner_on_ok(self, result):
        print(f"TASK {result.task_name} OK: {result._result}")
```

✅ **Why Use Callback Plugins?**  
- **Custom reporting and logging**.  
- **Better debugging** during playbook execution.  

---

<h2>D. Ansible Integration & Best Practices</h2>

### **1. Using Ansible in CI/CD (Jenkins, GitHub Actions)**  
✅ **Objective:** Automate deployments using Ansible in a CI/CD pipeline.  

#### **🔹 Using Ansible with Jenkins**
✅ **Jenkins Pipeline Example (`Jenkinsfile`)**  
```groovy
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/myrepo/ansible-playbooks.git'
            }
        }
        stage('Run Ansible Playbook') {
            steps {
                sh 'ansible-playbook -i inventory setup.yml'
            }
        }
    }
}
```
✅ **Why Use Jenkins?**  
- Automates infrastructure and app deployment.  
- Can integrate with Terraform, Docker, Kubernetes.  

#### **🔹 Using Ansible with GitHub Actions**
✅ **GitHub Actions Workflow Example (`ansible-deploy.yml`)**  
```yaml
name: Ansible Deployment
on: push
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repo
        uses: actions/checkout@v2
      - name: Install Ansible
        run: sudo apt update && sudo apt install -y ansible
      - name: Run Ansible Playbook
        run: ansible-playbook -i inventory setup.yml
```
✅ **Why Use GitHub Actions?**  
- Automates deployments directly from **GitHub**.  
- Supports **cloud-based** runners.  

---

### **2. Ansible with Kubernetes (Deploying Containers)**  
✅ **Objective:** Use Ansible to deploy and manage **Kubernetes** applications.  

#### **🔹 Install `k8s` Ansible Collection**  
```sh
ansible-galaxy collection install community.kubernetes
```

#### **🔹 Example Playbook to Deploy an Nginx Pod (`deploy-nginx.yml`)**  
```yaml
- name: Deploy Nginx to Kubernetes
  hosts: localhost
  tasks:
    - name: Create a Kubernetes namespace
      kubernetes.core.k8s:
        name: mynamespace
        api_version: v1
        kind: Namespace
        state: present

    - name: Deploy Nginx Pod
      kubernetes.core.k8s:
        state: present
        definition:
          apiVersion: apps/v1
          kind: Deployment
          metadata:
            name: nginx-deployment
            namespace: mynamespace
          spec:
            replicas: 2
            selector:
              matchLabels:
                app: nginx
            template:
              metadata:
                labels:
                  app: nginx
              spec:
                containers:
                  - name: nginx
                    image: nginx:latest
```

✅ **Why Use Ansible for Kubernetes?**  
- Manages **Kubernetes resources declaratively**.  
- Works well with **Terraform for infrastructure provisioning**.  

---

### **3. Ansible for Cloud Provisioning (AWS, Azure, GCP)**  
✅ **Objective:** Use Ansible to provision and configure cloud infrastructure.  

#### **🔹 AWS Example: Create an EC2 Instance (`aws-setup.yml`)**  
```yaml
- name: Launch an EC2 Instance
  hosts: localhost
  tasks:
    - name: Create EC2 instance
      amazon.aws.ec2_instance:
        name: "Ansible-Managed-EC2"
        key_name: my-key
        instance_type: t2.micro
        image_id: ami-123456
        region: us-east-1
        security_groups: ["default"]
        wait: yes
```

#### **🔹 Azure Example: Create a VM (`azure-setup.yml`)**  
```yaml
- name: Create Azure VM
  hosts: localhost
  tasks:
    - name: Create Azure VM
      azure.azcollection.azure_rm_virtualmachine:
        name: ansible-vm
        resource_group: myResourceGroup
        location: eastus
        vm_size: Standard_B1s
        admin_username: azureuser
        os_type: Linux
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 18.04-LTS
          version: latest
```

✅ **Why Use Ansible for Cloud Provisioning?**  
- Automates VM, network, and storage provisioning.  
- Works with **Terraform for end-to-end automation**.  

---

### **4. Best Practices for Writing Playbooks**  
✅ **Follow these best practices to write clean and efficient playbooks:**  

🔹 **1. Use YAML Formatting Correctly**  
```yaml
- name: Example Playbook
  hosts: all
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present
```

🔹 **2. Use Variables Instead of Hardcoding Values**  
```yaml
- name: Install Web Server
  vars:
    package_name: apache2
  tasks:
    - name: Install Package
      apt:
        name: "{{ package_name }}"
        state: present
```

🔹 **3. Keep Playbooks Modular (Use Roles)**  
```sh
ansible-galaxy init my_role
```
```
my_role/
  ├── tasks/main.yml
  ├── handlers/main.yml
  ├── templates/
  ├── files/
  ├── vars/main.yml
  ├── defaults/main.yml
  ├── meta/main.yml
```

🔹 **4. Use `tags` to Run Specific Tasks**  
```yaml
- name: Restart Service
  service:
    name: nginx
    state: restarted
  tags: restart
```
Run with:  
```sh
ansible-playbook playbook.yml --tags restart
```

🔹 **5. Use Handlers for Idempotency**  
```yaml
handlers:
  - name: Restart Apache
    service:
      name: apache2
      state: restarted
```

✅ **Why Follow Best Practices?**  
- **Easier debugging and troubleshooting**.  
- **Reusable and maintainable** automation.  
- **Faster execution with efficient task execution**.  

---

### **5. Ansible Security Best Practices**  
✅ **Follow these security best practices to protect Ansible automation:**  

🔹 **1. Use Ansible Vault for Secrets Management**  
```sh
ansible-vault encrypt secrets.yml
ansible-vault decrypt secrets.yml
```
```yaml
- name: Access Vault Secrets
  debug:
    msg: "Database password is {{ vault_db_password }}"
```

🔹 **2. Limit `become` Privileges (Sudo Access)**  
```yaml
- name: Run Task as Root
  become: yes
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present
```
✅ **Best Practice:** Restrict sudo usage only when necessary.  

🔹 **3. Use Role-Based Access Control (RBAC)** in Ansible Tower/AWX  
- Assign **different user roles** for different tasks.  
- Restrict who can **run or modify playbooks**.  

🔹 **4. Disable `host_key_checking` for Production**  
```ini
[defaults]
host_key_checking = False
```
✅ **Best Practice:** Use SSH key verification in production.  

🔹 **5. Limit Inventory Exposure**  
- Avoid **keeping plain-text inventory files**.  
- Use **dynamic inventory plugins** instead of static host lists.  

✅ **Why Follow Security Best Practices?**  
- **Protects sensitive credentials**.  
- **Prevents unauthorized access**.  
- **Reduces the risk of security breaches**.  

---

### **🚀 Summary of Ansible Integration & Best Practices**
| Topic | Key Takeaways |
|-------|--------------|
| **CI/CD Integration** | Use Jenkins or GitHub Actions for automated Ansible deployments. |
| **Kubernetes** | Use `kubernetes.core.k8s` to manage containers. |
| **Cloud Provisioning** | Automate AWS, Azure, and GCP infrastructure. |
| **Best Playbook Practices** | Use variables, roles, handlers, and tags efficiently. |
| **Security Best Practices** | Use Vault for secrets, RBAC for access control, and limit `become` usage. |

---

