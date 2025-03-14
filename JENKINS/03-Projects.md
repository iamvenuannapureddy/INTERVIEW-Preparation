<h1>Hands-on Jenkins Projects for Interviews</h1>
<h2>Basic Projects</h2>

### **1. Automate a Simple Java/Maven Build Using Jenkins**  
✅ **Objective:** Set up Jenkins to build a Java project using Maven automatically.  

✅ **Steps:**  
1. **Install Maven in Jenkins:**  
   - Go to `Manage Jenkins → Global Tool Configuration → Add Maven`.  
2. **Create a Freestyle Job:**  
   - `New Item → Freestyle Project → Source Code Management → Git`.  
3. **Configure Build Step:**  
   - Select **"Invoke top-level Maven targets"** and use:  
     ```
     clean package
     ```
4. **Save and Build the Job.**  

✅ **Example Jenkinsfile for Automating Maven Build:**  
```groovy
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/myrepo/java-app.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
    }
}
```

✅ **Outcome:**  
- Jenkins **automatically compiles Java code**, runs tests, and generates a JAR/WAR file.  

---

### **2. Create a Jenkins Pipeline That Triggers on Git Commits**  
✅ **Objective:** Configure Jenkins to **trigger a pipeline automatically on GitHub commits**.  

✅ **Steps:**  
1. **Create a Multibranch Pipeline Job:**  
   - `New Item → Multibranch Pipeline → Add GitHub Repo`.  
2. **Configure Webhook in GitHub:**  
   - Go to `Settings → Webhooks → Add Webhook`.  
   - **Payload URL:**  
     ```
     http://JENKINS_URL/github-webhook/
     ```
   - Select **push events**.  
3. **Add a `Jenkinsfile` to the Repository.**  

✅ **Example Jenkinsfile:**  
```groovy
pipeline {
    agent any
    triggers {
        githubPush()
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
    }
}
```

✅ **Outcome:**  
- **Every Git commit triggers a Jenkins pipeline execution**.  

---

### **3. Deploy an Application to Tomcat Using Jenkins**  
✅ **Objective:** Automatically deploy a WAR file to an Apache Tomcat server.  

✅ **Steps:**  
1. **Install "Deploy to Container Plugin"** in Jenkins.  
2. **Create a Pipeline Job** with Tomcat credentials.  
3. **Use `scp` or `Deploy to Container Plugin` to transfer WAR file.**  

✅ **Example Jenkinsfile for Tomcat Deployment:**  
```groovy
pipeline {
    agent any
    environment {
        TOMCAT_USER = credentials('tomcat-user')
        TOMCAT_URL = 'http://tomcat-server:8080/manager/text'
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Deploy to Tomcat') {
            steps {
                sh '''
                curl -u $TOMCAT_USER -T target/myapp.war "$TOMCAT_URL/deploy?path=/myapp&update=true"
                '''
            }
        }
    }
}
```

✅ **Outcome:**  
- **Jenkins automatically deploys the WAR file to Tomcat** after a successful build.  

---

### **📌 Summary of Basic Jenkins Projects**  

| Project | Key Takeaways |
|---------|--------------|
| **Automate Java/Maven Build** | Jenkins builds Java projects using Maven |
| **Trigger Jenkins on Git Commits** | Uses **GitHub Webhooks** to trigger jobs automatically |
| **Deploy to Tomcat** | Automates WAR deployment using Jenkins |

---

### **4. Build and Deploy a Dockerized Application with Jenkins**  
✅ **Objective:** Automate **Docker image building, pushing to Docker Hub, and deployment** using Jenkins.  

✅ **Steps:**  
1. **Install Docker on Jenkins Server.**  
2. **Create a Jenkins Pipeline Job.**  
3. **Authenticate with Docker Hub** using Jenkins Credentials Manager.  
4. **Build, Push, and Run the Docker Container.**  

