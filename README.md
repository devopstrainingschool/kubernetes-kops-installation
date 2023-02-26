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
AdministratorAccess

Then click on next,
![image](https://user-images.githubusercontent.com/107158398/180996681-ce17a559-df80-4289-b431-bf1a120f11a6.png)


Name the role kops and finally click on create role.
### Let's create a EC2 Instance using Ubuntu 20.04 flavor
![image](https://user-images.githubusercontent.com/107158398/180997566-17c88886-ccac-44b6-be42-ab8adcf16aa2.png)

Make sure to update the inbound in the security group
### Attach the IAM Role to the instance
Once you have the role, attach it to the EC2 instance you will use to execute the kops commands. Go to EC2 --> select the EC2 instance --> click on Actions --> Security -- > Modify IAM role.
![image](https://user-images.githubusercontent.com/107158398/180998369-711f12ae-881d-4455-b640-9c07003f7777.png)
Select the  IAM role we created early (kops) and save the changes.
![image](https://user-images.githubusercontent.com/107158398/180998795-9b94c7bc-ef74-4cc4-9375-ddb5641ac4a0.png)


## Install Kubectl and Kops on the EC2 instance
Till this point, you have an S3 bucket and an EC2 instance with the required role attached to it. Now login into the EC2 instance you will use to create a cluster using Kops.

The next step is to install Kubectl on the EC2 instance.

Execute the following commands to install kubectl on Ubuntu Server

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

```
curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
```
```
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
```
```
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```
```
mkdir -p ~/.local/bin/kubectl
```
```
mv ./kubectl ~/.local/bin/kubectl
```
Check the kubectl version using the following command
```
kubectl version --client
```
Now you are ready to install Kops on the same EC2 instance.

Check if the kops exists, if not then install it using the following commands on Ubuntu Server.
```
kops
```
```
curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
```
```
chmod +x kops-linux-amd64
```
```
sudo mv kops-linux-amd64 /usr/local/bin/kops
```
Now you should have kops on the server.
```
kops
```
The output should be similar to this:
![image](https://user-images.githubusercontent.com/107158398/181126963-992fd8d8-71a9-4303-9f5c-7f9815273d0f.png)

## Validate  Recordset rules and a hosted zone.
Kops needs the required DNS records to build a cluster. 
### Go to Services , then Route 53 , then hosted zones, and click on create hosted zone
#### Domain: kops.devosptrainingschool.com
#### Type: Private hosted zone , and region: ohio and , VPC ID the default one
#### click one create hosted zone
![image](https://user-images.githubusercontent.com/107158398/181127832-5e45ea1f-6432-4f24-937b-b3185b8ccf3e.png)
#### This is the output
![image](https://user-images.githubusercontent.com/107158398/181127959-87a50290-9b92-4d55-ad42-9eeb4378da03.png)

## Create SSH Keys
Create ssh keys on Ubuntu instance to exchange kubernetes cluster and connect
```
ssh-keygen 
```
## Install AWS CLI
```
apt-get update
```
### Install AWS CLI
```
sudo apt-get install awscli
```
### check the version
```
aws --version
```
## Configure the AWS configure
### Create and configure IAM User in AWS
Create IAM user in AWS using login console -> IAM -> ADD User -> Username -> Select Programmatic access -> select AdministratorAccess , then you will get Access Key ID and Secret Access Key.

Configure AWS CLI with your Access Key ID,  Secret Access  key and region
![image](https://user-images.githubusercontent.com/107158398/181133981-6079cf37-89ae-41ae-bbdb-0c639a0f686f.png)

```
aws configure
```
### Access key:put your access key
### Secret key : put the secet key
### Region: us-east-2
### Default output format: (press enter only)
![image](https://user-images.githubusercontent.com/107158398/181129977-552aad19-0c2b-4eb2-abd4-9b3979517001.png)




## Create a Kubernetes Cluster using Kops
Now we are all set to create a cluster. Before creating a cluster, let's see what we get when we try to list the clusters.
```
kops get clusters
```
The above command will fail as it needs an S3 bucket as a parameter.
```
kops get clusters --state s3://kops.devopstrainingschool.com
```
### Add Environment variables
```
export KOPS_STATE_STORE=s3://kops.devopstrainingschool.com
```
This time you don't need to specify the S3 bucket in the command
```
kops get clusters
```
###  The output should be similar to this
![image](https://user-images.githubusercontent.com/107158398/221391604-c03247f0-ad96-4e92-b334-42ec61672022.png)

## RUN this command to install KOPS Cluster
```
kops create cluster --cloud=aws --zones=us-east-2b --name=kops.devopstrainingschool.com --dns-zone=kops.devopstrainingschool.com --dns private
```
the --zones=make sure it is the same as where you create your EC2
Finally configure your cluster with:
```
kops update cluster --name kops.devopstrainingschool.com --yes --admin
```
Suggestions:
 * validate cluster: 
 ```
 kops validate cluster --wait 10m
 ```
 * list nodes:
 ```
 kubectl get nodes --show-labels
 ```
 If in the future , you want to connect (ssh) to the master , run this command:
 ```
 ssh -i ~/.ssh/id_rsa ubuntu@api.kops.devopstrainingschool.com
 ```
 

# Check that all is good
## Check the nodes
```
kubectl get nodes
```
![image](https://user-images.githubusercontent.com/107158398/181138709-870c5d85-0004-4be3-ad7f-f8e0809b8c8b.png)

## Let run of second app
```
kubectl run my-app --image=devopstrainingschool/java-maven-jenkins --port=8080
```
## add the service to access it from the browser
```
kubectl expose pod my-app --type=NodePort --port=8080 --target-port=8080
```
## check that the services are up and running
```
kubectl get svc -A
```

# WHEN you are done , please delete the cluster by running these commands:
 ## run this command to get the cluster name:
 ```
 kops validate cluster
 ```
 ![image](https://user-images.githubusercontent.com/107158398/181135171-52fc3a09-cd75-45ba-8547-93c7b8e4cbfa.png)

## run this command to delete it
```
kops delete cluster kops.devopstrainingschool.com --yes
```
## check that the cluster is deleted
```
kops validate cluster
```
# Thank you

 
