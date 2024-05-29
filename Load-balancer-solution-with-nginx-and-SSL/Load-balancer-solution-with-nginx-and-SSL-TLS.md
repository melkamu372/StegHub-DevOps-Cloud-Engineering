# Load Balancer Solution With Nginx and SSL/TLS
**Load balancing** is an excellent way to scale out your application and increase its performance and redundancy. Nginx, a popular web server software, can be configured as a simple yet powerful load balancer to improve your server’s resource availability and efficiency.

**SSL/TLS Termination** Generate an SSL certificate and private key for the domain name users will access. You can use a free Let's Encrypt certificate or obtain one from a certificate authority. Configure Nginx to use the SSL certificate and key within the server block listening on port 443.Nginx will handle the SSL/TLS encryption and decryption for incoming connections. Traffic between Nginx and the backend servers will be unencrypted HTTP.

**Task**
This project consists of two parts:

1. Configure Nginx as a Load Balancer
2. Register a new domain name and configure secured connection using SSL/TLS certificates


# Part 1 Configure Nginx as a load balancer

1. Create an EC2 VM based on Ubuntu Server 24.04 LTS and name it nginx Ls 

- **open TCP port 80 for HTTP connections** 
- **open TCP port 443 for secured HTTPS connections**

2. Update `/etc/hosts` file for local DNS with Web Servers' names (e.g web1 and web2) and their local IP addresses

3. Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers

4. Update the instance and Install Nginx Install Nginx
```
sudo apt update
```
```
sudo apt install nginx
```
5. Edit the Nginx configuration file to set up load balancing
```
  sudo vi /etc/nginx/nginx.conf
```
6. insert the following configuration in `http` section

```
    upstream backend {
       server Webl weight=5;
       server Web2 weight=5;
    }

    server {
        listen 80;
        server_name ww.domain.com;

        location / {
            proxy_pass http://myproject;
        }
    }
    # comment out this line
    # include /ete/nginx/sites-enabled/

```
7. Test the Configuration

Before reloading Nginx, test the configuration to ensure there are no syntax errors
```
sudo nginx -t
```
8. Reload Nginx
If the configuration test is successful, reload Nginx to apply the changes

```
sudo systemctl reload nginx
```
9. Verify  the service is up and running
```
sudo systemctl status nginx
```
