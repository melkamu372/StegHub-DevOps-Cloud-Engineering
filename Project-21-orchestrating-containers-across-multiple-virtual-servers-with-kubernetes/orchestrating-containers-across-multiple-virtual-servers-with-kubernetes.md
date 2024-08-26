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
  https://pkg.cfssl.org/R1.2/cfssl_linux-amd64 \
  https://pkg.cfssl.org/R1.2/cfssljson_linux-amd64
```
```
chmod +x cfssl_linux-amd64 cfssljson_linux-amd64
```
```
sudo mv cfssl_linux-amd64 /usr/bin/cfssl
```

```
sudo mv cfssljson_linux-amd64 /usr/bin/cfssljson
```
```
cfssl version
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

### Step -2 CREATE COMPUTE RESOURCES

**AMI**

1. Get an image to create EC2 instances. Before getting the Image you need to  install JQ. Then copy and paste these commands to your terminal:
```
sudo snap install jq
```

```
IMAGE_ID=$(aws ec2 describe-images --owners 099720109477 \
  --filters \
  'Name=root-device-type,Values=ebs' \
  'Name=architecture,Values=x86_64' \
  'Name=name,Values=ubuntu/images/hvm-ssd-gp3/ubuntu-noble-24.04-amd64-server-*' \
  | jq -r '.Images|sort_by(.Name)[-1]|.ImageId')
```

**SSH key-pair**

2. Create SSH Key-Pair

```
mkdir -p ssh

aws ec2 create-key-pair \
  --key-name ${NAME} \
  --output text --query 'KeyMaterial' \
  > ssh/${NAME}.id_rsa
chmod 600 ssh/${NAME}.id_rsa
```

**EC2 Instances for Controle Plane (Master Nodes)**

3. Create 3 Master nodes: Note – Using t2.micro instead of t2.small as t2.micro is covered by AWS free tier

```
for i in 0 1 2; do
  instance_id=$(aws ec2 run-instances \
    --associate-public-ip-address \
    --image-id ${IMAGE_ID} \
    --count 1 \
    --key-name ${NAME} \
    --security-group-ids ${SECURITY_GROUP_ID} \
    --instance-type t2.micro \
    --private-ip-address 172.31.0.1${i} \
    --user-data "name=master-${i}" \
    --subnet-id ${SUBNET_ID} \
    --output text --query 'Instances[].InstanceId')
  aws ec2 modify-instance-attribute \
    --instance-id ${instance_id} \
    --no-source-dest-check
  aws ec2 create-tags \
    --resources ${instance_id} \
    --tags "Key=Name,Value=${NAME}-master-${i}"
done
```


**EC2 Instances for Worker Nodes**

1. Create 3 worker nodes:

