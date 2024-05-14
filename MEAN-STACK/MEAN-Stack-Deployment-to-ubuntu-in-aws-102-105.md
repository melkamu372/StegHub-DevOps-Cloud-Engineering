#  MEAN Stack Deployment to Ubuntu in AWS- project 4. 

> The MEAN stack is a popular open-source JavaScript software stack used for building dynamic web applications. The name "MEAN" is an acronym that stands for:


1. **MongoDB**: A NoSQL database that stores data in JSON-like documents. It's known for its flexibility and scalability, making it suitable for handling large volumes of unstructured or semi-structured data.

2. **Express.js**: A minimalist web application framework for Node.js. Express.js simplifies the process of building web applications and APIs by providing a robust set of features for handling HTTP requests, routing, middleware, and more.

3. **Angular (formerly AngularJS)**: A front-end JavaScript framework maintained by Google. Angular provides a comprehensive toolkit for building single-page applications (SPAs) with features like data binding, dependency injection, routing, and more.

4. **Node.js**: A server-side JavaScript runtime built on Chrome's V8 JavaScript engine. Node.js enables developers to run JavaScript code outside of a web browser, making it possible to build server-side applications and APIs using JavaScript.


# Step 0 - Preparing prerequisites

>Hint #1: In order to complete this project you will need an AWS account and a virtual server with Ubuntu Server OS.
Log to aws account console and create EC2 instance of t2.micro type with Ubuntu 24.04 LTS (HVM) Server launch in the default region us-east-1.  When you create your EC2 Instances, you can add Tag "Name" to it with a value that corresponds to a current project you are working on - it will be reflected in the name of the EC2 Instance.

1. Log to aws account console and create EC2 instance of t2.micro type with Ubuntu 24.04 LTS (HVM) Server launch in the default region us-east-1.

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/adfd1ab4-cb63-4425-a529-688e2be33d32)

2. Select Ubuntu free tire eligable version

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/ec166508-1853-4d50-8a33-1ea2d37b4b17)


3. Create new key pair or select existing key

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/64a8dc41-7021-4669-a928-52d183a4a965)

4. Network setting create new security group or use existing security group

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/3b4a61df-cc57-4d45-bc40-dfaf2bcf16dd)


5. Configure Storage and launch the instance

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/2a090cfe-07f4-410f-b0ca-4a1998dd3d6e)

6. View Instance

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/b410e746-5853-43a3-bbd6-cf3b0ae8fd8e)


7. Instance Details

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d6a974d2-6824-4d86-9cf1-0c26d6641101)

8. Configure security group with the following inbound rules:

  - Allow traffic on port 22 (SSH) with source from any IP address. This is opened by default.
  - Allow traffic on port 80 (HTTP) with source from anywhere on the internet.
  - Allow traffic on port 443 (HTTPS) with source from anywhere on the internet.
  - Allow traffic on port 5000 (Custom) with source from anywhere on the internet.
    
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/761bf0f3-f25a-436e-87e9-ab8a38e0d82e)

9. Default VPC Network Configuration

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/6fca2513-ce0c-4640-ab8d-b3a129457e01)

10. Connect to EC2 instance from SSH client

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/1570d064-5d53-4268-b2fb-c9a7d33fc613)

11. Give Permission for the private ssh key that we used either created or existing during launching instance and downloaded
```
sudo chmod 0400 melkamu_key.pem
```
12. Then connect the instance using instance Public Ip Addresss or domain name
```
ssh -i melkamu_key.pem ubuntu@3.87.100.101
```
or
```
ssh -i "melkamu_key.pem" ubuntu@ec2-3-87-100-101.compute-1.amazonaws.com
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/5789d228-9c85-4c3c-b76d-b4913135fca5)


> Hint #2:
> 
> ![aws cli](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d26e55db-a14c-4ebc-bd85-96f909558ed3) In previous projects we used different tools to connect to an EC2 instance, but if you do not want to install or launch anything outside of AWS, you can open your CLI straight from Web Console in AWS.


AWS Command Line Interface (AWS CLI) is an open source tool from Amazon Web Services (AWS). You can use it to interact with AWS services using commands in your command line shell.
It provides a unified interface to manage AWS resources, enabling users to perform tasks such as deploying applications, managing EC2 instances, configuring AWS services, and automating tasks through scripts.
With AWS CLI, users can access almost all AWS services and features without needing to navigate through the AWS Management Console, making it particularly useful for automating repetitive tasks, scripting workflows, and integrating AWS with other tools and systems. You can use like this:

### Here we see installing and Configuring AWS CLI on Windows 

 **Install AWS CLI**
1. **Download the MSI Installer**: Go to the [AWS CLI Installation page](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-windows.html) and download the MSI installer for Windows.
2. **Run the Installer**: Double-click the downloaded MSI file to launch the installer.
3. **Follow Installation Wizard**: Follow the on-screen instructions in the installation wizard. You can choose the default settings or customize the installation as needed.
4. **Verify Installation**: Once the installation is complete, open a command prompt (CMD) or PowerShell window and type `aws --version` to verify that the AWS CLI has been installed correctly.

```
aws --version
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/7a73a7d0-4e9c-4a95-8fa6-1c9ac02ac199)

 **Configure AWS CLI**
 Download an AWS access key, that generated through the AWS Management Console
