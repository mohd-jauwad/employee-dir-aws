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


<!--
Task 3: Creating an IAM user

In this task, you will create an IAM user with administrator access.

    In the Services search box, enter IAM, and open the IAM console.

    In the navigation pane, choose Users.
    Choose Add users and in the Set user details page, configure the following settings.
        User name: Admin
        Select AWS credential type:
            Access key - Programmatic access
            Password - AWS Management Console access
        Console password: Custom password and enter a password of your choosing
        Require password reset: Clear this option

    Choose Next: Permissions.

    In the Set permission page, choose Attach existing policies directly.

    In the Filter policies box, search for administrator.

    Under Policy name, select AdministratorAccess.

    Choose Next: Tags, and then choose Next: Review.

    Choose Create user.

    You can sign in with the new IAM admin user by choosing the URL at the bottom of the Success window.

    Note: The sign-in URL should look like the following: https://123456789012.signin.aws.amazon.com/console.

    Log in to the console with the Admin user and password that you created.

Task 4: Setting up an IAM role for an EC2 instance

In this task, you will log in as the Admin user and create an IAM role. The role allows Amazon Elastic Compute Cloud (Amazon EC2) to access both Amazon Simple Storage Service (Amazon S3) and Amazon DynamoDB. You will later assign this role to an EC2 instance that hosts the employee directory application.

    Now that you are logged in as the Admin user, use the Services search bar to search for IAM again, and open the service by choosing IAM.

    In the navigation pane, choose Roles.

    Choose Create role.
    In the Select trusted entity page, configure the following settings.
        Trusted entity type: AWS service
        Use case: EC2

    Choose Next.

    In the permissions filter box, search for amazons3full, and select AmazonS3FullAccess.

    In the filter box, search for amazondynamodb, and select AmazonDynamoDBFullAccess.

    Choose Next.

    For Role name, paste S3DynamoDBFullAccessRole and choose Create role.
---
Task 1: Creating the VPC

In this task, you will create a new VPC.

    If needed, log in to the AWS Management Console as your Admin user.

    In the Services search box, enter VPC and open the VPC console by choosing VPC from the list.

    In the navigation pane, under Virtual private cloud, choose Your VPCs.

    Choose Create VPC.
    Configure these settings:
        Name tag: app-vpc
        IPv4 CIDR block: 10.1.0.0/16

    Choose Create VPC.

    In the navigation pane, under Virtual private cloud, choose Internet gateways

    Choose Create internet gateway.

    For Name tag, paste app-igw and choose Create internet gateway.

    In the details page for the internet gateway, choose Actions and then choose Attach to VPC.

    For Available VPCs, choose app-vpc and then choose Attach internet gateway.

Task 2: Creating subnets

In this task, you will create the four subnets for your VPC. You will configure the two public subnets first, and then configure the two private subnets.

    From the navigation pane, choose Subnets.

    Choose Create subnet.
    For the first public subnet, configure these settings:
        VPC ID: app-vpc
        Subnet name:Public Subnet 1
        Availability Zone: Choose the first Availability Zone
            Example: If you are in US West (Oregon), you would choose us-west-2a
        IPv4 CIDR block: 10.1.1.0/24

    Choose Add new subnet.
    For the second public subnet, configure these settings:
        Subnet name: Public Subnet 2
        Availability Zone: Choose the second Availability Zone
            Example: If you are in US West (Oregon), you would choose us-west-2b
        IPv4 CIDR block: 10.1.2.0/24
    Choose Add new subnet and for the first private subnet, configure these settings:
        Subnet name: Private Subnet 1
        Availability Zone: Choose the first Availability Zone
            Example: If you are in US West (Oregon), you would choose us-west-2a
        IPv4 CIDR block: 10.1.3.0/24.
    Choose Add new subnet and for the second private subnet, configure the following:
        Subnet name: Private Subnet 2
        Availability Zone: Choose the second Availability Zone
            Example: If you are in US West (Oregon), you would choose us-west-2b
        IPv4 CIDR block: 10.1.4.0/24

    Finally, choose Create subnet.

    After the subnets are created, select the check box for Public Subnet 1.

    Choose Actions and then choose Edit subnet settings.

    For Auto-assign IP settings, select Enable auto-assign public IPv4 address and then choose Save.

    Clear the check box for Public Subnet 1 and select the check box for Public Subnet 2.

    Again, choose Actions and then Edit subnet settings.

    For Auto-assign IP settings, select Enable auto-assign public IPv4 address and save the settings.

