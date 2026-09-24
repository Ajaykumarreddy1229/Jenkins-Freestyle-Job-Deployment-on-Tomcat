# Jenkins Freestyle Job – Deployment on Tomcat

> **Topic:** Jenkins Freestyle Job
> **Project:** Java Web Application Deployment
> **Tools:** Jenkins, Maven, GitHub, Apache Tomcat, AWS EC2

---

# 1. Project Overview

This project demonstrates how to use a **Jenkins Freestyle Job** to automate the build and deployment of a Java web application to an Apache Tomcat server.

The basic CI/CD flow is:

```text
CODE
  ↓
BUILD
  ↓
TEST
  ↓
ARTIFACT
  ↓
DEPLOYMENT
```

---

# 2. Project Architecture

The project uses two servers.

```text
                    GitHub
                      |
                      ↓
                Jenkins Server
                      |
                      |
                Maven Build
                      |
                      ↓
                  WAR File
                      |
                      ↓
                Tomcat Server
                      |
                      ↓
               Java Web App
```

---

# 3. Servers Used

## Jenkins Server

Jenkins is used for:

```text
CI Automation
Source Code Checkout
Maven Build
WAR Generation
Tomcat Deployment
```

## Tomcat Server

Tomcat is used for:

```text
Application Deployment
Application Execution
Web Application Hosting
```

---

# 4. Tomcat Server Setup

Launch an **Amazon Linux 2023 EC2 instance** for the Tomcat server.

---

## Step 1: Install Java

Install Java 21:

```bash
sudo dnf install java-21-amazon-corretto -y
```

Check Java:

```bash
java -version
```

---

# 5. Download Tomcat

Install `wget`:

```bash
sudo yum install -y wget
```

Download Apache Tomcat:

```bash
wget https://dlcdn.apache.org/tomcat/tomcat-11/v11.0.26/bin/apache-tomcat-11.0.26.tar.gz
```

---

# 6. Extract Tomcat

Extract the downloaded Tomcat archive:

```bash
tar -zxvf apache-tomcat-11.0.26.tar.gz
```

Tomcat directory:

```text
apache-tomcat-11.0.26/
```

---

# 7. Configure Tomcat User and Roles

Go to the Tomcat configuration directory:

```bash
cd apache-tomcat-11.0.26/conf/
```

Open:

```bash
vi tomcat-users.xml
```

Configure a Tomcat user with the required roles.

---

# 8. Required Tomcat Roles

For Jenkins deployment, the required roles can include:

```xml
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
```

A Tomcat user can then be configured with the appropriate roles.

Example structure:

```xml
<user username="jenkins"
      password="YOUR_PASSWORD"
      roles="manager-gui,manager-script"/>
```

> Use your own secure password. Do not commit Tomcat credentials to GitHub.

---

# 9. Configure Tomcat Manager Application

The Tomcat Manager application is used for deployment.

Go to:

```bash
cd apache-tomcat-11.0.26/webapps/manager/META-INF
```

Open:

```bash
vi context.xml
```

Configure the Manager application so that Jenkins can communicate with Tomcat.

> The exact access-control configuration depends on the Tomcat version and your network/security setup. Avoid disabling security controls unnecessarily.

---

# 10. Start Tomcat

Go to the Tomcat `bin` directory:

```bash
cd apache-tomcat-11.0.26/bin
```

Start Tomcat:

```bash
sh startup.sh
```

Check that Tomcat is running.

Tomcat uses:

```text
Port: 8080
```

---

# 11. Access Tomcat

Open:

```text
http://<TOMCAT-SERVER-IP>:8080
```

Replace `<TOMCAT-SERVER-IP>` with the address of your Tomcat server.

---

# 12. Jenkins Configuration

The Jenkins server is used to automate the build and deployment process.

The Jenkins job will:

```text
Clone Code
    ↓
Build Application
    ↓
Generate WAR
    ↓
Deploy WAR
    ↓
Tomcat
```

---

# 13. Install Deploy to Container Plugin

In Jenkins:

```text
Manage Jenkins
      ↓
Manage Plugins
      ↓
Available Plugins
      ↓
Search: Deploy to Container
```

Install the:

```text
Deploy to Container
```

plugin.

This plugin provides Jenkins integration for deploying applications to supported servlet containers such as Tomcat.

---

# 14. Create Jenkins Freestyle Job

Create a new Jenkins job:

```text
New Item
   ↓
Freestyle Project
```

Give the project a name.

Example:

```text
Tomcat-Deployment
```

---

# 15. Source Code Management

Inside the Jenkins job configuration:

```text
Source Code Management
        ↓
Git
```

Provide the Git repository URL.

Example:

```text
https://github.com/ReyazShaik/java-project-maven-new.git
```

Use your own repository URL if you are working with a different project.

---

# 16. GitHub → Jenkins Flow

The source-code flow is:

```text
GitHub Repository
       ↓
      Git
       ↓
Jenkins Freestyle Job
       ↓
Workspace
```

Jenkins checks out the source code into the job workspace.

---

# 17. Build Step

Go to:

```text
Build Steps
```

Select:

```text
Invoke top-level Maven targets
```

Use:

```text
clean package
```

This runs Maven and generates the application artifact.

---

# 18. Maven Build Flow

