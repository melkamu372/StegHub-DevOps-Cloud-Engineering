# MERN web stack implementation in AWS project 3
> The MERN stack is a collection of technologies that help developers build robust and scalable web applications using JavaScript. The acronym **“MERN”** stands for **`MongoDB`**, **`Express`**, **`React`**, and **`Node. js`**, with each component playing a role in the development process.

# Step 0 - Preparing prerequisites
In order to complete this project you will need an AWS account and a virtual server with Ubuntu Server OS.

> Hint #1: When you create your EC2 Instances, you can add Tag "Name" to it with a value that corresponds to a current project you are working on - it will be reflected in the name of the EC2 Instance.

1. Log to aws account console and create EC2 instance of t2.micro type with Ubuntu 24.04 LTS (HVM) Server launch in the default region us-east-1.

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/cd89b74e-db5e-484d-8c28-9d55d22abc57)

2. Select Ubuntu free tire eligable version

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a6ae3d4f-229c-439b-9549-9a8b55f12356)

3. Create new key pair or select existing key

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/14fc69b8-8135-4c33-9b44-554d7e8bb894)

4. Network setting create new security group or use existing security group
   
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/9aeb791b-e15e-4468-8029-1f63c43b26ec)

5. Configure Storage and launch the instance 

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/020de6a3-2006-4f14-b77c-c4367813ac3b)

6. View Instance

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/c8bf7b82-5986-4322-81bc-780dc1ebdb1c)

7. Instance Details

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/74916b95-2c37-428b-983b-56eff93b3d48)

8. Configure security group with the following inbound rules:

- Allow traffic on port 22 (SSH) with source from any IP address. This is opened by default.
- Allow traffic on port 80 (HTTP) with source from anywhere on the internet.
- Allow traffic on port 443 (HTTPS) with source from anywhere on the internet.
- Allow traffic on port 5000 (Custom) with source from anywhere on the internet.

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/96d9eb2d-98b2-42d6-b1a8-93b61333177d)


9. Default VPC Network Configuration

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/69ff0b1a-1a59-4c14-8d45-c501bb610ff1)

    
10. Connect to EC2 instance from SSH client

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a97dc8ae-2957-4b86-8364-f517584e1662)

11. Give Permission for the private ssh key that we used either created or existing during launching instance and downloaded

  ```
sudo chmod 0400 melkamu_key.pem
```
12. Then connect the instance using instance Public Ip Addresss or domain name
```
ssh -i melkamu_key.pem ubuntu@54.144.78.202
```
or
```
ssh -i "melkamu_key.pem" ubuntu@ec2-54-144-78-202.compute-1.amazonaws.com
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/fa410929-61e7-4ce3-b92c-e694111bca3c)

> Hint #2 (for Windows users only): In previous projects we used Putty and Git Bash to connect to our EC2 Instances. In this project and going forward, we are going to explore the usage **windows terminal** and **mobaxterm**.
## Alternative tool for Putty, Git Bash for windows os users **windows terminal** and **mobaxterm**   

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d16e6873-a3aa-47cd-b5be-b4eaee2a7b2d) **Windows Terminal** 
it is a modern and feature-rich terminal application provided by Microsoft for Windows users. It's designed to be a central place for accessing various command-line tools, shells, and environments, providing a more unified and customizable experience compared to the traditional Command Prompt or PowerShell.

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/b1dbb459-6fd3-4414-8b4a-b30736fffdc1) **MobaXterm**
it is a comprehensive software package for Windows that provides various network tools, remote connectivity features, and a Unix-like terminal environment. It's often used by developers, system administrators, and IT professionals for managing remote servers, accessing SSH sessions, transferring files, and more.


Watch this videos to learn how to set up windows terminal on your pc

- [How to install Windows terminal](https://www.youtube.com/watch?v=R-qcpehB5HY)

 - [How to connect to an EC2 instance on AWS using Mobaxterm](https://www.youtube.com/watch?v=g8XiC9Q2EEE)

You can also watch this video to set up your .


## Task
To deploy a simple To-Do application that creates To-Do lists 

# Step 1 - Backend configuration
1. **Update ubuntu**
```
sudo apt update
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d607e9b3-bfe2-4384-8645-fce884c05078)


**Upgrade ubuntu**

```
sudo apt upgrade
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/f002269b-00f4-4af1-b493-943a5a307e70)

2. **Lets get the location of Node.js software from Ubuntu repositories and install**
```
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/77be70dd-cd4a-424d-abbb-3136e6121a15)

