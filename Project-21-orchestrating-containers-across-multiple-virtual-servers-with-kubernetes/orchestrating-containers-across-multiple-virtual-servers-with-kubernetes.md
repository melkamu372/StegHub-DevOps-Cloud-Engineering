# Orchestrating Containers across multiple virtual servers with kubernetes

## K8s From-Ground-Up

Let us begin building out Kubernetes cluster from the ground

**DISCLAIMER:** The following setup of Kubernetes should be used for learning purpose only, and not to be considered for production.
This is because setting up a K8s cluster for production use has a lot more moving parts, especially when it comes to planning the 
nodes, and securing the cluster. The purpose of **"K8s From-Ground-Up"** is to get you much closer to the different components as shown in the architecture diagram and relate with what you have been learning about Kubernetes.


**Tools to be used and expected result of the Project 20**

- VM: AWS EC2
- OS: Ubuntu 20.04 lts+
- Docker Engine
- kubectl console utility
- cfssl and cfssljson utilities
- Kubernetes cluster


**You will create 3 EC2 Instances, and in the end, we will have the following parts of the cluster properly configured:**

- One Kubernetes Master
- Two Kubernetes Worker Nodes
- Configured SSL/TLS certificates for Kubernetes components to communicate securely
- Configured Node Network
- Configured Pod Network


### STEP 0-INSTALL CLIENT TOOLS BEFORE BOOTSTRAPPING THE CLUSTER.

First, you will need some client tools installed and configurations made on your client workstation:


