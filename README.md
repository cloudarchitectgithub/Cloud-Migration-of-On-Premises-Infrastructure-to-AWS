# Cloud Migration of On-Premises Infrastructure to AWS

<p align="center">
  <img src="https://i.imgur.com/V1hkN05.png" height="80%" width="80%" alt="Cloud Migration Banner"/>
</p>

## Project Description
This project focuses on migrating an on-premises infrastructure to AWS, transforming the existing environment into a scalable and cost-effective cloud solution. The primary aim is to move the application server to an Amazon EC2 instance and the database server to Amazon RDS, while adhering to best practices, such as implementing Virtual Private Clouds (VPCs), securing resources, and following a methodical, phased migration process. This hands-on project simulates a real-world cloud migration, showcasing each step, from planning to final migration, ensuring the infrastructure is successfully migrated, validated, and fully operational on AWS.

## Project Objectives
- **Migrate an on-premises application and database server to AWS using Amazon EC2 and RDS.**
- **Design and implement a secure cloud infrastructure with VPCs and subnets.**
- **Ensure smooth transition by following best practices for cloud migration, including validation, dry runs, and final cutover.**
- **Enable scalability and future-proofing for cloud-native operations.**
- **Provide a detailed understanding of cloud migration, focusing on automation, security, and infrastructure deployment.**

## Tools and Technologies
- **AWS Services:** Amazon EC2, Amazon RDS, Amazon VPC, AWS CLI, Amazon S3.
- **Database:** MySQL (RDS).
- **Operating System:** Ubuntu 18.04 for the EC2 instance.
- **Other Tools:** Git Bash, SSH, and MySQL Client for database management.

---

## Planning Phase
The planning phase is crucial in ensuring a successful migration. In this phase, we outlined the sizing of the infrastructure, identifying the CPU, memory, and storage needs for both the application and database servers. We used a sizing spreadsheet to map on-premises server specifications to the appropriate AWS instance types. Prerequisites such as operating system, software versions, and network configurations were also considered.

- **Tools Used:** Spreadsheets, AWS instance types documentation.
- **Sizing:** Mapped on-premises environment to AWS instances, such as using `t2.micro` for both application and database servers.
- **Prerequisites:**
  - EC2 instance should have Python 3.5+ installed with required libraries.
  - RDS must use MySQL 5.7.30, matching the on-premises environment.

---

## Part 1: Deployment - Creating EC2 and RDS Instances

### Step 1: Create VPC and Subnets
1. Log in to AWS Management Console.
2. Navigate to VPC Dashboard.
3. Click "Create VPC."

<p align="center">
  <img src="https://i.imgur.com/16CWsiR.png" height="80%" width="80%" alt="Create VPC Screenshot"/>
</p>

- **VPC Name:** `vpc-bootcamp`
- **CIDR Block:** `10.0.0.0/16`

<p align="center">
  <img src="https://i.imgur.com/LZX0hM1.png" height="80%" width="80%" alt="VPC Configuration Screenshot"/>
</p>

<p align="center">
  <img src="https://i.imgur.com/sHAMqGP.png" height="80%" width="80%" alt="Public Subnet Screenshot"/>
</p>

4. In "Subnets," click "Create Subnet" for each:

#### Public Subnet:
- **Name:** `Public Subnet`
- **VPC:** `vpc-bootcamp`
- **Availability Zone:** `us-east-1a`
- **CIDR Block:** `10.0.0.0/24`

<p align="center">
  <img src="https://i.imgur.com/rFexpks.png" height="80%" width="80%" alt="Public Subnet Screenshot"/>
</p>

#### Private Subnet 2:
- **Name:** `Private Subnet`
- **VPC:** `vpc-bootcamp`
- **Availability Zone:** `us-east-1a`
- **CIDR Block:** `10.0.1.0/24`

<p align="center">
  <img src="https://i.imgur.com/Oh2RR9Q.png" height="80%" width="80%" alt="Private Subnet 2 Screenshot"/>
</p>

#### Private Subnet 3:
- **Name:** `Private Subnet`
- **VPC:** `vpc-bootcamp`
- **Availability Zone:** `us-east-1b`
- **CIDR Block:** `10.0.2.0/24`

<p align="center">
  <img src="https://i.imgur.com/KyntGEB.png" height="80%" width="80%" alt="Private Subnet 3 Screenshot"/>
</p>

<p align="center">
  <img src="https://i.imgur.com/YZP1Hl7.png" height="80%" width="80%" alt="Internet Gateway Screenshot"/>
</p>
---

### Step 2: Create Internet Gateway
1. In VPC Dashboard, click "Create Internet Gateway."

