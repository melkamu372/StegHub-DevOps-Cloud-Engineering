# Migration to the Сloud with containerization (Docker & Docker Compose)1- 101

Until now, you have been using VMs (AWS EC2) in Amazon Virtual Private Cloud (AWS VPC) to deploy your web solutions, and it works well 
in many cases. You have learned how easy to spin up and configure a new EC2 manually or with such tools as Terraform and Ansible to
automate provisioning and configuration. You have also deployed two different websites on the same VM; this approach is scalable, but
to some extent; imagine what if you need to deploy many small applications (it can be web front-end, web-backend, processing jobs, 
monitoring, logging solutions, etc.) and some of the applications will require various OS and runtimes of different versions and 
conflicting dependencies – in such case you would need to spin up serves for each group of applications with the exact 
OS/runtime/dependencies requirements. When it scales out to tens/hundreds and even thousands of applications (e.g., when we
talk of microservice architecture), this approach becomes very tedious and challenging to maintain.

In this project, we will learn how to solve this problem and practice the technology that revolutionized application distribution
and deployment back in 2013 We are talking of Containers and imply Docker. Even though there are other application containerization
technologies, Docker is the standard and the default choice for shipping your app in a container!


> ### Side self study: Before starting to work with this project, it is very important to understand what Docker is and what it is used for.
To get a sufficient level of theoretical knowledge it is recommended to take Docker course before you continue with this project.**


**Once you have finished the Docker course, you can proceed with this practical project**

Install Docker and prepare for migration to the Cloud  First, we need to install Docker Engine, which is a client-server application that contains:

- A server with a long-running daemon process dockerd.
- APIs that specify interfaces that programs can use to talk to and instruct the Docker daemon.
- A command-line interface (CLI) client docker.

