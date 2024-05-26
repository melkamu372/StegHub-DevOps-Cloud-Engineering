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

2. Open TCP port 80 on Apache-lb by creating an Inbound Rule in Security Group.

3. Install Apache Load Balancer on Apache-lb server and configure it to point traffic coming to LB to both Web Servers:

**Install apache2**
```
sudo apt update
sudo apt install apache2 -y
sudo apt-get install libxml2-dev
```
**Enable following modules**:
```
sudo a2enmod rewrite
sudo a2enmod proxy
sudo a2enmod proxy_balancer
sudo a2enmod proxy_http
sudo a2enmod headers
sudo a2enmod lbmethod_bytraffic
```

**Restart apache2 service**

```
sudo systemctl restart apache2
```
**Make sure apache2 is up and running**
```
sudo systemctl status apache2
```

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
               BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
               BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
               ProxySet lbmethod=bytraffic
               # ProxySet lbmethod=byrequests
        </Proxy>
```

```
ProxyPreserveHost On
ProxyPass / balancer://mycluster/
ProxyPassReverse / balancer://mycluster/
```

**Restart apache server**
```
sudo systemctl restart apache2
```
> `bytraffic` balancing method will distribute incoming load between our Web Servers according to current traffic load. We can control in which proportion the traffic must be distributed by loadfactor parameter.

we can also use other methods, like: `bybusyness`, `byrequests`, `heartbeat`

**Verify that our configuration works** 

```
http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php
```

> **Note**: If in the previous project, we mounted /var/log/httpd/ from our Web Servers to the **NFS server** - unmount them and make sure that each Web Server has its own log directory.

**Open two ssh/Putty consoles for both Web Servers and run following command:**
```
sudo tail -f /var/log/httpd/access_log
```
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
