# DevOps Tooling Website Solution
## Step 1 - Prepare NFS Server
1. Spin up a new EC2 instance with RHEL Linux 8 Operating System.

2. Based on your LVM experience from Project 6, Configure LVM on the Server.
  **List Available Disks**
```
sudo lsblk
```
**Create Physical Volumes**:
```
sudo pvcreate /dev/xvdb /dev/xvdc /dev/xvdd

```
**Create a Volume Group**
```
sudo vgcreate vg_data /dev/xvdb /dev/xvdc /dev/xvdd

```
**Create Logical Volumes**
```
sudo lvcreate -L 14G -n lv-apps vg_data
sudo lvcreate -L 14G -n lv-logs vg_data
sudo lvcreate -L 14G -n lv-opt vg_data
```

3. Instead of formatting the disks as ext4 you will have to format them as xfs

- Ensure there are 3 Logical Volumes `lv-opt` `lv-apps`, and `lv-logs`
```
sudo lsblk
```
**Format the Logical Volumes as XFS:**
```
sudo mkfs.xfs /dev/vg_data/lv-apps
sudo mkfs.xfs /dev/vg_data/lv-logs
sudo mkfs.xfs /dev/vg_data/lv-opt
```

- Create mount points on `/mnt` directory for the logical volumes as follows: `Mount lv-apps` on /mnt/apps - To be used by webservers ,`Mount lv-logs` on /mnt/logs - To be used by webserver logs, `Mount lv-opt` on /mnt/opt - To be used by Jenkins server in Project 8

**Create Directories**:
```
sudo mkdir /mnt/apps
sudo mkdir /mnt/logs
sudo mkdir /mnt/opt
```
**Mount Logical Volumes**
```
sudo mount /dev/vg_data/lv-apps /mnt/apps
sudo mount /dev/vg_data/lv-logs /mnt/logs
sudo mount /dev/vg_data/lv-opt /mnt/opt

```
**Add Mount Points to /etc/fstab**
```
sudo vi /etc/fstab
```
**Add the following lines**:
```
/dev/vg_data/lv-apps /mnt/apps xfs defaults 0 0
/dev/vg_data/lv-logs /mnt/logs xfs defaults 0 0
/dev/vg_data/lv-opt /mnt/opt xfs defaults 0 0
```

**Verify Mounts**:

```
sudo mount -a
```
```
df -h
```

4. Install NFS server, configure it to start on reboot and make sure it is up and running

**Update the System and Install NFS Utilities**:
```
sudo yum -y update
```
```
sudo yum install nfs-utils -y
```

**Start and Enable NFS Server**:

```
sudo systemctl start nfs-server.service
```
```
sudo systemctl enable nfs-server.service
```
```
sudo systemctl status nfs-server.service
```

5. Export the NFS Mounts'

> Use `subnet cidr` to connect as clients. For simplicity, we will install our all three Web Servers inside the same subnet, but in _production_ set up  would probably want to separate each tier inside its own subnet for higher level of security. To check our subnet cidr - open our EC2 details in AWS web console and locate `Networking` tab and open a Subnet link:

**Set Permissions on Mount Points**
```
sudo chown -R nobody:nobody /mnt/apps
sudo chown -R nobody:nobody /mnt/logs
sudo chown -R nobody:nobody /mnt/opt
sudo chmod -R 777 /mnt/apps
sudo chmod -R 777 /mnt/logs
sudo chmod -R 777 /mnt/opt
```

**Restart NFS Server**
```
sudo systemctl restart nfs-server.service
```

6. Configure access to NFS for clients within the same subnet (example of Subnet CIDR - 172.31.32.0/20 ):
```
sudo vi /etc/exports
```
**Add the following lines, replacing <Subnet-CIDR> with your actual subnet CIDR**:

```
/mnt/apps <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
```
**save and exit from the editor by** `Esc + :wq!`

**Export the NFS Shares**:

```
sudo exportfs -arv
```

7. Check which port is used by NFS and open necessary ports in Security Groups (add new Inbound Rule)

**Check NFS Ports**:

```
rpcinfo -p | grep nfs
```

> **Important note**: In order for NFS server to be accessible from our client,we open following ports:
- `TCP 111`
- `UDP 111`
- `UDP 2049`


8.  Final Verification

**Test NFS Export**: From a client within the same subnet, try mounting the NFS share.