**Install Node.js on the server with the command below**
```
sudo apt-get install -y nodejs
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/43352b3b-ab2b-44b6-8ed2-a513bd840679)

> Note: The command above installs both nodejs and npm. _NPM_ is a package manager for Node like apt for Ubuntu,used to install Node modules & packages to manage dependency conflicts.

3. **Verify the node installation**

```
node -v
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/59012fb7-a426-425e-8611-7b8dcff78f0e)

4. **Check the version of npm (Node Package Manager) installed**
```
npm -v 
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a370f919-dbee-46e6-a3e4-2e48346521af)

## Application Code Setup
1. **Create a new directory for your To-Do project:**
```
mkdir Todo
```
2. **Verify that the Todo directory is created**
```
ls
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/e6ec33df-c070-4d5f-bcf0-6c0fdc814d70)

> **TIP**: In order to see some more useful information about files and directories in a directory along with their inode numbers, file sizes in a human-readable format, and file permissions. you can use combination of keys _**ls-lih**_
```
ls -lih
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/2fd10091-2784-4b6b-907b-0b82f4c6597a)

**If you want to learen more about ls command different  keys use _ls --help_**
```
ls --help
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/7a025f08-3bfe-4f8d-8f45-d824576f801e)

3. **Change your current directory to the newly created one**
```
cd Todo
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/f26d78a1-e34c-49c5-a335-fe9c2eab54ee)

4. **Initialize a new Node.js project using npm init**
so that a new file named package.json will be created. This file will normally contain information about your application and the dependencies that it needs to run.
Answer Prompts: After running npm init, you'll be prompted to provide information about your project.
You can press Enter several times to accept default values, then accept to write out the package.json file by typing yes. You'll typically be asked for details such as:

- **package name**: The name of your project.
- **version**: The initial version of your project.
- **description**: A brief description of your project.
- **entry point**: The main entry point file of your project.
- **test command**: Command to run your project's tests.
- **git repository**: URL to the Git repository of your project.
- **keywords**: Keywords related to your project.
- **author**: Your name or the name of the project's author.
- **license**: The license type for your project.
- **Review and Confirm**: Once you've answered all the prompts, npm init will display a summary of the information you provided and ask you to confirm whether it's correct.
```
npm init
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/61f6480b-40fe-49b8-a617-9dd1143010d5)

5. **Confirm that you have package.json file created**

```
ls 
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/59f54c6c-c936-4478-8ba2-162e3cdc7a38)


## Install ExpressJS
 Express is a framework for Node.js, therefore a lot of things developers would have programmed is already taken care of out of the box. Therefore it simplifies development, and abstracts a lot of low level details. For example, Express helps to define routes of your application based on HTTP methods and URLs.
1. Install Express
```
npm install express
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/b890d79b-66d9-45af-8987-13f39c52f30a)

2. Create a file index.js
```
 touch index.js
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/149167f5-9bd3-43c0-aa40-5349a4270f78)

3. confirm that your index.js file is successfully created
```
ls
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/c15e066d-40fc-44ab-8857-1b4bac73c9af)

4. Install the dotenv module
It is a popular Node.js library used for loading environment variables from a .env file into process.env
```
npm install dotenv
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/e6b2df45-13e3-4a28-bd73-8784caed4610)

5. Open the index.js file using your editor vim or nano 
```
vim index.js
```
6. Type the code below into it and save.
```
const express = require('express');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use((req, res, next) => {
res.send('Welcome to Express');
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/292dbd2d-7290-4938-8fff-022698360567)


> **Notice** that we have specified to use port 5000 in the code. This will be required later when we go on the browser. But we allready add when creating the instance

Use :wq to save in vim and use :qa to exit vim

7. Start our server to see if it works. Open your terminal in the same directory as your index.js file and type:
```
node index.js
```
If every thing goes well, you should see Server running on port 5000 in your terminal.
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/359fe920-6dca-4715-b1f3-6ff9bacdbd39)


8. Now we need to open port 5000 in EC2 Security Groups. but we allready open 

 ![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/df2f0097-d00f-481b-96b1-77f2d31c7daf)

9. Open up your browser and try to access your server's Public IP or Public DNS name followed by port 5000:

> Quick reminder how to get your server's Public IP and public DNS name:

- You can find it in your AWS web console in EC2 details or
**For Public IP address** 

