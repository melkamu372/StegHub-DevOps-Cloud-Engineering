# Tooling Website deployment automation with Continuous Integration
In this project we are going to start automating part of our routine tasks with a free and open source automation server - **Jenkins**. It is one of the mostl popular CI/CD tools.

Acording to Circle CI, Continuous integration (CI) is a software development strategy that increases the speed of development while ensuring the quality of the code that teams deploy. Developers continually commit code in small increments which is then automatically built and tested before it is merged with the shared repository.

**Task**
Enhance the architecture prepared in `Project 8` by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS server.

# Step 1 - Install Jenkins server
1. Create an aws EC2 instance server based on Ubuntu Server 24.04 LTS and name it `Jenkins`

2. Install JDK since Jenkins is a Java-based application
```
sudo apt update
```
```
sudo apt install default-jdk-headless
```
 **check if the installation worked**
```
java -version
```
3. Install Jenkins
Add the Jenkins repository:
```
sudo wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
```
**Put the Jenkins repository to the list of package sources**
```
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
```
```
sudo apt update
```
**Installing and configuring Jenkins**
```
sudo apt-get install jenkins
```
**Enable jenkins**
```
sudo systemctl enable jenkins
```

**Check Jenkins is up and running**
```
sudo systemctl status jenkins
```
4. By default Jenkins server uses TCP port 8080 - open it by creating a new Inbound rule in our EC2 Security Group



5. Perform initial Jenkins setup

**Obtain Initial Admin Password**
After accessing Jenkins through your browser via http: <Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080 You will be prompted to provide a default admin password. This password is located in the file `/var/lib/jenkins/secrets/initialAdminPassword`

To retrieve it from our server:
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
Copy the password from this file and paste it into the command shell prompt. Subsequently, you’ll receive a unique code for Jenkins initialization.

This step enables us to authenticate as the administrator and proceed with Jenkins setup and configuration.

6. Create the First Admin User.
we will create the first admin user for our system, granting initial administrative privileges to manage and configure the platform.


7. Install Suggested Plugins.


8. Jenkins Setup Completion.

With the installation and configuration completed, Jenkins is now ready for use. Navigate to the Jenkins dashboard and click on “New Item” to create a new job.

# Step 2 - Configure Jenkins to retrieve source codes from GitHub using Webhooks
In this part, you will learn how to configure a simple Jenkins job/project. This job will will be triggered by GitHub webhooks and will execute a `build` task to retrieve codes from GitHub and store it locally on Jenkins server.

1. Enable webhooks in our GitHub repository settings

2. Go to Jenkins web console, click `New Item` and create a "Freestyle project"

 To connect our GitHub repository, we will need to provide its URL, we can copy from the repository itself


In configuration of our Jenkins freestyle project choose Git repository, provide there the link to our Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.


Save the configuration and let us try to run the build. For now we can only do it manually. Click ``Build Now` button, if we have configured everything correctly, the build will be successfull and you will see it under #1


You can open the build and check in "Console Output" if it has run successfully.



But this build does not produce anything and it runs only when we trigger it manually. Let us fix it.

Click "Configure" your job/project and add these two configurations
Configure triggering the job from GitHub webhook:


3. Configure `Post-build Actions` to archive all the files - files resulted from a build are called "artifacts".


