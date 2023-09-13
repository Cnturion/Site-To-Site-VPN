# Setting up a Test-Instance

This EC2 Intance will be used to test the connectivity between the two networks, the GovCloud and AWS commercial cloud. 

Step 1: Go to the AWS Console

Log in to the AWS Console and navigate to the EC2 service.

Step 2: Click on Launch Instance

In the left navigation pane, click on Launch Instance.

Step 3: Choose an AMI

In the AMI section, select the Amazon Linux 2023 AMI. This AMI is free tier eligible.

Step 4: Choose an instance type

In the Instance type section, select the t2.micro instance type. This instance type is also free tier eligible.

Step 5: Choose a key pair

In the Key pair section, select an existing key pair or create a new one. If you are creating a new key pair, make sure to download the private key file. You will need this file to connect to your EC2 instance.

![Screenshot 2023-09-12 at 4 33 45 PM](https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/4768282a-bde8-4704-8b04-feaaf789c8ab)

Step 6: Configure network settings

In the Network settings section, select the VPC that you created in the previous step. You should also select the public subnet for this instance.

Step 7: Create a security group

In the Security groups section, click on Create new security group.

In the Create security group dialog box, enter the following details:

    Name: Test Security Group
    Description: Allow SSH and ICMP
    VPC: Select the VPC that you created in the previous step.

![Screenshot 2023-09-12 at 4 43 01 PM](https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/8bc9df07-e37e-4bac-a20d-d71b790aaec5)

Click on the Review and Launch button.

Step 8: Review the instance launch

Review the instance launch details and make sure that they are correct. If they are correct, click on the Launch button.

AWS will launch the EC2 instance. This may take a few minutes.

Step 9: Connect to the instance

Once the instance is launched, you can connect to it using the SSH client and the private key file that you downloaded in step 5.