```
curl -s http://169.254.169.254/latest/meta-data/public-ipv4 
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/53960896-0cf2-49b8-a2b6-dc55a312b3e3)

**For Public DNS name**
```
curl -s http://169.254.169.254/latest/meta-data/public-hostname
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/ec7f249d-8241-41cc-83aa-e771ff850960)

[http://54.144.78.202:5000](http://54.144.78.202:5000)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/23ee0195-4e7b-4118-a003-769849d4f19b)


## Routes
There are three actions that our To-Do application needs to be able to do:

1. Create a new task
2. Display list of all tasks
3. Delete a completed task

1. Let's create a routes folder to define endpoints for each task associated with specific HTTP request methods: POST, GET, DELETE, required by the To-do app.
````
mkdir routes
````
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/bd33d45a-f0bd-4f52-a507-3a6538d0bd07)

> Tip: You can open multiple shells in Putty or Linux/Mac to connect to the same EC2

2. Change directory to routes folder
```
cd routes
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/fadf49b1-1dbc-4e9e-8dcc-d171ffbc5abb)

3. Create a file api.js
```
touch api.js
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/5ba2909e-c7d9-4955-aed7-5a4babd71f2f)

4. Open the file with your editor 
```
vim api.js
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/68e51cf8-255a-456f-a4fd-b0395112efe2)

5. Copy below code and paste in the file
```
const express = require ('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {

});

router.post('/todos', (req, res, next) => {

});

router.delete('/todos/:id', (req, res, next) => {

})

module.exports = router;
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a0babd28-1d9d-49c2-8282-5dc3e144003a)


## Models
Since the app is going to make use of Mongodb which is a NoSQL database, we need to create a model.

A model is at the heart of JavaScript based applications, and it is what makes it interactive.

We will also use models to define the database schema . This is important so that we will be able to define the fields stored in each Mongodb document. 

In essence, the Schema is a blueprint of how the database will be constructed, including other data fields that may not be required to be stored in the database. These are known as virtual properties

To create a Schema and a model, install mongoose which is a Node.js package that makes working with mongodb easier.

1. Change directory back Todo folder 
```
cd Todo
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/42af0731-d872-46bd-874b-0b7fd8f6c4d7)

2. Install mongoose
```
npm install mongoose
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/1b18d12e-e16e-435c-ad2e-851271961702)

3. Create a new folder models
  ```
  mkdir models 
  ```

4. Change directory into models
```
 cd models
 ```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/7916454f-a579-4432-abca-d5f521ec3d8a)


5. Inside the models folder, create a file and name it todo.js
```
touch todo.js
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/4e0b0866-bada-4dcb-aad4-9b80ca0068fd)

> Tip: All three commands above can be defined in one line to be executed consequently with help of && operator, like this:
```
mkdir models && cd models && touch todo.js
```
6. Open the file todo.js with your editor
```
 vim todo.js
 ```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/cc48fe30-ca20-4006-92c3-27eeccbf996c)

7. Paste the code below in the file:
```
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

//create schema for todo
const TodoSchema = new Schema({
action: {
type: String,
required: [true, 'The todo text field is required']
}
})

//create model for todo
const Todo = mongoose.model('todo', TodoSchema);

module.exports = Todo;
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/158562e5-bad0-4998-9f8d-22689c2483b2)

**Now we need to update our routes from the file api.js in routes directory to make use of the new model**

8. change directory to routes directory
```
cd routes
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/956187fe-4c38-4abb-bf09-a946f8e17eb3)


9. Open api.js with  your editor 
```
vim api.js
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/c0d0a7c2-7f39-4a98-a3ad-d9ab4d9e8a22)