<p align="center">
  <img src="https://i.imgur.com/60jjPkS.png" height="80%" width="80%" alt="Internet Gateway Screenshot"/>
</p>

2. **Name:** `IGW-production`
3. Click "Create Internet Gateway."
4. Attach Internet Gateway to VPC:

<p align="center">
  <img src="https://i.imgur.com/eesrKZ0.png" height="80%" width="80%" alt="Internet Gateway Screenshot"/>
</p> 

   - Choose Internet Gateway: `IGW-production`
   - Choose VPC: `vpc-bootcamp`
   - Click "Attach to VPC."

---

### Step 3: Create Route Table
1. In VPC Dashboard, click "Route Tables."

<p align="center">
  <img src="https://i.imgur.com/MARkWKx.png" height="80%" width="80%" alt="Route Table Screenshot"/>
</p>

2. Click "Create Route Table."
   - **Name:** `Public-RT`
   - **VPC:** `vpc-bootcamp`
   
3. In "Routes" tab, click "Edit Routes."

<p align="center">
  <img src="https://i.imgur.com/IemYFLm.png" height="80%" width="80%" alt="Route Table Screenshot"/>
</p>

   - Add Route:
     - **Destination:** `0.0.0.0/0`
     - **Target:** `IGW-production`
   - Save routes.
---

### Step 4: Provision EC2 Instance for Application Hosting
1. Navigate to the EC2 Dashboard.

<p align="center">
  <img src="https://i.imgur.com/HAOfdtB.png" height="80%" width="80%" alt="Route Table Screenshot"/>
</p>

2. Check if there are any existing instances running. If present, terminate them if not needed.
3. Click on "Launch Instances" to create a new EC2 instance.

<p align="center">
  <img src="https://i.imgur.com/E4vgYRC.png" height="80%" width="80%" alt="Launch EC2 Screenshot"/>
</p>

4. In the "Choose an Amazon Machine Image (AMI)" step:
   - Search and select "Ubuntu 18.04 LTS" (or the specified version).

<p align="center">
  <img src="https://i.imgur.com/ausIzNT.png" height="80%" width="80%" alt="Choose AMI Screenshot"/>
</p>

5. In the "Choose an Instance Type" step:
   - Choose "t2.micro" or the specified instance type.

<p align="center">
  <img src="https://i.imgur.com/itJKF5q.png" height="80%" width="80%" alt="Choose Instance Type Screenshot"/>
</p>

6. In the "Configure Instance Details" step:
   - Enter "AWS-US-E1-app01" (or the specified hostname) in the "Hostname" field.
   - Choose the VPC "vpc-bootcamp."
   - Select the public subnet created earlier.
   - Enable "Auto-assign Public IP" to allow internet access.

7. In the "Add Storage" step:
   - Keep the default storage size (8 GB) or adjust as needed.

8. Skip the "Add Tags" step for now.

9. In the "Configure Security Group" step:
   - Create a new security group:
     - **Security Group Name:** `app-sg01`
     - **Description:** `app-sg01`
     - Keep the default SSH rule (port 22) if necessary for your environment.
   
<p align="center">
  <img src="https://i.imgur.com/4kiR2e7.png" height="80%" width="80%" alt="Security Group Screenshot"/>
</p> 

   - Add a custom TCP rule:
     - **Type:** Custom TCP Rule
     - **Port Range:** `8080`
     - **Source:** Anywhere (`0.0.0.0/0`)

<p align="center">
  <img src="https://i.imgur.com/uRkM2VJ.png" height="80%" width="80%" alt="Security Group Screenshot"/>
</p>

10. Review your configuration and click "Launch."
11. Select "Create a new key pair," enter a name (e.g., "SSH-key"), and download the private key file (.pem). Store it in a secure location.
12. Click "Launch Instances."

---

### Step 5: Creating Amazon RDS Database Instance
1. Go to the AWS Console and search for "RDS."

<p align="center">
  <img src="https://i.imgur.com/E1HeyPZ.png" height="80%" width="80%" alt="RDS MySQL Screenshot"/>
</p>

2. Click on "Databases" to ensure no instances are currently running.
3. Click "Create Database."
4. Choose "Standard Create."
5. Select the MySQL engine with version 5.7.30.

<p align="center">
  <img src="https://i.imgur.com/EPaBnlJ.png" height="80%" width="80%" alt="RDS MySQL Screenshot"/>
</p>

6. Choose the "Free Tier" template.
7. Set the DB instance identifier as "awsuse1db01."
8. Set the master username as "admin" and password as "admin123456."
9. Choose DB instance class as "db.t2.micro."
10. Configure connectivity:
    - Choose the VPC you created.

