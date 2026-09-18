# Jenkins-Freestyle-Job-Deployment-on-Tomcat
**📌 Project Overview**:
This project demonstrates how to use Jenkins Freestyle Job to automate the build and deployment of a Java web application to an Apache Tomcat server.
**🔄 Deployment Flow**:
CODE → BUILD → TEST → ARTIFACT → DEPLOYMENT
**🏗️ Architecture**
The project uses two servers:
1.Jenkins Server – Used for CI and automation
2.Tomcat Server – Used to deploy and run the Java web application

**1. Tomcat Server Setup**
Launch an Amazon Linux 2023 EC2 instance and install Java and Tomcat.
**Step 1: Install Java**:
dnf install java-21-amazon-corretto -y
**Step 2: Download Tomcat**:
yum install -y wget
wget https://dlcdn.apache.org/tomcat/tomcat-11/v11.0.26/bin/apache-tomcat-11.0
Step 3: Extract Tomcat
tar -zxvf apache-tomcat-11.0.26.tar.gz
**Step 4: Configure Tomcat User and Roles**
Go to:
cd apache-tomcat-11.0.26/conf/
vi tomcat-users.xml
Configure the required Tomcat roles and user for Jenkins deployment.
**Required roles:**
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
Create a Tomcat user with the required roles.

**Step 5: Configure Manager Application**
Go to:
cd apache-tomcat-11.0.26/webapps/manager/META-INF
vi context.xml
Update the configuration so Jenkins can communicate with the Tomcat Manager application.

**Step 6: Start Tomcat**
cd apache-tomcat-11.0.26/bin
sh startup.sh
Tomcat runs on port 8080.
Access it using:
http://<TOMCAT-SERVER-IP>:8080


**2. Jenkins Configuration**
Install Deploy to Container Plugin
In Jenkins:
Manage Jenkins → Manage Plugins
Search for:
Deploy to Container
Install the plugin to integrate Jenkins with Tomcat.

**3. Create Jenkins Freestyle Job**
Create a new Jenkins job:
New Item → Freestyle Project
Source Code Management
Select:
Git
Provide the Git repository URL.
Example:
https://github.com/ReyazShaik/java-project-maven-new.git
Build Step
Select:
Invoke top-level Maven targets
Use:
clean package
This builds the Maven project and generates the WAR artifact.

**4. Deploy WAR File to Tomcat**
Under:
Post-build Actions
Select:
Deploy war/ear to a container
Configure:
WAR/EAR files: **/*.war
Context path: mywebapp
Add the Tomcat container and configure the required Jenkins credentials.
Provide the Tomcat server URL:
http://<TOMCAT-SERVER-IP>:8080/

**5. Build the Job**
Click:
Build Now
Jenkins will:
Clone the source code from Git
Build the Maven project
Generate the WAR file
Deploy the WAR file to Tomcat
After successful deployment, access the application using:
http://<TOMCAT-SERVER-IP>:8080/mywebapp