10. Delete the code inside with :%d command and paste there code below into it then save and exit
```
const express = require ('express');
const router = express.Router();
const Todo = require('../models/todo');

router.get('/todos', (req, res, next) => {

//this will return all the data, exposing only the id and action field to the client
Todo.find({}, 'action')
.then(data => res.json(data))
.catch(next)
});

router.post('/todos', (req, res, next) => {
if(req.body.action){
Todo.create(req.body)
.then(data => res.json(data))
.catch(next)
}else {
res.json({
error: "The input field is empty"
})
}
});

router.delete('/todos/:id', (req, res, next) => {
Todo.findOneAndDelete({"_id": req.params.id})
.then(data => res.json(data))
.catch(next)
})

module.exports = router;
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/2447a460-44c9-4964-acd8-81509261062c)

## MongoDB Database
We need a database where we will store our data. For this we will make use of mLab. mLab provides MongoDB database as a service solution (DBaaS), so to make life easy, you will need to sign up for a shared clusters free account, which is ideal for our use case. [Sign up here](https://www.mongodb.com/products/try-free/platform/atlas-signup-from-mlab). Follow the sign up process, select AWS as the cloud provider, and choose a region near you.

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/ec2cc480-e2c9-4c10-81a7-f1730860e6d1)


Allow access to the MongoDB database from anywhere (Not secure, but it is ideal for testing)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/4ae3a75a-e8f3-436c-bb36-7e5fcdafb018)

**IMPORTANT NOTE** In the image below, make sure you change the time of deleting the entry from 6 Hours to 1 Week

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/2ece9b2a-4b4e-47ba-b8e6-a99ca9ea84f5)


1. Create a MongoDB database and collection inside mLab

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/f08031e4-1282-4257-a660-364af29bdde0)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/7724b57f-b58d-4e90-855f-24e6d4a4040c)


In the index.js file, we specified process.env to access environment variables, but we have not yet created this file. So we need to do that now.

2. Create a file in your Todo directory and name it .env
```
cd Todo
``` 
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/22b633c9-178f-4ec5-9f82-a71c017cf489)

```
touch .env
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/4c7809a0-dce8-4476-893d-d0f7543ccb72)

3. Open .env file with your editor
```
vim .env
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/61e53656-8f6c-49bf-b2dc-16492e77847a)

4. Add the connection string to access the database in it, just as below:
```
DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'
```
> Ensure to update username, password, network-address and database according to your setup

Here is how to get your connection string 
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/b97e653b-e2bf-4dd2-99dd-d143c4014f7a)

**Choose connect to your Application Option**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/9889121b-ac24-46ba-842a-ee0d03d28002)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/5c54e16d-48d8-41e7-8111-242fdcc8c388)

**This is my connection string**
```
mongodb+srv://melkamu:<password>@cluster1.ksbphyk.mongodb.net/?retryWrites=true&w=majority&appName=Cluster1
```
> _Replace <password> with the password for the melkamu user_

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/8df2a5cf-e34d-45e6-8172-eca330d1c126)

Now we need to update the index.js to reflect the use of .env so that Node.js can connect to the database.

Simply delete existing content in the file, and update it with the entire code below.

5. To do that using vim, follow below steps

 5.1. Open the file with
```
vim index.js
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/742214cf-8b6b-422d-a4c7-a08769c62d10)

5.2. Press esc

5.3. Type :

5.4. Type %d

5.5. Hit 'Enter'

The entire content will be deleted, then,

5.6. Press i to enter the insert mode in vim

5.7. Paste the entire code below in the file

