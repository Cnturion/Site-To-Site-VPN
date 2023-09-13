# AWS GovCloud or AWS Cloud connecting to on-prem infrastructure

# Setting up a VPC

In the AWS GovCloud I will create a Virtual Private Cloud (VPC) with public and private subnets. 

Step 1: Go to the AWS Console

Log in to the AWS Console and navigate to the VPC service.

Step 2: Click on Create VPC

In the left navigation pane, click on VPCs.

Step 3: Click on Create VPC

Click on the Create VPC button.

Step 4: Enter the VPC details

<img width="2110" alt="Screenshot 2023-09-12 at 4 28 02 PM" src="https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/90669f2d-b275-4516-8610-bbccc074af66">

In the Create VPC dialog box, enter the following details:

    Name: Enter a name for your VPC.
    IPv4 CIDR block: Enter the IPv4 CIDR block for your VPC. In this case, it is 10.0.0.0/16.
    DNS options: Make sure all DNS options are enabled.

Leave all other settings as default.

Step 5: Click on Create

Click on the Create button.

AWS will create the VPC. This may take a few minutes.

Step 6: Verify the VPC

Once the VPC is created, you can verify it by clicking on the VPC name. The VPC details will be displayed.

When you create a VPC, the following components are created by default:

    A main route table: This route table contains a default route that sends all traffic to the internet gateway.
    A subnet: This subnet is created in each Availability Zone that you selected for your VPC.
    A network ACL: This network ACL allows all inbound and outbound traffic by default.
    A security group: This security group allows all inbound and outbound traffic by default.

You can create additional subnets, route tables, network ACLs, and security groups as needed.



# Virtual Private Network

To establish a secure connection between commercial cloud and govcloud I will deploy a Site-to-Site VPN consisting of a customer gateway, virtual private gateway and a S2S VPN connection. The image below shows an illustration of this connection where as the "On-premises network" is GovCloud in my case.

![vpn-how-it-works-vgw](https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/03b7537b-86f1-4cac-839f-06b627f78b30)

For a quick rundown on a S2S VPN please refer to https://docs.aws.amazon.com/vpn/latest/s2svpn/how_it_works.html. 

# Customer Gateway

An AWS Customer Gateway is a virtual representation of a physical or software device that you own and operate in your on-premises network. It is used to create a secure connection between your network and an Amazon Virtual Private Cloud (VPC).

When you create an AWS Customer Gateway, you provide information about your device to AWS, such as its public IP address and its BGP ASN. You also need to configure your device to work with the AWS Site-to-Site VPN connection.

Step 1: Go to the AWS Console

Log in to the AWS Console and navigate to the VPC service.

Step 2: Click on Customer gateways

In the left navigation pane, click on Customer gateways.

Step 3: Click on Create customer gateway

Click on the Create customer gateway button.

Step 4: Enter the customer gateway details

In the Create customer gateway dialog box, enter the following details:

    Name: California StrongSwan Server-CGW
    IP address: 54.176.218.164
    BGP ASN: 65000

Leave all other settings as default.

Step 5: Click on Create

Click on the Create button.

AWS will create the customer gateway. This may take a few minutes.

Step 6: Verify the customer gateway

Once the customer gateway is created, you can verify it by clicking on the customer gateway name. The customer gateway details will be displayed.