```
sudo mount -t nfs <NFS-Server-IP>:/mnt/apps /mnt
```
**Verify Access**:

```
ls /mnt
```

## Step 2 - Configure the database server

1. Install MySQL Server
First, we need to install MySQL on our server. For RHEL-based systems

1.1. Update the package index:
```
sudo yum update
```
1.2. Install MySQL server:
```
sudo yum install mysql-server
```
1.3 Start the MySQL service:
```
sudo systemctl start mysqld
```
1.4. Enable MySQL to start on boot:
```
sudo systemctl enable mysqld
```
1.5. Secure the MySQL installation (set root password, remove test databases, etc.):
```
sudo mysql_secure_installation

```
Follow the prompts to complete the configuration.


2. Create a database and name it tooling

 2.1. Log in to the MySQL server as the root user:
```
sudo mysql -u root -p
```
2.2. Create the database:
```
CREATE DATABASE tooling;
```

3. Create a database user and name it `webaccess`

```
CREATE USER 'webaccess'@'%' IDENTIFIED BY 'PassWord.1';
```
4. Grant permission to webaccess user on tooling database to do anything only from the webservers subnet `cidr`

```
GRANT ALL PRIVILEGES ON tooling.* TO 'webaccess'@'<Subnet-CIDR>';

```
**Apply the changes**:
```
FLUSH PRIVILEGES;
```
**Exit MySQL**
```
exit
```
## Step 3 - Prepare the Web Servers

 **In this step we will do the following**
 - Configure NFS client (this step must be done on all _three servers_)
 - Deploy a Tooling application to our Web Servers into a shared NFS folder
 - Configure the Web Servers to work with a single MySQL database
**For server One**

1. Launch a new EC2 instance with RHEL 8 Operating System

2. Install NFS client
```
sudo yum install nfs-utils nfs4-acl-tools -y
```
3. Mount /var/www/ and target the NFS server's export for apps
```
sudo mkdir /var/www
```
```
sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www
```
4. Verify that NFS was mounted successfully
```
 df -h
 ```
  
Make sure that the changes will persist on Web Server after reboot:
```
sudo vi /etc/fstab
```

add following line
```
<NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0
```

5. Install Remi's repository, Apache and PHP

```
sudo yum install httpd -y
```
```
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
```
```
sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
```
```
sudo dnf module reset php
```
```
sudo dnf module enable php:remi-7.4
```
```
sudo dnf install php php-opcache php-gd php-curl php-mysqlnd
```
```
sudo systemctl start php-fpm
```
```
sudo systemctl enable php-fpm
```
```
setsebool -P httpd_execmem 1
```
> Repeat steps 1-5 for another 2 Web Servers.

6. Verify NFS Mount and Apache Setup 

Verify that Apache files and directories are available on the Web Server in /var/www and also on the NFS server in /mnt/apps. If you see the same files - it means NFS is mounted correctly. You can try to create a new file
```
touch test.txt
```
from one server and check if the same file is accessible from other Web Servers.


7. Locate the log folder for Apache on the Web Server and mount it to NFS server's export for logs. Repeat step №4 to make sure the mount point will persist after reboot.

8. Fork the tooling source code from Darey.io Github Account to your Github account.


9. Deploy the tooling website's code to the Webserver. Ensure that the html folder from the repository is deployed to /var/www/html

> Note 1: Do not forget to open TCP port 80 on the Web Server.

> Note 2: If you encounter 403 Error - check permissions to your /var/www/html folder and also disable SELinux sudo setenforce 0 To make this change permanent - open following config file 

```
sudo vi /etc/sysconfig/selinux
```
 and set SELINUX=disabled, then restrt httpd.

10. Update the website's configuration to connect to the database (in /var/www/html/functions.php file). Apply `tooling-db.sql` script to your database using this command
 
 ```
  mysql -h <databse-private-ip> -u <db-username> -p <db-pasword> < tooling-db.sql
```

11. Create in MySQL a new admin user with `username: myuser` and `password: password`:
```
INSERT INTO 'users' ('id', 'username', 'password', 'email', 'user_type', 'status') VALUES -> (1, 'myuser', '5f4dcc3b5aa765d61d8327deb882cf99', 'user@mail.com', 'admin', '1');
```

12. Open the website in your browser http://<Web-Server-Public-IP-Address-or-Public-DNS-Name>/index.php and make sure you can login into the websute with myuser user.