```
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

//connect to the database
mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
.then(() => console.log(`Database connected successfully`))
.catch(err => console.log(err));

//since mongoose promise is depreciated, we overide it with node's promise
mongoose.Promise = global.Promise;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use(bodyParser.json());

app.use('/api', routes);

app.use((err, req, res, next) => {
console.log(err);
next();
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/fe4f51ee-19f5-4b28-8432-d5909e096079)

> Using environment variables to store information is considered more secure and best practice to separate configuration and secret data from the application, instead of writing connection strings directly inside the index.js application file.

6. Start your server:
```
node index.js
```
If you see a message Database connected successfully, our backend configured sucessfully congratulation.

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/f53a31cf-bd25-4c95-8d8c-b7a979e913bc)


## Testing Backend Code without Frontend using RESTful API
So far we have written backend part of our To-Do application, and configured a database, but we do not have a frontend UI yet. We need ReactJS code to achieve that. But during development, we will need a way to test our code using RESTfulL API. Therefore, we will need to make use of some API development client to test our code.

In this project, we will use **Postman** to test our API.

**How to Install Postman In our machine**

1. **Download Postman**: Go to the [Postman website](https://www.postman.com/downloads/) and download the version of Postman that is compatible with your operating system (Windows, macOS, or Linux).

2. **Install Postman**: Once the download is complete, run the installer file. Follow the on-screen instructions to complete the installation process.

3. **Launch Postman**: After installation, you can launch Postman by finding it in your list of installed applications or by searching for "Postman" in the search bar of your operating system.

4. **Sign in or Create an Account (Optional)**: While it's not required, signing in or creating a Postman account can unlock additional features and capabilities, such as cloud synchronization of your collections and environments.

5. **Start Using Postman**: Once Postman is launched, you can start using it to create and send HTTP requests, test APIs, and collaborate with your team on API development projects.

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d342c2dd-7c72-4028-abd5-8bf826af4adb)

If you want to learen more [Click HERE](https://www.youtube.com/watch?v=FjgYtQK_zLE)

Test all the API endpoints and make sure they are working. For the endpoints that require body, you should send JSON back with the necessary fields since it’s what we setup in our code.

Now open your Postman, create a POST request to the API http://<PublicIP-or-PublicDNS>:5000/api/todos. This request sends a new task to our To-Do list so the application could store it in the database.

**Note: make sure your set header key Content-Type as application/json** 

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/c0ae7224-01c4-4b63-8e45-2adb87da7b44)


1. Display a list of tasks: GET request to your API on http://<PublicIP-or-PublicDNS>:5000/api/todos. This request retrieves all existing records from out To-do application 

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/38bb9c7c-a76f-4b90-ac8e-59ad63980e9e)



2. Add a new task to the list - HTTP POST request to your API on http://<PublicIP-or-PublicDNS>:5000/api/todos

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/865618f7-e007-4fc8-b195-7ee38f6aa373)

**Check Data is inserted**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/ebacdee4-048a-4994-8d1b-4119fec6fb35)


3. Delete an existing task from the list - HTTP DELETE request to your API on http://<PublicIP-or-PublicDNS>:5000/api/todos

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/f8d4b4f7-9e30-4695-9b6c-350c57dae564)

**After Delete we check by get request**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/2fe85b0a-beb0-4a9d-9370-876168550e7e)


# Step 2 - Frontend creation
Since we have done all the functionality we want from our backend and API, it is time to create a user interface for a Web client (browser) to interact with the application via API. To start out with the frontend of the To-do app, we will use the create-react-app command to scaffold our app.

In the same root directory as your backend code, which is the Todo directory
1. change directory to Todo
```
cd Todo
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/71b284ab-cb52-4cc4-982e-63d1ffbb765c)

2. Create react app

```
 npx create-react-app client
```

This will create a new folder in your Todo directory called client, where you will add all the react code.
3. check it 
```
ls
```
## Running a React App
Before testing the react app, there are some dependencies that need to be installed.

1. Install concurrently. used to run more than one command simultaneously from the same terminal window.
```
 npm install concurrently --save-dev
```
2. Install nodemon used to run and monitor the server. If there is any change in the server code, nodemon will restart it automatically and load the new changes.
```
 npm install nodemon --save-dev
```

3. In Todo folder open the package.json file. 

```
vim package.json
```

4. Change the existing code  with the code below.
```
"scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},
```

 **Configure Proxy in package.json**
 1. Change directory to client
 
 ```
 cd client
 ```
 2. Open the package.json file
  ```
  vi package.json
  ```
3. Add the key value pair in the package.json file "proxy": "http://localhost:5000".
The whole purpose of adding the proxy configuration in number 3 above is to make it possible to access the application directly from the browser by simply calling the server url like http://localhost:5000 rather than always including the entire path like http://localhost:5000/api/todos

**Now, ensure you are inside the Todo directory and simply run npm run dev command :**
1. Change directory to Todo
```
cd Todo
``` 
2. run the application
```
npm run dev
```
Your app should open and start running on localhost:3000

> Important note: In order to be able to access the application from the Internet you have to open TCP port 3000 on EC2 by adding a new Security Group rule.


## Creating your React Components
One of the advantages of react is that it makes use of components, which are reusable and also makes code modular. 

1. change directory to Todo
```
cd Todo
```
2. move to the client directory 
```
cd client
```
3. move to the src directory
```
cd src
```

4. Inside your src folder create another folder called components
```
mkdir components
```
5. Move into the components directory 
```
cd components
```

6. Inside components directory create three files Input.js, ListTodo.js and Todo.js run code concurrently
```
touch Input.js ListTodo.js Todo.js
```
7. Open Input.js file
```
vim Input.js
```
8. Copy and paste the following
```
import React, { Component } from 'react';
import axios from 'axios';

class Input extends Component {

state = {
action: ""
}

addTodo = () => {
const task = {action: this.state.action}

    if(task.action && task.action.length > 0){
      axios.post('/api/todos', task)
        .then(res => {
          if(res.data){
            this.props.getTodos();
            this.setState({action: ""})
          }
        })
        .catch(err => console.log(err))
    }else {
      console.log('input field required')
    }

}

handleChange = (e) => {
this.setState({
action: e.target.value
})
}

render() {
let { action } = this.state;
return (
<div>
<input type="text" onChange={this.handleChange} value={action} />
<button onClick={this.addTodo}>add todo</button>
</div>
)
}
}

export default Input
```
To make use of Axios, which is a Promise based HTTP client for the browser and node.js, you need to cd into your client from your terminal and run yarn add axios or npm install axios.