```text
Source Code
     ↓
   Maven
     ↓
   Clean
     ↓
  Compile
     ↓
   Test
     ↓
  Package
     ↓
  WAR File
```

The generated WAR file is normally located inside:

```text
target/
```

Example:

```text
target/
   └── mywebapp.war
```

---

# 19. Deploy WAR File to Tomcat

After configuring the Maven build, go to:

```text
Post-build Actions
        ↓
Deploy war/ear to a container
```

Configure the deployment.

---

# 20. WAR/EAR Files

Example:

```text
**/*.war
```

This tells Jenkins to locate the generated WAR file.

---

# 21. Context Path

Example:

```text
mywebapp
```

The context path determines the URL used to access the deployed application.

Example:

```text
http://<TOMCAT-SERVER-IP>:8080/mywebapp
```

---

# 22. Configure Tomcat Container

Inside the deployment configuration:

```text
Add Container
      ↓
Tomcat
```

Configure the Tomcat server information.

Example:

```text
Tomcat URL:
http://<TOMCAT-SERVER-IP>:8080/
```

Configure the required Jenkins credentials for the Tomcat user.

---

# 23. Jenkins Credentials

Jenkins should store the Tomcat username and password securely.

Conceptually:

```text
Jenkins
   |
   ↓
Jenkins Credentials
   |
   ├── Tomcat Username
   └── Tomcat Password
```

Do not put the credentials directly inside the Jenkins job configuration when a secure credentials reference is available.

---

# 24. Complete Freestyle Job Flow

```text
                     GitHub
                       |
                       ↓
               Jenkins Freestyle
                       |
                       ↓
                  Git Checkout
                       |
                       ↓
                 Maven Build
                       |
                       ↓
                  mvn clean package
                       |
                       ↓
                    WAR File
                       |
                       ↓
              Deploy to Container
                       |
                       ↓
                Tomcat Server
                       |
                       ↓
               Java Web Application
```

---

# 25. Build the Jenkins Job

After completing the configuration:

```text
Save
  ↓
Build Now
```

Jenkins will execute the configured steps.

---

# 26. What Jenkins Does

When the job starts:

```text
1. Clone source code from GitHub
             ↓
2. Execute Maven build
             ↓
3. Run tests
             ↓
4. Generate WAR artifact
             ↓
5. Connect to Tomcat
             ↓
6. Deploy WAR file
```

---

# 27. Application Deployment

After successful deployment, the application can be accessed using:

```text
http://<TOMCAT-SERVER-IP>:8080/mywebapp
```

---

# 28. End-to-End Architecture

```text
                         Developer
                             |
                             ↓
                          GitHub
                             |
                             ↓
                  ┌───────────────────┐
                  │      Jenkins      │
                  │  Freestyle Job    │
                  └─────────┬─────────┘
                            |
                            ↓
                      Git Checkout
                            |
                            ↓
                      Maven Build
                            |
                     ┌──────┴──────┐
                     ↓             ↓
                  Compile         Test
                     |
                     ↓
                  Package
                     |
                     ↓
                  WAR File
                     |
                     ↓
             Deploy to Container
                     |
                     ↓
              ┌───────────────┐
              │ Tomcat Server │
              │    :8080      │
              └───────┬───────┘
                      |
                      ↓
              Java Web Application
```

---

# 29. Freestyle Job vs Pipeline

This project helped me understand the basic difference between Jenkins Freestyle Jobs and Pipeline jobs.

### Freestyle Job

```text
Jenkins UI
    ↓
Configure Job
    ↓
Build Steps
    ↓
Post-build Actions
```

### Pipeline

```text
Jenkinsfile
    ↓
Pipeline as Code
    ↓
Stages
    ↓
Steps
```

A Freestyle Job is configured mainly through the Jenkins UI, while a Pipeline can define the automation workflow as code.

---

# 30. Important Concepts Learned

Through this project, I practiced:

```text
✓ Jenkins Freestyle Jobs
✓ GitHub Integration
✓ Git Checkout
✓ Maven Build
✓ Maven Testing
✓ WAR Artifact Creation
✓ Apache Tomcat
✓ Tomcat Manager
✓ Jenkins Credentials
✓ Deploy to Container Plugin
✓ WAR Deployment
✓ AWS EC2
✓ CI/CD Workflow
```

---

# 31. Security Notes

Never commit sensitive information into GitHub.

Do not expose:

```text
❌ Tomcat Passwords
❌ Jenkins Passwords
❌ AWS Access Keys
❌ AWS Secret Keys
❌ SSH Private Keys
❌ API Tokens
```

Use:

```text
Jenkins Credentials
AWS IAM Roles
GitHub Secrets
Environment Variables
```

---

# 32. Key Takeaway

The main workflow I learned from this project is:

```text
SOURCE CODE
     ↓
   GITHUB
     ↓
   JENKINS
     ↓
    BUILD
     ↓
    TEST
     ↓
  WAR FILE
     ↓
   TOMCAT
     ↓
 APPLICATION
```

This project helped me understand how Jenkins Freestyle Jobs can automate the build and deployment of a Java web application.

---

# 33. DevOps Learning Flow

```text
GitHub
  ↓
Jenkins
  ↓
Freestyle Job
  ↓
Maven
  ↓
WAR Artifact
  ↓
Tomcat
  ↓
Web Application
```

**Learn → Practice → Troubleshoot → Automate → Improve 🚀**
