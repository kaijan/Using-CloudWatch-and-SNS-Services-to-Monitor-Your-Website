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

2.15.   Click Create a new key pair, enter the Key pair name (ex. amazonec2_keypair_virginia), click **Download Key Pair**.

2.16.   Click **Launch Instance**s.

2.17.   Scroll down and click **View Instances**.

2.18.   Wait until LAMP Server shows 2/2 checks passed in the **Status Checks** column. This will take 3‐5 minutes. Use the refresh icon at the top right to check for updates.


### Connect to your instance (Windows only)

3.1. Start PuTTYgen.exe, click **Load**. By default, PuTTYgen display only files with the extension .ppk. to locate your .pem file, select the option to display files of all types.

![4.png](/images/4.png)

3.2. Select your .pem file **(ex. amazonec2_keypair_virginia.pem)**, and then click **Open**. Click **OK** to dismiss the confirmation dialog box.

3.3. Click **Save private key** to save the key in the format that PuTTY can use. PuTTYgen displays a warning about saving the key without a passphrase, click **Yes**.

3.4. Specify the same name for the key that you used for the key pair **(ex. amazonec2_keypair_virginia.ppk)**. PuTTY automatically adds the .ppk extension.

3.5. Start **PuTTY.exe**, enter **Host Name**, find host name from AWS console. (select LAMP Server, and copy the **public IP** value.).

![5.png](/images/5.png)

![6.png](/images/6.png)

3.6. On the navigation pane, click **Connect>SSH>Auth**, click **Browse** to choose your key pair (ex.**amazonec2_keypair_virginia.ppk**), click **Open**.

![7.jpg](/images/7.jpg)

3.7. Enter **ec2-user**,and press ENTER.You are now logged into your **LAMP Server** instance.

![8.png](/images/8.png)

### Connect to your instance (Linux/OSX only)

>Note: This section is for Linux and Max OSX users only. If you are running Windows but have not yet connected to your instance, go back to previous step. If you have already connected to your instance, skip ahead to next step.

4.1. To connect to your EC2 instance, run the following commands in Terminal:
	
	chmod 400  <path and name of pem>
	ssh –i <path and name of pem> ec2-user@<public IP>

* For **path and name of pem**, substitute the path/filename to the .pem file you downloaded.
* For **public IP**, substitute the public IP address for your **Web Server** instance which you copied into a text editor earlier in the lab.

### Installing and start the LAMP server on EC2

5.1.    Update all your software package, enter:

    [ec2-user ~]$ sudo yum update -y

5.2.    Use yum install command to install multiple software packages and all related dependencies at the same time.

    [ec2-user ~]$ sudo yum install -y httpd24 php70 mysql56-server php70-mysqlnd

5.3.    Start Apache web server.

    [ec2-user ~]$ sudo service httpd start
    Starting httpd: 				[ OK ]

5.4.    Use the **chkconfig** command to configure the Apache web server to start at each system boot.

    [ec2-user ~]$ sudo chkconfig httpd on
    [ec2-user ~]$ chkconfig --list httpd 

>Note: The chkconfig command does not provide any confirmation message when you successfully use it to enable a service. You can verify that **httpd** dos on by running the following command:

![9.png](/images/9.png)

5.5.    Test your web server. In a web browser, enter Public DNS address (or the public IP address) of your EC2; you should see the Apache test page.

![10.png](/images/10.png)


### Create a VPC security group for the RDS DB Instance

6.1.	In the AWS Management Console, on the service menu, click **VPC**.

6.2.	In the navigation, click **Security Groups**.

6.3.	Click **Create Security Group**.

6.4.	In the Create Security Group dialog box, enter the following details:
* Name tag: DBSecurityGroup
* Group name: DBSecurityGroup
* Description: DB instance security group
* VPC: Click My Lab VPC

6.5.	Click **Yes, Create**.

6.6.	Select **DBSecurityGroup** you just created.

6.7.	Click the **Inbound Rules** tab, and then click **Edit**.

6.8.	Create an inbound rule with the following details:
* Type: MySQL/Auora(3306)
* Protocol: TCP(6)
* Source: Paste Security Group ID of LAMPSecurityGroup
>Search LAMPSecurityGroup to find sg-xxxxxxxx
 
6.9.	Click **Save**.


### Create Private Subnets for Your Amazon RDS Instances

7.1.    In the navigation pane, click **Subnets**.

7.2.	Click Create Subnet dialog box, enter the following details:
* Name tag : Private Subnet 3
* VPC : Select My Lab VPC
* Availability Zone : us-east-1a
* CIDR block : 10.0.5.0/24

7.3.	Click **Create**.

7.4.	Click **Create Subnet**.

7.5.	In Create Subnet dialog box, enter the following details:
* Name tag : Private Subnet 4
* VPC : Select My Lab VPC
* Availability Zone : us-east-1b
* CIDR block : 10.0.6.0/24

7.6.	Click **Create**.


### Create DB Subnet Group

8.1.	In the AWS Management Console, on the service menu, click **RDS**.

8.2.	In the navigation pane, click **Subnet Groups**.

8.3.	In the navigation pane, click **Create DB Subnet Groups**.
 
8.4.	On the Create DB Subnet Group page, enter the following details:

* Name: dbsubnetgroup
* Description: Lab DB Subnet Group
* VPC ID: Click My Lab VPC

8.5.	For Add subnet, click Availability zone, choose **us-east-1a**, click subnet, choose **10.0.5.0/24**, then click **Add subnet**.

