# ec2-instance-with-apache-servers
# Very good Job
How to Launch an EC2 Instance with Apache Server using AWS CLI

Introduction
AWS is the market leader and top innovator in the field of cloud computing. It helps companies with a wide variety of workloads such as game development, data processing, warehousing, archive, development, and many more. But, there is more to AWS than just the eye-catching browser console.

In this project, you will be introduced to AWS CLI(Command Line Interface). AWS CLI gives you the ability to automate the entire process of controlling and managing AWS services through scripts. These scripts makes it easy for the users to fully automate the cloud infrastructure.

Objective
In this project you will be creating an EC2 instance with Apache Webserver using AWS CLI. Instance type should be t2.micro. And you need to verify in the end that Apache Webserver is downloaded and installed using the public IP address.

Prerequisites
AWS IAM User Account with permissions to allow for AWS EC2 access
AWS CLI Installed and Configured (Refer to THIS document for help)
Vim text editor installed
Now lets get started!
Create AWS EC2 key pairs
EC2 key pairs are used to authenticate when you connect to the instance. You must provide the key pair to AWS EC2 when you first create the instance. Use the following command to create a key pair.

aws ec2 create-key-pair --key-name MyKeyPair --query 'KeyMaterial' --output text > MyKeyPair.pem
To verify that a key pair has been created, use the following command.

aws ec2 describe-key-pairs --key-name MyKeyPair

Create Security Groups for AWS EC2
AWS security groups operates as a firewall, with rules that determine what network traffic can enter and leave.

To create a security group, first you need to know the VPC(Virtual Private Cloud) ID number. Use the following command to find out the default VPC.

aws ec2 describe-vpcs
This will display VPC information as shown below:


Please make a note of this VPC ID number. You would need that ID to create security groups.

Now use the following command to create a security group.

aws ec2 create-security-group --group-name <Enter a group name> --description "Enter a description" --vpc-id vpc-0bac2b161de3f6b8a
A security group will be created as shown below:


Please make a note of the GroupId number. You would need it later on.

Add rules to the security group
Rules are added to the security group to allow incoming network traffic for your means of connecting to the image.

For example, if you want to launch a Linux instance, you would typically add a rule to allow inbound traffic on TCP port 22 to support SSH connections.

Also, you would want people to be able to access your webserver over port 80 which is HTTP.

First step is to find your IP address. Use the following command to start:

curl https://checkip.amazonaws.com
Next, enter the following command to add the rule to enable SSH to instances in the same security group:

aws ec2 authorize-security-group-ingress --group-id <group id> --protocol tcp --port 22 --cidr <ip address>
Now for port 80, enter the following command:

aws ec2 authorize-security-group-ingress --group-id <group id> --protocol tcp --port 80--cidr 0.0.0.0/0
Create a script for bootstrapping the EC2 instance
To make sure AWS installs Apache on the EC2 instance, write a bash script that will be executed when the instance is setup. Using the vim editor, open a script file by using the following command:

vim userscripts.sh
Once an empty file is open, press the “i” key enter into insert mode. Enter the following code in the script. After you are done, hit “Esc” and press “:wq” to save & exit.

#!/bin/bash
sudo yum update -y
sudo yum install httpd -y
systemctl enable httpd
systemctl start httpd
This code will first update all the packages and then install apache. Last two commands will start and enable the service.

AWS EC2 Setup Prerequisite
You would also need to select an Amazon Machine Image (AMI) to create an EC2 instance. To do that, go to THIS link and look for the machine you want to create and note the AMI ID number. You would need this to create an EC2 instance from the CLI.


Launch AWS EC2 Instance
Now you have everything to create an EC2 instance, so lets get started. Use the following command to launch your EC2 instance:

aws ec2 run-instances --image-id ami-0c02fb55956c7d316 --count 1 --instance-type t2.micro --key-name MyKeyPair --security-group-ids <security group id> --user-data file://userscripts.sh
On successful completion, use the following command to verify and grab the public IP address.

aws ec2 describe-instances --filter "Name=instance.group-name,Values='week7project-sg'"
Validate Apache
Next step is to test out the public IP address. To do that go to a web browser and paste the IP address. You should see a page as shown below:


Note: Open the Public IP address in “http” instead of “https”.

And Congratulations!! You have successfully completed the objective of this project.
