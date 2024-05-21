# Web Solution With WordPress

In this project we will be tasked to prepare storage infrastructure on `two` **Linux servers** and implement a basic web solution using `WordPress`. **WordPress** is a free and open-source content management system written in PHP and paired with MySQL or MariaDB as its backend Relational Database Management System (RDBMS).

## Project 6 consists of two parts:

1. Configure storage subsystem for Web and Database servers based on Linux OS. The focus of this part is to give us practical experience of working with disks, partitions and volumes in Linux.
2. Install WordPress and connect it to a remote MySQL database server. This part of the project will solidify our skills of deploying Web and DB tiers of Web solution.

### Three-tier Architecture
Generally, web, or mobile solutions are implemented based on what is called the Three-tier Architecture.

Three-tier Architecture is a client-server software architecture pattern that comprise of 3 separate layers.


1. **Presentation Layer (PL)**: This is the user interface such as the client server or browser on your laptop.
2. **Business Layer (BL)**: This is the backend program that implements business logic. Application or Webserver
3. **Data Access or Management Layer (DAL)**: This is the layer for computer data storage and data access. Database Server or File System Server such as File Transfer Protocol `FTP` server, orNetwork File System `NFS` Server

### Disk management in Linux

Disk management in Linux involves tasks such as partitioning, formatting, and mounting disks, as well as monitoring disk usage and health. Here's a brief overview of common tools and commands used for these tasks:

**Why Do We Need Disk Partitioning?**

Disk partitioning is the process of dividing a disk into separate regions, each of which can be managed independently.Some reasons why disk partitioning is necessary:

1. **Organization**: Separating system files, user files, and application files into different partitions can help keep the system organized.
2. **Performance**: Different partitions can be optimized for different types of access patterns, potentially improving performance.
3. **Security**: By isolating different types of data, you can reduce the risk of data corruption and improve security.
4. **Backup and Recovery**: Partitioning can make it easier to back up and restore specific parts of the system without affecting the entire disk.
5. **Multi-Boot Systems**: If you need to run multiple operating systems on the same machine, each OS can be installed on its own partition.

### How to Partition Disks in Linux

1. **`fdisk`**: A command-line utility to view and manipulate disk partitions. It's commonly used for MBR (Master Boot Record) disks.
   - Example: 
   ```sudo fdisk /dev/sda```
   
2. **`parted`**: A more advanced utility than `fdisk`, supporting both MBR and GPT (GUID Partition Table) disks.
   
   - Example: 
   
   ```sudo parted /dev/sda```
   
3. **`lsblk`**: Lists information about all available or the specified block devices.
   - Example: 
   ```lsblk```

### Formatting Partitions

1. **`mkfs`**: Used to create a filesystem on a partition. Different filesystems can be specified, such as ext4, xfs, etc.
   - Example: 
   ```sudo mkfs.ext4 /dev/sda1```
   
2. **`mkfs.xfs`**: Specifically formats a partition with the XFS filesystem.
   - Example: 
   ```sudo mkfs.xfs /dev/sda1```
   
3. **`mkfs.vfat`**: Used to create a FAT32 filesystem.
   - Example: 
   ```sudo mkfs.vfat /dev/sda1```

### Mounting and Unmounting Partitions

1. **`mount`**: Mounts a filesystem.
   - Example: 
   ```sudo mount /dev/sda1 /mnt```
   
2. **`umount`**: Unmounts a filesystem.
   - Example: 
   ```sudo umount /mnt```

3. **`/etc/fstab`**: Configuration file that contains information about how disk partitions, block devices, and remote filesystems should be mounted.
   - Example entry: 
   ```/dev/sda1 /mnt ext4 defaults 0 2```

### Checking Disk Usage

1. **`df`**: Reports filesystem disk space usage.
   - Example: 
   
   ```df -h```
   
2. **`du`**: Estimates file space usage.
   
   - Example: 
   
   ```du -sh /path/to/directory```

### Monitoring Disk Health

1. **`smartctl`**: A tool to control and monitor storage systems using the Self-Monitoring, Analysis and Reporting Technology (SMART) system.
   - Example: `sudo smartctl -a /dev/sda`

2. **`iostat`**: Reports CPU and I/O statistics.
   
   - Example: ```iostat -x```

## Additional Tools

1. **`gnome-disks`**: A graphical utility for managing disks and media.
2. **`gparted`**: A graphical partition editor for creating, reorganizing, and deleting disk partitions.

### Our 3-Tier Setup
1. A Laptop or PC to serve as a client
2. An EC2 Linux Server as a web server (This is where you will install WordPress)
3. An EC2 Linux server as a database (DB) server

> Use RedHat OS for this projects we will use very popular distribution called 'RedHat' (it also has a fully compatible derivative - CentOS)

> **Note**: for Ubuntu server, when connecting to it via SSH/Putty or any other tool, we used ubuntu user, but for RedHat you will need to use ec2-user user. Connection string will look like ec2-user@<Public-IP>

**Let us get started by creating web server and database server using redhat Os**

1. Log to aws account console and create EC2 instance of t2.micro type with RedHat Server launch in the default region us-east-1. name instance **web server**

2. Application and OS Images select RedHat free tire eligable version

3. Create new key pair or select existing key

4. Network setting create new security group or use existing security group

5. Configure Storage and launch the instance
6. View Instance
7. Instance Details for web 
8. Configure security group with the following inbound rules:

- Allow traffic on port 22 (SSH) with source from any IP address

### For DB Server follow same steps and our final instance detail looks like this

