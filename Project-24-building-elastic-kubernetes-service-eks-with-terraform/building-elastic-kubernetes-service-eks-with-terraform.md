### Building Elastic Kubernetes Service (EKS) With Terraform-101

**Building EKS with Terraform**

At this point you already have some Terraform experience. So, you have some work to do. But, you can get started with the steps below.
If you have terraform code from Project 16, simply update the code and include EKS starting from number 6 below. Otherwise, follow the
steps from number 1

> Note: Use Terraform version v1.0.2 and kubectl version v1.23.6

1. Open up a new directory on your laptop, and name it eks

```
mkdir Terraform_EKS_Cluster
```
```
cd Terraform_EKS_Cluster
```

2. Use AWS CLI to create an S3 bucket

```
aws s3 mb s3://melkamu-terraform-eks --region us-east-1
```
![image](https://github.com/user-attachments/assets/8e55e7e8-5535-4450-850f-f8a0ec6dcb15)

3. Create a file – backend.tf Task for you, ensure the backend is configured for remote state in S3

```
terraform {
  backend "s3" {
    bucket         = "melkamu-terraform-eks"
    key            = "eks/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
  }
}
```
![image](https://github.com/user-attachments/assets/d45bc183-58d2-45cf-97fe-2e10cd3c91e0)

4. Create a file – network.tf and provision Elastic IP for Nat Gateway, VPC, Private and public subnets.

```
# reserve Elastic IP to be used in our NAT gateway
resource "aws_eip" "nat_gw_elastic_ip" {
vpc = true

tags = {
Name            = "${var.cluster_name}-nat-eip"
iac_environment = var.iac_environment_tag
}
}
```

Create VPC using the official AWS module

```
module "vpc" {
source  = "terraform-aws-modules/vpc/aws"

name = "${var.name_prefix}-vpc"
cidr = var.main_network_block
azs  = data.aws_availability_zones.available_azs.names

private_subnets = [
# this loop will create a one-line list as ["10.0.0.0/20", "10.0.16.0/20", "10.0.32.0/20", ...]
# with a length depending on how many Zones are available
for zone_id in data.aws_availability_zones.available_azs.zone_ids :
cidrsubnet(var.main_network_block, var.subnet_prefix_extension, tonumber(substr(zone_id, length(zone_id) - 1, 1)) - 1)
]

public_subnets = [
# this loop will create a one-line list as ["10.0.128.0/20", "10.0.144.0/20", "10.0.160.0/20", ...]
# with a length depending on how many Zones are available
# there is a zone Offset variable, to make sure no collisions are present with private subnet blocks
for zone_id in data.aws_availability_zones.available_azs.zone_ids :
cidrsubnet(var.main_network_block, var.subnet_prefix_extension, tonumber(substr(zone_id, length(zone_id) - 1, 1)) + var.zone_offset - 1)
]

# Enable single NAT Gateway to save some money
# WARNING: this could create a single point of failure, since we are creating a NAT Gateway in one AZ only
# feel free to change these options if you need to ensure full Availability without the need of running 'terraform apply'
# reference: https://registry.terraform.io/modules/terraform-aws-modules/vpc/aws/2.44.0#nat-gateway-scenarios
enable_nat_gateway     = true
single_nat_gateway     = true
one_nat_gateway_per_az = false
enable_dns_hostnames   = true
reuse_nat_ips          = true
external_nat_ip_ids    = [aws_eip.nat_gw_elastic_ip.id]

# Add VPC/Subnet tags required by EKS
tags = {
"kubernetes.io/cluster/${var.cluster_name}" = "shared"
iac_environment                             = var.iac_environment_tag
}
public_subnet_tags = {
"kubernetes.io/cluster/${var.cluster_name}" = "shared"
"kubernetes.io/role/elb"                    = "1"
iac_environment                             = var.iac_environment_tag
}
private_subnet_tags = {
"kubernetes.io/cluster/${var.cluster_name}" = "shared"
"kubernetes.io/role/internal-elb"           = "1"
iac_environment                             = var.iac_environment_tag
}
}
```
![image](https://github.com/user-attachments/assets/c12e3fdb-1bfd-417b-8609-683cc5622c00)


**Note:** The tags added to the subnets is very important. The Kubernetes Cloud Controller Manager (cloud-controller-manager) and
AWS Load Balancer Controller (aws-load-balancer-controller) needs to identify the cluster’s. To do that, it querries the cluster’s 
subnets by using the tags as a filter.

- For public and private subnets that use load balancer resources: each subnet must be tagged

```
Key: kubernetes.io/cluster/cluster-name
Value: shared
```

- For private subnets that use internal load balancer resources: each subnet must be tagged


```
Key: kubernetes.io/role/internal-elb
Value: 1
```

- For public subnets that use internal load balancer resources: each subnet must be tagged

```
Key: kubernetes.io/role/elb
Value: 1
```


5. Create a file – variables.tf

```
# create some variables
variable "cluster_name" {
type        = string
description = "EKS cluster name."
}
variable "iac_environment_tag" {
type        = string
description = "AWS tag to indicate environment name of each infrastructure object."
}
variable "name_prefix" {
type        = string
description = "Prefix to be used on each infrastructure object Name created in AWS."
}
variable "main_network_block" {
type        = string
description = "Base CIDR block to be used in our VPC."
}
variable "subnet_prefix_extension" {
type        = number
description = "CIDR block bits extension to calculate CIDR blocks of each subnetwork."
}
variable "zone_offset" {
type        = number
description = "CIDR block bits extension offset to calculate Public subnets, avoiding collisions with Private subnets."
}
```
![image](https://github.com/user-attachments/assets/063a7ac0-4067-4dd7-b409-b4193fd80058)



6. Create a file – data.tf – This will pull the available AZs for use.

```
# get all available AZs in our region
data "aws_availability_zones" "available_azs" {
state = "available"
}
data "aws_caller_identity" "current" {} # used for accesing Account ID and ARN
```
![image](https://github.com/user-attachments/assets/e29b6b5d-9282-4109-a08f-d055b2901625)

7. Create a file – `eks.tf` and provision EKS cluster (Create the file only if you are not using your existing Terraform code. Otherwise
you can simply append it to the main.tf from your existing code) Read more about this module from the official documentation 
[here](https://registry.terraform.io/modules/terraform-aws-modules/eks/aws/18.20.5) – Reading it will help you understand more about 
the rich features of the module.

```
module "eks_cluster" {
  source  = "terraform-aws-modules/eks/aws"
  version = "~> 18.0"
  cluster_name    = var.cluster_name
  cluster_version = "1.22"
  vpc_id     = module.vpc.vpc_id
  subnet_ids = module.vpc.private_subnets
  cluster_endpoint_private_access = true
  cluster_endpoint_public_access = true

  # Self Managed Node Group(s)
  self_managed_node_group_defaults = {
    instance_type                          = var.asg_instance_types[0]
    update_launch_template_default_version = true
  }
  self_managed_node_groups = local.self_managed_node_groups

  # aws-auth configmap
  create_aws_auth_configmap = true
  manage_aws_auth_configmap = true
  aws_auth_users = concat(local.admin_user_map_users, local.developer_user_map_users)
  tags = {
    Environment = "prod"
    Terraform   = "true"
  }
}
```
![image](https://github.com/user-attachments/assets/722e62ed-74ed-4059-8963-bab5f114063b)

8. Create a file – `locals.tf` to create local variables. Terraform does not allow assigning variable to variables. There is good reasons 
for that to avoid repeating your code unecessarily. So a terraform way to achieve this would be to use locals so that your code can 
be kept [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)

```
# render Admin & Developer users list with the structure required by EKS module
locals {
  admin_user_map_users = [
    for admin_user in var.admin_users :
    {
      userarn  = "arn:aws:iam::${data.aws_caller_identity.current.account_id}:user/${admin_user}"
      username = admin_user
      groups   = ["system:masters"]
    }
  ]
  developer_user_map_users = [
    for developer_user in var.developer_users :
    {
      userarn  = "arn:aws:iam::${data.aws_caller_identity.current.account_id}:user/${developer_user}"
      username = developer_user
      groups   = ["${var.name_prefix}-developers"]
    }
  ]

  self_managed_node_groups = {
    worker_group1 = {
      name = "${var.cluster_name}-wg"

      min_size      = var.autoscaling_minimum_size_by_az * length(data.aws_availability_zones.available_azs.zone_ids)
      desired_size      = var.autoscaling_minimum_size_by_az * length(data.aws_availability_zones.available_azs.zone_ids)
      max_size  = var.autoscaling_maximum_size_by_az * length(data.aws_availability_zones.available_azs.zone_ids)
      instance_type = var.asg_instance_types[0].instance_type

      bootstrap_extra_args = "--kubelet-extra-args '--node-labels=node.kubernetes.io/lifecycle=spot'"

      block_device_mappings = {
        xvda = {
          device_name = "/dev/xvda"
          ebs = {
            delete_on_termination = true
            encrypted             = false
            volume_size           = 10
            volume_type           = "gp2"
          }
        }
      }

      use_mixed_instances_policy = true
      mixed_instances_policy = {
        instances_distribution = {
          spot_instance_pools = 4
        }

        override = var.asg_instance_types
      }
    }
  }
}
```
![image](https://github.com/user-attachments/assets/a15c9b70-6f1b-46a8-b3a5-bd362fe7034b)


9. Add more variables to the variables.tf file

```
# create some variables
variable "admin_users" {
  type        = list(string)
  description = "List of Kubernetes admins."
}
variable "developer_users" {
  type        = list(string)
  description = "List of Kubernetes developers."
}
variable "asg_instance_types" {
  description = "List of EC2 instance machine types to be used in EKS."
}
variable "autoscaling_minimum_size_by_az" {
  type        = number
  description = "Minimum number of EC2 instances to autoscale our EKS cluster on each AZ."
}
variable "autoscaling_maximum_size_by_az" {
  type        = number
  description = "Maximum number of EC2 instances to autoscale our EKS cluster on each AZ."
}
```


10. Create a file – `variables.tfvars` to set values for variables.

```
cluster_name            = "tooling-app-eks"
iac_environment_tag     = "development"
name_prefix             = "darey-io-eks"
main_network_block      = "10.0.0.0/16"
subnet_prefix_extension = 4
zone_offset             = 8

# Ensure that these users already exist in AWS IAM. Another approach is that you can introduce an iam.tf file to manage users separately, get the data source and interpolate their ARN.
admin_users                    = ["darey", "solomon"]
developer_users                = ["leke", "david"]
asg_instance_types             = [ { instance_type = "t3.small" }, { instance_type = "t2.small" }, ]
autoscaling_minimum_size_by_az = 1
autoscaling_maximum_size_by_az = 10
```

11. Create file – `provider.tf`


```
provider "aws" {
  region = "us-east-1"
}

provider "random" {
}
```
![image](https://github.com/user-attachments/assets/11be4c3a-62c6-4a44-a394-25be0bb4d01d)

 update a file – variables.tfvars to set values for variables.

```
cluster_name            = "tooling-app-eks"
iac_environment_tag     = "development"
name_prefix             = "darey-io-eks"
main_network_block      = "10.0.0.0/16"
subnet_prefix_extension = 4
zone_offset             = 8

# Ensure that these users already exist in AWS IAM. Another approach is that you can introduce an iam.tf file to manage users separately, get the data source and interpolate their ARN.
admin_users                              = ["dare", "solomon"]
developer_users                          = ["leke", "david"]
asg_instance_types                       = ["t3.small", "t2.small"]
autoscaling_minimum_size_by_az           = 1
autoscaling_maximum_size_by_az           = 10
autoscaling_average_cpu                  = 30
```

12. Run terraform init

Initialize the working directory:
```
terraform init
```
![image](https://github.com/user-attachments/assets/a390197a-becd-40bc-9a84-dc5eb5c7f9c9)

13. Run Terraform plan – Your plan should have an output

```
terraform plan
```
![image](https://github.com/user-attachments/assets/8718e1a5-d41f-472a-a869-8de64e7c12b6)

15. Run Terraform apply
This will begin to create cloud resources, and fail at some point with the error

```
terraform apply

```

```
╷
│ Error: Post "http://localhost/api/v1/namespaces/kube-system/configmaps": dial tcp [::1]:80: connect: connection refused
│ 
│   with module.eks-cluster.kubernetes_config_map.aws_auth[0],
│   on .terraform/modules/eks-cluster/aws_auth.tf line 63, in resource "kubernetes_config_map" "aws_auth":
│   63: resource "kubernetes_config_map" "aws_auth" {
```
![image](https://github.com/user-attachments/assets/f8893250-6033-4483-9cca-c1ba6673d51e)


That is because for us to connect to the cluster using the kubeconfig, Terraform needs to be able to connect and set the credentials
correctly.

Let fix the problem in the next section.

### FIXING THE ERROR

To fix this problem

- Append to the file data.tf

```
# get EKS cluster info to configure Kubernetes and Helm providers
data "aws_eks_cluster" "cluster" {
  name = module.eks_cluster.cluster_id
}
data "aws_eks_cluster_auth" "cluster" {
  name = module.eks_cluster.cluster_id
}
```
![image](https://github.com/user-attachments/assets/83881512-a5c9-4a70-89f3-64cb501aa9af)

- Append to the file provider.tf

```
# get EKS authentication for being able to manage k8s objects from terraform
provider "kubernetes" {
  host                   = data.aws_eks_cluster.cluster.endpoint
  cluster_ca_certificate = base64decode(data.aws_eks_cluster.cluster.certificate_authority.0.data)
  token                  = data.aws_eks_cluster_auth.cluster.token
}
```
![image](https://github.com/user-attachments/assets/c841dbc9-6a83-4c0a-b69c-8c7993991248)

- Run the init and plan again – This time you will see
![image](https://github.com/user-attachments/assets/25df68b9-ceaf-4e5c-aad3-99174bc6feeb)

15. Create kubeconfig file using awscli.

```
aws eks update-kubecofig --name <cluster_name> --region <cluster_region> --kubeconfig kubeconfig
```
