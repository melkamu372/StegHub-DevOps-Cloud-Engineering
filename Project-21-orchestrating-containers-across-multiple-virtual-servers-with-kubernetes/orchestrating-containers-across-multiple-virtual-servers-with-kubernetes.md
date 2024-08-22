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
```
mkdir k8s-cluster-from-ground-up
```
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
![image](https://github.com/user-attachments/assets/d28099ca-c78e-44c7-b14f-9ec9dee5ddeb)

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
![image](https://github.com/user-attachments/assets/3b61ee7a-be5d-4ff4-8321-0d5ca0474e15)

![image](https://github.com/user-attachments/assets/f027bbe7-bf24-4865-a7dd-fc34fa67f785)

**AWS Region**

6. Set the required region

```
AWS_REGION=us-east-1
```

**Subnet**

7. Create the Subnet:

```
SUBNET_ID=$(aws ec2 create-subnet \
  --vpc-id ${VPC_ID} \
  --cidr-block 172.31.0.0/24 \
  --output text --query 'Subnet.SubnetId')
```
![image](https://github.com/user-attachments/assets/d80773b5-dba5-4276-8e19-e9ac565050c0)

```
aws ec2 create-tags \
  --resources ${SUBNET_ID} \
  --tags Key=Name,Value=${NAME}
```
![image](https://github.com/user-attachments/assets/affbd0cd-f005-4096-9657-02afc64aaacd)

**Internet Gateway – IGW**

8. Create the Internet Gateway and attach it to the VPC:

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
![image](https://github.com/user-attachments/assets/e85d43ea-aafa-4f7a-9b02-45859ce70837)

**Route tables**

9. Create route tables, associate the route table to subnet, and create a route to allow external traffic to the Internet through 
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
![image](https://github.com/user-attachments/assets/580cd8c3-9e95-4332-80c0-19f2d504bac4)



**Security Groups**

10. Configure security groups

```
# Create the security group and store its ID in a variable
SECURITY_GROUP_ID=$(aws ec2 create-security-group \
  --group-name ${NAME} \
  --description "Kubernetes cluster security group" \
  --vpc-id ${VPC_ID} \
  --output text --query 'GroupId')

# Create the NAME tag for the security group
aws ec2 create-tags \
  --resources ${SECURITY_GROUP_ID} \
  --tags Key=Name,Value=${NAME}

# Create Inbound traffic for all communication within the subnet to connect on ports used by the master node(s)
aws ec2 authorize-security-group-ingress \
    --group-id ${SECURITY_GROUP_ID} \
    --ip-permissions IpProtocol=tcp,FromPort=2379,ToPort=2380,IpRanges='[{CidrIp=172.31.0.0/24}]'

# # Create Inbound traffic for all communication within the subnet to connect on ports used by the worker nodes
aws ec2 authorize-security-group-ingress \
    --group-id ${SECURITY_GROUP_ID} \
    --ip-permissions IpProtocol=tcp,FromPort=30000,ToPort=32767,IpRanges='[{CidrIp=172.31.0.0/24}]'

# Create inbound traffic to allow connections to the Kubernetes API Server listening on port 6443
aws ec2 authorize-security-group-ingress \
  --group-id ${SECURITY_GROUP_ID} \
  --protocol tcp \
  --port 6443 \
  --cidr 0.0.0.0/0

# Create Inbound traffic for SSH from anywhere (Do not do this in production. Limit access ONLY to IPs or CIDR that MUST connect)
aws ec2 authorize-security-group-ingress \
  --group-id ${SECURITY_GROUP_ID} \
  --protocol tcp \
  --port 22 \
  --cidr 0.0.0.0/0

# Create ICMP ingress for all types
aws ec2 authorize-security-group-ingress \
  --group-id ${SECURITY_GROUP_ID} \
  --protocol icmp \
  --port -1 \
  --cidr 0.0.0.0/0
```


**Network Load Balancer**

11. Create a network Load balancer

```
LOAD_BALANCER_ARN=$(aws elbv2 create-load-balancer \
--name ${NAME} \
--subnets ${SUBNET_ID} \
--scheme internet-facing \
--type network \
--output text --query 'LoadBalancers[].LoadBalancerArn')
```

![image](https://github.com/user-attachments/assets/0c1426d0-abc0-4d50-b504-872485e73b21)


**Tagret Group**

12. Create a target group: (For now it will be unhealthy because there are no real targets yet.)

```
TARGET_GROUP_ARN=$(aws elbv2 create-target-group \
  --name ${NAME} \
  --protocol TCP \
  --port 6443 \
  --vpc-id ${VPC_ID} \
  --target-type ip \
  --output text --query 'TargetGroups[].TargetGroupArn')
```

![image](https://github.com/user-attachments/assets/a1bab2e7-d436-41e5-9db0-a9266e8700ff)

13. Register targets: (Just like above, no real targets. You will just put the IP addresses so that, when the nodes become available,
 they will be used as targets.)
 
```
aws elbv2 register-targets \
  --target-group-arn ${TARGET_GROUP_ARN} \
  --targets Id=172.31.0.1{0,1,2}
```

![image](https://github.com/user-attachments/assets/71355f37-aba4-49bf-aa75-f86b4d33d003)


14. Create a listener to listen for requests and forward to the target nodes on TCP port 6443

```
aws elbv2 create-listener \
--load-balancer-arn ${LOAD_BALANCER_ARN} \
--protocol TCP \
--port 6443 \
--default-actions Type=forward,TargetGroupArn=${TARGET_GROUP_ARN} \
--output text --query 'Listeners[].ListenerArn'
```

![image](https://github.com/user-attachments/assets/cf19a03c-f129-4a23-b7a3-d8420d801cf4)


**K8s Public Address**

15. Get the Kubernetes Public address

```
KUBERNETES_PUBLIC_ADDRESS=$(aws elbv2 describe-load-balancers \
--load-balancer-arns ${LOAD_BALANCER_ARN} \
--output text --query 'LoadBalancers[].DNSName')
```
![image](https://github.com/user-attachments/assets/c94d7676-463d-40f8-9887-58cef220e70d)


