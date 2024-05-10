# Web stack implementation (LEMP)  in  AWS project  2
> LEMP stack is a popular open-source software stack typically used for web development. It's similar to the more commonly known LAMP stack but substitutes Apache with Nginx as the web server. Here's what each letter stands for:

- **L: Linux**, the operating system on which the stack runs.
- **E: Nginx** (pronounced "engine x"), the web server software.
- **M: MySQL** or MariaDB, the database management system.
- **P: PHP**, Python, or Perl, the programming languages used for server-side scripting.
So, in essence, the LEMP stack consists of Linux, Nginx, MySQL/MariaDB, and PHP/Python/Perl. It's a powerful combination for serving dynamic websites and web applications.

## Project 2 covers similar concepts as Project 1 and help to cement your skill of deploying Web solutions using LA(E)MP stacks
 ### Step 0
  1. Log to aws account console and create  EC2 instance of t2.micro type with Ubuntu 24.04 LTS (HVM) Server  launch in the default region us-east-1. 

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/6f418a6c-2250-4fda-bf27-bb10b38ae3e0)

**Select Ubuntu free tire eligable version**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/46ad3fbf-d096-449e-a216-c8c1e3516333)

**Create new key pair or select existing key**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/b200fdca-dc51-4ac2-a45c-bfc0461b9b24)

**Network setting create new security group or use existing security group**
 
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/24df2a51-eaf7-4da9-ba8a-279d83928d18)

**Configure Storage and launch the instance**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/fb87e787-7961-4e11-9c25-e0a1794d9dc8)

**View Instance**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/00b3b452-0ec1-46a6-a4dc-176e4227a630)

**Instance Details**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/42672cdc-531b-4d06-b20b-d07c5e2db835)

**Configure security group with the following inbound rules:**

- Allow traffic on port 22 (SSH) with source from any IP address. This is opened by default.
- Allow traffic on port 80 (HTTP) with source from anywhere on the internet.
- Allow traffic on port 443 (HTTPS) with source from anywhere on the internet.
  
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/50bb530a-72d3-4b17-b9a8-381399af286e)

**Default VPC Network Configuration**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a3b7f37c-45fb-4c39-b81d-435a24ce9b25)

**Connect to EC2 instance from  SSH client**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/89d95de5-19af-4730-9bce-033414b977ba)

**Give Permission for the private ssh key that we used either created or existing during launching instance and downloaded**

```
sudo chmod 0400 melkamu_key.pem
```
**Then connect the instance using instance Public Ip Addresss or domain name**
```
ssh -i melkamu_key.pem ubuntu@35.175.237.174
```
**or**
```
ssh -i "melkamu_key.pem" ubuntu@ec2-35-175-237-174.compute-1.amazonaws.com
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d5cdb14f-f199-455e-beaf-bab867bdea0c)


###  Alternative tool for Putty for windows os users _Git Bash_

![gitbash](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/9985bca0-f088-46fa-808f-a467ad157f7a)  **Git Bash Alternative tool for Putty on windows to connect to our EC2 instance** 

Git Bash is a command-line interface application for Windows that allows you to interact with Git repositories using Unix-like commands. It provides a Unix-like environment on Windows, enabling you to run Git commands and other Unix utilities directly from the command line Like ours to connect EC2 instance.

For Windows: Unlike macOS and Linux which have built-in Unix-based shells, Windows uses a different command prompt environment.
 - **Git Bash provides**  a Unix-like shell experience (specifically, an emulation of Bash) on Windows.
- **Interact with Git**: Through Git Bash, you can use Git commands to perform various version control operations. This includes cloning repositories, making changes to files, committing those changes, pushing and pulling code, and more.
- **Power of Shell Scripting**: Bash also allows you to write shell scripts to automate tasks you perform frequently with Git.

> N.B When you connect to our EC2 instance using Git Bash  not require to conversion of .pem to .ppk simply use .pem  
```
ssh -i melkamu_key.pem ubuntu@34.229.199.16
```
### Step 1 - Installing the Nginx Web Server
In order to Display Web pages to our site visitors , we are going to employee Nginx, ahigh-performance web server. We will use **_apt _** package manager to install this package. since it is first time using apt for this session, start off by updating your server index.
```
sudo apt update
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/52a57af5-53be-4fa2-9a9c-384d12121665)


```
sudo apt install nginx
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/8db79350-5358-47a6-b792-fe3b6e5e90f7)

**Verify that nginx was successfully installed and running**

If it is green and running, then you did everything correctly

```
sudo systemctl status nginx
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/3849aa58-217a-421e-a8ce-1499988f0409)

