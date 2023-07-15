<h1 align="center"> Employee Directory App | AWS Cloud </h1>

<h3 align="center"> This is a basic CRUD app bulit using Amazon Web Services such as Amazon EC2, S3, DynamoDB, CloudWatch..
 </h3> 
<hr>
<h4>-  &nbsp; The employee directory application will be hosted across multiple EC2 instances inside of a VPC in private subnets. <br>
-  &nbsp; The EC2 instances are part of an EC2 auto scaling group and traffic is being distributed across them using an Application Load Balancer. <br>
-  &nbsp; The database is being hosted on Amazon DynamoDB and the images are stored in S3 </h4> <br>


- 📍 &nbsp; As mentioned above, This is a basic CRUD app or a Create, Read, Update, Delete application
- 📍 &nbsp; Built entirely using Amazon Web Services 
- 📍 &nbsp; This app keeps track of employees working within a company.
- 📍 &nbsp; This a 3-tier application

<h1 align="center"> Here's the High-Level Architecture Diagram of the application </h1>

-------------------------
<img align="center" src="https://i.imgur.com/wL00reJ.png">

# Employee Directory - Amazon Web Services

## Lab Overview And Architecture

<img align="center" src="https://github.com/mohd-jauwad/AWS-PowerOfMath/blob/master/Images/architecture.png?raw=true">
<br>