Task 3: Creating route tables

In this task, you will create the route tables for your VPC.

First, you will create the public route table.

    In the navigation pane, choose Route Tables.

    Choose Create route table.
    For the route table, configure these settings:
        Name: app-routetable-public
        VPC: app-vpc

    Choose Create route table.

    If needed, open the route table details pane by choosing app-routetable-public from the list.

    Choose the Routes tab and choose Edit routes.
    Choose Add route and configure these settings:
        Destination: 0.0.0.0/0
        Target: Internet Gateway, then choose app-igw (which you set up in the VPC task)

    Choose Save changes.

    Choose the Subnet associations tab.

    Scroll to Subnets without explicit associations and choose Edit subnet associations.

    Select the two public subnets that you created (Public Subnet 1 and Public Subnet 2) and choose Save associations.

    Next, you will create the private route table.

    In the navigation pane, choose Route Tables.
    Choose Create route table and configure these settings:
        Name: app-routetable-private
        VPC: app-vpc

    Choose Create route table.

    If needed, open the details pane for app-routetable-private by choosing it from the list.

    Choose the Subnet associations tab.

    Scroll to Subnets without explicit associations and choose Edit subnet associations.

    Select the two private subnets (Private Subnet 1 and Private Subnet 2) and choose Save associations.
--
Task 1: Creating an S3 bucket

In this task, you will create an S3 bucket.

    If needed, log in to the AWS Management Console with your Admin user.

    In the search box, enter S3 and open the Amazon S3 console by choosing S3.

    Choose Create bucket.

    For Bucket name, enter employee-photo-bucket-<your initials>-<unique number>.

    Example:

    employee-photo-bucket-al-907

    Choose Create bucket.

Task 2: Uploading a photo

In this task, you will upload an object (a photo) to the S3 bucket.

    Open the details of your newly created bucket by choosing the bucket name.

    Choose Upload.

    Choose Add files.

    Choose a photo of your choice from your computer and choose Open.

    Choose Upload.

    At the top, you should see Upload succeeded in green.

    Choose Close.

Task 3: Modifying the S3 bucket policy

In this task, you will update the bucket policy. The updated configuration allows the IAM role that you created previously to access the bucket.

    Choose the Permissions tab.

    Scroll down to Bucket policy and choose Edit.

    In the box, paste the following policy:

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

Replace the <INSERT-ACCOUNT-NUMBER> placeholder with your account number.

Replace the <INSERT-BUCKET-NAME> placeholder with your bucket name.

Example:

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

    Choose Save changes.

Task 4: Modifying the application to use the S3 bucket

In this task, you will launch another EC2 instance. This time, you will modify the user data script so that the application uses the S3 bucket.

    In the Services search box, enter EC2 and open the service by choosing EC2.

    In the navigation pane, under Instances, choose Instances.

    Select the employee-directory-app instance, which should be in the Stopped state.

    Choose Actions and then choose Image and templates, Launch more like this.

    For Name and at the end of the Value, append -s3.

    Example:

       employee-directory-app-s3

For Key pair name, select app-key-pair.

Under Network settings and Auto-assign Public IP, choose Enable.

Scroll down and expand Advanced Details.

In the User data box, update the values for the PHOTOS_BUCKET variable and (if needed) the AWS_DEFAULT_REGION variable.

#!/bin/bash -ex
wget https://aws-tc-largeobjects.s3-us-west-2.amazonaws.com/DEV-AWS-MO-GCNv2/FlaskApp.zip
unzip FlaskApp.zip
cd FlaskApp/
yum -y install python3 mysql
pip3 install -r requirements.txt
amazon-linux-extras install epel
yum -y install stress
export PHOTOS_BUCKET=<INSERT-BUCKET-NAME-HERE>
export AWS_DEFAULT_REGION=<INSERT-REGION-NAME-HERE>
export DYNAMO_MODE=on
FLASK_APP=application.py /usr/local/bin/flask run --host=0.0.0.0 --port=80