<p align="center">
  <img src="https://i.imgur.com/impzpcf.png" height="80%" width="80%" alt="RDS Connectivity Screenshot"/>
</p> 

    - Create a new DB subnet group for the selected VPC.
    - Choose "No" for public accessibility.

<p align="center">
  <img src="https://i.imgur.com/NvHtrRZ.png" height="80%" width="80%" alt="RDS Connectivity Screenshot"/>
</p>   

    - Create a new security group allowing port 3306.

11. Review the configuration and click "Create Database."

<p align="center">
  <img src="https://i.imgur.com/7FPtOhs.png" height="80%" width="80%" alt="RDS Review Screenshot"/>
</p>

---

### Step 6: Installing and Setting up the Packages and Libraries for the App and DB Connection
1. **Connecting to the VM - EC2:**
   - Install Git Bash from [https://git-scm.com/downloads](https://git-scm.com/downloads).
   - Ensure the private key downloaded during EC2 instance creation is in the AWS folder on your desktop.

2. **Remote Connectivity - SSH:**
   - Open Git Bash.
   - Navigate to the AWS folder using `cd ~/Desktop/AWS`.
   - Copy the public IP address of the EC2 instance.
   - Run SSH command in Git Bash:

<p align="center">
  <img src="https://i.imgur.com/EGuuAqJ.png" height="80%" width="80%" alt="SSH Connection Screenshot"/>
</p>

3. **Updating Operating System:**
   - Run the following command to update the operating system:

<p align="center">
  <img src="https://i.imgur.com/GEefqWY.png" height="80%" width="80%" alt="Update OS Screenshot"/>
</p>

4. **Installing Packages:**
   - Install required packages using the following commands:

<p align="center">
  <img src="https://i.imgur.com/0HfZlCx.png" height="80%" width="80%" alt="Install Packages Screenshot"/>
</p>

5. **Installing Python Libraries:**
   - Run the following commands to install Python libraries:

<p align="center">
  <img src="https://i.imgur.com/NoXMxy9.png" height="80%" width="80%" alt="Install Python Libraries Screenshot"/>
</p>

6. **Installing AWS CLI:**
   - Install AWS CLI with the command:

<p align="center">
  <img src="https://i.imgur.com/hZqbcKI.png" height="80%" width="80%" alt="Install AWS CLI Screenshot"/>
</p>

7. **Installing MySQL Client:**
   - Install MySQL client with the command:

<p align="center">
  <img src="https://i.imgur.com/5wEIjaD.png" height="80%" width="80%" alt="Install MySQL Client Screenshot"/>
</p>

We have the necessary files downloaded: the application deployment file and the database dump file from the on-premise environment. We have configured the prerequisite for the EC2 instance to deploy the application. 
---

## Cloud Migration Go-Live Instructions

### Search for Security Group for RDS
1. Navigate to [VPC | Security Group].

<p align="center">
  <img src="https://i.imgur.com/WDzKyHs.png" height="80%" width="80%" alt="Security Group for RDS Screenshot"/>
</p>

2. **Description:** SG to allow access to MySQL by the application running at EC2.
3. **VPC:** `vpc-bootcamp`
4. **Inbound Rules | Add Rule | Type:** `MYSQL/Aurora` | **Destination:** `0.0.0.0/0`

<p align="center">
  <img src="https://i.imgur.com/5Z6didQ.png" height="80%" width="80%" alt="Security Group for RDS Screenshot"/>
</p>

### Associating the SG (EC2toRDS-sg) to the RDS Instance (awsuse1db01)
1. Navigate to RDS > DB Instances > `awsuse1db01` > Modify.
2. **Connectivity | SG:** `EC2toRDS-sg`
3. Click "Continue... Apply immediately" and "Modify DB instance."

<p align="center">
  <img src="https://i.imgur.com/GbXeE8g.png" height="80%" width="80%" alt="Associate SG to RDS Screenshot"/>
</p>

---

## Import Database Data to RDS
1. **Connect to the EC2 Instance:**
   - Open a terminal and use SSH to connect to your EC2 instance.

<p align="center">
  <img src="https://i.imgur.com/Um9xJnW.png" height="80%" width="80%" alt="SSH to EC2 Screenshot"/>
</p>

2. **Downloading the Application and the 'Dump' Files:**

<p align="center">
  <img src="https://i.imgur.com/xf8z3rh.png" height="80%" width="80%" alt="Download Files Screenshot"/>
</p>

3. **Connect to RDS MySQL Database:**
   - Retrieve the RDS endpoint from the AWS console (RDS > Instances).
   - Access MySQL on RDS:

<p align="center">
  <img src="https://i.imgur.com/KeCiTao.png" height="80%" width="80%" alt="Connect to RDS Screenshot"/>
</p>

4. **Import Database Dump:**

<p align="center">
  <img src="https://i.imgur.com/MGADBTQ.png" height="80%" width="80%" alt="Import Database Dump Screenshot"/>
</p>

5. **Create a New Database User:**
   - Create a user for application connectivity:

<p align="center">
  <img src="https://i.imgur.com/F9NkIhG.png" height="80%" width="80%" alt="Create DB User Screenshot"/>
</p>

6. **Exit MySQL client:** `exit`
7. **Run the source command to import data from the dump file:**

<p align="center">
  <img src="https://i.imgur.com/EKmZsc9.png" height="80%" width="80%" alt="Import Data Screenshot"/>
</p>

Creating a DB 'wikidb' and importing data to it:
- show databases;
- create database wikidb;
- show databases;
- use wikidb;
- show tables;
- source dump.sql;
- show tables;
- select * from articles;
Creating an user 'wiki' in the "wikidb"

<p align="center">
  <img src="https://i.imgur.com/Sl65KoR.png" height="80%" width="80%" alt="Validation Screenshot"/>
</p>

- Unzipping the application's file
- unzip wikiapp.zip
- Editing the file 'wiki.py'
- cd wikiapp/
- vi wiki.py
- ► session '# Config MySQL'
- 'MYSQL_HOST' = 'awsuse1db01.cfhprbugweo6.us-east-1.rds.amazonaws.com' 
- 'MYSQL_USER' = 'wiki'
- Loading the application:

<p align="center">
  <img src="https://i.imgur.com/H54Md4e.png" height="80%" width="80%" alt="Validation Screenshot"/>
</p>

---

## Validation and Final Migration (Go-Live)
After deploying and configuring the infrastructure, the next steps involved the go-live process, which consists of two key stages: validation (dry run) and final migration (cutover).

- **Dry Run:** Performed all migration steps without shutting down the on-premises environment, ensuring the process was smooth and error-free.
- **Cutover:** Final migration involved:
  1. Exporting the database and application deployment files from the on-premises environment to an S3 bucket.
  2. Importing the deployment files and database dump into the AWS environment.
  3. Validating connectivity between the EC2 instance (hosting the application) and RDS instance (hosting the database).
  4. Testing the application to confirm both read and write access to the database.

<p align="center">
  <img src="https://i.imgur.com/z8x69Zd.png" height="80%" width="80%" alt="Validation Screenshot"/>
</p>

- Copy the Public IP of your EC2 instance and add to it the port "8080":
- <PUBLIC_IP_OF_YOUR_EC2>:8080

<p align="center">
  <img src="https://i.imgur.com/IPjxOEP.png" height="80%" width="80%" alt="Validation Screenshot"/>
</p>

- Login: admin / admin

<p align="center">
  <img src="https://i.imgur.com/kagzeI3.png"80%" width="80%" alt="Validation Screenshot"/>
</p>

---

## Project Conclusion
This cloud migration project successfully moved an on-premises application and database server to AWS. The application is now hosted on an EC2 instance, while the database runs on an Amazon RDS instance. The infrastructure was deployed with best practices in place, including the use of VPCs, security groups, and AWS CLI for efficient management. Post-migration, the application was validated to function seamlessly, ensuring data integrity and connectivity between the application and database servers.

---

## Challenges Encountered
1. **Sizing Mismatch:** During the planning phase, ensuring the correct mapping of on-premises resources to AWS instances was critical but required careful evaluation.
2. **Security Group Configuration:** Modifying security group rules for optimal security without sacrificing connectivity posed a challenge, especially when allowing access between EC2 and RDS.

---

## Lessons Learned
- **Thorough Planning is Key:** Accurate sizing and prerequisite mapping help avoid complications during implementation.
- **Automation and Best Practices:** Leveraging AWS CLI for file uploads and following AWS’s well-architected framework significantly improved efficiency and security.
- **Importance of Validation:** Running a dry run before the final migration ensured a smooth cutover with minimal downtime.

---

## Future Improvements
- **High Availability:** The current setup could be improved by incorporating high availability, such as deploying RDS instances in multiple availability zones.
- **Scaling:** Explore adding an autoscaling group for the EC2 instance to handle variable traffic demands in a production environment.
- **Automation:** Implement AWS CloudFormation templates to automate the deployment process for faster and repeatable migrations.
