# Web stack(LAMP) Implimentation Preparation Pre requisites

1. aws account setup and provisioning an Ubuntu Server
create Aws aacount to access aws console to run over all action Ec2 
![image](https://github.com/melkamu372/web_stack_implimentation_lamp_stack_in_aws/assets/47281626/900b9f4f-4238-4c4c-a8b2-75fc6a5ab7e8)

2. Connect Ec2 Instance
First  get private key[melkamu_key.pem] of EC2 instance server by download and give permission
```
sudo chmod 0400 melkamu_key.pem
```
The connect the instance  using instance Public Ip Addresss
```
ssh -i melkamu_key.pem ubuntu@34.229.199.16
```
![image](https://github.com/melkamu372/web_stack_implimentation_lamp_stack_in_aws/assets/47281626/670874e1-e0d8-48cf-bea8-86cac6a2b8c9)

3. Install Apache and Updating Firewall
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
:80![image](https://github.com/melkamu372/web-stack-implementation-lamp-stack-in-aws-/assets/47281626/c32d582e-39fd-4da8-96d0-d121f1390f16)

**Check acccess locally**
```
curl http://localhost:80
```
```
curl http://127.0.0.1:80
```
**Test using browser**

`http://3.208.10.85`

4. Installing MYSQL
```
sudo apt install mysql-server
```
*Log to mysqlconsole*
```
sudo mysql
```

5.  Installing PHP
```
sudo install phph libapache2-mod-php  php-mysql
```
**Confirm php version**
```
php -v
```