Example:

This example uses a sample bucket name.

export PHOTOS_BUCKET=employee-photo-bucket-al-907

    Choose Launch instance.

    Choose View all instances.

    The new instance should now be in the Instances list.

    Wait for the Instance state to change to Running and the Status check to change to 2/2 checks passed.

    Note: You can refresh the page to update the instance status.

    If needed, clear the check box for the stopped instance that you created previously.

    Select the check box for the employee-directory-app-s3 instance.

    Copy the Public IPv4 address.

    Note: Make sure that you only copy the address instead of choosing the open address link.

    In a new browser window, paste the IP address that you copied. Make sure to remove the ‘S’ after HTTP so you are using only HTTP instead.

You should see an Employee Directory placeholder. You won’t be able to interact with the application yet because it’s not connected to a database.

Congratulations! You launched an EC2 instance that uses the S3 bucket you created.
Exercise 6: Setting up the Database

In this scenario, part of your responsibility is to keep the employee database up to date.

In this exercise, you first launch another EC2 instance. Then, you create the DynamoDB table for the employee directory application.
Task 1: Launching an EC2 instance

In this task, you will launch another EC2 instance.

    If needed, log in to the AWS Management Console as your Admin user.

    Open the Amazon EC2 console by searching for and choosing EC2.

    In the navigation pane, choose Instances.

    If needed, select the check box for the employee-directory-app-s3 instance, which should be in the Stopped state.

    Choose Actions and then choose Image and templates, Launch more like this.

    For Name and at the end of the Value, append -exercise6.

    Example:

       employee-directory-app-exercise6

    For Key pair name, select app-key-pair.

    Under Network settings and Auto-assign Public IP, choose Enable.

    Choose Launch instance.

    Choose View all instances.

    The instance should now be in the Instances list.

    Wait for the Instance state to change to Running and the Status check to change to 2/2 checks passed.

    If needed, clear the check box for the employee-directory-app-s3 instance.

    Select the check box for employee-directory-app-exercise6.

    Copy the Public IPv4 address.

    Note: Make sure that you only copy the address instead of choosing the open address link.

    In a new browser window, paste the IP address that you copied. Make sure to remove the ‘S’ after HTTP so you are using only HTTP instead.

You should see an Employee Directory placeholder. You won’t be able to interact with the application yet because it’s not connected to a database.

    Close the application browser window.

Task 2: Creating the DynamoDB table

To connect the application to a database, you first need to create one! In this task, you will create a database by using DynamoDB.

    Return to the console, and search for and open DynamoDB.

    In the navigation pane, choose Tables.
    Choose Create table and configure the following settings.
        Table name: Employees
        Partition key: id

    Choose Create table.

Task 3: Testing the application

In this task, you will test whether the application works by using it to create an employee entry and upload a photo.

    Return to the Amazon EC2 console by searching for and opening EC2.

    In the instance list, select the check box for the employee-directory-app-exercise6 instance.

    On the Details tab, copy the Public IPv4 address and in a new browser window, paste the IP address.

    In the application interface, choose Add.

    Create a new employee entry by entering a name, location, and job title, and by selecting some attributes.

    Upload a picture by choosing Browse and uploading a picture of your choice.

    Choose Save.

    Create and save a few employee entries.

Note: You can also edit and delete entries.

In the employee directory application, you should now see the list of employees (and their photos) that you added.
Task 4: Viewing the item in the database

In this task, you will see how the employee entries are stored in DynamoDB.

    Return to the console, and search for and open DynamoDB.

    In the navigation pane, choose Tables.

    Open the table details by choosing the Employees link.

    Choose Explore table items.

In the Items returned list, you should now see the entries in the database that you made by using the application on the EC2 instance.

Congratulations! You launched an EC2 instance that uses the S3 bucket, and is connected to the DynamoDB table.

Exercise 7: Load Balancing and Auto Scaling

For this scenario, you are tasked with setting up an ELB load balancer and an Auto Scaling group so that your application can scale horizontally.

