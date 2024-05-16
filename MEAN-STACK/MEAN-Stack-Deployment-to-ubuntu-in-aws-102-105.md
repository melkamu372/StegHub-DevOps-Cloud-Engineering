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
**Since I am using ubuntu-noble-24.04-amd64-server I follow this step [How to Install MongoDB on Ubuntu 22.04 | 7 Steps](https://www.cherryservers.com/blog/install-mongodb-ubuntu-22-04)  because installation step bellow 22.04 is different from this one**

**MongoDB** stores data in flexible, JSON-like documents. Fields in a database can vary from document to document and data structure can be changed over time. For our example application, we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages.

0. Prerequisite packages.

**The first step is install the prerequisite packages needed during the installation**

```
sudo apt install software-properties-common gnupg apt-transport-https ca-certificates -y
```

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/4de16558-a4df-4169-ae1d-faaf1389d724)

**To install the most recent MongoDB package, we need to add the MongoDB package repository to your sources list file on Ubuntu. Before that, you need to import the public key for MongoDB on your system using the wget command as follows:**

```
curl -fsSL https://pgp.mongodb.com/server-7.0.asc |  sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg --dearmor
```

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/23ae917d-7596-43bd-b79d-7cee77682482)

**Next, add MongoDB 7.0 APT repository to the /etc/apt/sources.list.d directory.**
```
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
```
**Once the repository is added, reload the local package index.**
```
sudo apt update
```

**The command refreshes the local repositories and makes Ubuntu aware of the newly added MongoDB 7.0 repository.**

1. Install MongoDB
```
sudo apt install mongodb-org -y
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/5deecc06-2291-44c0-ad8c-4c99c6d5324f)


2. Verify the version of MongoDB installed
```
 mongod --version
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/859e58b7-d324-4c05-98a6-1676273fd43e)

2. Start The server
**The MongoDB service is disabled upon installation by default, and you can verify this by running the below command:**
```
sudo systemctl status mongod
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d62e8c10-df5b-4b0a-8f62-67c551b46601)

**To start the MongoDB service, execute the command:**
```
sudo systemctl start mongod
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/3b243b5a-99f0-4472-9972-883b3422c7ae)


3. Verify that the service is up and running
```
sudo systemctl status mongod
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/8e22ced3-d709-4eac-ade3-1ed5e43e2ddd)

4. Check Node package manager(NPM).
```
npm -v 
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/0b66c542-e6f7-4e01-94d0-018dea1a38e0)
**if not installed install using**
```
sudo apt install -y npm
```
5. Install body-parser package to help us process JSON files passed in requests to the server.
```
sudo npm install body-parser
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/5246befb-2e81-455e-ac60-e32eeba76b41)

6. Create a folder Books
```
mkdir Books && cd Books
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/61ec4fab-dd08-4927-ac29-ad2e0ab5dada)

6. In the Books directory, Initialize npm project
```
npm init
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/0be5d542-d2e7-497e-9d78-45385603175a)

7. Add a file to it named server.js
```
vim server.js
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/7890488c-4aed-48d6-ab52-46c56f08546b)

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
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/ff0ed935-edb9-44c9-be95-0963738fd124)

# Step 3: Install Express and set up routes to the server
Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. We will use it to pass book information to and from our MongoDB database.
We also will use Mongoose package which provides a straight-forward, schema-based solution to model our application data. 
1. Install express and mongoose
```
sudo npm install express mongoose
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/e70a9d15-a24f-4627-ba93-84d9759e49f3)

2. In 'Books' folder, create a folder named apps
```
mkdir apps && cd apps
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/1a6e0367-4d0b-4c33-a034-97a5b5c175ac)

3. Create a file named routes.js
```
vim routes.js
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/339c2314-a543-4f90-8bdc-d45e214720c9)

4. Write the code below into routes.js
```
var Book = require('./models/book');
module.exports = function(app) {
  app.get('/book', function(req, res) {
    Book.find({}, function(err, result) {
      if ( err ) throw err;
      res.json(result);
    });
  }); 
  app.post('/book', function(req, res) {
    var book = new Book( {
      name:req.body.name,
      isbn:req.body.isbn,
      author:req.body.author,
      pages:req.body.pages
    });
    book.save(function(err, result) {
      if ( err ) throw err;
      res.json( {
        message:"Successfully added book",
        book:result
      });
    });
  });
  app.delete("/book/:isbn", function(req, res) {
    Book.findOneAndRemove(req.query, function(err, result) {
      if ( err ) throw err;
      res.json( {
        message: "Successfully deleted the book",
        book: result
      });
    });
  });
  var path = require('path');
  app.get('*', function(req, res) {
    res.sendfile(path.join(__dirname + '/public', 'index.html'));
  });
};
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/dafe6881-74f6-40d3-a9f9-d17b8dd9a282)

5. In the apps folder, create a folder named models
```
mkdir models && cd models
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/23776309-3016-4ae3-a332-300e0e658e14)

6. Create a file named book.js
```
vim book.js
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/b50a4109-1e94-46a7-85bf-b085d4dee10d)