✅ **Example Jenkinsfile:**  
```groovy
pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'myrepo/myapp:latest'
        DOCKER_CREDENTIALS = credentials('docker-hub-credentials')
    }
    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/myrepo/docker-app.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }
        stage('Push to Docker Hub') {
            steps {
                sh 'echo $DOCKER_CREDENTIALS_PSW | docker login -u $DOCKER_CREDENTIALS_USR --password-stdin'
                sh 'docker push $DOCKER_IMAGE'
            }
        }
        stage('Deploy Container') {
            steps {
                sh 'docker run -d -p 8080:8080 $DOCKER_IMAGE'
            }
        }
    }
}
```

✅ **Outcome:**  
- **Automatically builds and pushes Docker images to Docker Hub**.  
- **Deploys the containerized app** on the target server.  

---

### **5. CI/CD Pipeline with Jenkins, SonarQube, and Nexus**  
✅ **Objective:** Automate **code quality analysis, artifact storage, and deployments** using Jenkins.  

✅ **Steps:**  
1. **Install SonarQube and Nexus** on Jenkins Server.  
2. **Run SonarQube scan for static code analysis.**  
3. **Store build artifacts in Nexus Repository.**  
4. **Deploy application if SonarQube passes quality checks.**  

✅ **Example Jenkinsfile:**  
```groovy
pipeline {
    agent any
    environment {
        SONAR_URL = 'http://sonarqube-server:9000'
        SONAR_TOKEN = credentials('sonar-token')
        NEXUS_URL = 'http://nexus-server/repository/releases/'
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/myrepo/java-app.git'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                sh 'mvn sonar:sonar -Dsonar.host.url=$SONAR_URL -Dsonar.login=$SONAR_TOKEN'
            }
        }
        stage('Build and Upload to Nexus') {
            steps {
                sh 'mvn clean package'
                sh "curl -u admin:admin123 --upload-file target/myapp.jar $NEXUS_URL/myapp-1.0.jar"
            }
        }
        stage('Deploy Application') {
            steps {
                sh 'scp target/myapp.jar user@server:/opt/app/'
            }
        }
    }
}
```

✅ **Outcome:**  
- **Ensures code quality with SonarQube before deployment.**  
- **Stores build artifacts securely in Nexus.**  
- **Automates deployment only after passing all checks.**  

---

### **6. Automated Testing Pipeline with Selenium and Jenkins**  
✅ **Objective:** Run **automated UI tests** using Selenium in Jenkins pipeline.  

✅ **Steps:**  
1. **Install Selenium on Jenkins Server.**  
2. **Write Selenium test cases using Java/TestNG.**  
3. **Run tests after every build to detect UI issues.**  
4. **Generate and store test reports.**  

✅ **Example Jenkinsfile:**  
```groovy
pipeline {
    agent any
    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/myrepo/selenium-tests.git'
            }
        }
        stage('Run Selenium Tests') {
            steps {
                sh 'mvn test -Dsurefire.suiteXmlFiles=testng.xml'
            }
        }
        stage('Archive Test Reports') {
            steps {
                junit '**/target/surefire-reports/*.xml'
            }
        }
    }
}
```

✅ **Outcome:**  
- **Detects UI issues early by automating Selenium tests.**  
- **Generates and stores test reports in Jenkins.**  

---

### **📌 Summary of Intermediate Jenkins Projects**  

| Project | Key Takeaways |
|---------|--------------|
| **Build & Deploy Dockerized App** | **Automates Docker builds, pushes, and deployments** |
| **CI/CD with SonarQube & Nexus** | **Ensures code quality and manages artifacts before deployment** |
| **Automated Selenium Testing** | **Runs UI tests and stores reports automatically** |

---

### **7. Deploy a Kubernetes Application Using Jenkins and Helm**  
✅ **Objective:** Automate **Kubernetes deployments using Helm** in a Jenkins pipeline.  

✅ **Steps:**  
1. **Install Helm and kubectl** on Jenkins.  
2. **Use Helm to package and deploy the application to Kubernetes.**  
3. **Trigger updates on code changes.**  

