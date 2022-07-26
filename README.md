# kubernetes-kops-installation

### Kops is used to bringing up the Kubernetes cluster in the easiest possible way. It is a command-line tool used to create Kubernetes Clusters. Kops officially supports AWS where GCP, DigitalOcean, and OpenStack are in Beta. Kops can also generate Terraform files for the required cluster configuration. One can not only easily create a cluster using Kops, but also modify, delete and upgrade the Kubernetes version in the cluster.
### Pre-requisites
AWS Account(Create if you don’t have one).
EC2 Ubuntu 18.04 Instance (learn to create an EC2 instance on AWS ).
S3 Bucket (learn to create an S3 Bucket on AWS).
Domain Name (Search for "How to buy a Domain Name on AWS?" to understand the steps to create a Domain on AWS).
IAM Role with sufficient/admin permissions( learn to create an IAM role on AWS).

### What will we do?
Login to AWS.
Check the S3 Bucket, IAM Role.
Attach the IAM Role to the instance.
Install Kubectl and Kops on the EC2 instance.
Validate  Recordset rules and a hosted zone.
Create a Kubernetes Cluster using Kops.
Delete the cluster.
### Login to AWS
go to the login page where you can enter your credentials to get into the account.
![image](https://user-images.githubusercontent.com/107158398/180991166-7671111f-af9a-49ae-93ff-0f6b1b3971be.png)


Once you successfully login to your AWS account, you will see the main AWS Management Console as follows
![image](https://user-images.githubusercontent.com/107158398/180991448-34665260-3d1a-4db3-a11e-d15e94218609.png)

### Check the S3 Bucket (or create one), IAM Role
To create a cluster using Kops, we need an S3 bucket where Kops will store all the cluster configuration.
Let's create one called kops.devopstrainingschool.com
Go to services, then Storage, then click on S3

![image](https://user-images.githubusercontent.com/107158398/180992787-17bbbb4c-e00d-4e93-bc89-9484973e2137.png)

#### Clik on create bucket
![image](https://user-images.githubusercontent.com/107158398/180993491-c8fd8553-7bcc-4296-8256-ca87ddc4950e.png)
fill out the info with the bucket name and region and the click on Create
If everything is successfull, you will a similar output
![image](https://user-images.githubusercontent.com/107158398/180993991-8b176296-2cd5-42ee-bcf2-8763694e3efd.png)

### Let's create an IAM Role for KOPS
Go to services, then click on IAM 
![image](https://user-images.githubusercontent.com/107158398/180994933-a13bd077-82cf-4c01-9150-383647f1ec16.png)
Click on Role, then Create role.
![image](https://user-images.githubusercontent.com/107158398/180995218-f797895e-933a-48e4-9ab5-4c267e7a7387.png)

Select the  AWS service and EC2 under "Select trusted entity" , then click on next
![image](https://user-images.githubusercontent.com/107158398/180995564-a8ab3120-3a90-4595-ac5e-9a1630bf4dcb.png)

Under Add Permissions, select these permissions:
AmazonS3FullAccess, 
IAMFullAccess, 
AmazonRoute53FullAccess,
AmazonEC2FullAccess

Then click on next,
![image](https://user-images.githubusercontent.com/107158398/180996681-ce17a559-df80-4289-b431-bf1a120f11a6.png)