9. Move to the src folder
```
cd ..
```

10. Move to clients folder
```
cd ..
```
11. Install Axios
```
 npm install axios
```
12. Go to components directory
```
cd src/components
```
13. Open your ListTodo.js
```
vim ListTodo.js
```
14. Write the following cod
```
import React from 'react';

const ListTodo = ({ todos, deleteTodo }) => {

return (
<ul>
{
todos &&
todos.length > 0 ?
(
todos.map(todo => {
return (
<li key={todo._id} onClick={() => deleteTodo(todo._id)}>{todo.action}</li>
)
})
)
:
(
<li>No todo(s) left</li>
)
}
</ul>
)
}

export default ListTodo
```

15. Then open Todo.js 
```
vim Todo.js
```
16. Write the following code
```
import React, {Component} from 'react';
import axios from 'axios';

import Input from './Input';
import ListTodo from './ListTodo';

class Todo extends Component {

state = {
todos: []
}

componentDidMount(){
this.getTodos();
}

getTodos = () => {
axios.get('/api/todos')
.then(res => {
if(res.data){
this.setState({
todos: res.data
})
}
})
.catch(err => console.log(err))
}

deleteTodo = (id) => {

    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if(res.data){
          this.getTodos()
        }
      })
      .catch(err => console.log(err))

}

render() {
let { todos } = this.state;

    return(
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos}/>
        <ListTodo todos={todos} deleteTodo={this.deleteTodo}/>
      </div>
    )

}
}

export default Todo;
```
**We need to make little adjustment to our react code. Delete the logo and adjust our App.js to look like this.**

1. Move to the src folder
```
cd ..
```
2. Make sure that you are in the src folder and run
```
vim App.js
```

3. Write the following code
```
import React from 'react';

import Todo from './components/Todo';
import './App.css';

const App = () => {
return (
<div className="App">
<Todo />
</div>
);
}

export default App;
```
4. After writting, exit the editor based on editor command

5. In the src directory open the App.css
```
vim App.css
```
6. Then Write the following code:
```
.App {
text-align: center;
font-size: calc(10px + 2vmin);
width: 60%;
margin-left: auto;
margin-right: auto;
}

input {
height: 40px;
width: 50%;
border: none;
border-bottom: 2px #101113 solid;
background: none;
font-size: 1.5rem;
color: #787a80;
}

input:focus {
outline: none;
}

button {
width: 25%;
height: 45px;
border: none;
margin-left: 10px;
font-size: 25px;
background: #101113;
border-radius: 5px;
color: #787a80;
cursor: pointer;
}

button:focus {
outline: none;
}

ul {
list-style: none;
text-align: left;
padding: 15px;
background: #171a1f;
border-radius: 5px;
}

li {
padding: 15px;
font-size: 1.5rem;
margin-bottom: 15px;
background: #282c34;
border-radius: 5px;
overflow-wrap: break-word;
cursor: pointer;
}

@media only screen and (min-width: 300px) {
.App {
width: 80%;
}

input {
width: 100%
}

button {
width: 100%;
margin-top: 15px;
margin-left: 0;
}
}

@media only screen and (min-width: 640px) {
.App {
width: 60%;
}

input {
width: 50%;
}

button {
width: 30%;
margin-left: 10px;
margin-top: 0;
}
}
```

7. After writing save and exit

8. In the src directory open the index.css
```
vim index.css
```
9. Write the following code:
```
body {
margin: 0;
padding: 0;
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
"Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
sans-serif;
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
box-sizing: border-box;
background-color: #282c34;
color: #787a80;
}

code {
font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
monospace;
}
```

10. Go to the root directory  Todo directory
```
cd ../..
```
11. When you are in the Todo directory run:
```
npm run dev
```

### The End of project 3 MERN WEB STACK
In this Project We have created a simple To-Do Application and deployed it to MERN stack. 
- Frontend application using React.js 
- Backend application written using Expressjs.
- Mongodb backend for storing tasks in a database.