7. write the code below into book.js
```
var mongoose = require('mongoose');
var dbHost = 'mongodb://localhost:27017/test';
mongoose.connect(dbHost);
mongoose.connection;
mongoose.set('debug', true);
var bookSchema = mongoose.Schema( {
  name: String,
  isbn: {type: String, index: true},
  author: String,
  pages: Number
});
var Book = mongoose.model('Book', bookSchema);
module.exports = mongoose.model('Book', bookSchema);
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/2491df17-f206-4c95-9c53-8792b43429e6)

# Step 4 - Access the routes with _AngularJS_
AngularJS provides a web framework for creating dynamic views in our web applications.Here we use AngularJS to connect our web page with Express and perform actions on our book register

1. Change the directory back to Books
```
cd ../..
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/64e3edba-edad-4202-8217-7d7ed4e90f17)

2. Create a folder named public
```
mkdir public && cd public
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/bebafc3b-976e-40d9-8077-f612724b735b)

3. Add a file named script.js
```
vim script.js
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/0e5c1df5-62e9-47e5-9132-467f468234a2)

4. Write the Code below (controller configuration defined) into the script.js file
```
var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope, $http) {
  $http( {
    method: 'GET',
    url: '/book'
  }).then(function successCallback(response) {
    $scope.books = response.data;
  }, function errorCallback(response) {
    console.log('Error: ' + response);
  });
  $scope.del_book = function(book) {
    $http( {
      method: 'DELETE',
      url: '/book/:isbn',
      params: {'isbn': book.isbn}
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
  $scope.add_book = function() {
    var body = '{ "name": "' + $scope.Name + 
    '", "isbn": "' + $scope.Isbn +
    '", "author": "' + $scope.Author + 
    '", "pages": "' + $scope.Pages + '" }';
    $http({
      method: 'POST',
      url: '/book',
      data: body
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
});
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/6e3e2c7c-ba27-4b7a-ae59-cf6ca040988d)

5. In public folder, create a file named index.html
```
vim index.html
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/846b576f-6faa-49b6-8d2d-4e7b4c7b9423)

6. Write the code below into index.html
```
<!doctype html>
<html ng-app="myApp" ng-controller="myCtrl">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div>
      <table>
        <tr>
          <td>Name:</td>
          <td><input type="text" ng-model="Name"></td>
        </tr>
        <tr>
          <td>Isbn:</td>
          <td><input type="text" ng-model="Isbn"></td>
        </tr>
        <tr>
          <td>Author:</td>
          <td><input type="text" ng-model="Author"></td>
        </tr>
        <tr>
          <td>Pages:</td>
          <td><input type="number" ng-model="Pages"></td>
        </tr>
      </table>
      <button ng-click="add_book()">Add</button>
    </div>
    <hr>
    <div>
      <table>
        <tr>
          <th>Name</th>
          <th>Isbn</th>
          <th>Author</th>
          <th>Pages</th>

        </tr>
        <tr ng-repeat="book in books">
          <td>{{book.name}}</td>
          <td>{{book.isbn}}</td>
          <td>{{book.author}}</td>
          <td>{{book.pages}}</td>

          <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
        </tr>
      </table>
    </div>
  </body>
</html>
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/28666673-3304-484d-a4a4-7dc139d2c3a6)

7. Change the directory back up to Books
```
cd ..
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/b4d09f7b-2c09-4e86-ba8c-c15d394fe9b2)

8. Start the server by running this command:
```
node server.js
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a50da275-9a8d-49be-942a-70c247f65637)

**The server is now up and running, we can connect it via port 3300. Launch a separate Putty or SSH console to test what curl command returns locally.**
```
curl -s http://localhost:3300
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/86a3ea50-65fa-4312-bf2e-17edf4e53e3a)

9. We can try and access it from the Internet.

For this - we need to open TCP port 3300 in our AWS Web Console for our EC2 Instance so we add port 3300.

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/cde3e958-5e90-4229-a0fe-610f03dadabf)

Your Security group shall look like this:

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a475a763-189b-469b-ad31-df3cb903e38e)


Now we can access our Book Register web application from the Internet with a browser using Public IP address or Public DNS name.

> - Quick reminder how to get your server's Public IP and public DNS name:

> -  You can find it in your AWS web console in EC2 details or Run
>   
**For Public IP address** 
```
curl -s http://169.254.169.254/latest/meta-data/public-ipv4
``` 
**For Public DNS name**
```
curl -s http://169.254.169.254/latest/meta-data/public-hostname 
```
## This is how our Book Register Application will look like in browser:

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/8d8bbb9b-5e78-472e-b035-f9744feb25a6)

- Register new book and see the update 
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/086e8d6d-b16b-4bc7-a40f-e0bd3c3115b6)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/5ef795c1-7b87-4d45-9952-e92b7b698755)

- Retrived Data 
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/3c500d91-69ad-4cca-b030-62c247bd30df)


- now we can see inserted data in database it looks
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/00eef40a-ea15-42ec-8e45-504466e93bd0)


- Delete Inserted Data

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/aa01e633-0444-4113-abdd-5ca60d4b7d18)
  
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a725f3bd-0acb-4245-950b-3d000f0ced14)

- Verify is it deleted

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/57da9229-ad23-428a-8f3a-9c27a45bc4c4)

**In database**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d6f7d10d-1e50-48b5-8799-1fb9286a1391)

### The End of project 4 MEAN WEB STACK
In this Project We have created a simple Book Register Application and deployed it to MEAN stack.

- Frontend application using Angular.js
- Backend application written using Expressjs.
- Mongodb backend for storing tasks in a database.
- Platform details  Linux/UNIX ubuntu-noble-**24.04-amd64**-server 




