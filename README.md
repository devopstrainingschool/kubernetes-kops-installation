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
curl -LO "https://dl.k8s.io/release/$(curl -L -s 
```
```
curl -LO "https://dl.k8s.io/$(curl -L -s 
```
```
echo "$(<kubectl.sha256) kubectl" | sha256sum --check
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