```
for i in 0 1 2; do
  instance_id=$(aws ec2 run-instances \
    --associate-public-ip-address \
    --image-id ${IMAGE_ID} \
    --count 1 \
    --key-name ${NAME} \
    --security-group-ids ${SECURITY_GROUP_ID} \
    --instance-type t2.micro \
    --private-ip-address 172.31.0.2${i} \
    --user-data "name=worker-${i}|pod-cidr=172.20.${i}.0/24" \
    --subnet-id ${SUBNET_ID} \
    --output text --query 'Instances[].InstanceId')
  aws ec2 modify-instance-attribute \
    --instance-id ${instance_id} \
    --no-source-dest-check
  aws ec2 create-tags \
    --resources ${instance_id} \
    --tags "Key=Name,Value=${NAME}-worker-${i}"
done
```
![image](https://github.com/user-attachments/assets/e4c9281c-c849-4ad2-95e9-3198ca788b79)


### STEP 3 PREPARE THE SELF-SIGNED CERTIFICATE AUTHORITY AND GENERATE TLS CERTIFICATES

The following components running on the Master node will require TLS certificates.

- kube-controller-manager
- kube-scheduler
- etcd
- kube-apiserver

The following components running on the Worker nodes will require TLS certificates.

- kubelet
- kube-proxy

Therefore, you will provision a PKI Infrastructure using cfssl which will have a Certificate Authority. The CA will then generate
certificates for all the individual components.

**Self-Signed Root Certificate Authority (CA)**

Here, you will provision a CA that will be used to sign additional TLS certificates.

Create a directory and cd into it:

```
mkdir ca-authority && cd ca-authority
```

Generate the CA configuration file, Root Certificate, and Private key:

```
{

cat > ca-config.json <<EOF
{
  "signing": {
    "default": {
      "expiry": "8760h"
    },
    "profiles": {
      "kubernetes": {
        "usages": ["signing", "key encipherment", "server auth", "client auth"],
        "expiry": "8760h"
      }
    }
  }
}
EOF

cat > ca-csr.json <<EOF
{
  "CN": "Kubernetes",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "UK",
      "L": "England",
      "O": "Kubernetes",
      "OU": "TOTAL.COM",
      "ST": "London"
    }
  ]
}
EOF

cfssl gencert -initca ca-csr.json | cfssljson -bare ca

}
```

The file defines the following:


```
CN – Common name for the authority

algo – the algorithm used for the certificates

size – algorithm size in bits

C – Country

L – Locality (city)

ST – State or province

O – Organization

OU – Organizational Unit
```

![image](https://github.com/user-attachments/assets/094f5b10-e4fa-42fc-9ed6-896b575b53ae)

List the directory to see the created files

```
ls -ltr

-rw-r--r--  1 ann  ann   232 16 May 20:18 ca-config.json
-rw-r--r--  1 ann  ann   207 16 May 20:18 ca-csr.json
-rw-r--r--  1 ann  ann  1306 16 May 20:18 ca.pem
-rw-------  1 ann  ann  1679 16 May 20:18 ca-key.pem
-rw-r--r--  1 ann  ann  1001 16 May 20:18 ca.csr
```
![image](https://github.com/user-attachments/assets/aa9a3e04-da63-4c72-9131-7b59ce9c0a4f)

**The 3 important files here are:**

- ca.pem – The Root Certificate
- ca-key.pem – The Private Key
- ca.csr – The Certificate Signing Request

**Generating TLS Certificates For Client and Server**

You will need to provision Client/Server certificates for all the components. It is a MUST to have encrypted communication within 
the cluster. Therefore, the server here are the master nodes running the api-server component. While the client is every other 
component that needs to communicate with the api-server.

Now we have a certificate for the Root CA, we can then begin to request more certificates which the different Kubernetes components,
i.e. clients and server, will use to have encrypted communication.

Remember, the clients here refer to every other component that will communicate with the api-server. These are:

- kube-controller-manager
- kube-scheduler
- etcd
- kubelet
- kube-proxy
-  Admin User

**Let us begin with the Kubernetes API-Server Certificate and Private Key**


The certificate for the Api-server must have IP addresses, DNS names, and a Load Balancer address included. Otherwise, you will have
a lot of difficulties connecting to the api-server.

1. Generate the Certificate Signing Request (CSR), Private Key and the Certificate for the Kubernetes Master Nodes.

```
{
cat > master-kubernetes-csr.json <<EOF
{
  "CN": "kubernetes",
   "hosts": [
   "127.0.0.1",
   "172.31.0.10",
   "172.31.0.11",
   "172.31.0.12",
   "ip-172-31-0-10",
   "ip-172-31-0-11",
   "ip-172-31-0-12",
   "ip-172-31-0-10.${AWS_REGION}.compute.internal",
   "ip-172-31-0-11.${AWS_REGION}.compute.internal",
   "ip-172-31-0-12.${AWS_REGION}.compute.internal",
   "${KUBERNETES_PUBLIC_ADDRESS}",
   "kubernetes",
   "kubernetes.default",
   "kubernetes.default.svc",
   "kubernetes.default.svc.cluster",
   "kubernetes.default.svc.cluster.local"
  ],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "UK",
      "L": "England",
      "O": "Kubernetes",
      "OU": "TOTAL.COM",
      "ST": "London"
    }
  ]
}
EOF

cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -profile=kubernetes \
  master-kubernetes-csr.json | cfssljson -bare master-kubernetes
}
```
![image](https://github.com/user-attachments/assets/26c1935d-6f35-48a0-9bfb-03a8cca1b05c)

**Creating the other certificates: for the following Kubernetes components:**

- Scheduler Client Certificate
-  Proxy Client Certificate
- Controller Manager Client Certificate
- Kubelet Client Certificates
- K8s admin user Client Certificate

2. kube-scheduler **Client Certificate and Private Key**
```
{

cat > kube-scheduler-csr.json <<EOF
{
  "CN": "system:kube-scheduler",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "UK",
      "L": "England",
      "O": "system:kube-scheduler",
      "OU": "TOTAL.COM",
      "ST": "London"
    }
  ]
}
EOF

cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -profile=kubernetes \
  kube-scheduler-csr.json | cfssljson -bare kube-scheduler

}
```


*If you see any warning message, it is safe to ignore it.*

3. kube-proxy **Client Certificate and Private Key**


```
{

cat > kube-proxy-csr.json <<EOF
{
  "CN": "system:kube-proxy",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "UK",
      "L": "England",
      "O": "system:node-proxier",
      "OU": "TOTAL.COM",
      "ST": "London"
    }
  ]
}
EOF

cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -profile=kubernetes \
  kube-proxy-csr.json | cfssljson -bare kube-proxy

}
```


4. kube-controller-manager **Client Certificate and Private Key**


```
{
cat > kube-controller-manager-csr.json <<EOF
{
  "CN": "system:kube-controller-manager",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "UK",
      "L": "England",
      "O": "system:kube-controller-manager",
      "OU": "TOTAL.COM",
      "ST": "London"
    }
  ]
}
EOF

cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -profile=kubernetes \
  kube-controller-manager-csr.json | cfssljson -bare kube-controller-manager

}
```

5. kubelet Client **Certificate and Private Key**
Similar to how you configured the api-server's certificate, Kubernetes requires that the hostname of each worker node is included in
the client certificate.

Also, Kubernetes uses a special-purpose authorization mode called Node Authorizer, that specifically authorizes API requests made 
by **kubelet services**. In order to be authorized by the Node Authorizer, kubelets must use a credential that identifies them as being
in the system:nodes group, with a username of system:node:<nodeName>. Notice the "CN": "system:node:${instance_hostname}", in the
below code.

Therefore, the certificate to be created must comply to these requirements. In the below example, there are 3 worker nodes, hence 
we will use bash to loop through a list of the worker nodes’ hostnames, and based on each index, the respective Certificate Signing
Request (CSR), private key and client certificates will be generated.
  
```
for i in 0 1 2; do
  instance="${NAME}-worker-${i}"
  instance_hostname="ip-172-31-0-2${i}"
  cat > ${instance}-csr.json <<EOF
{
  "CN": "system:node:${instance_hostname}",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "UK",
      "L": "England",
      "O": "system:nodes",
      "OU": "TOTAL.COM",
      "ST": "London"
    }
  ]
}
EOF

  external_ip=$(aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=${instance}" \
    --output text --query 'Reservations[].Instances[].PublicIpAddress')

  internal_ip=$(aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=${instance}" \
    --output text --query 'Reservations[].Instances[].PrivateIpAddress')

  cfssl gencert \
    -ca=ca.pem \
    -ca-key=ca-key.pem \
    -config=ca-config.json \
    -hostname=${instance_hostname},${external_ip},${internal_ip} \
    -profile=kubernetes \
    ${NAME}-worker-${i}-csr.json | cfssljson -bare ${NAME}-worker-${i}
done
```
  
  
6. Finally, kubernetes admin user's **Client Certificate and Private Key**
  

```
{
cat > admin-csr.json <<EOF
{
  "CN": "admin",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "UK",
      "L": "England",
      "O": "system:masters",
      "OU": "TOTAL.COM",
      "ST": "London"
    }
  ]
}
EOF

cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -profile=kubernetes \
  admin-csr.json | cfssljson -bare admin
}
```
  ![image](https://github.com/user-attachments/assets/a8dfb26f-5096-454a-9629-5b7ab5492772)

  
7. Actually, we are not done yet!
There is one more pair of certificate and private key we need to generate. That is for the Token Controller: a part of the Kubernetes 
Controller Manager kube-controller-manager responsible for generating and signing service account tokens which are used by pods or
other resources to establish connectivity to the api-server. Read more about Service Accounts from the official documentation.

Alright, let us quickly create the last set of files, and we are done with PKIs
  
  
```
{

cat > service-account-csr.json <<EOF
{
  "CN": "service-accounts",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "UK",
      "L": "England",
      "O": "Kubernetes",
      "OU": "TOTAL.COM",
      "ST": "London"
    }
  ]
}
EOF

cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -profile=kubernetes \
  service-account-csr.json | cfssljson -bare service-account
}
```
![image](https://github.com/user-attachments/assets/c3b44691-4747-4434-af2b-0d95ce5e2fc1)
![image](https://github.com/user-attachments/assets/97eca3c5-f19d-4538-9573-0afca9d4e2a5)

### Step 4  DISTRIBUTING THE CLIENT AND SERVER CERTIFICATES

Now it is time to start sending all the client and server certificates to their respective instances.

Let us begin with the worker nodes:

Copy these files securely to the worker nodes using scp utility

- Root CA certificate – ca.pem
- X509 Certificate for each worker node
- Private Key of the certificate for each worker node


```
for i in 0 1 2; do
  instance="${NAME}-worker-${i}"
  external_ip=$(aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=${instance}" \
    --output text --query 'Reservations[].Instances[].PublicIpAddress')
  scp -i ../ssh/${NAME}.id_rsa \
    ca.pem ${instance}-key.pem ${instance}.pem ubuntu@${external_ip}:~/; \
done
```
![image](https://github.com/user-attachments/assets/8a24f340-501e-45c9-8087-a1add84d0402)


**Master or Controller node:** – Note that only the api-server related files will be sent over to the master nodes.

```
for i in 0 1 2; do
instance="${NAME}-master-${i}" \
  external_ip=$(aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=${instance}" \
    --output text --query 'Reservations[].Instances[].PublicIpAddress')
  scp -i ../ssh/${NAME}.id_rsa \
    ca.pem ca-key.pem service-account-key.pem service-account.pem \
    master-kubernetes.pem master-kubernetes-key.pem ubuntu@${external_ip}:~/;
done
```
![image](https://github.com/user-attachments/assets/21a5511b-d128-44e0-8a44-dd6e7f78f92a)

The kube-proxy, kube-controller-manager, kube-scheduler, and kubelet client certificates will be used to generate client authentication 
configuration files later.


### Step 4  Use `KUBECTL` TO GENERATE KUBERNETES CONFIGURATION FILES FOR AUTHENTICATION

All the work you are doing right now is ensuring that you do not face any difficulties by the time the Kubernetes cluster is up and 
running. In this step, you will create some files known as kubeconfig, which enables Kubernetes clients to locate and authenticate to 
the Kubernetes API Servers.

You will need a client tool called kubectl to do this. And, by the way, most of your time with Kubernetes will be spent using kubectl
commands.

Now it’s time to generate kubeconfig files for the kubelet, controller manager, kube-proxy, and scheduler clients and then the admin
user.

First, let us create a few environment variables for reuse by multiple commands.


```
KUBERNETES_API_SERVER_ADDRESS=$(aws elbv2 describe-load-balancers --load-balancer-arns 
${LOAD_BALANCER_ARN} --output text --query 'LoadBalancers[].DNSName')
```

1. Generate the kubelet kubeconfig file
For each of the nodes running the kubelet component, it is very important that the client certificate configured for that node is used
to generate the kubeconfig. This is because each certificate has the node’s DNS name or IP Address configured at the time the 
certificate was generated. It will also ensure that the appropriate authorization is applied to that node through the Node Authorizer

Below command must be run in the directory where all the certificates were generated.

```
for i in 0 1 2; do

instance="${NAME}-worker-${i}"
instance_hostname="ip-172-31-0-2${i}"

 # Set the kubernetes cluster in the kubeconfig file
  kubectl config set-cluster ${NAME} \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://$KUBERNETES_API_SERVER_ADDRESS:6443 \
    --kubeconfig=${instance}.kubeconfig

# Set the cluster credentials in the kubeconfig file
  kubectl config set-credentials system:node:${instance_hostname} \
    --client-certificate=${instance}.pem \
    --client-key=${instance}-key.pem \
    --embed-certs=true \
    --kubeconfig=${instance}.kubeconfig

# Set the context in the kubeconfig file
  kubectl config set-context default \
    --cluster=${NAME} \
    --user=system:node:${instance_hostname} \
    --kubeconfig=${instance}.kubeconfig

  kubectl config use-context default --kubeconfig=${instance}.kubeconfig
done
```
![image](https://github.com/user-attachments/assets/f53c45a0-b9ca-46f8-abf1-68d595d26d15)




**List the output**

```
ls -ltr *.kubeconfig
```
![image](https://github.com/user-attachments/assets/6cacd24f-c0b0-455d-a4bc-a7427190990f)

Open up the kubeconfig files generated and review the 3 different sections that have been configured:

- Cluster
- Credentials
- And Kube Context

Kubeconfig file is used to organize information about clusters, users, namespaces and authentication mechanisms. By default, kubectl
looks for a file named config in the $HOME/.kube directory. You can specify other kubeconfig files by setting the KUBECONFIG 
environment variable or by setting the --kubeconfig flag. To get to know more how to create your own kubeconfig files – 
[read this documentation](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/).

Context part of kubeconfig file defines three main parameters: cluster, namespace and user. You can save several different contexts
with any convenient names and switch between them when needed.

```
kubectl config use-context %context-name%
```

2. Generate the kube-proxy kubeconfig

```
{
  kubectl config set-cluster ${NAME} \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://${KUBERNETES_API_SERVER_ADDRESS}:6443 \
    --kubeconfig=kube-proxy.kubeconfig

  kubectl config set-credentials system:kube-proxy \
    --client-certificate=kube-proxy.pem \
    --client-key=kube-proxy-key.pem \
    --embed-certs=true \
    --kubeconfig=kube-proxy.kubeconfig

  kubectl config set-context default \
    --cluster=${NAME} \
    --user=system:kube-proxy \
    --kubeconfig=kube-proxy.kubeconfig

  kubectl config use-context default --kubeconfig=kube-proxy.kubeconfig
}
```

3. Generate the Kube-Controller-Manager kubeconfig
Notice that the --server is set to use 127.0.0.1. This is because, this component runs on the API-Server so there is no point 
routing through the Load Balancer.

```
{
  kubectl config set-cluster ${NAME} \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://127.0.0.1:6443 \
    --kubeconfig=kube-controller-manager.kubeconfig

  kubectl config set-credentials system:kube-controller-manager \
    --client-certificate=kube-controller-manager.pem \
    --client-key=kube-controller-manager-key.pem \
    --embed-certs=true \
    --kubeconfig=kube-controller-manager.kubeconfig

  kubectl config set-context default \
    --cluster=${NAME} \
    --user=system:kube-controller-manager \
    --kubeconfig=kube-controller-manager.kubeconfig

  kubectl config use-context default --kubeconfig=kube-controller-manager.kubeconfig
}
```
![image](https://github.com/user-attachments/assets/dc5a1252-1f99-467c-a0af-df5591d5ae3e)


4. Generating the Kube-Scheduler Kubeconfig

```
{
  kubectl config set-cluster ${NAME} \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://127.0.0.1:6443 \
    --kubeconfig=kube-scheduler.kubeconfig

  kubectl config set-credentials system:kube-scheduler \
    --client-certificate=kube-scheduler.pem \
    --client-key=kube-scheduler-key.pem \
    --embed-certs=true \
    --kubeconfig=kube-scheduler.kubeconfig

  kubectl config set-context default \
    --cluster=${NAME} \
    --user=system:kube-scheduler \
    --kubeconfig=kube-scheduler.kubeconfig

  kubectl config use-context default --kubeconfig=kube-scheduler.kubeconfig
}
```
![image](https://github.com/user-attachments/assets/cfd49d52-45c8-4f6b-965e-ce207b335aba)


5. Finally, generate the kubeconfig file for the admin user

```
{
  kubectl config set-cluster ${NAME} \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://${KUBERNETES_API_SERVER_ADDRESS}:6443 \
    --kubeconfig=admin.kubeconfig

  kubectl config set-credentials admin \
    --client-certificate=admin.pem \
    --client-key=admin-key.pem \
    --embed-certs=true \
    --kubeconfig=admin.kubeconfig

  kubectl config set-context default \
    --cluster=${NAME} \
    --user=admin \
    --kubeconfig=admin.kubeconfig

  kubectl config use-context default --kubeconfig=admin.kubeconfig
}
```
![image](https://github.com/user-attachments/assets/643b5959-3a08-4d27-b879-acd9be8e4827)


**TASK: Distribute the files to their respective servers, using scp and a for loop like we have done previously. This is a test to 
validate that you understand which component must go to which node.**
Distribute the files to their respective servers, using scp and a for loop
Copy the config files for kublet and kube-proxy to the worker nodes.
```
for i in 0 1 2; do
   instance="${NAME}-worker-${i}"
   external_ip=$(aws ec2 describe-instances \
     --filters "Name=tag:Name,Values=${instance}" \
     --output text --query 'Reservations[].Instances[].PublicIpAddress')
    scp -i ../ssh/${NAME}.id_rsa \
    ${instance}.kubeconfig kube-proxy.kubeconfig ubuntu@${external_ip}:~/
done
```
![image](https://github.com/user-attachments/assets/27e916e3-b79f-4db4-aae1-eded22f17ba1)

Copy the appropriate kube-controller-manager and kube-scheduler kubeconfig files to each master instance
```
for i in 0 1 2; do
instance="${NAME}-master-${i}" \
  external_ip=$(aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=${instance}" \
    --output text --query 'Reservations[].Instances[].PublicIpAddress')
  scp -i ../ssh/${NAME}.id_rsa \
  kube-scheduler.kubeconfig kube-controller-manager.kubeconfig ubuntu@${external_ip}:~/;
done
```
![image](https://github.com/user-attachments/assets/44b71553-811d-4c80-a7aa-d1e29541efe0)

### Step 5 PREPARE THE `etcd` DATABASE FOR ENCRYPTION AT REST.

Kubernetes uses etcd (A distributed key value store) to store variety of data which includes the cluster state, application 
configurations, and secrets. By default, the data that is being persisted to the disk is not encrypted. Any attacker that is able to
gain access to this database can exploit the cluster since the data is stored in plain text. Hence, it is a security risk for Kubernetes
that needs to be addressed.

To mitigate this risk, we must prepare to encrypt etcd at rest. "At rest" means data that is stored and persists on a disk. Anytime you
hear "in-flight" or "in transit" refers to data that is being transferred over the network. "In-flight" encryption is done through TLS.

Generate the encryption key and encode it using base64

```
ETCD_ENCRYPTION_KEY=$(head -c 64 /dev/urandom | base64) 
```

See the output that will be generated when called. Yours will be a different random string.
```
echo $ETCD_ENCRYPTION_KEY
```
![image](https://github.com/user-attachments/assets/15c9f16e-c3a4-4828-afc2-41de930d1f20)


**Create an encryption-config.yaml file as**  [documented officially by kubernetes](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/#understanding-the-encryption-at-rest-configuration)


```
cat > encryption-config.yaml <<EOF
kind: EncryptionConfig
apiVersion: v1
resources:
  - resources:
      - secrets
    providers:
      - aescbc:
          keys:
            - name: key1
              secret: ${ETCD_ENCRYPTION_KEY}
      - identity: {}
EOF
```

Send the encryption file to the Controller nodes using scp and a for loop.

```
for i in 0 1 2; do
  instance="${NAME}-master-${i}"
  external_ip=$(aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=${instance}" \
    --output text --query 'Reservations[].Instances[].PublicIpAddress')
  scp -i ../ssh/${NAME}.id_rsa \
    encryption-config.yaml ubuntu@${external_ip}:~/; \
done
```
![image](https://github.com/user-attachments/assets/f2d794cf-ce78-4d5f-ad6a-02fcd14ee48c)


**Bootstrap `etcd` cluster**

> **TIPS**: Use a terminal multi-plexer like [multi-tabbed putty](https://youtu.be/0c1cWrMnZlc) or [tmux](https://youtu.be/Yl7NFenTgIo) to
work with multiple terminal sessions simultaneously. It will make your life easier, especially when you need to work on multiple 
nodes and run the same command across all nodes. Imagine repeating the same commands on 10 different nodes, and you don not intend
to start automating with a configuration management tool like Ansible yet.

The primary purpose of the `etcd` component is to store the state of the cluster. This is because Kubernetes itself is stateless.
Therefore, all its stateful data will persist in etcd. Since Kubernetes is a distributed system – it needs a distributed storage
to keep persistent data in it. etcd is a highly-available key value store that fits the purpose. All K8s cluster configurations
are stored in a form of key value pairs in etcd, it also stores the actual and desired states of the cluster. etcd cluster is
intelligent enough to watch for changes made on one instance and almost instantly replicate those changes to the rest of the
instances, so all of them will be always reconciled.

**NOTE:** Don not just copy and paste the commands, ensure that you go through each and understand exactly what they will do on your
servers. Use tools like tmux to make it easy to run commands on multiple terminal screens at once.


I use Tmux (Terminal Multiplexer) is a powerful tool that allows you to manage multiple terminal sessions within a single window. 

**Installation**
```
sudo apt update
sudo apt install tmux

```
**Starting Tmux**
```
tmux
```
**To split the current window into two panes side by side**

`Press Ctrl + b, then %`

**To split the window into top and bottom panes:**

`Press Ctrl + b, then " `





![image](https://github.com/user-attachments/assets/f23b586b-2965-4068-af2d-aaa401739658)




1. SSH into the controller server

- Master-1

```
master_1_ip=$(aws ec2 describe-instances \
--filters "Name=tag:Name,Values=${NAME}-master-0" \
--output text --query 'Reservations[].Instances[].PublicIpAddress')
ssh -i k8s-cluster-from-ground-up.id_rsa ubuntu@${master_1_ip}
```

- Master-2

```
master_2_ip=$(aws ec2 describe-instances \
--filters "Name=tag:Name,Values=${NAME}-master-1" \
--output text --query 'Reservations[].Instances[].PublicIpAddress')
ssh -i k8s-cluster-from-ground-up.id_rsa ubuntu@${master_2_ip}
```

- Master-3

```
master_3_ip=$(aws ec2 describe-instances \
--filters "Name=tag:Name,Values=${NAME}-master-2" \
--output text --query 'Reservations[].Instances[].PublicIpAddress')
ssh -i k8s-cluster-from-ground-up.id_rsa ubuntu@${master_3_ip}
```

You should have a a similar pane like below. You should be able to see all the files that have been sent to the nodes.
![image](https://github.com/user-attachments/assets/4858eff8-be08-4035-8f60-f01684c18412)

2. Download and install etcd

```
 wget -q --show-progress --https-only --timestamping \
  "https://github.com/etcd-io/etcd/releases/download/v3.4.15/etcd-v3.4.15-linux-amd64.tar.gz"
```

3. Extract and install the etcd server and the etcdctl command line utility:

```
{
tar -xvf etcd-v3.4.15-linux-amd64.tar.gz
sudo mv etcd-v3.4.15-linux-amd64/etcd* /usr/local/bin/
}
```

4. Configure the etcd server

```
{
  sudo mkdir -p /etc/etcd /var/lib/etcd
  sudo chmod 700 /var/lib/etcd
  sudo cp ca.pem master-kubernetes-key.pem master-kubernetes.pem /etc/etcd/
}
```

5. The instance internal IP address will be used to serve client requests and communicate with etcd cluster peers. Retrieve the
 internal IP address for the current compute instance:
 
 ```
 export INTERNAL_IP=$(curl -s http://169.254.169.254/latest/meta-data/local-ipv4)
 ```
 
 6. Each etcd member must have a unique name within an etcd cluster. Set the etcd name to node Private IP address so it will uniquely 
 identify the machine:

```
ETCD_NAME=$(curl -s http://169.254.169.254/latest/user-data/ \
  | tr "|" "\n" | grep "^name" | cut -d"=" -f2)

echo ${ETCD_NAME}
```

7. Create the etcd.service systemd unit file:
The flags are well documented [here](https://www.bookstack.cn/read/etcd-3.2.17-en/717bafd59fa87192.md)

```
cat <<EOF | sudo tee /etc/systemd/system/etcd.service
[Unit]
Description=etcd
Documentation=https://github.com/coreos

[Service]
Type=notify
ExecStart=/usr/local/bin/etcd \\
  --name ${ETCD_NAME} \\
  --trusted-ca-file=/etc/etcd/ca.pem \\
  --peer-trusted-ca-file=/etc/etcd/ca.pem \\
  --peer-client-cert-auth \\
  --client-cert-auth \\
  --listen-peer-urls https://${INTERNAL_IP}:2380 \\
  --listen-client-urls https://${INTERNAL_IP}:2379,https://127.0.0.1:2379 \\
  --advertise-client-urls https://${INTERNAL_IP}:2379 \\
  --initial-cluster-token etcd-cluster-0 \\
  --initial-cluster master-0=https://172.31.0.10:2380,master-1=https://172.31.0.11:2380,master-2=https://172.31.0.12:2380 \\
  --cert-file=/etc/etcd/master-kubernetes.pem \\
  --key-file=/etc/etcd/master-kubernetes-key.pem \\
  --peer-cert-file=/etc/etcd/master-kubernetes.pem \\
  --peer-key-file=/etc/etcd/master-kubernetes-key.pem \\
  --initial-advertise-peer-urls https://${INTERNAL_IP}:2380 \\
  --initial-cluster-state new \\
  --data-dir=/var/lib/etcd
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```

8. Start and enable the etcd Server

```
{
sudo systemctl daemon-reload
sudo systemctl enable etcd
sudo systemctl start etcd
}
```

9. Verify the etcd installation

```
sudo ETCDCTL_API=3 etcdctl member list \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/etcd/ca.pem \
  --cert=/etc/etcd/master-kubernetes.pem \
  --key=/etc/etcd/master-kubernetes-key.pem
```


**Output:** 
```
6709c481b5234095, started, master-0, https://172.31.0.10:2380, https://172.31.0.10:2379, false
ade74a4f39c39f33, started, master-1, https://172.31.0.11:2380, https://172.31.0.11:2379, false
ed33b44c0b153ee3, started, master-2, https://172.31.0.12:2380, https://172.31.0.12:2379, false
```
![image](https://github.com/user-attachments/assets/d0089866-4e23-4c7d-97d8-d3c4d73f09d5)


```
systemctl status etcd
```

![image](https://github.com/user-attachments/assets/a729b8cc-f4b6-4c1f-86e3-6eeeb991a8d2)



**BOOTSTRAP THE `CONTROL PLANE`**


In this section, you will configure the components for the control plane on the master/controller nodes.

1. Create the Kubernetes configuration directory:

```
sudo mkdir -p /etc/kubernetes/config
```

2. Download the official Kubernetes release binaries:

```
sudo wget -q --show-progress --https-only --timestamping \
"https://storage.googleapis.com/kubernetes-release/release/v1.21.0/bin/linux/amd64/kube-apiserver" \
"https://storage.googleapis.com/kubernetes-release/release/v1.21.0/bin/linux/amd64/kube-controller-manager" \
"https://storage.googleapis.com/kubernetes-release/release/v1.21.0/bin/linux/amd64/kube-scheduler" \
"https://storage.googleapis.com/kubernetes-release/release/v1.21.0/bin/linux/amd64/kubectl"
```

3. Install the Kubernetes binaries:

```
{
  sudo chmod +x kube-apiserver kube-controller-manager kube-scheduler kubectl
  sudo mv kube-apiserver kube-controller-manager kube-scheduler kubectl /usr/local/bin/
}

```

4. Configure the Kubernetes API Server:

```
{
sudo mkdir -p /var/lib/kubernetes/

sudo mv ca.pem ca-key.pem master-kubernetes-key.pem master-kubernetes.pem \
service-account-key.pem service-account.pem \
encryption-config.yaml /var/lib/kubernetes/
}
```

The instance internal IP address will be used to advertise the API Server to members of the cluster. Retrieve the internal IP
address for the current compute instance:

```
export INTERNAL_IP=$(curl -s http://169.254.169.254/latest/meta-data/local-ipv4)
```

Create the kube-apiserver.service systemd unit file: Ensure to read each startup flag used in below systemd file from the 
documentation [here](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/)


```
cat <<EOF | sudo tee /etc/systemd/system/kube-apiserver.service
[Unit]
Description=Kubernetes API Server
Documentation=https://github.com/kubernetes/kubernetes

[Service]
ExecStart=/usr/local/bin/kube-apiserver \\
  --advertise-address=${INTERNAL_IP} \\
  --allow-privileged=true \\
  --apiserver-count=3 \\
  --audit-log-maxage=30 \\
  --audit-log-maxbackup=3 \\
  --audit-log-maxsize=100 \\
  --audit-log-path=/var/log/audit.log \\
  --authorization-mode=Node,RBAC \\
  --bind-address=0.0.0.0 \\
  --client-ca-file=/var/lib/kubernetes/ca.pem \\
  --enable-admission-plugins=NamespaceLifecycle,NodeRestriction,LimitRanger,ServiceAccount,DefaultStorageClass,ResourceQuota \\
  --etcd-cafile=/var/lib/kubernetes/ca.pem \\
  --etcd-certfile=/var/lib/kubernetes/master-kubernetes.pem \\
  --etcd-keyfile=/var/lib/kubernetes/master-kubernetes-key.pem\\
  --etcd-servers=https://172.31.0.10:2379,https://172.31.0.11:2379,https://172.31.0.12:2379 \\
  --event-ttl=1h \\
  --encryption-provider-config=/var/lib/kubernetes/encryption-config.yaml \\
  --kubelet-certificate-authority=/var/lib/kubernetes/ca.pem \\
  --kubelet-client-certificate=/var/lib/kubernetes/master-kubernetes.pem \\
  --kubelet-client-key=/var/lib/kubernetes/master-kubernetes-key.pem \\
  --runtime-config='api/all=true' \\
  --service-account-key-file=/var/lib/kubernetes/service-account.pem \\
  --service-account-signing-key-file=/var/lib/kubernetes/service-account-key.pem \\
  --service-account-issuer=https://${INTERNAL_IP}:6443 \\
  --service-cluster-ip-range=172.32.0.0/24 \\
  --service-node-port-range=30000-32767 \\
  --tls-cert-file=/var/lib/kubernetes/master-kubernetes.pem \\
  --tls-private-key-file=/var/lib/kubernetes/master-kubernetes-key.pem \\
  --v=2
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```

5. Configure the Kubernetes Controller Manager:
Move the kube-controller-manager kubeconfig into place:

```
sudo mv kube-controller-manager.kubeconfig /var/lib/kubernetes/
```


Export some variables to retrieve the vpc_cidr – This will be required for the bind-address flag:


```
export AWS_METADATA="http://169.254.169.254/latest/meta-data"
export EC2_MAC_ADDRESS=$(curl -s $AWS_METADATA/network/interfaces/macs/ | head -n1 | tr -d '/')
export VPC_CIDR=$(curl -s $AWS_METADATA/network/interfaces/macs/$EC2_MAC_ADDRESS/vpc-ipv4-cidr-block/)
export NAME=k8s-cluster-from-ground-up
```

Create the kube-controller-manager.service systemd unit file:


```
cat <<EOF | sudo tee /etc/systemd/system/kube-controller-manager.service
[Unit]
Description=Kubernetes Controller Manager
Documentation=https://github.com/kubernetes/kubernetes

[Service]
ExecStart=/usr/local/bin/kube-controller-manager \\
  --bind-address=0.0.0.0 \\
  --cluster-cidr=${VPC_CIDR} \\
  --cluster-name=${NAME} \\
  --cluster-signing-cert-file=/var/lib/kubernetes/ca.pem \\
  --cluster-signing-key-file=/var/lib/kubernetes/ca-key.pem \\
  --kubeconfig=/var/lib/kubernetes/kube-controller-manager.kubeconfig \\
  --authentication-kubeconfig=/var/lib/kubernetes/kube-controller-manager.kubeconfig \\
  --authorization-kubeconfig=/var/lib/kubernetes/kube-controller-manager.kubeconfig \\
  --leader-elect=true \\
  --root-ca-file=/var/lib/kubernetes/ca.pem \\
  --service-account-private-key-file=/var/lib/kubernetes/service-account-key.pem \\
  --service-cluster-ip-range=172.32.0.0/24 \\
  --use-service-account-credentials=true \\
  --v=2
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```

6. Configure the Kubernetes Scheduler:
Move the kube-scheduler kubeconfig into place:

```
sudo mv kube-scheduler.kubeconfig /var/lib/kubernetes/
sudo mkdir -p /etc/kubernetes/config
```

Create the kube-scheduler.yaml configuration file:

```
cat <<EOF | sudo tee /etc/kubernetes/config/kube-scheduler.yaml
apiVersion: kubescheduler.config.k8s.io/v1beta1
kind: KubeSchedulerConfiguration
clientConnection:
  kubeconfig: "/var/lib/kubernetes/kube-scheduler.kubeconfig"
leaderElection:
  leaderElect: true
EOF
```

Create the kube-scheduler.service systemd unit file:

```
cat <<EOF | sudo tee /etc/systemd/system/kube-scheduler.service
[Unit]
Description=Kubernetes Scheduler
Documentation=https://github.com/kubernetes/kubernetes

[Service]
ExecStart=/usr/local/bin/kube-scheduler \\
  --config=/etc/kubernetes/config/kube-scheduler.yaml \\
  --v=2
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```

7. Start the Controller Services

```
{
sudo systemctl daemon-reload
sudo systemctl enable kube-apiserver kube-controller-manager kube-scheduler
sudo systemctl start kube-apiserver kube-controller-manager kube-scheduler
}
```


Check the status of the services. Start with the kube-scheduler and kube-controller-manager. It may take up to 20 seconds for 
kube-apiserver to be fully loaded.
```
{
sudo systemctl status kube-apiserver
sudo systemctl status kube-controller-manager
sudo systemctl status kube-scheduler
}
```
NOTE: There is a trap in the entire setup you have been going through, and so the api-server will not start up on your server if 
you have followed the exact steps so far. As a DevOps engineer, you must be able to solve problems.

HINTS:

1. The problem relates to etcd configuration.
2. Check the systemd logs for the api-server. The problem will be clearly logged, and it will give you an idea what is wrong. Find 
out how to fix it.
![image](https://github.com/user-attachments/assets/7a42a165-4bdd-4293-bd89-b207e599ff3c)

![image](https://github.com/user-attachments/assets/143c455c-fc71-431e-9325-926ea36b8b1d)

![image](https://github.com/user-attachments/assets/3369ead7-e1df-4ff8-9d20-bca13e799c95)

### TEST THAT EVERYTHING IS WORKING FINE

1. To get the cluster details run:

```
kubectl cluster-info  --kubeconfig admin.kubeconfig
```

**OUTPUT:**

```
Kubernetes control plane is running at https://k8s-api-server.svc.total.io:6443

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

2. To get the current namespaces:

```
kubectl get namespaces --kubeconfig admin.kubeconfig
```

![7019](https://user-images.githubusercontent.com/85270361/210211191-7f775ab0-533f-40ec-aae0-2f2662eb2090.PNG)


3. To reach the Kubernetes API Server publicly

```
curl --cacert /var/lib/kubernetes/ca.pem https://$INTERNAL_IP:6443/version
```

![7020](https://user-images.githubusercontent.com/85270361/210211357-537df061-762f-4180-8c08-bccefc37a566.PNG)


4. To get the status of each component:

```
kubectl get componentstatuses --kubeconfig admin.kubeconfig
```

![7021](https://user-images.githubusercontent.com/85270361/210212939-8514d8c6-b276-45de-a682-9b7cde6cf588.PNG)


5. On one of the controller nodes, configure Role Based Access Control (RBAC) so that the api-server has necessary authorization for 
for the kubelet.

Create the ClusterRole:

```
cat <<EOF | kubectl apply --kubeconfig admin.kubeconfig -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
  name: system:kube-apiserver-to-kubelet
rules:
  - apiGroups:
      - ""
    resources:
      - nodes/proxy
      - nodes/stats
      - nodes/log
      - nodes/spec
      - nodes/metrics
    verbs:
      - "*"
EOF
```

Create the ClusterRoleBinding to bind the kubernetes user with the role created above:

```
cat <<EOF | kubectl --kubeconfig admin.kubeconfig  apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:kube-apiserver
  namespace: ""
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:kube-apiserver-to-kubelet
subjects:
  - apiGroup: rbac.authorization.k8s.io
    kind: User
    name: kubernetes
EOF
```