In this exercise, you first launch another EC2 instance. You then create an Application Load Balancer and a launch template. Next, you set up an Auto Scaling group that uses the load balancer and launch template that you created. Finally, you test and stress the application, and watch your application scale in real time.
Task 1: Launching an EC2 instance

In this task, you will launch an EC2 instance that hosts the application.

    If needed, log in to the AWS Management Console as your Admin user.

    Search for and open EC2.

    In the navigation pane, choose Instances.

    Select the check box for the employee-directory-app-exercise6 instance, which should be in the Stopped state.

    Choose Actions and then choose Image and templates, Launch more like this.

    For Name and at the end of the Value, append -exercise7.

    Example:

       employee-directory-app-exercise7

    For Key pair name, select app-key-pair.

    Under Network settings and Auto-assign Public IP, choose Enable.

    Choose Launch instance.

    Choose View all instances.

    The instance should now be in the Instances list.

    Wait for the Instance state to change to Running and the Status check to change to 2/2 checks passed.

    Select the check box for employee-directory-app-exercise7.

    On the Details tab, copy the Public IPv4 address and paste it into a new browser window.

    In a new browser window, paste the IP address that you copied. Make sure to remove the ‘S’ after HTTP so you are using only HTTP instead.

Task 2: Creating the Application Load Balancer

