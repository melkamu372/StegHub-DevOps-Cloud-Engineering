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
