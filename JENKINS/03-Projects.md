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