✅ **Example Jenkinsfile:**  
```groovy
pipeline {
    agent any
    environment {
        KUBE_CONFIG = credentials('kubeconfig')
    }
    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/myrepo/k8s-app.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t myrepo/k8s-app:latest .'
                sh 'docker push myrepo/k8s-app:latest'
            }
        }
        stage('Deploy with Helm') {
            steps {
                sh 'helm upgrade --install my-app ./helm-chart --set image.tag=latest'
            }
        }
    }
}
```

✅ **Outcome:**  
- **Automates Kubernetes deployments using Helm.**  
- **Ensures consistency and versioning of deployments.**  

---

### **8. Jenkins Pipeline for AWS Infrastructure with Terraform & Ansible**  
✅ **Objective:** Automate **AWS infrastructure provisioning using Terraform and Ansible** in a Jenkins pipeline.  

✅ **Steps:**  
1. **Use Terraform to provision AWS EC2, S3, RDS.**  
2. **Use Ansible to configure and deploy applications.**  
3. **Manage infrastructure as code with Jenkins.**  

✅ **Example Jenkinsfile:**  
```groovy
pipeline {
    agent any
    environment {
        AWS_CREDENTIALS = credentials('aws-access-key')
    }
    stages {
        stage('Provision Infrastructure') {
            steps {
                sh 'terraform init && terraform apply -auto-approve'
            }
        }
        stage('Configure Servers with Ansible') {
            steps {
                sh 'ansible-playbook -i inventory setup.yml'
            }
        }
    }
}
```

✅ **Outcome:**  
- **Automates AWS infrastructure setup and configuration.**  
- **Ensures reproducibility and scalability.**  

---

### **9. Jenkins with Blue-Green Deployment for Zero Downtime Releases**  
✅ **Objective:** Deploy new versions **without downtime** by routing traffic between **Blue (current) and Green (new) environments**.  

✅ **Steps:**  
1. **Deploy new version (Green) while keeping old version (Blue) live.**  
2. **Run tests on Green, switch traffic if successful.**  
3. **Rollback to Blue if issues occur.**  

✅ **Example Jenkinsfile:**  
```groovy
pipeline {
    agent any
    environment {
        BLUE_SERVICE = 'my-app-blue'
        GREEN_SERVICE = 'my-app-green'
    }
    stages {
        stage('Deploy Green Version') {
            steps {
                sh 'kubectl apply -f deployment-green.yaml'
            }
        }
        stage('Switch Traffic to Green') {
            steps {
                sh 'kubectl patch service my-app --patch-file=green-service.yaml'
            }
        }
    }
}
```

✅ **Outcome:**  
- **Zero downtime deployment.**  
- **Rollback option in case of failures.**  

---

### **10. Jenkins Monitoring Setup with Prometheus and Grafana**  
✅ **Objective:** Monitor Jenkins performance using **Prometheus for metrics collection** and **Grafana for visualization**.  

✅ **Steps:**  
1. **Install Prometheus Plugin in Jenkins.**  
2. **Enable the metrics endpoint (`/prometheus`).**  
3. **Configure Prometheus to scrape Jenkins metrics.**  
4. **Visualize data using Grafana dashboards.**  

✅ **Example Prometheus Config for Jenkins:**  
```yaml
scrape_configs:
  - job_name: 'jenkins'
    metrics_path: '/prometheus'
    static_configs:
      - targets: ['jenkins-server:8080']
```

✅ **Outcome:**  
- **Monitors Jenkins job failures, queue times, and system health.**  
- **Improves CI/CD pipeline reliability.**  

---

### **📌 Summary of Advanced Jenkins Projects**  

| Project | Key Takeaways |
|---------|--------------|
| **Kubernetes Deployment with Helm** | **Automates K8s deployments with Helm charts** |
| **AWS Infrastructure with Terraform & Ansible** | **Manages AWS infrastructure as code** |
| **Blue-Green Deployment** | **Ensures zero downtime application updates** |
| **Jenkins Monitoring with Prometheus & Grafana** | **Tracks Jenkins performance and job execution metrics** |

---

