# Implementing a Load balancer Solution with Apache
Load balancers are a special type of server farm that helps in efficiently distributing incoming network traffic across a group of backend servers.

> In this project we will enhance our Tooling Website solution by adding a ***Load Balancer*** to distribute traffic between `Web Servers` and allow users to access our website using a single URL.

**Task**
Deploy and configure an Apache Load Balancer for Tooling Website solution on a separate Ubuntu EC2 intance. Make sure that users can be served by Web servers through the Load Balancer.

## Prerequisite for the projects.
1. AWS account log in with EC2 instances
2. Two Webserver Linux: Red Hat Enterprise Linux 9
3. Database Server: On Ubuntu 24.04 MySQL
4. Storage Server: Red Hat Enterprise Linux 9 (NFS Server)

# Configure Load Balancing Using Apache HTTP Server 

1. Create an Ubuntu Server 24.04 EC2 instance and name it **Project-8-apache-lb**
Log to aws account console and create EC2 instance of t2.micro type with Ubuntu Server 20.04 LTS (HVM) server launch in the default region us-east-1. 
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/1905c1cf-16a2-4732-9640-d385bfcead1b)

Application and OS Images select Ubuntu Server 20.04 LTS (HVM)  free tire eligable version
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/27a2211a-1db1-414d-a35a-d3ff955c122c)

Create new key pair or select existing key 
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/b3d6fcaa-6efc-4434-a573-c88b4eadba41)

Network setting create new security group or use existing security group
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/e08a7f52-4316-4c2e-8938-e2949ea66748)

Configure Storage and launch the instance
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/c4e457b6-62d3-4a14-80bd-b14ae52d1ae9)

View Instance
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/f2125d56-f39d-41d2-a652-f24d5af0d2da)

Instance Details for Project-8-apache-lb 
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/611d7789-ec7a-48d2-8b84-3a66dc95209c)

2. Open TCP port 80 on Apache-lb by creating an Inbound Rule in Security Group.
Configure security group with the following inbound rules:

Allow traffic on port 22 (SSH) with source from any IP address. This is opened by default.
Allow traffic on port 80 (HTTP) with source from anywhere on the internet.
Allow traffic on port 443 (HTTPS) with source from anywhere on the internet.

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/cb5ffba2-7431-420a-8d16-44f090d20c20)

3. Install Apache Load Balancer on Apache-lb server and configure it to point traffic coming to LB to both Web Servers:

Connect to our lb server 
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/56d5215c-03d3-4894-b1f3-8003b82c886d)


**Install apache2**
```
sudo apt update
sudo apt install apache2 -y
sudo apt-get install libxml2-dev
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/f66b2584-c389-4a40-9489-ff20cbb94813)


**Enable following modules**:
```
sudo a2enmod rewrite

sudo a2enmod proxy
sudo a2enmod proxy_balancer
sudo a2enmod proxy_http
sudo a2enmod headers
sudo a2enmod lbmethod_bytraffic
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/0419ada4-db6b-4a1f-b4cc-e16c64d1ff0c)

**Restart apache2 service**

```
sudo systemctl restart apache2
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/5ec23076-abc0-477e-a0ce-41aa5ce16aab)

**Make sure apache2 is up and running**
```
sudo systemctl status apache2
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a8612fac-f3d9-4bdd-a9f9-2d55b7e564e0)

**Configure load balancing**
```
sudo vi /etc/apache2/sites-available/000-default.conf
```

**Add this configuration into this section**
`
 <VirtualHost *:80>
 
 </VirtualHost>
`

```
<Proxy "balancer://mycluster">
               BalancerMember http://172.31.29.247:80 loadfactor=5 timeout=1
               BalancerMember http:172.31.20.250:80 loadfactor=5 timeout=1
               ProxySet lbmethod=bytraffic
               # ProxySet lbmethod=byrequests
        </Proxy>
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/38a38810-0ffb-453d-8985-9b64cd2c2c22)

```
ProxyPreserveHost On
ProxyPass / balancer://mycluster/
ProxyPassReverse / balancer://mycluster/
```

**Restart apache server**
```
sudo systemctl restart apache2
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/835312f2-a626-4ba0-b741-6b5548e2a14c)

> `bytraffic` balancing method will distribute incoming load between our Web Servers according to current traffic load. We can control in which proportion the traffic must be distributed by loadfactor parameter.

we can also use other methods, like: `bybusyness`, `byrequests`, `heartbeat`

**Verify that our configuration works** 

```
http://3.90.26.133/index.php
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/4c04e86b-1d67-478e-a3e9-e6eb14e1438c)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/6535d21e-cb09-48b3-acdf-58b2e94e5675)
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/43ca0184-fe0e-4056-a236-55a2c2e0c83d)
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/97b07510-1767-4645-96cc-199c60155067)

> **Note**: If in the previous project, we mounted /var/log/httpd/ from our Web Servers to the **NFS server** - unmount them and make sure that each Web Server has its own log directory.

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/0f0d47ca-ad2b-4dde-9097-280f6183e046)
**Unmounting the NFS Directory**
1. Identify the NFS mount: Check the current NFS mounts:
```
df -h
```
2. Look for the line showing the /var/log/httpd mount point. Unmount the NFS directory:
```
sudo umount /var/log/httpd
```
> If the directory is busy, you might need to stop the services using it first:

```
sudo systemctl stop httpd
```
3. Verify the unmount:
Check that the directory is unmounted:
```
df -h
```
we should do the above step for all
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/7b8d9e2f-da24-4bd5-b05f-7df0362bbcf5)


**Open two ssh/Putty consoles for both Web Servers and run following command:**
```
sudo tail -f /var/log/httpd/access_log
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/3116605c-4bd2-46f4-94d2-3f5da23d0e8f)

Try to refresh your browser page 
```
http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php 
```

several times and make sure that both servers receive HTTP GET requests from our LB - new records must appear in each server's log file. The number of requests to each server will be approximately the same since we set loadfactor to the same value for both servers - it means that traffic will be distributed evenly between them.

# Optional Step - Configure Local DNS Names Resolution
Sometimes it is tedious to remember and switch between IP addresses, especially if we have a lot of servers. What we can do, is to configure `local domain name resolution`. The easiest way is use /etc/hosts file, although this approach is not very scalable, but it is very easy to configure and shows the concept well.

So let us configure IP address to domain name mapping for our Load Balancer.

1. Open this file on your LB server
```
sudo vi /etc/hosts
```
2. add this file with **Local IP address** and **arbitrary name** for our Web Servers
```
<WebServer1-Private-IP-Address> Web1
<WebServer2-Private-IP-Address> Web2
```
Now we can update our LB config file with those names instead of IP addresses
```
BalancerMember http://Web1:80 loadfactor=5 timeout=1
BalancerMember http://Web2:80 loadfactor=5 timeout=1
```
now we can try to curl our Web Servers from LB locally
```
curl http://Web1
```
 **or**
 ```
 curl http://Web2
```
> **Remember**, This is only internal configuration and also local to our **LB server**, these names will neither be 'resolvable' from other servers internally nor from the Internet.
