### EKS Cluster Creation on AWS Cloud

This guide will walk you through the process of creating an EKS (Elastic Kubernetes Service) Cluster on AWS Cloud.

 1. Access the EKS Console

To start, navigate to the AWS EKS console to create a new cluster.
![image](https://github.com/user-attachments/assets/b96fd1bc-700a-4304-93f6-7ce81da45f0f)
![image](https://github.com/user-attachments/assets/da36f9b6-4061-4087-9e65-bfea339c6d05)

2. Create Cluster Role

You'll need to create a service role for your EKS cluster. Follow the steps below:

2.1. Open IAM Console
- Open the IAM console: [IAM Console](https://console.aws.amazon.com/iam/)
- Navigate to **Roles** and click on **Create role**.
  
2.2. Set Up Trusted Entity
- Under **Trusted entity type**, select **AWS service**.
  ![image](https://github.com/user-attachments/assets/18fbf474-5a97-41f7-9d10-82635e94bea6)

- From the **Use cases for other AWS services** dropdown list, choose **EKS**.
- Select **EKS - Cluster** for your use case, then click **Next**.
![image](https://github.com/user-attachments/assets/2709ad4f-6d04-45bf-8dee-c7cfb8212255)
2.3. Configure Permissions
- On the **Add permissions** tab, click **Next**.
![image](https://github.com/user-attachments/assets/7e891ad7-4e10-4c86-949b-388447cd262a)

2.4. Name and Review the Role
- For **Role name**, enter `eksClusterRole`.
- For **Description**, you can enter something like `My First EKS Cluster Role`.
- Click **Create role**.

![image](https://github.com/user-attachments/assets/bffd120f-ae27-4bf2-9493-f18fa20377e6)
![image](https://github.com/user-attachments/assets/1d1167aa-4c11-4cf9-a812-4ada7684ff00)

3. Cluster Creation Process

Proceed with the cluster creation by following these steps:
![image](https://github.com/user-attachments/assets/d4ae91af-847f-4d27-afbf-081e37bfb2f0)

3.1. Specify Networking
- Choose default configurations under the **Specify Networking** section.
- Click **Next**.
![image](https://github.com/user-attachments/assets/cc116bee-474b-46ba-bab0-a997fda577d0)
3.2. Configure observability
- Disable logging to save costs.
- Click **Next**.
![image](https://github.com/user-attachments/assets/9e0f52e1-9c16-4891-a63f-411ef2ecc168)

![image](https://github.com/user-attachments/assets/399872a1-8a00-41b5-abbe-7eed4667c7c9)

3.3. Add-ons Configuration
- Select the default add-ons.
- Click **Next**.
![image](https://github.com/user-attachments/assets/c85f9ac7-4f0f-4b31-a953-cc5c5aabfaaf)

3.4. Configure selected add-ons settings
![image](https://github.com/user-attachments/assets/80cba8b8-9a14-4de7-b4ef-c1e2310c2809)

3.5. Review and Create
- Review your settings.
- Click **Create** to start the cluster creation process.
![image](https://github.com/user-attachments/assets/4c4c26eb-24ae-4f59-a44e-2cea9ee3f67f)
![image](https://github.com/user-attachments/assets/1c1b5ad9-e61e-42f0-bb28-2d797ebb21fc)

4. Add Node Group to Your Cluster

Once your cluster is created, you need to add a node group.

4.1. Node Configuration
- Begin by specifying your node configuration details.

4.2. Create a Node IAM Role
You will need to create an IAM role for your nodes. Follow these steps:

4.2.1. Open IAM Console
- Open the IAM console: [IAM Console](https://console.aws.amazon.com/iam/)
- In the left navigation pane, select **Roles**.
![image](https://github.com/user-attachments/assets/8274c6c7-832b-4cb2-bac8-ce618812e4dd)

 4.2.2. Create a New Role
- On the **Roles** page, click **Create role**.
- On the **Select trusted entity** page, choose **AWS service**.
- Under **Use case**, select **EC2**.
- Click **Next**.
![image](https://github.com/user-attachments/assets/7bc0fd13-f972-47a2-973a-633b03a8e9dc)

4.2.3. Add Permissions
- In the **Filter policies** box, enter `AmazonEKSWorkerNodePolicy`.
- Select the checkbox next to `AmazonEKSWorkerNodePolicy` in the search results.
- Clear the filters.
- In the **Filter policies** box, enter `AmazonEC2ContainerRegistryReadOnly`.
- Select the checkbox next to `AmazonEC2ContainerRegistryReadOnly`.
![image](https://github.com/user-attachments/assets/8b7acb7d-bfbb-4583-8106-577889f5ef13)

- Attach the `AmazonEKS_CNI_Policy` or an IPv6 policy if required. This policy can be attached to this role or another role mapped to the `aws-node` Kubernetes service account.
![image](https://github.com/user-attachments/assets/9c969033-24c9-437b-b0d5-3c663c5eda99)

- Click **Next**.

 4.2.4. Name and Review the Role
- For **Role name**, enter `AmazonEKSNodeRole`.
- For **Description**, you can enter `Amazon EKS - Node role`.
- (Optional) Add tags as key–value pairs for metadata.

![image](https://github.com/user-attachments/assets/6498e6bf-c833-4c7e-9b90-60d99f6533df)
![image](https://github.com/user-attachments/assets/b1aafb19-1f5b-46da-9c05-0489b9bf544e)
- Click **Create role**.
4.3. Select Node Group Role
- After the role is created, select it as your node group role.

 5. Connect to the Cluster

To manage your cluster, connect to it using AWS CLI.