You can learn how to install Docker Engine on your PC [here](https://docs.docker.com/engine/install/)

First, update your existing list of packages:
```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```
Next, install a few prerequisite packages which let apt use packages over HTTPS:
```
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Add the Docker repository to APT sources:
```
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
```
Make sure you are about to install from the Docker repo instead of the default Ubuntu repo
```
apt-cache policy docker-ce
```
You’ll see output like this, although the version number for Docker may be different:
![image](https://github.com/user-attachments/assets/d44840ef-8851-4b3e-bc86-3b88a0f89193)

Finally, install Docker:
```
sudo apt install docker-ce
```

```
sudo systemctl start docker
sudo systemctl enable docker
``` 
![image](https://github.com/user-attachments/assets/5946a61f-7065-42e6-a90c-cdb63436b655)

**Verify the installation:**

```
docker --version
```
![image](https://github.com/user-attachments/assets/6bd8ec2a-bbc3-4df9-b9b7-904b17ab374b)

**Executing the Docker Command Without Sudo**

If you want to avoid typing sudo whenever you run the docker command, add your username to the docker group:

```
sudo usermod -aG docker ${USER}
```
To apply the new group membership, log out of the server and back in, or type the following:
```
su - ${USER}
```

Before we proceed further, let us understand why we even need to move from VM to Docker.

As you have already learned – unlike a VM, Docker allocated not the whole guest OS for your application, but only isolated minimal
part of it – this isolated container has all that your application needs and at the same time is lighter, faster, and can be shipped
as a Docker image to multiple physical or virtual environments, as long as this environment can run Docker engine. This approach 
also solves the environment incompatibility issue. It is a well-known problem when a developer sends his application to you, you 
try to deploy it, deployment fails, and the developer replies, "- It works on my machine!". With Docker – if the application is
shipped as a container, it has its own environment isolated from the rest of the world, and it will always work the same way on 
any server that has Docker engine.`IT WORKS ON MY MACHINE`

Now, when we understand the benefits we can get by using Docker containerization, let us learn what needs to be done to migrate to
Docker. [Read this excellent article for some insight](https://www.accenture.com/us-en/blogs/software-engineering-blog).


As a part of this project, you will use already well-known by you Jenkins for Continous Integration (CI). So, when it is time to 
write Jenkinsfile, update your Terraform code to spin up an EC2 instance for Jenkins and run Ansible to install & configure it.

To begin our migration project from VM based workload, we need to implement a [Proof of Concept (POC)](https://en.wikipedia.org/wiki/Proof_of_concept). 
In many cases, it is good to start with a small-scale project with minimal functionality to prove that technology can fulfill specific 
requirements. So, this project will be a precursor before you can move on to deploy enterprise-grade microservice solutions with Docker.

You can start with your own workstation or spin up an EC2 instance to install Docker engine that will host your Docker containers.

Remember our Tooling website? It is a PHP-based web solution backed by a MySQL database – all technologies you are already 
familiar with and which you shall be comfortable using by now.

**So, let us migrate the Tooling Web Application from a VM-based solution into a containerized one**


**MySQL in container**

Let us start assembling our application from the Database layer – we will use a pre-built MySQL database container, configure it,
and make sure it is ready to receive requests from our PHP application.

## Step 1: Pull MySQL Docker Image from [Docker Hub Registry](https://hub.docker.com/)

Start by pulling the appropriate [Docker image for MySQL](https://hub.docker.com/_/mysql). 
You can download a specific version or opt for the latest release, as seen in the following command:

Search for the available MySql docker image in the docker hub registry
 ```
 docker search mysql-server
```
![image](https://github.com/user-attachments/assets/433c1053-e911-4bcc-a3f9-7d72282aafb3)
Next, we will pull the first on the list, which is the offical and latest version 
```
docker pull mysql/mysql-server:latest
```
![image](https://github.com/user-attachments/assets/a03a7880-62c3-44f3-b439-a48ed1eb41cb)

If you are interested in a particular version of MySQL, replace latest with the version number. Visit Docker Hub to check other
tags here

List the images to check that you have downloaded them successfully:

```
docker image ls
```
![image](https://github.com/user-attachments/assets/a93344b5-dbff-41a0-8431-fe0c3383aa3a)

## Step 2: Deploy the MySQL Container to your Docker Engine
1. Once you have the image, move on to deploying a new MySQL container with:

```
docker run --name <container_name -e MYSQL_ROOT_PASSWORD=<my-secret-pw> -d mysql/mysql-server:latest
```
- Replace <container_name> with the name of your choice. If you do not provide a name, Docker will generate a random one
- The -d option instructs Docker to run the container as a service in the background
- Replace <my-secret-pw> with your chosen password
- In the command above, we used the latest version tag. This tag may differ according to the image you downloaded
  
![image](https://github.com/user-attachments/assets/9d3ebf60-298f-486b-a17e-91a625fab064)


2. Then, check to see if the MySQL container is running: Assuming the container name specified is mysql-server
  
```
docker ps -a
```
```
CONTAINER ID   IMAGE                                COMMAND                  CREATED          STATUS                             PORTS                       NAMES
7141da183562   mysql/mysql-server:latest            "/entrypoint.sh mysq…"   12 seconds ago   Up 11 seconds (health: starting)   3306/tcp, 33060-33061/tcp   mysql-server
```
You should see the newly created container listed in the output. It includes container details, one being the status of this virtual
environment. The status changes from health: starting to healthy, once the setup is complete.

  ![image](https://github.com/user-attachments/assets/f5447dd0-9496-4e83-acbd-8443ac929564)
  
  ## Step 3: Connecting to the MySQL Docker Container
We can either connect directly to the container running the MySQL server or use a second container as a MySQL client. Let us see 
what the first option looks like.

**Approach 1**

Connecting directly to the container running the MySQL server:
```
docker exec -it mysqldb mysql -uroot -p
```
![image](https://github.com/user-attachments/assets/8baf55e3-d03c-4d8b-b6d5-38c65a4158a3)

Provide the root password when prompted. With that, you’ve connected the MySQL client to the server.

Finally, change the server root password to protect your database.
Exit the mysql mode Exit with exit command
```
exit
```

**Approach 2**

At this stage you are now able to create a docker container but we will need to add a network. So, stop and remove the previous
mysql docker container.

```
docker ps -a
docker stop mysqldb 
docker rm mysqldb or <container ID> 04a34f46fb98
```

verify that the container is deleted
```
docker ps -a
```
![image](https://github.com/user-attachments/assets/27e6da06-ec69-44b0-bb6f-2c7336f0a923)


 First, create a network:
```
docker network create --subnet=172.18.0.0/24 tooling_app_network 
```
![image](https://github.com/user-attachments/assets/c8500496-52a4-4365-b737-f4f508f8c1e1)

Creating a custom network is not necessary because even if we do not create a network, Docker will use the default network for all 
the containers you run. By default, the network we created above is of DRIVER Bridge. So, also, it is the default network. You can
verify this by running the `docker network ls` command
```
docker network ls
```
![image](https://github.com/user-attachments/assets/26aa771b-e8ab-4acb-8226-f214ac3da39f)
to see details 
```
sudo docker network  inspect  tooling_app_network
```
![image](https://github.com/user-attachments/assets/543f5839-8598-4b8a-b255-3db440ebd713)

But there are use cases where this is necessary. For example, if there is a requirement to control the cidr range of the containers
running the entire application stack. This will be an ideal situation to create a network and specify the --subnet

For clarity’s sake, we will create a network with a subnet dedicated for our project and use it for both MySQL and the application 
so that they can connect. Run the MySQL Server container using the created network.

First, let us create an environment variable to store the root password:
```
export MYSQL_PW=PassWord.1 
````
verify the environment variable is created
```
echo $MYSQL_PW
```
![image](https://github.com/user-attachments/assets/04f7eaba-1349-4adc-84a1-a927168becb4)


Then, pull the image and run the container, all in one command like below:
```
  docker run --network tooling_app_network -h mysqlserverhost --name=mysql-server -e MYSQL_ROOT_PASSWORD=$MYSQL_PW  -d mysql/mysql-server:latest 
```
Flags used

- -d runs the container in detached mode
- --network connects a container to a network
- -h specifies a hostname
![image](https://github.com/user-attachments/assets/0a2b1ca6-c34c-4f23-9180-9f723287a661)

If the image is not found locally, it will be downloaded from the registry.

Verify the container is running:
```
docker ps -a 
 ```
 
 ```
 CONTAINER ID   IMAGE                                COMMAND                  CREATED          STATUS                             PORTS                       NAMES
7141da183562   mysql/mysql-server:latest            "/entrypoint.sh mysq…"   12 seconds ago   Up 11 seconds (health: starting)   3306/tcp, 33060-33061/tcp   mysql-server
 ```
![image](https://github.com/user-attachments/assets/afb5c23e-06e6-4f61-9ca1-18cd84eaadfc)
 
As you already know, it is best practice not to connect to the MySQL server remotely using the root user. Therefore, we will create
an SQL script that will create a user we can use to connect remotely.

Create a file and name it `reate_user.sql` and add the below code in the file:
```
CREATE USER 'melkamu'@'%' IDENTIFIED BY 'PassWord.1'; 
GRANT ALL PRIVILEGES ON * . * TO 'melkamu'@'%'; 
```
![image](https://github.com/user-attachments/assets/a5cc3d2c-9f29-4733-b46a-ccd073888fca)

**Run the script:**
Ensure you are in the directory create_user.sql file is located or declare a path
```
docker exec -i mysql-server mysql -uroot -p$MYSQL_PW < create_user.sql 
```
If you see a warning like below, it is acceptable to ignore:
```
mysql: [Warning] Using a password on the command line interface can be insecure.
```
![image](https://github.com/user-attachments/assets/5b6ec49d-183c-4e47-bf29-1834b4044bb5)

**Connecting to the MySQL server from a second container running the MySQL client utility**

The good thing about this approach is that you do not have to install any client tool on your laptop, and you do not need to connect
directly to the container running the MySQL server.

Run the MySQL Client Container:
```
sudo docker run --network tooling_app_network --name mysql-client -it --rm mysql mysql -h mysqlserverhost -u melkamu -p
```
Flags used:

- --name gives the container a name
- -it runs in interactive mode and Allocate a pseudo-TTY
- --rm automatically removes the container when it exits
- --network connects a container to a network
- -h a MySQL flag specifying the MySQL server Container hostname
- -u user created from the SQL script
- admin username-for-user-created-from-the-SQL-script-create_user.sql
- -p password specified for the user created from the SQL script
![image](https://github.com/user-attachments/assets/87553fd4-44de-4618-9fe3-a08ad6819b9c)


**Prepare database schema**

Now you need to prepare a database schema so that the Tooling application can connect to it.

1. Clone the Tooling-app repository from [here](https://github.com/StegTechHub/tooling-02)
 ```
git clone https://github.com/StegTechHub/tooling-02 
```
![image](https://github.com/user-attachments/assets/ded4ae41-9989-402d-b87b-b1f21b6a18d2)

2. On your terminal, export the location of the SQL file
```
 export tooling_db_schema=tooling-02/html/tooling_db_schema.sql
```
You can find the tooling_db_schema.sql in the tooling-02/html/tooling_db_schema.sql folder of cloned repo.
![image](https://github.com/user-attachments/assets/b4710c30-3dd6-44cb-a6c9-acb867bae801)

![image](https://github.com/user-attachments/assets/a4fb8bd3-ae09-4876-aa06-f66f4fcb28b9)

Verify that the path is exported
```
echo $tooling_db_schema
```
![image](https://github.com/user-attachments/assets/90b24d5d-6feb-4531-aaf7-51405c312f26)

3. Use the SQL script to create the database and prepare the schema. With the docker exec command, you can execute a command in a 
running container.
```
  docker exec -i mysql-server mysql -uroot -p$MYSQL_PW < $tooling_db_schema 
```
![image](https://github.com/user-attachments/assets/cf5a8ccf-1cdc-4a0e-aaf3-b6f2bd2e08ba)

4. Update the db_conn.php file with connection details to the database
```
MYSQL_IP=mysqlserverhost
MYSQL_USER=username
MYSQL_PASS=client-secrete-password
MYSQL_DBNAME=toolingdb
```
![image](https://github.com/user-attachments/assets/cda87987-794e-4c35-948e-1fd3123c97b5)
![image](https://github.com/user-attachments/assets/ebde3a94-7e5c-4c6c-9c3d-32a25bba5031)


5. Run the Tooling App
Containerization of an application starts with creation of a file with a special name - 'Dockerfile' (without any extensions). 
This can be considered as a 'recipe' or 'instruction' that tells Docker how to pack your application into a container. In this
project, you will build your container from a pre-created Dockerfile, but as a DevOps, you must also be able to write Dockerfiles.

You can [watch this video](https://youtu.be/hnxI-K10auY) to get an idea how to create your Dockerfile and build a container from it.

And on this [page](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/), you can find official Docker best 
practices for writing Dockerfiles.

So, let us containerize our Tooling application; here is the plan:

- Make sure you have checked out your Tooling repo to your machine with Docker engine
- First, we need to build the Docker image the tooling app will use. The Tooling repo you cloned above has a Dockerfile for this purpose. 
Explore it and make sure you understand the code inside it.
- Run docker build command
- Launch the container with docker run
- Try to access your application via port exposed from a container


**Let us begin:**

Ensure you are inside the directory "tooling" that has the file Dockerfile and build your container :
```
docker build -t tooling:0.0.1 . 
```
![image](https://github.com/user-attachments/assets/f241fd28-2554-453f-a83f-d6bdf7682c0d)
![image](https://github.com/user-attachments/assets/9925cf8e-6146-4d16-abeb-2f3e30f3112f)

In the above command, we specify a parameter -t, so that the image can be tagged tooling"0.0.1 - Also, you have to notice the . at 
the end. This is important as that tells Docker to locate the Dockerfile in the current directory you are running the command. 
Otherwise, you would need to specify the absolute path to the Dockerfile.

6. Run the container:
``` 
docker run --network tooling_app_network -d -p 8085:80 -it tooling:0.0.1 
```
![image](https://github.com/user-attachments/assets/69f952ff-ea71-4848-aa2b-b13427026ec3)

we should open port 8085
![image](https://github.com/user-attachments/assets/bf221d5a-5af8-4843-8c61-4142acdea72c)

Let us observe those flags in the command.


We need to specify the --network flag so that both the Tooling app and the database can easily connect on the same virtual network
we created earlier.
The -p flag is used to map the container port with the host port. Within the container, apache is the webserver running and, by default,
it listens on port 80. You can confirm this with the CMD ["start-apache"] section of the Dockerfile. But we cannot directly use 
port 80 on our host machine because it is already in use. The workaround is to use another port that is not used by the host machine.
In our case, port 8085 is free, so we can map that to port 80 running in the container.

**Note:** You will get an error. But you must troubleshoot this error and fix it. Below is your error message.


```
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 172.18.0.3. Set the 'ServerName' directive globally to suppress this message
```


> Hint: You must have faced this error in some of the past projects. It is time to begin to put your skills to good use. Simply do a
google search of the error message, and figure out where to update the configuration file to get the error out of your way.

If everything works, you can open the browser and type http://localhost:8085

You will see the login page.
![image](https://github.com/user-attachments/assets/d335944c-9e82-423d-be6c-eda785334a80)


The default email is test@gmail.com, the password is 12345 or you can check users' credentials stored in the toolingdb.user table.

## PRACTICE TASK
Practice Task 1 – Implement a POC to migrate the PHP-Todo app into a containerized application.
Download php-todo repository from [here](https://github.com/StegTechHub/tooling)

The project below will challenge you a little bit, but the experience there is very valuable for future projects.

**Part 1**

1. Write a Dockerfile for the TODO app
![image](https://github.com/user-attachments/assets/1bf9ae4d-828e-4704-92c8-dc15422d47f7)

2. Run both database and app on your laptop Docker Engine
Build the Application Docker Image
```
docker build -t php-todo-app .
```
![image](https://github.com/user-attachments/assets/17c393d6-d12b-4312-af27-18e862b4c731)

Run the MySQL container with the following command:
```
docker run --name mysql-db -e MYSQL_ROOT_PASSWORD=rootpassword -e MYSQL_DATABASE=tododb -e MYSQL_USER=todo_user -e MYSQL_PASSWORD=todo_pass -p 3306:3306 -d mysql:8.0
```
docker run --name mysql-db -e MYSQL_ROOT_PASSWORD=rootpassword -e MYSQL_DATABASE=mydatabase -e MYSQL_USER=myuser -e MYSQL_PASSWORD=mypassword -d mysql:8.0
docker run --name php-apache-app -p 8080:80 --link mysql-db:db -d php-apache-app
Run the PHP-Todo application container with:
```
docker run --name php-todo -p 8080:80 --link mysql-db:mysql -d php-apache-app
```
![image](https://github.com/user-attachments/assets/83f512c5-2c45-44fc-a924-e64f04e9a578)

3. Access the application from the browser
![image](https://github.com/user-attachments/assets/46a02ed0-50dc-443b-a437-f9c8f636a6b8)


**Part 2**
1. Create an account in Docker Hub
![image](https://github.com/user-attachments/assets/0fabd82a-d5db-4788-ae72-f23b77f53242)

2. Create a new Docker Hub repository
![image](https://github.com/user-attachments/assets/79142718-4f41-4066-8e72-4104527318ee)

3. Push the docker images from your PC to the repository
Tag Your Docker Image
```
docker tag php-apache-app melkamu372/php-todo-app:latest

```
Log In to Docker Hub:
```
docker login
```
![image](https://github.com/user-attachments/assets/2b55c1de-8a09-4008-a46b-2f7f1388f50a)
Push the Docker Image:
```
docker push melkamu372/php-todo-app:latest

```
![image](https://github.com/user-attachments/assets/603e5a21-1848-40fe-af24-6da1cc051212)
![image](https://github.com/user-attachments/assets/cd36cb1f-d9ab-4d8f-814c-bb44286d1c0f)

**Part 3**
1. Write a Jenkinsfile that will simulate a Docker Build and a Docker Push to the registry

![image](https://github.com/user-attachments/assets/50ca483b-dfb6-45dc-8e6d-e6d7b3128800)

2. Connect your repo to Jenkins
![image](https://github.com/user-attachments/assets/c56227b2-e0e0-4610-99f2-f2f8f4491c15)

3. Create a multi-branch pipeline
- Install Blue Ocean plugin and Open it dashboard
- Add your Docker login credentials in Manage Credentials in Jenkins> create New pipeline and  Select Github and your Github account >  Select the repo for the pipeline > create pipeline

4. Simulate a CI pipeline from a feature and master branch using previously created Jenkinsfile
![image](https://github.com/user-attachments/assets/4fef6aa1-01c4-4c5c-8f77-bc7421a33bca)

5. Ensure that the tagged images from your Jenkinsfile have a prefix that suggests which branch the image was pushed from. 
For example, feature-0.0.1.
6. Verify that the images pushed from the CI can be found at the registry.
![image](https://github.com/user-attachments/assets/a14915ef-ff91-445b-a228-badaf7eea043)


## Deployment with [Docker Compose](https://docs.docker.com/compose/)

All we have done until now required quite a lot of effort to create an image and launch an application inside it. We should not have
to always run Docker commands on the terminal to get our applications up and running. There are solutions that make it easy to write 
[declarative code](https://en.wikipedia.org/wiki/Declarative_programming) in [YAML](https://en.wikipedia.org/wiki/YAML), and get all
the applications and dependencies up and running with minimal effort by launching a single command.


**In this section, we will refactor the Tooling app POC so that we can leverage the power of Docker Compose**

1. First, install Docker Compose on your workstation from [here](https://docs.docker.com/compose/install/)
```
docker-compose --version
docker-compose version 1.28.5, build c4eb3a1f 
```
![image](https://github.com/user-attachments/assets/46484fc9-e0e2-4c6c-a168-716d0aed25f7)

2. Create a file, name it tooling.yaml
3. Begin to write the Docker Compose definitions with YAML syntax. The YAML file is used for defining services, networks, and volumes:

```
version: "3.9"
services:
  tooling_frontend:
    build: .
    ports:
      - "5000:80"
    volumes:
      - tooling_frontend:/var/www/html
```
![image](https://github.com/user-attachments/assets/f5c7dd6b-31ff-4adb-9acc-6f80999a4cea)

The YAML file has declarative fields, and it is vital to understand what they are used for.

- version: Is used to specify the version of Docker Compose API that the Docker Compose engine will connect to. This field is optional
from docker compose version v1.27.0. You can verify your installed version with:

- service: A service definition contains a configuration that is applied to each container started for that service. In the snippet 
above, the only service listed there is tooling_frontend. So, every other field under the tooling_frontend service will execute 
some commands that relate only to that service. Therefore, all the below-listed fields relate to the tooling_frontend service.
- build
- port
- volumes
- links

You can visit the site here to find all the fields and read about each one that currently [matters to you](https://www.balena.io/docs/reference/supervisor/docker-compose/)

You may also go directly to the official documentation site to read about each field [here](https://docs.docker.com/compose/compose-file/compose-file-v3/)


Let us fill up the entire file and test our application:

```
version: "3.9"
services:
  tooling_frontend:
    build: .
    ports:
      - "5000:80"
    volumes:
      - tooling_frontend:/var/www/html
    links:
      - db
  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: <The database name required by Tooling app >
      MYSQL_USER: <The user required by Tooling app >
      MYSQL_PASSWORD: <The password required by Tooling app >
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    volumes:
      - db:/var/lib/mysql
volumes:
  tooling_frontend:
  db:
```

Run the command to start the containers

```
docker-compose -f tooling.yml  up -d 
```
![image](https://github.com/user-attachments/assets/88432ee7-ed43-4d6d-8481-4e009092a623)

Verify that the compose is in the running status:

```
docker compose ls
```
![image](https://github.com/user-attachments/assets/fcfa9939-1fe6-4113-8200-e9aef6081d95)

To stop containers
```
docker-compose -f tooling.yml down
```
![image](https://github.com/user-attachments/assets/423694de-51f2-486d-8782-51f319abc1d9)

**Practice Task 2 – Complete Continous Integration With A Test Stage**
1. Document your understanding of all the fields specified in the Docker Compose file tooling.yaml
**Services**
- tooling_frontend:
  - Build: Creates an image from the Dockerfile in the current directory.
  - Ports: Maps port 80 in the container to port 5000 on the host.
  - Volumes: Mounts a named volume tooling_frontend to /var/www/html in the container.
  - Links: Connects to the db service by name.
- db:
 - Image: Uses the MySQL 5.7 image.
- Restart: Automatically restarts if it stops.
- Environment: Sets up database credentials and configuration.
- Volumes: Mounts a named volume db to /var/lib/mysql in the container for persistent data.
   
**Links**

- links: Connects tooling_frontend to db, allowing the frontend to access the database service by its name.

**Volumes**
- tooling_frontend: Named volume for the frontend data.
- db: Named volume for MySQL data.

2. Update your Jenkinsfile with a test stage before pushing the image to the registry  What you will be testing here is to ensure that the tooling site http endpoint is able to return status code 200. Any other code 
will be determined a stage failure.
![image](https://github.com/user-attachments/assets/0c2af7bb-d0f8-485b-89ad-a3fb2b44a60e)
![image](https://github.com/user-attachments/assets/31b9fba4-df63-41b8-ad2b-e91ed37b9355)

4. Implement a similar pipeline for the PHP-todo app.


5. Ensure that both pipelines have a clean-up stage where all the images are deleted on the Jenkins server.

![image](https://github.com/user-attachments/assets/21667d36-7f23-460a-8633-14b25b38d049)

![image](https://github.com/user-attachments/assets/8bcdff3b-231e-4b63-a7cb-68ef23c336f4)

![image](https://github.com/user-attachments/assets/feaba5c2-d314-4788-9e02-8946fc2ccfa4)

![image](https://github.com/user-attachments/assets/0e05c357-18fe-4273-9e7d-b6899d87c7de)

## The End of Project 20
In this project we have started our journey into migrating an application running on virtual machines into the Cloud with containerization.
Now we know how to prepare a Dockerfile, build an image with Docker build and deploy it with Docker Compose!
In the next project, we will expand our skills further into more advanced use cases and technologies.
 


  