In this task, you will create the Application Load Balancer.

    Return to the Amazon EC2 console.

    In the navigation pane, under Load Balancing, choose Load Balancers.

    Choose Create Load Balancer.

    On the Application Load Balancer card, choose Create.
    Configure the following load balancer settings.
        Load balancer name: app-alb
        VPC: app-vpc
        Mappings: Select both Availability Zones
            Example: If you are in US West (Oregon), you would select both us-west-2a and us-west-2b
        First Availability Zone Subnet: Public Subnet 1
        Second Availability Zone Subnet: Public Subnet 2

    In the Security groups section, remove the default security group (by choosing the X) and choose Create new security group.

    A new window opens for creating a security group.
    Configure the following security group settings:
        Security group name: load-balancer-sg
        Description: HTTP access
        VPC: If needed, paste the VPC ID for app-vpc and choose it when it appears under the box
            Note: You can find the app-vpc ID by opening the VPC console in a new window
        Inbound rules: Add Rule
            Type: HTTP
            Source: Anywhere-IPv4

    Choose Create security group.

    Close the security group browser window or return to the Load balancers window.

    For Security groups, add the new load-balancer-sg group. Note: To see the new security group, you might need to refresh the Security groups list.

    In Listeners and routing, choose Create target group.

    A new window opens for creating a target group.
    For Specify group details, configure the following settings.
        Choose a target type: Keep Instances selected
        Target group name: app-target-group
        Health checks: Expand Advanced health check settings and configure the following:
            Healthy threshold: 2
            Unhealthy threshold: 5
            Timeout:30
            Interval: 40

    Choose Next.

    For Register targets, select employee-directory-app-exercise7 and choose Include as pending below.

    Choose Create target group.

    Close the target groups window or return to the Load balancers window.

    Under Listeners and routing, refresh the available listener and choose app-target-group.

    Finally, choose Create load balancer.

    Choose View load balancer.

    Make sure that app-alb is selected and wait for the load balancer State to become Active.

    On the Description tab, copy DNS name and paste it into a text editor of your choice.

    In the text editor, at the beginning of the URL, add http://.

    Example:

    http://app-elb-123456789012.us-west-2.elb.amazonaws.com

    Copy the DNS name (with http:// added) and paste it into a new browser window.

You should see the employee directory application.
Task 3: Creating the launch template

Now that you can access your application from a singular DNS name, you can scale the application horizontally. To scale horizontally, you need a launch template. In this task, you will create a launch template.

    Back in the console, if needed, search for and open EC2.

    In the navigation pane, under Instances, choose Launch Templates.
    Choose Create launch template and configure the following settings.
        Launch template name: app-launch-template
        Template version description: A web server for the employee directory application
        Auto Scaling guidance: Provide guidance to help me set up a template that I can use with EC2 Auto Scaling
        Application and OS Images (Amazon Machine Image) - required: Currently in use
        Instance type: t2.micro
        Key pair name: app-key-pair
        Security groups: web-security-group

    Expand the Advanced details section.

    For IAM instance profile, choose S3DynamoDBFullAccessRole.

    Scroll to User data and paste the following code:

    #!/bin/bash -ex
    wget https://aws-tc-largeobjects.s3-us-west-2.amazonaws.com/DEV-AWS-MO-GCNv2/FlaskApp.zip
    unzip FlaskApp.zip
    cd FlaskApp/
    yum -y install python3 mysql
    pip3 install -r requirements.txt
    amazon-linux-extras install epel
    yum -y install stress
    export PHOTOS_BUCKET=${SUB_PHOTOS_BUCKET}
    export AWS_DEFAULT_REGION=<INSERT REGION HERE>
    export DYNAMO_MODE=on
    FLASK_APP=application.py /usr/local/bin/flask run --host=0.0.0.0 --port=80

In the user data code, replace the PHOTOS_BUCKET placeholder value with the name of your bucket.

Example:

export PHOTOS_BUCKET=employee-photo-bucket-al-907

Replace the AWS_DEFAULT_REGION placeholder value with your Region (the Region is listed at the top right, next to your user name).

Example:

This example uses US West (Oregon) (us-west-2) as the Region.

export AWS_DEFAULT_REGION=us-west-2

    Choose Create launch template.

    Choose View Launch templates.

Task 4: Creating the Auto Scaling group

In this task, you will create the Auto Scaling group.

    In the navigation pane, under Auto Scaling, choose Auto Scaling Groups.

    Choose Create Auto Scaling group.
    For Choose launch template or configuration, configure these settings:
        Auto Scaling group name: app-asg
        Launch template: app-launch-template

    Choose Next.
    For Choose instance launch options, configure these settings:
        VPC: app-vpc
        Availability Zones and subnets: Choose the Availability Zones with Public Subnet 1 and Public Subnet 2

    Choose Next.
    For Configure advanced options, use these settings:
        Load balancing: Attach to an existing load balancer
        Attach to an existing load balancer: Choose from your load balancer target groups
        Existing load balancer target groups: app-target-group
        Health checks: ELB

    Choose Next.
    For Configure group size and scaling policies, use these settings:
        Desired capacity: 2
        Minimum capacity: 2
        Maximum capacity: 4
        Scaling policies: Target tracking scaling policy
        Target value: 60
        Instances need: 300

    Choose Next.
    For Add notifications, choose Add notification and configure these settings:
        SNS Topic: Create a topic
        Send a notification to: app-sns-topic
        With these recipients: Enter your email address

    Choose Next and then choose Next again.

    Choose Create Auto Scaling group.

    You should receive an AWS Notification - Subscription Confirmation email.

    Open this email message and choose Confirm subscription.

A web browser window should open with a Subscription confirmed! message.
Task 5: Testing the application

In this task, you will stress-test the application and confirm that it scales.

    Return to the Amazon EC2 console.

    In the navigation pane, under Load Balancing, choose Target Groups.

    Make sure that app-target-group is selected and choose the Targets tab.

    You should see two additional instances launching.

    Wait until the Status for both instances is healthy.

    In the navigation pane, choose Load Balancers and make sure that app-alb is selected.

    Again, copy the DNS name and paste it into a text editor of your choice.

    In the text editor, at the beginning of the URL, add http:// and copy the modified URL.

    Example:

    http://app-elb-123456789012.us-west-2.elb.amazonaws.com

    In a new browser window, paste the URL.

    At the end of the URL, append /info.

    Example:

    http://app-alb-123456789012.us-west-2.elb.amazonaws.com/info

    You should see an Instance Info page, which shows which instance_id and availability_zone you are being routed to.

    Refresh the page a few times. Each time, note that the values for instance_id or availability_zone can be different from the previous ones.

    Now, you need to test auto scaling by stressing the CPU of the instance.

    For Stress cpu, choose 10 min.

    The top of the browser window should show a message that says Stressing CPU.

    Wait for 10 minutes and after the 10 minutes are over, return to the Amazon EC2 console window.

    In the navigation pane, under Load Balancing, choose Target Groups.

    Select app-target-group and choose the Targets tab.

You should see additional instances were launched because of the stress test. You should also see a notification email.
-->
