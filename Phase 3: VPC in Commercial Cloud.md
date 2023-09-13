# Create a VPC in the commercial cloud -or- on-premises

Step 1: Go to the AWS Console

Log in to the AWS Console and navigate to the VPC service.

Step 2: Click on Create VPC

In the left navigation pane, click on VPCs.

Step 3: Click on Create VPC

Click on the Create VPC button.

Step 4: Enter the VPC details

In the Create VPC dialog box, enter the following details:

    Name: Enter a name for your VPC. In this case, it is "Commercial VPC".
    Select: VPC and more
    CIDR block: Enter the IPv4 CIDR block for your VPC. In this case, it is 10.10.0.0/16.

<img width="1435" alt="Screenshot 2023-09-13 at 12 04 41 PM" src="https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/db563876-66ae-4ad3-8a59-e2f7677be868">

Leave all other settings as default.

Step 5: Click on Create

Click on the Create button.

AWS will create the VPC. This may take a few minutes.

Step 6: Verify the VPC

Once the VPC is created, you can verify it by clicking on the VPC name. The VPC details will be displayed.

The VPC you are creating will create a virtual private cloud (VPC) in the AWS cloud. A VPC is a logically isolated section of the AWS cloud that you can use to launch your AWS resources.

The VPC will have the following features:

A private IP address space: The VPC will have a private IP address space that is not accessible from the public internet.
Subnets: The VPC will have subnets, which are smaller networks within the VPC. You can use subnets to group your AWS resources together.
Route tables: The VPC will have route tables, which control how traffic is routed within the VPC.
Security groups: The VPC will have security groups, which control which traffic is allowed to reach your AWS resources.