## Table of Contents
+ [About](#about)
+ [Create an IAM user](#create_IAM_user)
+ [Setup an IAM role](#setup_IAM_role)   
+ [Setup VPC](#setup_VPC)
+ [Setup Amazon DynamoDB](#setup_Amazon_DynamoDB_IAM)   

## About <a name = "about"></a>
In this project, we'll be using 5 AWS Services as below to build an end to end web application. 

* AWS IAM
* AWS Lambda
* Amazon API Gateway
* Amazon DynamoDB
* AWS Identity & Access Management (IAM)
  
This will be a super simple application but  it ties together all the components you need to build a much larger real world application.  This  takes two difffernet numbers, a base and an exponent, and it is going to return the result the base to the power of the exponent. The reuslt would also be saved in the DynamoDB table in the backend 

## Create an IAM user <a name = "create_IAM_user"></a>

**Creating an IAM user with administrator access**

1. In the "**Services**" search box, enter IAM, and open the **IAM** console.

2. In the navigation pane, choose **Users**.
3. Choose **Add users **and in the **Set user details** page, configure the following settings.
   * User name: Admin
   * Select AWS credential type:
   * * Access key - Programmatic access
     * Password - AWS Management Console access
   * Console password: Custom password and enter a password of your choosing
   * Require password reset: Clear this option
        
4. Choose **Next: Permissions**.
5. In the **Set permission** page, choose **Attach existing policies directly**.
6. In the **Filter policies** box, search for **administrator**.
7. Under **Policy name**, select **AdministratorAccess**.
8. Choose **Next: Tags**, and then choose **Next: Review**.
9. Choose **Create user**.
10. You can sign in with the new IAM admin user by choosing the URL at the bottom of the **Success** window.
    
Note: The sign-in URL should look like the following: https://123456789012.signin.aws.amazon.com/console.

11. Log in to the console with the **Admin** user and password that you created.


## Setup an IAM role<a name = "setup_IAM_role"></a>

**Setting up an IAM role for an EC2 instance**

Here, we will log in as the _**Admin**_ user and create an IAM role. 
The role allows Amazon Elastic Compute Cloud (Amazon EC2) to access both Amazon Simple Storage Service (Amazon S3) and Amazon DynamoDB. 
We will later assign this role to an EC2 instance that hosts the employee directory application.

1. Now that you are logged in as the Admin user, use the **Services** search bar to search for **IAM** again, and open the service by choosing **IAM**.
2. In the navigation pane, choose **Roles**.
3. Choose **Create role**.
4. In the **Select trusted entity** page, configure the following settings. 
   * **Trusted entity type**: _AWS service_
   * **Use case**: _EC2_
5. Choose **Next**.
6. In the permissions filter box, search for _amazons3full_, and select **AmazonS3FullAccess**.
7. In the filter box, search for _amazondynamodb_, and select **AmazonDynamoDBFullAccess**.
8. Choose **Next**.
9. For **Role name**, paste _S3DynamoDBFullAccessRole_ and choose **Create role**. 


## Setup VPC <a name = "setup_VPC"></a>
Here, we create the underlying network infrastructure where the EC2 instance that hosts the employee directory will live.

We will go ahead and set up a new virtual private cloud (VPC). This new VPC will have four subnets (two public subnets and two private subnets) and two route tables (one public route table and one private route table). 

**Creating the VPC**

1. In the Services search box, enter VPC and open the VPC console by choosing VPC from the list.
2. In the navigation pane, under **Virtual private cloud**, choose **Your VPCs**.
3. Choose **Create VPC**.
4. Configure these settings: 
   * **Name tag**: _app-vpc_
   * **IPv4 CIDR block**: _10.1.0.0/16_
5. Choose **Create VPC**.
6. In the navigation pane, under **Virtual private cloud**, choose **Internet gateways**
7. Choose **Create internet gateway**.
8. For **Name tag**, paste _app-igw_ and choose **Create internet gateway**.
9. In the details page for the internet gateway, choose **Actions** and then choose **Attach to VPC**.
10. For **Available VPCs**, choose app-vpc and then choose **Attach internet gateway**.

**Creating subnets**

Here we will create the four subnets for our VPC. Two public subnets and two private subnets.
1. From the navigation pane, choose **Subnets**
2. Choose **Create subnet**
   
3. For the first public subnet, configure these settings:
   * **VPC ID**: _app-vpc_
   * **Subnet name**: _Public Subnet 1_
   * **Availability Zone**: Choose the first Availability Zone
   * * Example: If you are in US West (Oregon), you would choose us-west-2a
   * **IPv4 CIDR block**: _10.1.1.0/24_
     
4. Choose **Add new subnet**.
   
5. For the second public subnet, configure these settings:
   * **Subnet name**: _Public Subnet 2_
   * **Availability Zone**: Choose the second Availability Zone
   * * Example: If you are in US West (Oregon), you would choose us-west-2b
   * **IPv4 CIDR block**: _10.1.2.0/24_
     
6. Choose **Add new subnet** and for the first private subnet, configure these settings:
   * **Subnet name**: _Private Subnet 1_
   * **Availability Zone**: Choose the first Availability Zone 
   * * Example: If you are in US West (Oregon), you would choose us-west-2a
   * **IPv4 CIDR block**: _10.1.3.0/24_
     
7. Choose **Add new subnet** and for the second private subnet, configure these settings:
   * **Subnet name**: _Private Subnet 2_
   * **Availability Zone**: Choose the second Availability Zone 
   * * Example: If you are in US West (Oregon), you would choose us-west-2b
   * **IPv4 CIDR block**: _10.1.4.0/24_

8. Finally, choose **Create subnet**.
9. After the subnets are created, select the check box for **Public Subnet 1**
10. Choose **Actions** and then choose **Edit subnet settings**.
11. For **Auto-assign IP settings**, select **Enable auto-assign public IPv4 address** and then choose **Save**.
12. Clear the check box for **Public Subnet 1** and select the check box for **Public Subnet 2**.
13. Again, choose **Actions** and then **Edit subnet settings**.
14. For** Auto-assign IP settings**, select **Enable auto-assign public IPv4 address** and save the settings.

 **Creating route tables**
 
Here we will create the route tables for our VPC.

First, let us create the public route table

1. In the navigation pane, choose **Route Tables**.
2. Choose **Create route table**.
3. For the route table, configure these settings: 
   * **Name**: _app-routetable-public_
   * **VPC**: _app-vpc_
4. Choose **Create route table**.
5. If needed, open the route table details pane by choosing **app-routetable-public** from the list.
6. Choose the **Routes** tab and choose **Edit routes**.
7. Choose **Add route** and configure these settings:
   * **Destination**: 0.0.0.0/0
   * **Target**: Internet Gateway, then choose app-igw (which you set up in the VPC task)
8. Choose **Save changes**.
9. Choose the **Subnet associations** tab.
10. Scroll to **Subnets without explicit associations **and choose **Edit subnet associations**.
11. Select the two public subnets that you created (**Public Subnet 1** and **Public Subnet 2**) and choose **Save associations**.

Now, let's create the private route table

1. In the navigation pane, choose **Route Tables**.
2. Choose **Create route table**.
3. For the route table, configure these settings: 
   * **Name**: _app-routetable-private_
   * **VPC**: _app-vpc_
4. Choose **Create route table**.
5. If needed, open the details pane for **app-routetable-private** by choosing it from the list.
6. Choose the **Subnet associations** tab.
7. Scroll to **Subnets without explicit associations** and choose **Edit subnet associations**.
8. Select the two private subnets (**Private Subnet 1** and **Private Subnet 2**) and choose **Save associations**.


