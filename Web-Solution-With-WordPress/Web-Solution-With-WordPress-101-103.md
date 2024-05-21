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
     
```
sudo fdisk /dev/sda
```
   
2. **`parted`**: A more advanced utility than `fdisk`, supporting both MBR and GPT (GUID Partition Table) disks.
   
- Example: 
   
```
sudo parted /dev/sda
```
   
3. **`lsblk`**: Lists information about all available or the specified block devices.
- Example: 
```
lsblk
```

### Formatting Partitions

1. **`mkfs`**: Used to create a filesystem on a partition. Different filesystems can be specified, such as ext4, xfs, etc.
- Example: 
```
sudo mkfs.ext4 /dev/sda1
```
   
2. **`mkfs.xfs`**: Specifically formats a partition with the XFS filesystem.
- Example: 
```
sudo mkfs.xfs /dev/sda1
```
   
3. **`mkfs.vfat`**: Used to create a FAT32 filesystem.
- Example: 
```
sudo mkfs.vfat /dev/sda1
```

### Mounting and Unmounting Partitions

1. **`mount`**: Mounts a filesystem.
- Example: 
```
sudo mount /dev/sda1 /mnt
```
   
2. **`umount`**: Unmounts a filesystem.
- Example: 
```
sudo umount /mnt
```

3. **`/etc/fstab`**: Configuration file that contains information about how disk partitions, block devices, and remote filesystems should be mounted.
- Example entry: 
```
/dev/sda1 /mnt ext4 defaults 0 2
```

### Checking Disk Usage

1. **`df`**: Reports filesystem disk space usage.
- Example: 
   
```
df -h
```
   
2. **`du`**: Estimates file space usage.
   
- Example: 
   
```
du -sh /path/to/directory
```

### Monitoring Disk Health

1. **`smartctl`**: A tool to control and monitor storage systems using the Self-Monitoring, Analysis and Reporting Technology (SMART) system.
- Example:
  ```
  sudo smartctl -a /dev/sda
  ```

2. **`iostat`**: Reports CPU and I/O statistics.
   
- Example:
```
iostat -x
```

## Additional Tools

1. **`gnome-disks`**: A graphical utility for managing disks and media.
2. **`gparted`**: A graphical partition editor for creating, reorganizing, and deleting disk partitions.

### Our 3-Tier Setup
1. A Laptop or PC to serve as a client
2. An EC2 Linux Server as a web server (This is where you will install WordPress)
3. An EC2 Linux server as a database (DB) server

> Use RedHat OS for this projects we will use very popular distribution called 'RedHat' (it also has a fully compatible derivative - CentOS)


**Let us get started by creating web server and database server using redhat Os**

1. Log to aws account console and create EC2 instance of t2.micro type with RedHat Server launch in the default region us-east-1. name instance **web server**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/99319c00-fd57-434d-b584-756bd396fbae)

2. Application and OS Images select RedHat free tire eligable version

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/47800606-58c2-4233-b7d4-19235461335e)

3. Create new key pair or select existing key

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/fb1410f5-33cf-4f1f-b6b8-bb43f1636cd6)

4. Network setting create new security group or use existing security group

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/64844d2b-56ac-4667-8f28-20e1823d95f5)

5. Configure Storage and launch the instance

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/ae30ed2f-7b43-4679-94ed-6de5eba8a959)

   
6. View Instance

   ![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/e05fc97c-ecd1-4a49-baa8-6e7e24056585)

7. Instance Details for web

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/371c3b99-edd5-4d80-a5be-25150d0d9ac5)

   
8. Configure security group with the following inbound rules:

- Allow traffic on port 22 (SSH) with source from any IP address. This is opened by default.
- Allow traffic on port 80 (HTTP) with source from anywhere on the internet.
- Allow traffic on port 443 (HTTPS) with source from anywhere on the internet.

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/0b3dd6b9-527e-4db8-8fd6-509a4556abba)

### For DB Server follow same steps and our final instance detail looks like this
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/e63bfe7c-9bcf-4e0c-bb9f-212194726d05)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/cff44690-f4f9-44df-97a4-6b6fdf622f7b)


### Now Two Servers UP are we can go to do our 3-Tire  server architecture

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d1bda6a5-a142-4ba6-bd7b-87ab9155971a)

> **Note**: for Ubuntu server, when connecting to it via SSH/Putty or any other tool, we used ubuntu user, but for RedHat you will need to use ec2-user user. Connection string will look like ec2-user@<Public-IP>

**Let us see how to connect webserver**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/2730dd36-2176-4736-9587-2369c7c7a5b2)


