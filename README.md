# Infra-Automation
Terraform code for Ec2 creation

create S3 bucket for storing terraform state info and Dynamo DB table for providing state lock capability. 

We will try to create an EC2 instance and S3 Bucket using Terraform and Azure DevOps in AWS cloud. Look at the diagram that describes the whole flow. 

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/e22f2cc0-a579-4e5e-8fd9-1e5c36bb3532)


Pre-requisites:
Azure DevOps organization
Add Azure pipelines Terraform tasks 
Create AWS service connection in Azure DevOps for Terraform to use
Create service connection for connecting to GitHub
Create S3 bucket for storing TF state
Create dynamo DB table for providing lock capability
I have provided my public repo as an example which you can use.


Step # 1 - Create S3 Bucket:
Login to AWS, S3. Click on create S3 bucket.

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/89aea583-fa74-4a9b-a55e-8d06a25f88bb)


Give unique name to the bucket, name needs to be unique.

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/abf69fba-5832-441a-9492-4e6f17f43009)

Block all public access, enable bucket versioning as well.

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/2bb7fc8b-148d-4f78-81a5-092c59a91d2b)

Enable encryption.

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/4cb44ec5-ec49-40c2-9f81-268b06fe087f)

Step # 2 - Create DynamoDB Table
Create a new table with LockID as partition Key

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/73b4ef60-66cb-4d56-8d7e-d8aabab63db7)

Step # 3 - Create Service connection to connect to AWS from Azure DevOps
Go to Azure Devops, select your project. Project Settings

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/28962017-3d38-4b71-9ac4-39142a300f1a)

Click Service Connections

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/d5c08ba8-94f9-4fc3-be17-487a3a794ca2)

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/7191caf1-3151-4ba4-a6d3-e0afb8adf02c)

Select AWS for Terraform

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/2786f1f7-726d-462a-8335-20c71f28c386)

Enter Access Key, Secret Key and region code, enter name for service connection and choose Grant Access to all pipelines.

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/491cae7d-a176-42c4-8dbe-f866d2571b77)

Click Save.

Step 4 - Create a new Release Pipeline
Click on Releases, New, choose New Release pipeline


Select empty job. 

Click on Add Artifacts


Click on Add tasks, type terraform
choose the task

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/98c08115-f313-4f73-8d20-d0d62b5f085c)

Add task, search for install terraform and select installer task

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/e2fc3110-b296-40cd-95fd-82799d44a847)

It should show something like this:

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/690db336-1751-497f-8c1a-ff2a7a95ae74)

Add another terraform task for init

Search for terraform and add task

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/ad45d35d-e1af-4739-91a1-a8dbd169f4cb)

select AWS from drop down, choose init as command and add -reconfigure as additional command arguments. Select AWS service connection and enter bucket name


![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/7a679898-5c8a-4c2e-8e51-fbea4da88d80)

Add another task for plan, select right values.
enter -out dev-plan 

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/4579c8ea-f251-4b25-97a0-a82444d14f6b)

Add another task by cloning for apply.
enter "dev-plan" as additional arguments

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/9f29d03e-dcdf-472b-8ecf-088fa0cab312)

Click on Save. 
Create Release, now job should be running.

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/e815115f-221c-4352-8373-1e902319aa9f)

Login to AWS--> S3 Bucket, you should see terraform state info 

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/fddb6399-a486-47d0-8e03-d3dcfa21fe7c)

How to destroy all the resources created using Terraform?

Clone the current infra setup release pipeline.

![image](https://github.com/Diwakar-hub123/Infra-Automation/assets/83933859/9f156072-59dd-45ef-8fd0-af1d92a6932a)

Modify the pipeline name.

Add 
Modify the apply task to as shown in the diagram
enter -destroy as additional argument.

Click on Create Release to make sure all the resources are destroyed.

















