# Step 0 - Preparing prerequisites
In order to complete this project you will need an AWS account and a virtual server with Ubuntu Server OS.

> Hint #1: When you create your EC2 Instances, you can add Tag "Name" to it with a value that corresponds to a current project you are working on - it will be reflected in the name of the EC2 Instance.

> Hint #2 (for Windows users only): In previous projects we used Putty and Git Bash to connect to our EC2 Instances. In this project and going forward, we are going to explore the usage windows terminal and mobaxterm.
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
**Upgrade ubuntu**

```
sudo apt upgrade
```
2. **Lets get the location of Node.js software from Ubuntu repositories and install**
```
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
```
**Install Node.js on the server with the command below**
```
sudo apt-get install -y nodejs
```
> Note: The command above installs both nodejs and npm. _NPM_ is a package manager for Node like apt for Ubuntu,used to install Node modules & packages to manage dependency conflicts.

3. **Verify the node installation**

```
node -v
```
4. **Check the version of npm (Node Package Manager) installed**
```
npm -v 
```
## Application Code Setup
1. **Create a new directory for your To-Do project:**
```
mkdir Todo
```
2. **Verify that the Todo directory is created**
```
ls
```
> **TIP**: In order to see some more useful information about files and directories in a directory along with their inode numbers, file sizes in a human-readable format, and file permissions. you can use combination of keys _**ls-lih**_
```
ls -lih
```
**If you want to learen more about ls command different  keys use _ls --help_**
```
ls --help
```   
3. **Change your current directory to the newly created one**
```
cd Todo
```
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

5. **Confirm that you have package.json file created**

```
ls 
```

## Install ExpressJS
 Express is a framework for Node.js, therefore a lot of things developers would have programmed is already taken care of out of the box. Therefore it simplifies development, and abstracts a lot of low level details. For example, Express helps to define routes of your application based on HTTP methods and URLs.
1. Install Express
```
npm install express
```
2. Create a file index.js
```
 touch index.js
```
3. confirm that your index.js file is successfully created
```
ls
```
4. Install the dotenv module
It is a popular Node.js library used for loading environment variables from a .env file into process.env
```
npm install dotenv
```
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

> **Notice** that we have specified to use port 5000 in the code. This will be required later when we go on the browser.

Use :w to save in vim and use :qa to exit vim

7. Start our server to see if it works. Open your terminal in the same directory as your index.js file and type:
```
node index.js
```
If every thing goes well, you should see Server running on port 5000 in your terminal.


8. Now we need to open port 5000 in EC2 Security Groups. 
 
9. Open up your browser and try to access your server's Public IP or Public DNS name followed by port 5000:

> Quick reminder how to get your server's Public IP and public DNS name:

- You can find it in your AWS web console in EC2 details or
**For Public IP address** 

