# Web stack(LAMP) Implimentation Preparation Pre requisites

1. ### aws account setup and provisioning an Ubuntu Server
create Aws aacount to access aws console to run over all action Ec2 
![image](https://github.com/melkamu372/web_stack_implimentation_lamp_stack_in_aws/assets/47281626/900b9f4f-4238-4c4c-a8b2-75fc6a5ab7e8)

2. ### Connect Ec2 Instance
   
First  get private key[melkamu_key.pem] of EC2 instance server by download and give permission
```
sudo chmod 0400 melkamu_key.pem
```

The connect the instance  using instance Public Ip Addresss
```
ssh -i melkamu_key.pem ubuntu@34.229.199.16
```
![image](https://github.com/melkamu372/web_stack_implimentation_lamp_stack_in_aws/assets/47281626/670874e1-e0d8-48cf-bea8-86cac6a2b8c9)

3. ### Install Apache and Updating Firewall

```
sudo apt update
```

```
sudo apt install apache2
```
**Verify Installation**

```
sudo systemctl status apache2
```
![image](https://github.com/melkamu372/web-stack-implementation-lamp-stack-in-aws-/assets/47281626/c32d582e-39fd-4da8-96d0-d121f1390f16)

**Check acccess locally**

```
curl http://localhost:80
```
```
curl http://127.0.0.1:80
```
**Add inbound rule 80**

![image](https://github.com/melkamu372/web-stack-implementation-lamp-stack-in-aws-/assets/47281626/741d6a70-8d96-4179-9df8-70df804c5ebb)

**Test using browser**

`http://52.207.80.161/`

![image](https://github.com/melkamu372/web-stack-implementation-lamp-stack-in-aws-/assets/47281626/5b575bd3-1d9b-4f4a-9b18-5b89a0299202)

4. ### Installing MYSQL
   
```
sudo apt install mysql-server
```

*Log to mysqlconsole*
```
sudo mysql
```
![image](https://github.com/melkamu372/web-stack-implementation-lamp-stack-in-aws-/assets/47281626/362b8648-9d13-4685-b8d3-b9f7b855d499)

**Alter the authentication method for the 'root' user at the 'localhost' host and set the password to 'PassWord.1'**

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
```

![image](https://github.com/melkamu372/web-stack-implementation-lamp-stack-in-aws-/assets/47281626/9e11615b-aeec-4098-a033-96d3cae25c32)

**Interactive Script**
```
sudo mysql_secure_installation
```

![image](https://github.com/melkamu372/web-stack-implementation-lamp-stack-in-aws-/assets/47281626/73226b37-992a-4615-9c4d-8fa884619b57)
![image](https://github.com/melkamu372/web-stack-implementation-lamp-stack-in-aws-/assets/47281626/ade441a0-b3f6-419f-bf05-cd5afe541296)


5. ### Installing PHP
```
sudo install phph libapache2-mod-php  php-mysql
```
**Confirm php version**
```
php -v
```
![image](https://github.com/melkamu372/web-stack-implementation-lamp-stack-in-aws-/assets/47281626/a6956805-4519-4bde-b81d-96611f59fee7)

6. ### Creating Virtual Host For Your Website using Apache

1. Create directory projectlamp inside /var/www
```
sudo mkdir /var/www/projectlamp

```
2. Give Ownerhip for current User
   
```
sudo chown -R $user:$user /var/www/projectlamp
```
3. Create Virtual Host Configuration File and edit using editor
   
```
sudo vim /etc/apache2/sites-available/projectlamp.conf
```
![image](https://github.com/melkamu372/web-stack-implementation-lamp-stack-in-aws-/assets/47281626/3c1a8c28-30a4-45d7-ae55-6843c4143830)

4. see available files in sites-available folder
   
```
sudo ls /etc/apache2/sites-available

```
![image](https://github.com/melkamu372/web-stack-implementation-lamp-stack-in-aws-/assets/47281626/b376e777-62fc-4f03-955a-819df5e08ae9)

5. Enable new virtual host
```
udo a2ensite projectlamp

```
6. Disable default website

```
sudo a2dissite 000-default
```
7. Check Syntax error
```
sudo apache2ctl configtest
```
![image](https://github.com/melkamu372/web-stack-implementation-lamp-stack-in-aws-/assets/47281626/83e1d0f1-a676-40b3-a3b6-52ae8e0b3385)

8. Reload Apache to change effect
   
```
sudo systemctl reload apache2
```
9. create index file to the root folder
```
sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with publicIP' $(curl -s http://169.254.169.254/latest-meta-data/public-ipv4) > /var/www/projectlamp/index.html 
```
![image](https://github.com/melkamu372/web-stack-implementation-lamp-stack-in-aws-/assets/47281626/345d55c3-3c31-4b84-8a6c-ce90ad2ce3a3)

10. Browse the website
    [Website URL http://52.207.80.161/ ](http://52.207.80.161/)
![image](https://github.com/melkamu372/web-stack-implementation-lamp-stack-in-aws-/assets/47281626/eb84d0d7-8f17-4f61-9683-3cedad28af45)

  