Here's a step-by-step guide on how to do it:
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/ccd15a32-e9ee-4699-ab99-20ff5bce6cb7)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/c8590e2e-96ba-4a1f-b6d2-283bbb11bcde)


1. **Open Command Prompt or PowerShell**: Open a command prompt or PowerShell window.
2. **Run Configuration Wizard**: Type `aws configure` and press Enter.
3. **Enter AWS Access Key ID**: You'll be prompted to enter your AWS Access Key ID. You can find this in your AWS Management Console under IAM (Identity and Access Management).
4. **Enter AWS Secret Access Key**: Enter your AWS Secret Access Key when prompted. This is the private key associated with your Access Key ID.
5. **Default Region**: Specify a default region. This is the region where your AWS resources will be created by default. For me , I use `us-east-1`.
6. **Default Output Format**: Choose a default output format for the AWS CLI. The default is typically `json`.
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/ec8bb030-2d50-4aea-9e35-119e406e6e79)

**Start Using AWS CLI**

Once you've configured the AWS CLI, you can start using it to interact with AWS services from the command line. Here are a few examples:

- To list EC2 instances:
```
aws ec2 describe-instances
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/f05c2635-46f0-442f-b30e-061aa64afab1)



### Task

**_In this assignment we are going to implement a simple Book Register web form using MEAN stack._**

# Stp 1: Install NodeJs

**Node. js** is a JavaScript runtime built on Chrome's V8 JavaScript engine. In this tutorial _Node.js_ is used to set up the Express routes and AngularJS controllers.

1. Update ubuntu
```
sudo apt update
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a75600e4-59a2-4136-b59e-e1e2173878bc)

2. Upgrade ubuntu
```
sudo apt upgrade
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/6fe17de5-4ac6-42ab-9a14-7c5e5ef7e550)

3. add certificates
```
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a0d04484-10bf-4f0f-a0c5-cb904c7ef629)

```
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/4cbd81fa-1bab-47e5-b18c-f2433be4c193)

4. Install NodeJS
```
sudo apt install -y nodejs
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/ed27a1f0-4324-4c5f-b530-90801d9a2807)
5. confirm installation
```
node -v
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/c325256b-764c-4396-8dbe-3e136c6cf158)

# Step 2: Install MongoDB
**MongoDB** stores data in flexible, JSON-like documents. Fields in a database can vary from document to document and data structure can be changed over time. For our example application, we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages.
0. Add a key from a key server to the list of trusted keys used by the Advanced Package Tool (APT).
```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/4de16558-a4df-4169-ae1d-faaf1389d724)

```
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/23ae917d-7596-43bd-b79d-7cee77682482)

1. Install MongoDB
```
sudo apt install -y mongodb
```
2. Start The server
```
sudo service mongodb start
```

3. Verify that the service is up and running
```
sudo systemctl status mongodb
```
4. Install Node package manager(NPM).
```
sudo apt install -y npm
```
5. Install body-parser package to help us process JSON files passed in requests to the server.
```
sudo npm install body-parser
```
6. Create a folder Books
```
mkdir Books && cd Books
```
6. In the Books directory, Initialize npm project
```
npm init
```
7. Add a file to it named server.js
```
vi server.js
```
8. add the web server code below into the server.js file
```
var express = require('express');
var bodyParser = require('body-parser');
var app = express();
app.use(express.static(__dirname + '/public'));
app.use(bodyParser.json());
require('./apps/routes')(app);
app.set('port', 3300);
app.listen(app.get('port'), function() {
    console.log('Server up: http://localhost:' + app.get('port'));
});
```