**Our Server is running we can access it locally on the Ubuntu shell either using DNS Name or Ip Address**

```
curl http://localhost:80
```

```
curl http://127.0.0.1:80
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/4c606042-65f3-493c-8218-4ea4d03a61f6)

**Now Test Our Nginx server on the Internet**
[http://35.175.237.174/](http://35.175.237.174/)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/08e8ac6a-b0f9-4574-bed6-8c76b19e8e66)

**Another way to retrive public Ip address other than check in Aws web console**

```
curl -s http://169.254.169.254/latest/meta-data/public-ipv4
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/51b75752-ef44-4e63-a90b-cde85b58107f)

### Step 2 Installing MySQL
**Install mysql relational Data Base Managment System (DBMS)**

```
sudo apt install mysql-server
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/de6c52a4-1742-4749-8e49-8527302510ce)

**Log to mysql**
```
sudo mysql
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/2d406dc0-2b7a-4128-ab7f-966b75aae596)

**Set a password for root user using mysql_native_password as default authentication method.**
```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/76292a3c-92a0-4f0a-8d8f-bdfa404ff05c)

**Exit the MySql shell with**
```
exit
```
**Start Interactive Scripting by running**

```
sudo mysql_secure_installation
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/4539ff5f-2b45-4797-bf0d-822a578671a8)

**After finished ,test you are able to log to the MySql Console**

```
sudo mysql -p
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/30dfda60-1eb6-4a00-90d4-7e72ed3b052b)

**Exit the MySql shell with**
```
exit
```

### Step 3 Installing PHP
**Nginx installed to serve our content and MySQL Installed to store and manage our data now we install PHP to process code and generate dynamic content for the web server
Apache embeds the PHP interprater in each request ,Nginx requires an external program to handle PHP processing and act as bridge between the PHP interprator itself and web server.
The following two packages would be  installed:**

- php-fpm (PHP fastCGI process manager)
- php-mysql
```
sudo apt install php-fpm php-mysql
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/87fb6aec-1c27-4e10-81d1-f8d67202cef4)

### Step 4 Configuring Nginx to Use PHP Processor 
**Create a root web directory for your_domain**
```
sudo mkdir /var/www/projectLEMP
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/6c79e7df-7215-47fd-8317-ab6e687e0331)

**Assign Ownership of the directory**
```
sudo chown -R $USER:$USER /var/www/projectLEMP
```
**Then Open new configuration file in Nginx's _sites-available_ directory using command line editor nano**
```
sudo nano /etc/nginx/sites-available/projectLEMP
```
**Paste in the following bare-bones configuration:**
```
server {
  listen 80;
  server_name projectLEMP www.projectLEMP;
  root /var/www/projectLEMP;

  index index.html index.htm index.php;

  location / {
    try_files $uri $uri/ =404;
  }

  location ~ \.php$ {
    include snippets/fastcgi-php.conf;
    fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
  }

  location ~ /\.ht {
    deny all;
  }
}
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/bbd851c1-ae03-42b1-bebe-7b1c6702316f)


**Here’s what each of these  directives and location blocks do:**

- **listen** - Defines what port nginx listens on. In this case it will listen on port 80, the default port for HTTP.

- **root** - Defines the document root where the files served by this website are stored.

- **index** - Defines in which order Nginx will prioritize the index files for this website. It is a common practice to list index.html files with a higher precedence than index.php files to allow for quickly setting up a maintenance landing page for PHP applications. You can adjust these settings to better suit your application needs.

- **server_name** - Defines which domain name and/or IP addresses the server block should respond for. Point this directive to your domain name or public IP address.