```
curl -s http://169.254.169.254/latest/meta-data/public-ipv4 
```
**For Public DNS name**
```
curl -s http://169.254.169.254/latest/meta-data/public-hostname
```
  [http://<PublicIP-or-PublicDNS>:5000](http://<PublicIP-or-PublicDNS>:5000)

## Routes
There are three actions that our To-Do application needs to be able to do:

1. Create a new task
2. Display list of all tasks
3. Delete a completed task

1. Let's create a routes folder to define endpoints for each task associated with specific HTTP request methods: POST, GET, DELETE, required by the To-do app.
````
mkdir routes
````
> Tip: You can open multiple shells in Putty or Linux/Mac to connect to the same EC2

2. Change directory to routes folder
```
cd routes
```
3. Create a file api.js
```
touch api.js
```
4. Open the file with your editor 
```
vim api.js
```
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
2. Install mongoose
```
npm install mongoose
```
3. Create a new folder models
  ```
  mkdir models 
  ```
4. Change directory into models
```
 cd models
 ```

5. Inside the models folder, create a file and name it todo.js
```
touch todo.js
```
> Tip: All three commands above can be defined in one line to be executed consequently with help of && operator, like this:
```
mkdir models && cd models && touch todo.js
```
6. Open the file todo.js with your editor
```
 vim todo.js
 ```
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

**Now we need to update our routes from the file api.js in routes directory to make use of the new model**

8. change directory to routes directory
```
cd routes
```

9. Open api.js with  your editor 
```
vim api.js
```
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

## MongoDB Database
We need a database where we will store our data. For this we will make use of mLab. mLab provides MongoDB database as a service solution (DBaaS), so to make life easy, you will need to sign up for a shared clusters free account, which is ideal for our use case. [Sign up here](https://www.mongodb.com/products/try-free/platform/atlas-signup-from-mlab). Follow the sign up process, select AWS as the cloud provider, and choose a region near you.

Allow access to the MongoDB database from anywhere (Not secure, but it is ideal for testing)

**IMPORTANT NOTE** In the image below, make sure you change the time of deleting the entry from 6 Hours to 1 Week


1. Create a MongoDB database and collection inside mLab

In the index.js file, we specified process.env to access environment variables, but we have not yet created this file. So we need to do that now.

2. Create a file in your Todo directory and name it .env
```
cd Todo
``` 
```
touch .env
```
3. Open .env file with your editor
```
vi .env
```
4. Add the connection string to access the database in it, just as below:
```
DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'
```
> Ensure to update username, password, network-address and database according to your setup

Here is how to get your connection string 


Now we need to update the index.js to reflect the use of .env so that Node.js can connect to the database.

Simply delete existing content in the file, and update it with the entire code below.

5. To do that using vim, follow below steps
 5.1. Open the file with
```
vim index.js
```
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
> Using environment variables to store information is considered more secure and best practice to separate configuration and secret data from the application, instead of writing connection strings directly inside the index.js application file.

6. Start your server:
```
node index.js
```
If you see a message Database connected successfully, our backend configured sucessfully congratulation.


## Testing Backend Code without Frontend using RESTful API
So far we have written backend part of our To-Do application, and configured a database, but we do not have a frontend UI yet. We need ReactJS code to achieve that. But during development, we will need a way to test our code using RESTfulL API. Therefore, we will need to make use of some API development client to test our code.

In this project, we will use **Postman** to test our API.

**How to Install Postman In our machine**
1. **Download Postman**: Go to the [Postman website](https://www.postman.com/downloads/) and download the version of Postman that is compatible with your operating system (Windows, macOS, or Linux).

2. **Install Postman**: Once the download is complete, run the installer file. Follow the on-screen instructions to complete the installation process.

3. **Launch Postman**: After installation, you can launch Postman by finding it in your list of installed applications or by searching for "Postman" in the search bar of your operating system.

4. **Sign in or Create an Account (Optional)**: While it's not required, signing in or creating a Postman account can unlock additional features and capabilities, such as cloud synchronization of your collections and environments.

5. **Start Using Postman**: Once Postman is launched, you can start using it to create and send HTTP requests, test APIs, and collaborate with your team on API development projects.


If you want to learen more [Click HERE](https://www.youtube.com/watch?v=FjgYtQK_zLE)

Test all the API endpoints and make sure they are working. For the endpoints that require body, you should send JSON back with the necessary fields since it’s what we setup in our code.

Now open your Postman, create a POST request to the API http://<PublicIP-or-PublicDNS>:5000/api/todos. This request sends a new task to our To-Do list so the application could store it in the database.

**Note: make sure your set header key Content-Type as application/json** 


1. Display a list of tasks: GET request to your API on http://<PublicIP-or-PublicDNS>:5000/api/todos. This request retrieves all existing records from out To-do application 

2. Add a new task to the list - HTTP POST request to your API on http://<PublicIP-or-PublicDNS>:5000/api/todos
3. Delete an existing task from the list - HTTP DELETE request to your API on http://<PublicIP-or-PublicDNS>:5000/api/todos


# Step 2 - Frontend creation
Since we have done all the functionality we want from our backend and API, it is time to create a user interface for a Web client (browser) to interact with the application via API. To start out with the frontend of the To-do app, we will use the create-react-app command to scaffold our app.

In the same root directory as your backend code, which is the Todo directory
1. change directory to Todo
```
cd Todo
```
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












