# Automate infrastructure with iac using Terraform part 4 – (Terraform Cloud)

**What Terraform Cloud is and why use it**

By now, you should be pretty comfortable writing Terraform code to provision Cloud infrastructure using [Configuration Language (HCL)](https://developer.hashicorp.com/terraform/language). 
Terraform is an open-source system, that you installed and ran a Virtual Machine (VM) that you had to create, maintain and keep up to 
date. In Cloud world it is quite common to provide a managed version of an open-source software. Managed means that you do not have to 
install, configure and maintain it yourself – you just create an account and use it as A Service.

[Terraform Cloud](https://cloud.hashicorp.com/products/terraform) is a managed service that provides you with Terraform CLI to 
provision infrastructure, either on demand or in response to various events. By default, Terraform CLI performs operation on the server whene it is invoked, it is perfectly fine if you have a dedicated role 
who can launch it, but if you have a team who works with Terraform – you need a consistent remote environment with remote workflow and shared state to run Terraform commands.

Terraform Cloud executes Terraform commands on disposable virtual machines, this remote execution is also called remote operations.


Migrate your .tf codes to Terraform Cloud
Le us explore how we can migrate our codes to Terraform Cloud and manage our AWS infrastructure from there:

1. Create a Terraform Cloud account
Follow this [link](https://app.terraform.io/public/signup/account), create a new account, verify your email and you are ready to start

![image](https://github.com/user-attachments/assets/1b3e92da-2c74-454e-8cd5-b3d032459dfb)

Most of the features are free, but if you want to explore the difference between free and paid plans – you can check it on this [page](https://www.hashicorp.com/products/terraform/pricing).
2. Create an organization
Select "Start from scratch", choose a name for your organization and create it.
![image](https://github.com/user-attachments/assets/8021821a-d7e8-48c3-811b-86a6132a36c2)
![image](https://github.com/user-attachments/assets/ca5a7499-7b77-483b-bb48-ac62055de240)

3. Configure a workspace
Before we begin to configure our workspace – [watch this part of the video](https://www.youtube.com/watch?v=m3PlM4erixY&t=287s) 
to better understand the difference between version control workflow, CLI-driven workflow and API-driven workflow and other 
configurations that we are going to implement.

We will use version control workflow as the most common and recommended way to run Terraform commands triggered from our git repository.

Create a new repository in your GitHub and call it terraform-cloud, push your Terraform codes developed in the previous projects to 
the repository.

Choose version control workflow
![image](https://github.com/user-attachments/assets/4d995f27-6adf-4a5a-b82b-692ba6e0ef9b)


 and you will be promped to connect your GitHub account to your workspace – follow the prompt and 
add your newly created repository to the workspace.
![image](https://github.com/user-attachments/assets/6558f15c-211e-4365-84b2-d6358ebc8136)

create Workspace
![image](https://github.com/user-attachments/assets/482371c3-6ba0-4497-a7c7-992905aae38f)


Move on to "Configure settings", provide a description for your workspace and leave all the rest settings default, click "Create 
workspace".

4. Configure variables

Terraform Cloud supports two types of variables: environment variables and Terraform variables. Either type can be marked as sensitive,
which prevents them from being displayed in the Terraform Cloud web UI and makes them write-only.

Set two environment variables: AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY, set the values that you used in Project 16. These 
credentials will be used to privision your AWS infrastructure by Terraform Cloud.

After you have set these 2 environment variables – yout Terraform Cloud is all set to apply the codes from GitHub and create all 
necessary AWS resources.
![image](https://github.com/user-attachments/assets/51031436-a6b6-48c5-87d2-1a9d51316cb1)

![image](https://github.com/user-attachments/assets/7965d668-f3dd-4a88-9003-f5263edd1544)

5. Now it is time to run our Terrafrom scripts, but in our previous project which was project 18, we talked about using Packer to
build our images, and Ansible to configure the infrastructure, so for that we are going to make few changes to our our existing 
[respository](https://github.com/melkamu372/PBL/tree/project-18) from Project 18.

**The files that would be Addedd is**
- AMI: for building packer images

- Ansible: for Ansible scripts to configure the infrastucture

**Before you proceed ensure you have the following tools installed on your local machine**;
- [packer](https://developer.hashicorp.com/packer/tutorials/docker-get-started/get-started-install-cli)

**Installing Packer and Verify the Installation**
Open a new Command Prompt Type `packer` and press **Enter**.
![image](https://github.com/user-attachments/assets/2da04723-7853-401d-9b3d-9a5150f5a4af)

- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.htm)

**Installing Ansible and Verify the Installation**
![image](https://github.com/user-attachments/assets/f97347d1-6a72-4044-b211-6e14b11fd771)


Refer to this [repository](https://github.com/darey-devops/PBL-project-19) for guidiance on how to refactor your enviroment to meet 
the new changes above and ensure you go through the README.md file.

**Install graphviz**
Graphviz is a powerful open-source tool for creating diagrams and visualizations of graph structures
```
sudo apt install graphviz
```
**Generate a Terraform Dependency Graph**
```
terraform graph -type=plan | dot -Tpng > graph.png
```
```
terraform graph | dot -Tpng > graph.png
```
![graph](https://github.com/user-attachments/assets/f8d89e97-49a3-4773-bcd3-2761b165bf26)

### Action Plan for this project
- Build images using packer

- confirm the AMIs in the console

- update terraform script with new ami IDs generated from packer build

- create terraform cloud account and backend

- run terraform script

- update ansible script with values from teraform output

   - RDS endpoints for wordpress and tooling
   - Database name, password and username for wordpress and tooling
   - Access point ID for wordpress and tooling
   - Internal load balancer DNS for nginx reverse proxy
- run ansible script

- check the website

- Install packer on your machine and input data required in each AMI created.

- Run packer build for each of the files required and confirm if the AMI's were created.

To follow file structure create a new folder and name it `AMI`
**Creating Bastion, Nginx, Tooling and Wordpress AMI Packer template**

The Packer template is a JSON or HCL file that defines the configurations for creating an AMI. Each AMI Bastion, Nginx, Tooling, and WordPress will have its own Packer template, or you can use a single template with multiple builders.
Create a files named `bastion.pkr.hcl`, `nginx.pkr.hcl`,`ubuntu.pkr.hcl` and `web.pkr.hcl`  create  packer template code for each 

for `bastion.pkr.hcl`
```
variable "aws_region" {
  type    = string
  default = "us-east-1"
}

variable "instance_type" {
  type    = string
  default = "t2.micro"
}

variable "ami_name" {
  type    = string
  default = "bastion-ami-${timestamp()}"
}

source "amazon-ebs" "bastion" {
  region           = var.aws_region
  instance_type    = var.instance_type
  source_ami       = "ami-0c55b159cbfafe1f0"
  ssh_username     = "ubuntu"
  ami_name         = var.ami_name
}

build {
  sources = ["source.amazon-ebs.bastion"]

  provisioner "shell" {
    inline = [
      "sudo apt-get update",
      "sudo apt-get install -y htop",
      # Add any additional Bastion-specific tools or configurations
    ]
  }
}

``` 
![image](https://github.com/user-attachments/assets/d137862b-420d-408a-af04-31051ab960c8)


To format a specific Packer configuration file, use the following command
```
packer fmt <name>.pkr.hcl
```
![image](https://github.com/user-attachments/assets/d8d5490d-e95c-46de-85f2-800a956d1c91)

Add ebs plugin, copy and paste this code into your Packer configuration, then run packer init
```
packer {
  required_plugins {
    amazon = {
      source  = "github.com/hashicorp/amazon"
      version = "~> 1"
    }
  }
}

```

**Initialize Plugins**
```
packer init bastion.pkr.hcl
packer init nginx.pkr.hcl
packer init ubuntu.pkr.hcl
packer init web.pkr.hcl
```

**Validate and Build the AMI**

Validate: For each template, run:
```
packer validate bastion.pkr.hcl
packer validate nginx.pkr.hcl
packer validate ubuntu.pkr.hcl
packer validate web.pkr.hcl
```
![image](https://github.com/user-attachments/assets/b8727b1b-ef23-435d-bbc6-e702868606dc)

Build the AMI: For each template, run:
```
packer build bastion.pkr.hcl
packer build nginx.pkr.hcl
packer build ubuntu.pkr.hcl
packer build web.pkr.hcl
```
**For Bastion**

![image](https://github.com/user-attachments/assets/b95b9f85-cd70-4bd5-9e5f-b29e060e9d86)
![image](https://github.com/user-attachments/assets/a3454615-3647-4b7f-a68d-7ab29fd996f8)
![image](https://github.com/user-attachments/assets/2739407e-171b-4ab2-967d-87d44388821b)
![image](https://github.com/user-attachments/assets/829c61b3-133a-4a51-b1e6-be48ce8c339f)

**For nginx**
![image](https://github.com/user-attachments/assets/2cdcdf32-eaf5-438f-b649-edc648b4ad95)
![image](https://github.com/user-attachments/assets/d42bc992-c855-4964-be6d-eaada4d43733)
![image](https://github.com/user-attachments/assets/ab66d562-2e2b-40df-b056-5f30f9033746)
![image](https://github.com/user-attachments/assets/db8162fe-ea50-44da-99a2-9adfb50caf7e)

**For ubuntu**
![image](https://github.com/user-attachments/assets/a39aba16-8df9-49b2-a40e-768f98bf02df)
![image](https://github.com/user-attachments/assets/8d9e39e5-6040-4dbc-9f4f-ea7ddfbf741b)
![image](https://github.com/user-attachments/assets/a8425bf2-5b35-4b42-bbd2-1aa6b0936fce)

**For web**
![image](https://github.com/user-attachments/assets/796f3833-aa84-4e3d-9655-b54e0c351502)

![image](https://github.com/user-attachments/assets/331371ba-896b-4367-9236-269b74bbdeea)
![image](https://github.com/user-attachments/assets/d177bafa-63bd-4ac5-8c85-e67f4446af6f)
![image](https://github.com/user-attachments/assets/383f6289-7cf9-4e0d-b113-67338c4e41d6)

**The new AMI's ID from the packer build in the terraform script**
![image](https://github.com/user-attachments/assets/a1c1ce0c-cc3d-442e-ad28-17bbc7913a0e)

**Deploy the AMIs**
Once the AMIs are built, you can deploy them in AWS by launching instances using these AMIs. You can do this through the AWS Management Console, AWS CLI, or with automation tools like Terraform.
we use terraform.
Update the AMis in the variable file terraform.auto.tfvars
```
# AMI IDs
ami_web           = "ami-0f52b35733fd9d953"
ami_bastion       = "ami-041ae799ea3b965a2"
ami_nginx         = "ami-06fc8c99aa46e502f"
```

![image](https://github.com/user-attachments/assets/2fddf73b-5e54-4a14-b44e-225ead280130)


6. Run terraform plan and terraform apply from web console

Switch to `Runs` tab and click on `Queue plan manualy` button. If planning has been successfull, you can proceed and confirm Apply
– press `Confirm and apply`, provide a comment and `Confirm plan`
![image](https://github.com/user-attachments/assets/6b6311ff-c4b1-4988-a29b-1816f7ce89aa)

![image](https://github.com/user-attachments/assets/5d89d2ec-4ddf-431d-bae2-2515d7844d69)
![image](https://github.com/user-attachments/assets/656eedfa-acd1-42e1-a34b-7b4eae44a181)
![image](https://github.com/user-attachments/assets/d609e345-ec5f-4098-9fe6-2ae578b6675c)

Check the logs and verify that everything has run correctly. Note that Terraform Cloud has generated a unique state version that you 
can open and see the codes applied and the changes made since the last run.
**aftter apply**
![image](https://github.com/user-attachments/assets/3fe8d088-84b0-48b2-8633-8f2e491962e3)

![image](https://github.com/user-attachments/assets/734a2e9f-5093-4d10-a865-e23bfbf1e6ae)

![image](https://github.com/user-attachments/assets/589962c3-257d-49fd-9b47-63c615bc604a)

![image](https://github.com/user-attachments/assets/baa0e4f2-7ced-4dd6-a431-662c0d11794a)
 
![image](https://github.com/user-attachments/assets/a4ed4296-762e-4258-8c5a-c56c0533a41d)

![image](https://github.com/user-attachments/assets/871feed3-7cee-41c1-b8c0-e591270a8bb1)

![image](https://github.com/user-attachments/assets/a33d683b-7cf8-4c18-862b-1a679c3550ac)
![image](https://github.com/user-attachments/assets/26f25f86-1427-490f-86d9-a8bd7cd6cdd7)
![image](https://github.com/user-attachments/assets/60542f71-86de-4a52-915c-2c9be59a7d98)
![image](https://github.com/user-attachments/assets/4575bbd0-8076-48e8-8406-8cc79b983885)
![image](https://github.com/user-attachments/assets/762fc008-f889-4033-ab37-a3e78c6b245f)
![image](https://github.com/user-attachments/assets/ac35e3ef-baac-4fda-be68-c6bcee65732d)
![image](https://github.com/user-attachments/assets/88cc260e-3f3f-4bd9-b0fa-90ab5fafe9c5)

7. Test automated terraform plan

By now, you have tried to launch plan and apply manually from Terraform Cloud web console. But since we have an integration with GitHub,
the process can be triggered automatically. Try to change something in any of .tf files and look at `Runs` tab again – `plan must be 
launched automatically`, but to `apply` you still need to approve manually.

**To set up automatic triggers for Terraform plans and apply operations using GitHub and Terraform Cloud, follow these steps:**

1.  Configuring a GitHub account as a Version Control System (VCS) provider in Terraform Cloud and follow steps
![image](https://github.com/user-attachments/assets/f14b123b-d346-4817-acb2-10f9d6d3a938)
![image](https://github.com/user-attachments/assets/5b061292-d9d7-46cc-b6bd-20f72163a451)

3. Verify the Webhook Integration Make a change to any Terraform configuration file (.tf file) in your repository. Commit and push the changes to the branch that is linked to your Terraform Cloud workspace.
Check Terraform Cloud:
![image](https://github.com/user-attachments/assets/66820a9e-9d0b-473f-8a8b-cc6a5ed986d8)
Go to the "Runs" tab in your Terraform Cloud workspace.
You should see that a new plan was automatically triggered as a result of your push.
Since provisioning of new Cloud resources might incur significant costs. Even though you can configure "Auto apply", it is always a
good idea to verify your plan results before pushing it to apply to avoid any misconfigurations that can cause ‘bill shock’.
![image](https://github.com/user-attachments/assets/61d51671-79bd-4fa8-a8e8-380d5f157fb7)
To do thise I use [GitHub Version Control (VCS) Workspace in Terraform Cloud](https://www.youtube.com/watch?v=75Y0B7vsBPg)

> Note: First, try to approach this project on  your own, but if you hit any blocker and could not move forward with the project, [refer
to project 19 video](https://www.youtube.com/watch?v=nCemvjcKuIA)

**Practice Task 1**

1. Configure 3 branches in your terraform-cloud repository for dev, test, prod environments
First, ensure that your repository is set up with three branches corresponding to the environments: dev, test, and prod.
Create Branches
```
git checkout -b dev
git push origin dev

git checkout -b test
git push origin test

git checkout -b prod
git push origin prod

```
![image](https://github.com/user-attachments/assets/a88107f8-c08c-4ac3-b85e-cecb34750525)

2. Make necessary configuration to trigger runs automatically only for dev environment
Create Workspaces: Create three workspaces, one for each environment (e.g., dev, test, prod).
![image](https://github.com/user-attachments/assets/d8baacee-8430-40e4-b045-2c53c057e67c)

Now let us change our prod branch and test trigger in our prod workspace
![image](https://github.com/user-attachments/assets/3ff925ae-f453-42cf-a91f-037f45b78156)
![image](https://github.com/user-attachments/assets/6b7a99af-eabd-4062-8719-2bc943b4d642)

**Configure Auto-Apply for Dev Environment Only**
Enable Auto-Apply in Dev Workspace: > Go to the dev workspace in Terraform Cloud> Navigate to Settings > General Settings > Under Execution Mode, select Auto Apply
![image](https://github.com/user-attachments/assets/6f49ad1e-528a-49e3-800f-c4280ce33c7f)

This will automatically apply the changes after a successful plan

**Disable Auto-Apply for Test and Prod:**

Go to the test and prod workspaces. Ensure that Auto Apply is disabled, so that plans require manual approval before applying.

> To do this I use  [Structuring Repositories for Terraform Workspaces](https://www.youtube.com/watch?v=IDLGpkRmDXg) 
3. Create an Email and Slack notifications for certain events (e.g. started plan or errored run) and test it
![image](https://github.com/user-attachments/assets/79c06adb-24c2-4992-b4f6-8f909e0eeb67)

**Email Notification:**
In the dev workspace, go to Settings > Notifications  > Add a new notification with the following settings >
- Type: Email
- Recipient Email: (Enter your email address)
- Triggers: Select events like Run Started, Run Completed, Run Errored, etc.

![image](https://github.com/user-attachments/assets/81318058-2c62-45a6-a152-2762f068ddc8)
![image](https://github.com/user-attachments/assets/533ca5de-c19a-4785-91f4-138700e1ec79)

**Slack Notification:**

Set up a Slack Webhook URL in your Slack workspace.
In the dev workspace, go to Settings > Notifications.
Add a new notification with the following settings:
Type: Slack
Webhook URL: (Enter your Slack Webhook URL)
Triggers: Select similar events like Run Started, Run Completed, Run Errored, etc
![image](https://github.com/user-attachments/assets/54c62bf9-2cd1-4580-a828-2e78fc6e3b16)
![image](https://github.com/user-attachments/assets/9a95c766-a1fb-4dcd-8bc5-1266880f75b3)
![image](https://github.com/user-attachments/assets/bcf7021e-0daf-4997-a362-52a21d92129d)

4. Apply destroy from Terraform Cloud web console
![image](https://github.com/user-attachments/assets/ad394c9a-39a8-4006-ab01-8d448576e029)


**Public Module Registry vs Private Module Registry**

Terraform has a quite strong community of contributors (individual developers and 3rd party companies) that along with HashiCorp 
maintain a Public Registry, where you can find reusable configuration packages (modules). We strongly encourage you to explore modules
shared to the public registry, specifically for this project – you can check out this [AWS provider registy page] (https://github.com/hashicorp/learn-private-module-aws-s3-webapp).

As your Terraform code base grows, your DevOps team might want to create you own library of reusable components – Private Registry 
can help with that.


**Practice Task No2 Working with Private repository**

1. Create a simple Terraform repository (you can clone one from [here](https://github.com/hashicorp/learn-private-module-aws-s3-webapp))
 that will be your module
![image](https://github.com/user-attachments/assets/d20b1c14-79df-42da-8cd3-59d9bb4bec91)
create atleast on release
![image](https://github.com/user-attachments/assets/a59d2f0e-b321-4780-ae47-8f489921e694)

2. Import the module into your private registry
![image](https://github.com/user-attachments/assets/7b1b1812-a0da-49c8-9263-61af75835f5f)
![image](https://github.com/user-attachments/assets/c98a36ee-30d5-48e7-b15a-84bc9878dda1)
![image](https://github.com/user-attachments/assets/6df7bdca-18dd-4b58-9d60-f9366c7fbce2)

3. Create a configuration that uses the module
- In your local machine, create a new directory for the Terraform configuration
- Create a main.tf file to use the module
```
module "s3_webapp" {
  source  = "app.terraform.io/melkamu-pbl-19/s3-bucket-static-website/aws"
  version = "1.0.0"  # Use the appropriate version
  prefix = "my-prefix"
  name   = "my-webapp-bucket"
  region = "us-east-1"
}

```
Replace your-org with your Terraform Cloud organization name.
Initialize the Configuration
```
terraform init
```
![image](https://github.com/user-attachments/assets/aa81b354-c43c-4188-84c1-9e5e85b4226d)


4. Create a workspace for the configuration Select CLI-driven workflow Name the workspace `s3-webapp-workspace`
![image](https://github.com/user-attachments/assets/c933bfa0-6dd7-4e2f-9685-f7b016755a0c)

**Configure the Workspace**:
Link the workspace to the configuration by setting the environment variable TF_WORKSPACE in your terminal:
```
export TF_WORKSPACE=s3-webapp-workspace
```

5. Deploy the infrastructure

In your terminal, execute `terraform apply` to deploy the infrastructure:
```
terraform apply
```
![image](https://github.com/user-attachments/assets/8e1effc7-c8cc-4415-8048-c0f8d03d6145)

**Verify the Deployment:**
Check the AWS console or the Terraform Cloud UI to confirm that the S3 bucket  were created successfully.
![image](https://github.com/user-attachments/assets/d9d43913-abde-471d-bcd8-ffb71e083c6e)
![image](https://github.com/user-attachments/assets/239e9bb8-cba1-4d24-afe0-9535b2fab9ab)
![image](https://github.com/user-attachments/assets/787f381b-8150-4261-b2a1-390a32636913)

6. Destroy your deployment
Once you're done, clean up the resources by running
```
terraform destroy
```
Confirm the destroy operation by typing yes
![image](https://github.com/user-attachments/assets/7bd36d90-eeb2-4d4e-acb3-3c81f526bfd4)

**Verify Destruction**:
Ensure that all resources were destroyed in the AWS console and Terraform Cloud

I use this to do [Publishing Private Modules to the Terraform Private Registry] (https://www.youtube.com/watch?v=axPsEfJSXBg&t=141s)

> Note: First, try to approach this task oun your own, but if you have any difficulties with it, refer to this [tutorial](https://developer.hashicorp.com/terraform/tutorials/modules/module-private-registry-share).

### The End of Project 19

We have learned how to effectively use managed version of Terraform – Terraform Cloud. We practiced in finding modules 
in a Public Module Registry as well as build and deploy our own modules to a Private Module Registry.

Move ahead to the next project!

