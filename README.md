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

<br>

## Table of Contents
+ [About](#about)
+ [Create an IAM user](#create_IAM_user)
+ [Setup an IAM role](#setup_IAM_role)   
+ [Setup VPC](#setup_VPC)
+ [Create a S3 Bucket](#create_S3)   
+ [Setup the Database](#setup_database)
+ [Setup Load Balancing and Auto Scaling](#setup_loadbalance_autoscaling)
+ [Create Auto Scaling Group](#create_auto_scaling)

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

## Create a S3 Bucket <a name = "create_S3"></a>

Here we'll create an S3 bucket where the employee photos will be stored. 

 **Creating an S3 bucket**

1. If needed, log in to the AWS Management Console with your Admin user.
2. In the search box, enter _S3_ and open the Amazon S3 console by choosing **S3**.
3. Choose **Create bucket**.
4. For **Bucket name**, enter _employee-photo-bucket-<your initials>-<unique number>_.

Example-
```html
employee-photo-bucket-al-907
```

5. Choose **Create bucket**.


 **Modifying the S3 bucket policy**

 1. Choose the Permissions tab.
 2. Scroll down to Bucket policy and choose Edit.
 3. Enter the following policy in the box-

```html
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowS3ReadAccess",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::<INSERT-ACCOUNT-NUMBER>:role/S3DynamoDBFullAccessRole"
            },
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::<INSERT-BUCKET-NAME>",
                "arn:aws:s3:::<INSERT-BUCKET-NAME>/*"
            ]
        }
    ]
}
```

4. Replace the <INSERT-ACCOUNT-NUMBER> placeholder with your account number.
5. Replace the <INSERT-BUCKET-NAME> placeholder with your bucket name.

Example-
```html
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowS3ReadAccess",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::123456789012:role/S3DynamoDBFullAccessRole"
            },
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::employee-photo-bucket-al-907",
                "arn:aws:s3:::employee-photo-bucket-al-907/*"
            ]
        }
    ]
}
```

6. Choose **Save changes**.

## Setup the database <a name = "setup_database"></a>

 **Creating the DynamoDB table**

Let us now create a database to connect our application with.

1. Return to the console, and search for and open** DynamoDB**.
2. In the navigation pane, choose **Tables**.
3. Choose **Create table** and configure the following settings. 
   * Table name: **Employees**__
   * Partition key: **id**__
4. Choose **Create table**.


Launching an EC2 instance

1. Search for and open **EC2**__.
2. In the navigation pane, choose **Instances** and choose **Launch instances**.
3. For Name use _employee-directory-app_.
4. Under Application and OS Images (Amazon Machine Image), choose the default Amazon Linux 2023.
5. Under Instance type, select t2.micro.
6. Under Key pair (login), choose **Create a new key pair**__.
7. For Key pair name, paste app-key-pair. Choose Create key pair. The required .pem file should automatically download for you.
8. Configure the following settings under Network settings and Edit.
   * VPC: app-vpc
   * Subnet: Public Subnet 1
   * Auto-assign Public IP: Enable
9. Under Firewall (security groups) choose Create security group. Use web-security-group as the Security group name and change Description to Enable HTTP access.
10. Under Inbound security groups rules choose Remove above the ssh rule.
11. Choose Add security group rule. For Type choose HTTP. Under Source type choose Anywhere.
12. Choose Add security group rule. For Type choose HTTPS. Under Source type choose Anywhere.
13. Expand Advanced details and under IAM instance profile choose S3DynamoDBFullAccessRole.
14. In the User data box, paste the following code:

```html
#!/bin/bash -ex
 wget https://aws-tc-largeobjects.s3-us-west-2.amazonaws.com/DEV-AWS-MO-GCNv2/FlaskApp.zip
 unzip FlaskApp.zip
 cd FlaskApp/
 yum -y install python3-pip
 pip install -r requirements.txt
 yum -y install stress
 export PHOTOS_BUCKET=${SUB_PHOTOS_BUCKET}
 export AWS_DEFAULT_REGION=<INSERT REGION HERE>
 export DYNAMO_MODE=on
 FLASK_APP=application.py /usr/local/bin/flask run --host=0.0.0.0 --port=80

```
Example:
This example uses a sample bucket name.
```
export PHOTOS_BUCKET=employee-photo-bucket-al-907
```

15. Choose Launch instance.
16. Choose View all instances.
17. The new instance should now be in the Instances list.
18. Wait for the Instance state to change to Running and the Status check to change to 2/2 checks passed.

## Setup the Application Load Balancer and  <a name = "setup_loadbalance_autoscaling"></a>

Here we will create the Application Load Balancer

1. Return to the Amazon EC2 console.
2. In the navigation pane, under Load Balancing, choose Load Balancers.
3. Choose Create Load Balancer.
4. On the Application Load Balancer card, choose Create.
5. Configure the following load balancer settings. 

   * Load balancer name: app-alb
   * VPC: app-vpc
   * Mappings: Select both Availability Zones
         * * Example: If you are in US West (Oregon), you would select both us-west-2a and us-west-2b
   * First Availability Zone Subnet: Public Subnet 1
   * Second Availability Zone Subnet: Public Subnet 2

6. In the Security groups section, remove the default security group (by choosing the X) and choose Create new security group.

(A new window opens for creating a security group.)

7. Configure the following security group settings:

      * Security group name: load-balancer-sg
      * Description: HTTP access
      *VPC: If needed, paste the VPC ID for app-vpc and choose it when it appears under the box
      * * Note: You can find the app-vpc ID by opening the VPC console in a new window
      *Inbound rules: Add Rule
        *   *   Type: HTTP
        *   *Source: Anywhere-IPv4

8. Choose Create security group.
9. Close the security group browser window or return to the Load balancers window.
10. For Security groups, add the new load-balancer-sg group. Note: To see the new security group, you might need to refresh the Security groups list.
11. In Listeners and routing, choose Create target group.

A new window opens for creating a target group.

12. For Specify group details, configure the following settings. 
    * Choose a target type: Keep Instances selected
    * Target group name: app-target-group
    * Health checks: Expand Advanced health check settings and configure the following:
        * * Healthy threshold: 2
        * * Unhealthy threshold: 5
        * * Timeout:30
        * * Interval: 40

13. Choose Next.
14. For Register targets, select employee-directory-app-exercise7 and choose Include as pending below.
15. Choose Create target group.
16. Close the target groups window or return to the Load balancers window.
17. Under Listeners and routing, refresh the available listener and choose app-target-group.
18. Finally, choose Create load balancer.
19. Choose View load balancer.
20. Make sure that app-alb is selected and wait for the load balancer State to become Active.
21. On the Description tab, copy DNS name and paste it into a text editor of your choice.
22. In the text editor, at the beginning of the URL, add http://.
Example:
``` http://app-elb-123456789012.us-west-2.elb.amazonaws.com ```

23. Copy the DNS name (with http:// added) and paste it into a new browser window.

You should see the employee directory application.

## Create the Auto Scaling Group <a name = "create_auto_sclaing"></a>

1. In the navigation pane, under Auto Scaling, choose Auto Scaling Groups.
2. Choose Create Auto Scaling group.
3. For Choose launch template or configuration, configure these settings: 
    * Auto Scaling group name: app-asg
    * Launch template: app-launch-template
4. Choose Next.
5. For Choose instance launch options, configure these settings:
    * VPC: app-vpc
    * Availability Zones and subnets: Choose the Availability Zones with Public Subnet 1 and Public Subnet 2
6. Choose Next.
7. For Configure advanced options, use these settings: 
   
    * Load balancing: Attach to an existing load balancer
    * Attach to an existing load balancer: Choose from your load balancer target groups
    * Existing load balancer target groups: app-target-group
    * Health checks: ELB

8. Choose Next.
9. For Configure group size and scaling policies, use these settings: 
    * Desired capacity: 2
    * Minimum capacity: 2
    * Maximum capacity: 4
    * Scaling policies: Target tracking scaling policy
    * Target value: 60
    * Instances need: 300

10. Choose Next.
11. For Add notifications, choose Add notification and configure these settings: 
    * SNS Topic: Create a topic
    * Send a notification to: app-sns-topic
    * With these recipients: Enter your email address

12. Choose Next and then choose Next again.
13. Choose Create Auto Scaling group.

You should receive an AWS Notification - Subscription Confirmation email.

14. Open this email message and choose Confirm subscription.