- **location /** - The first location block includes the try_files directive, which checks for the existence of files or directories matching a URI request. If Nginx cannot find the appropriate result, it will return a 404 error.

- **location ~ .php$** - This location handles the actual PHP processing by pointing Nginx to the fastcgi-php.conf configuration file and the php7.4-fpm.sock file, which declares what socket is associated with php-fpm.

- **location ~ /.ht** - The last location block deals with .htaccess files, which Nginx does not process. By adding the deny all directive, if any .htaccess files happen to find their way into the document root, they will not be served to visitors.

**Activate the configuration by linking to the config file from Nginx’s sites-enabled directory**
```
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/eeea6740-a0e6-4ec1-9629-47e85ea1182b)

This will tell Nginx to use this configuration when next it is reloaded.

 **Test your configuration for syntax error by**
```
sudo nginx -t
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/b00f21f9-bf2c-4890-83f5-3ddb4942e82b)

**We also need to disable the default Nginx host that is currently configured to listen on port 80**
```
sudo unlink /etc/nginx/sites-enabled/default
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/155792ed-3347-4c67-ad84-34782bd0693d)

**We are ready reload nginx to apply the changes**
```
sudo systemctl reload nginx
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/244137ff-8c3c-4eae-97a5-10387711ef90)

**The new website is now active but the web root /var/www/projectLEMP is still empty. Create an index.html file in that location so that we can test the new server block works as expected**

```
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
```
**Then run locally**
```
curr http://localhost:80
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/cc3069f1-be80-4c3d-a093-b866f71cad21)

**Now Open the website on your browser using IP address**
[http://35.175.237.174/](http://35.175.237.174/)
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/29e44b5e-3a6e-4341-9096-a096041e3643)

**Or alternative use  public DNS name** 

[http://ec2-35-175-237-174.compute-1.amazonaws.com/](http://ec2-35-175-237-174.compute-1.amazonaws.com/)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a3b4beb6-a5c4-4522-97c7-5edce5c51fb6)

### Step 5 - Test PHP with Nginx
**Test the LEMP stack to validate that nginx can  correctly handle the .php files off your  PHP processor we can do by create a test PHP file in the document root. Open a new file called info.php within the document root in your text editor**.
```
sudo nano /var/www/projectLEMP/info.php
```
**Past:**
```
<?php
phpinfo();
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a7ae92a9-557d-4ec9-abf5-87ad21c4e9be)

**You can access the page in your browser using public Ip followed by info.php**
[http://54.80.11.17/info.php](http://35.175.237.174/info.php)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/85e86a5a-8a9f-413e-94b6-2b5cd083da66)

**After checking the relevant information about your php server through that page, it’s best to remove the file you created as it contains sensitive information about the PHP environment and the ubuntu server.**
```
sudo rm /var/www/projectLEMP/info.php
```
## Step 6 Retrieving data from MySQL database with PHP
**We will create a simple 'to do list database' and a new user with the mysql_native_password authentication to connect to MySQL database from PHP.
 a database named todo_db and a user named todo_user**

1. connect to the MySQL console using the root account.
````
sudo mysql -p
````
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/fa856dfc-025a-42e3-b88a-4d453e828ac3)
2. create database todo_db
```
CREATE DATABASE todo_db;
````
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/c0f5f771-0c48-4965-8527-ccf996006580)

3. Create a new user 
```
CREATE USER 'todo_user'@'%' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d4db0a9b-b866-4ab3-9127-6e43def1097c)

4. Give permission full privileges on the new database.
```
GRANT ALL ON todo_db.* TO 'todo_user'@'%';
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/8ff208be-adb2-40b9-9e23-13a37171e8a4)

**Exit the MySql shell with**
```
exit
```

5. Test new user have proper permission
```
mysql -u todo_user -p
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/b8747f41-ca4d-4b32-a42b-da44feda81b9)


6. List database 
```
SHOW DATABASES;
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/c084778d-58dd-4539-8221-da8730038914)

7. Create a test table named todo_list

**From MySQL console, run the following statment :**
```
CREATE TABLE todo_db.todo_list (
  item_id INT AUTO_INCREMENT,
  content VARCHAR(255),
  PRIMARY KEY(item_id)
);
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/4b041131-3c2e-4c68-9b57-796ecdf6a327)

8. Insert a few rows of content to the test table.

```
INSERT INTO todo_db.todo_list (content) VALUES ("My first important item");

INSERT INTO todo_db.todo_list (content) VALUES ("My second important item");

INSERT INTO todo_db.todo_list (content) VALUES ("My third important item");
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/149ce4d5-ee35-41ad-b8aa-9ad69e397fe5)

9. Confirm that the data was successfully saved :

```
SELECT * FROM todo_db.todo_list;
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/29debb54-83ed-44dc-87d3-bd8437240f44)


10.  Exit the MySql shell with
```
exit
```
## Create a PHP script that will connect to MySQL and query the content.first create a new PHP  file in the custom web root directory using your editor

```
sudo nano /var/www/projectLEMP/todo_list.php
```
**Then past the following content for test**
```
<?php
$user = "todo_user";
$password = "PassWord.1";
$database = "todo_db";
$table = "todo_list";
try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
?>
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/04e9299b-3d88-4ecb-808c-2ce603701db7)

 **Access this page on your  browser by using the domain name or public IP address followed by /todo_list.php**

[http://54.80.11.17/todo_list.php](http://54.80.11.17/todo_list.php)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/ff78b771-4043-4f74-a974-e01857caabda)

### This is the end of this page which describes all about LEMP Stack deployement on aws!!! 