- [awscli](https://aws.amazon.com/cli/) – is a unified tool to manage your AWS services
- [kubectl](https://kubernetes.io/docs/reference/kubectl/) – this command line utility will be your main control tool to manage your 
K8s cluster. You will use this tool so many times, so you will be able to type ‘kubetcl’ on your keyboard with a speed of light. 
You can always make a shortcut (alias) to just one character ‘k’. Also, add this extremely useful official kubectl Cheat Sheet to 
your bookmarks, it has examples of the most used ‘kubectl’ commands.
- [cfssl](https://blog.cloudflare.com/introducing-cfssl/) – an open source toolkit for everything TLS/SSL from [Cloudflare](https://www.cloudflare.com/)
- [cfssljson](https://github.com/cloudflare/cfssl) – a program, which takes the JSON output from the cfssl and writes certificates, 
keys, [CSRs](https://en.wikipedia.org/wiki/Certificate_signing_request), and bundles to disk.


###### Install and configure AWS CLI

Configure AWS CLI to access all AWS services used, for this you need to have a user with programmatic access keys configured in
AWS Identity and Access Management (IAM):

Navigate to the IAM Service
![image](https://github.com/user-attachments/assets/612cc100-fbe9-4eea-b11a-1e92dc56c220)

Create a New IAM User
![image](https://github.com/user-attachments/assets/45497e2d-c860-4c60-b66c-5b7be244483d)

Set Permissions for the User
![image](https://github.com/user-attachments/assets/c5395973-af6d-4533-9e54-5065fefbbc8b)


Generate access keys and store them in a safe place.

On your local workstation download and install the latest version of [AWS CLI](https://aws.amazon.com/cli/)

[To configure your AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html) – run your shell 
(or cmd if using Windows) and run:


```
aws configure 
AWS Access Key ID [None]: 
AWS Secret Access Key [None]: 
Default region name [None]: us-east-1
Default output format [None]: json
```
![image](https://github.com/user-attachments/assets/28027a77-f06a-4d28-b5b9-134a57de03a4)


Test your AWS CLI by running:

```
aws ec2 describe-vpcs
```
![image](https://github.com/user-attachments/assets/501a8a68-0255-4a5f-b7cd-83ea9bcbc26d)


and check if you can see VPC details.

###### Install kubectl

Kubernetes cluster has a Web API that can receive HTTP/HTTPS requests, but it is quite cumbersome to curl an API each and every time
you need to send some command, so kubectl command tool was developed to ease a K8s administrator’s life.

With this tool you can easily interact with Kubernetes to deploy applications, inspect and manage cluster resources, view logs and
perform many more administrative operations.


## Installing kubectl

**Mac OS X**

Download the binary
```
curl -o kubectl https://storage.googleapis.com/kubernetes-release/release/v1.21.0/bin/darwin/amd64/kubectl
```
Make it executable
```
chmod +x kubectl
```
Move to the Bin directory
```
sudo mv kubectl /usr/local/bin/
```
**Linux Or Windows using Gitbash or similar tool**

Download the binary
```
wget https://storage.googleapis.com/kubernetes-release/release/v1.21.0/bin/linux/amd64/kubectl
```
Make it executable
```
chmod +x kubectl
```
Move to the Bin directory
```
sudo mv kubectl /usr/local/bin/
```
Verify that kubectl version 1.21.0 or higher is installed:
```
kubectl version --client
```
**Output:**

```
Client Version: version.Info{Major:"1", Minor:"20+", GitVersion:"v1.20.4-dirty", GitCommit:"e87da0bd6e03ec3fea7933c4b5263d151aafd07c", GitTreeState:"dirty", 
BuildDate:"2021-03-15T10:03:32Z", GoVersion:"go1.16.2", Compiler:"gc", Platform:"darwin/amd64"}
```
![image](https://github.com/user-attachments/assets/5dfdaea0-39e5-4f3c-8380-a36ba0ee03a3)


**Install CFSSL and CFSSLJSON**

cfssl is an open source tool by Cloudflare used to setup a `Public Key Infrastructure (PKI Infrastructure)` for generating, signing and bundling TLS certificates. In previous projects you have experienced the use of Letsencrypt for the similar use case. Here, cfssl will be configured as a Certificate Authority which will issue the certificates required to spin up a Kubernetes cluster.

Download, install and verify successful installation of cfssl and cfssljson:

**Mac OS X**
```
curl -o cfssl https://storage.googleapis.com/kubernetes-the-hard-way/cfssl/1.4.1/darwin/cfssl
curl -o cfssljson https://storage.googleapis.com/kubernetes-the-hard-way/cfssl/1.4.1/darwin/cfssljson
chmod +x cfssl cfssljson
sudo mv cfssl cfssljson /usr/local/bin/

```
If you have issues using the binaries directly, you should consider using the package manager Homebrew and that might be a better option:
```
brew install cfssl
```
Verify that cfssl version 1.4.1 or higher is installed:

Output: cfssl version

```
Version: 1.4.1
Runtime: go1.12.12
```
cfssljson --version
```
Version: 1.4.1
Runtime: go1.12.12
```

**Linux Or Windows using Gitbash or similar tool**
```
wget -q --show-progress --https-only --timestamping \
  https://storage.googleapis.com/kubernetes-the-hard-way/cfssl/1.4.1/linux/cfssl \
  https://storage.googleapis.com/kubernetes-the-hard-way/cfssl/1.4.1/linux/cfssljson
```

```
chmod +x cfssl cfssljson
```

```
sudo mv cfssl cfssljson /usr/local/bin/
```
![image](https://github.com/user-attachments/assets/8281d9c1-c8ab-48c0-943c-ca8c357dd5f2)
https://petermd.github.io/kubernetes-the-hard-way/02-client-tools.html


## AWS CLOUD RESOURCES FOR KUBERNETES CLUSTER

As we already know, we need some machines to run the control plane and the worker nodes. In this section, you will provision EC2 
Instances required to run your K8s cluster. You can use Terraform for this. But it is highly recommended to start out first with 
manual provisioning using awscli and have thorough knowledge about the whole setup. After that, you can destroy the entire project 
and start all over again using Terraform. This manual approach will solidify your skills and give you the opportunity to face more 
challenges.


### Step 1 – Configure Network Infrastructure

**Virtual Private Cloud –VPC**

1. Create a directory named k8s-cluster-from-ground-up
2. Create a VPC and store the ID as a variable:

```
VPC_ID=$(aws ec2 create-vpc \
--cidr-block 172.31.0.0/16 \
--output text --query 'Vpc.VpcId'
)

```


3. Tag the VPC so that it is named:

```
NAME=k8s-cluster-from-ground-up

aws ec2 create-tags \
  --resources ${VPC_ID} \
  --tags Key=Name,Value=${NAME} 
```


**Domain Name System – DNS**

4. Enable DNS support for your VPC:

```
aws ec2 modify-vpc-attribute \
--vpc-id ${VPC_ID} \
--enable-dns-support '{"Value": true}'
```

5. Enable DNS support for hostnames:

```
aws ec2 modify-vpc-attribute \
--vpc-id ${VPC_ID} \
--enable-dns-hostnames '{"Value": true}'
```

![7006](https://user-images.githubusercontent.com/85270361/210191966-626e2e0b-085b-4e64-944e-5d9b789a87d6.PNG)


**AWS Region**

6. Set the required region

```
AWS_REGION=eu-central-1
```


**Dynamic Host Configuration Protocol – DHCP**

7. Configure DHCP Options Set:

[Dynamic Host Configuration Protocol (DHCP)](https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol)is a network 
management protocol used on Internet Protocol networks for automatically assigning IP addresses and other communication parameters
to devices connected to the network using a client–server architecture.

**AWS** automatically creates and associates a DHCP option set for your **Amazon VPC** upon creation and sets two options: 
domain-name-servers (defaults to AmazonProvidedDNS) and **domain-name** (defaults to the domain name for your set region). 
**AmazonProvidedDNS** is an Amazon Domain Name System (DNS) server, and this option enables DNS for instances to communicate using
DNS names.

By default EC2 instances have fully qualified names like ip-172-50-197-106.eu-central-1.compute.internal. 
But you can set your own configuration using an example below.


```
DHCP_OPTION_SET_ID=$(aws ec2 create-dhcp-options \
  --dhcp-configuration \
    "Key=domain-name,Values=$AWS_REGION.compute.internal" \
    "Key=domain-name-servers,Values=AmazonProvidedDNS" \
  --output text --query 'DhcpOptions.DhcpOptionsId')
```


8. Tag the DHCP Option set:

```
aws ec2 create-tags \
  --resources ${DHCP_OPTION_SET_ID} \
  --tags Key=Name,Value=${NAME}
```


![7007](https://user-images.githubusercontent.com/85270361/210192103-d973f4af-3a3f-4ad9-8536-d087c8529855.PNG)


9. Associate the DHCP Option set with the VPC:

```
aws ec2 associate-dhcp-options \
  --dhcp-options-id ${DHCP_OPTION_SET_ID} \
  --vpc-id ${VPC_ID}
```

![7008](https://user-images.githubusercontent.com/85270361/210192123-64f6e029-84d5-4ae1-b550-e244f54328cb.PNG)


**Subnet**

10. Create the Subnet:

```
SUBNET_ID=$(aws ec2 create-subnet \
  --vpc-id ${VPC_ID} \
  --cidr-block 172.31.0.0/24 \
  --output text --query 'Subnet.SubnetId')
```


```
aws ec2 create-tags \
  --resources ${SUBNET_ID} \
  --tags Key=Name,Value=${NAME}
```

**Internet Gateway – IGW**

11. Create the Internet Gateway and attach it to the VPC:

```
INTERNET_GATEWAY_ID=$(aws ec2 create-internet-gateway \
  --output text --query 'InternetGateway.InternetGatewayId')
aws ec2 create-tags \
  --resources ${INTERNET_GATEWAY_ID} \
  --tags Key=Name,Value=${NAME}
```


```
aws ec2 attach-internet-gateway \
  --internet-gateway-id ${INTERNET_GATEWAY_ID} \
  --vpc-id ${VPC_ID}
```



**Route tables**

12. Create route tables, associate the route table to subnet, and create a route to allow external traffic to the Internet through 
the Internet Gateway:

```
ROUTE_TABLE_ID=$(aws ec2 create-route-table \
  --vpc-id ${VPC_ID} \
  --output text --query 'RouteTable.RouteTableId')
aws ec2 create-tags \
  --resources ${ROUTE_TABLE_ID} \
  --tags Key=Name,Value=${NAME}
aws ec2 associate-route-table \
  --route-table-id ${ROUTE_TABLE_ID} \
  --subnet-id ${SUBNET_ID}
aws ec2 create-route \
  --route-table-id ${ROUTE_TABLE_ID} \
  --destination-cidr-block 0.0.0.0/0 \
  --gateway-id ${INTERNET_GATEWAY_ID}
 ```
 
 
 
**OUTPUT:**
 
 ```
 {
    "AssociationId": "rtbassoc-07a8877e92504def7",
    "AssociationState": {
        "State": "associated"
    }
}
{
    "Return": true
}
```