![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/15bb6adc-d1e2-4e75-bb55-c2ba721a1b65)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/9ff07f1f-2fa8-47f9-8c83-b55ca964249c)

### Step 1 - Prepare a Web Server
Launch an EC2 instance that will serve as Web Server. Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB.

1. Add EBS Volume to an EC2 instance

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/47681df8-7b7c-410e-987a-fdb72e60844f)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d7516732-5aa5-4ba6-b57e-932c436a3e72)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/4192ee2d-fa09-465c-9aa8-4c63cb26a575)


**Created volumes in same availablity zone**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/c7739cff-53cd-4dfd-957d-c1d0e2204c34)


2. Attach all three volumes one by one to our Web Server EC2 instance

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/4752ae95-89ac-4829-a8ea-fa7827cb7acc)

**After three voumes attached to the webserver instance**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/3c191531-c56e-4540-a0ea-0a4f405f9ec5)

3. Open up the Linux terminal to begin configuration

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/88502c8d-ac9b-4d80-99ee-06a372432baf)


4. Use `lsblk` command to inspect what block devices are attached to the server.
```
lsblk
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/5d815dde-fc74-4c2a-a7c1-0cca7e8d724b)

5. Use df -h command to see all mounts and free space 
```
df -h
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/1108fc69-c1c1-403e-abce-d0db003e51e8)


6. Use `gdisk` utility to create a single partition on each of the 3 disks

```
sudo gdisk /dev/xvdb
```
**List Existing Partitions: To see the current partitions, use the p command:**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/f77f78ad-6018-458b-94a2-c0770f89cc72)

**Create a New Partition: To add a new partition, enter n: then Press Enter to accept default value**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/9bb8e08d-1386-4438-9afd-6850c2e32189)

**Write Changes: Once you've created the desired partitions, write the changes to the disk with w:**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/77bbc089-b4f5-4f54-94e5-5a8362eb9e43)


**Yes to proceed and complete** 
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/9eb8faf4-4f71-497a-8480-81d26c513c08)

**we follow the same steps for remaining two**

7. Use lsblk utility to view the newly configured partition on each of the 3 disks.
```
lsblk
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/3003793e-4891-45dc-9412-0ad016b6c7e6)

8. Install `lvm2` package 
```
sudo yum install lvm2
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d88545f4-321a-4f51-944b-5caa7e20982f)


9.  Check for available partitions.
```
sudo lvmdiskscan 
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/cf26fad7-5683-4c3c-9f26-e5b3693c7838)

> Note:  In Ubuntu we used apt command to install packages, in **RedHat/CentOS** a different package manager is used, so we shall use yum command instead.

10. Use `pvcreate` utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM
```
sudo pvcreate /dev/xvdb1
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/e364a48b-2aff-47fe-a7a5-fd8c580c9e38)

```
sudo pvcreate /dev/xvdc1
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/1b8518c3-8a09-411a-9cd0-4e06e73931df)

```
sudo pvcreate /dev/xvdd1
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d743a184-6c75-4229-ba3d-7890b2e3ad6a)

11. Verify that your Physical volume has been created successfully 

```
sudo pvs
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/9a550cbc-7572-48cd-9722-6301c9f12644)

12. Use `vgcreate` utility to add all 3 PVs to a volume group (VG) Name the VG `webdata-vg`
```
sudo vgcreate webdata-vg /dev/xvdb1 /dev/xvdc1 /dev/xvdd1
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/e14f1d1d-0674-4d3a-a29c-618c1296e3a9)

13. Verify that your VG has been created successfully
```
 sudo vgs
``` 
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/ccfb186a-3c7a-4902-8cf7-5a7afaec46ad)

14.  Use `lvcreate` utility to create 2 logical volumes. `apps-lv` (**Use half of the PV size**), and `logs-lv` Use the remaining space of the PV size.

>  NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.
```
sudo lvcreate -n apps-lv -L 14G webdata-vg
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a87d734f-fb9d-4542-8cee-8f21ccc0b02c)


```
sudo lvcreate -n logs-lv -L 14G webdata-vg
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/707742d5-a8fa-4107-ac58-789be223aa82)

15. Verify that our Logical Volume has been created successfully
```
sudo lvs
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/622c094c-74ae-40fe-9ee8-c9fae70a78b0)

16.  Verify the entire setup
#view complete setup - VG , PV, and LV
```
sudo vgdisplay -v
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/6f4aab54-43f0-4cde-bc02-da88c8915ac0)

```
sudo lsblk
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a7f48bd6-f109-4df0-a1e4-a09cb84b513f)

**Use mkfs.ext4 to format the logical volumes with ext4 filesystem**
```
sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
```

```
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
```