8.6.	Choose another Availability Zone **us-east-1b**, click the Availability Zone you selected for Private Subnet 4. For Subnet ID, click **10.0.6.0/24**, then click **Add subnet**.

8.7.	Click **Create**.

8.8.	If you do not see your new subnet group, click the refresh icon in the upper-right corner of the console.


### To launch a MySQL DB instance

9.1.	In the AWS Management Console, on the service menu, click **RDS**.

9.2.	In the navigation pane, choose **Instances**.

9.3.	Choose **Launch DB Instance** to start the **Launch DB Instance Wizard**. The wizard opens on the **Select Engine** page.

![11.png](/images/11.png)

9.4.	In the **Select Engine** window, click the **Next** button for the MySQL DB engine.

9.5.	Click **Use case** step. Click **Production-MySQL**. Click **Next**.

9.6.	On the **Specify DB Details** page, enter the following details:
* DB Instance Class: Choose db.t2.small—1 vCPU, 2GiB RAM.
* Multi-AZ Deployment: Click Yes.
* DB Instance Identifier: LabDBInstance
* Master Username: labuser
* Master Password: labpassword.
* Confirm  Password: labpassword

9.7.	Click **Next**.

9.8.	On the **Configure Advanced Setting**s page, enter the following details and leave all other values with their default:
* VPC: My Lab VPC
* Subnet Group: dbsubnetgroup
* Publicly Accessible: No
* VPC Security Group(s): Select existing VPC Security Group -> **DBSecurityGroup**
* Database Name: sampledb
* Encryption: **Disable Encrytion**
* Backup: Backup retention period : **0 days**
* Monitoring: **Disable enhanced monitoring**
* Maintenance : **choose Disable auto minor version upgrade**

9.9.	Click **Launch DB Instance**.

9.10.	Click **View DB Instances details**.

9.11.	Select **labdbinstance** and scroll down to **DETAILS** wait until **Endpoint** is available or modifying – this may take up to 10 minutes. Use the refresh icon in the top right corner to check for updates.

![12.png](/images/12.png)

9.12.	Copy and save the **Endpoint**, making sure to not copy the :3306 – your **Endpoint**. **Endpoint** should look similar to the following as below example: **db.choi5coyenv6.us-east-1.rds.amazonaws.com**. You will change localhost to this endpoint later.

![13.png](/images/13.png)


### Use EC2 to connect with database

10.1.	Log in to your **Lab Server** instance using SSH.

10.2.	Install MySQL tool.

    [ec2-user ~]$ sudo yum install mysql

10.3.	After check state, please key **y** to start install.
    
    Is this ok? [y/d/N]: y

10.4.   After installed MySQL, to log-in RDS server, key **mysql ‐h  [Endpoint]  –u [Username] -p**, and press enter to key **[password]**.

    [ec2-user ~]$ mysql -h	db.choi5coyenv6.us-west- 2.rds.amazonaws.com -u labuser -p

10.5.	If you log-in to RDS, you can see the **mysql>**, and you can use the database.

    mysql> select database();
    +------------+
    | database() |
    +------------+
    | NULL	|

10.6.	If you want to leave the RDS, please press **Ctrl+c** to exit.


### Create an alarm in CloudWatch and SNS service

11.1.   In the AWS Management Console, on service menu, click **CloudWatch**.

11.2.	In the navigation pane, choose **Alarms**, **Create Alarm**.

11.3.	For the Select **Metric** step, you can set up the metric.

11.4.	Choose a metric category - EC2 Metrics.

11.5.	Select an instance (LAMPServer ID) and metric - **CPUUtilization**.

![14.png](/images/14.png)

11.6.	For the Define Alarm step, you can set the alarm.

11.7.	Under Alarm Threshold, type a unique name for the alarm (for example, **myHighCpuAlarm**) and a description of the alarm (for example, **CPU usage exceeds 30 percent**).

11.8.	Under Whenever, for is, choose > and type **30**. For for, type **1**.

![15.png](/images/15.png)

11.9.	Under Additional settings, for Treat missing data as, choose **bad (breaching threshold)**, as missing data points may indicate the instance is down

11.10.	Under Actions, for Whenever this alarm, select State is ALARM. 
* For Send notification to, Click **NEW List**.
* Send notification to: my topic
* Sendemail : Your email

>You must confirm the subscription before notifications can be sent.

![16.png](/images/16.png)

11.11.	Choose Create Alarm.

11.12.	When have alarm, the mailbox will get the alarm mail which sent from AWS

11.13.	To test SNS function, Log in to your Lab Server instance using SSH.

11.14.	You can test Alarm function. Try to increase CPU Utilization to 30%. you can Log in to your **LAMP Server** though SSH.

    [ec2-user ~]$ sudo yum update -y
    [ec2-user ~]$ sudo yum install stress -y

11.15.	Burn up CPU in 120s.

    [ec2-user ~]$ sudo stress --cpu 1 --timeout 120

11.16.	If CPU alarm trggers by Cloudwatch, the email box will get the alarm mail which sent from AWS.

![17.png](/images/17.png)


## Conclusion

Congratulations! You now have learned how to:
* Logged into Amazon Management Console
* Create an Amazon Linux Instance from and Amazon Machine Image (AMI).
* Find your instance in the Amazon Management Console
* Logged into your instance
* Setup network security included subnets and security group.
* Create an Amazon Relation Database (RDS).
* Logged into your DB instance.
* Monitor service states and send SNS when alarm.
