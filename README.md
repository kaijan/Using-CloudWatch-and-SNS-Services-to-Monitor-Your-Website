# Working with EC2/EBS/RDS/CW/SNS

## Scenario
The following procedures help you install an Apache web server with PHP and MySQL support on your Amazon Linux instance. You can use this server to host a static website or deploy a dynamic PHP application that reads and writes information to a database.

Please referred to following architecture of this lab.

![1.jpg](/images/1.jpg)

## Prerequisites
>The workshop’s region will be in ‘N.Virginia’

>Download Putty and PuTTYgen: IF you don’t already have the PuTTy client/PuTTYgen installed on your machine, you can download and then launch it from here: https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html

## Lab tutorial
### Create Your VPC
1.1.    In the AWS Management Console, on the service menu, click **VPC**.

1.2.    In the navigation pane, click **Your VPCs**.

1.3.	Click **Create VPC**, enter the following details:

* Name tag: My Lab VPC
* IPv4 CIDR block: 10.0.0.0/16

1.4.	Click **Yes, Create**.

1.5.	In the navigation pane, click **Internet Gateways**.

1.6.	Click **Create Internet Gateway**, enter Name tag as **My Lab IGW**.

1.7.	Click **Create**.

1.8.	Choose **My Lab IGW**, click **action** button and choose **Attach to VPC**, and then choose **My Lab VPC** you created, click **Attach**.

1.9.	In the navigation pane, click **Subnets**.

1.10.	Click **Create Subnet**, enter the following details:

* Name tag: Public Subnet 1
* VPC: My Lab VPC
* Availability Zone : us-east-1a
* IPv4 CIDR block: 10.0.1.0/24

1.11.	Click **Create**.

1.12.	Click **Create Subnet**, enter the following details:

* Name tag: Public Subnet 2
* VPC: My Lab VPC
* Availability Zone : us-east-1b
* IPv4 CIDR block: 10.0.2.0/24

1.13.	Click **Create**.
 
1.14.	In the navigation pane, click **Route Tables**. Then choose route table of **My Lab VPC**, in the **Routes** tab, click **Edit**, click **Add another route**, enter **Destination: 0.0.0.0/0**, **Target: igw‐xxxxxxxx**. Click **Save**.

1.15.	In the **Subnet Associations** tab, click **Edit**, check both **Public Subnet 1** and **Public Subnet 2**, then click **Save**.


### Launch an instance

2.1.	In the AWS Management Console, on the service menu, click **EC2**.

2.2.	Click **Launch Instance**.

2.3.	In the navigation pane, choose Quick Start, in the row for **Amazon Linux AMI 2018.03.0 (HVM)** which ami id is ami-cfe4b2b0, click **Select**.

![2.png](/images/2.png)

2.4.    On **Step2: Choose a Instance Type page**, make sure **t2.micro** is selected and click **Next: Configure Instance Details**.

2.5.    On **Step3: Configure Instance Details page**, enter the following and leave all other values with their default:
* Network: My Lab VPC
* Subnet: Public Subnet 1
* Auto-assign Public IP: click **Enable**

2.6.    Click **Next: Add Storage**, leave all values with their default.

2.7.    Click **Next: Add Tag**.

2.8.    On **Step5: Tag Instance page**, enter the following information:

* Key: Name
* Value: LAMP Server

2.9.    Click **Next: Configure Security Group**.

2.10.   On **Setp6: Configure Security Group page**, click create a new security group, enter the following information:

* Security group name: LAMPSecurityGroup
* Description: Enable SSH, HTTP and HTTPS access

2.11.   Click **Add Rule**.

2.12.   For Type, click SSH (22), HTTP (80) and HTTPs (443).

![3.png](/images/3.png)

2.13.   Click **Review and Launch**.

2.14.   Review the instance information and click **Launch**.

2.15.   Click Create a new key pair, enter the Key pair name (ex. amazonec2_keypair_oregon), click **Download Key Pair**.

2.16.   Click **Launch Instance**s.

2.17.   Scroll down and click **View Instances**.

2.18.   Wait until LAMP Server shows 2/2 checks passed in the **Status Checks** column. This will take 3‐5 minutes. Use the refresh icon at the top right to check for updates.
