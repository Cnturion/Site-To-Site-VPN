# StrongSwan Server using EC2 Instance

StrongSwan is an open-source IPSec VPN solution that can be used to create secure tunnels between networks. It is a powerful and flexible tool that can be used to meet a variety of security requirements.

In this case, the StrongSwan server is located in AWS commercial cloud. It is configured with a public IP address so that it can be accessed by the customer gateway in AWS GovCloud. The customer gateway is a virtual appliance that provides a secure connection between the AWS GovCloud and the StrongSwan server.

The StrongSwan server and the customer gateway use IPSec to encrypt traffic between the two networks. IPSec is a set of protocols that provide secure communication over an untrusted network. It uses strong encryption to protect data from being intercepted or modified.

The StrongSwan server running in Ubuntu makes it possible to set up a site-to-site VPN between GovCloud and AWS commercial cloud by providing a secure tunnel between the two networks. This tunnel can be used to securely transfer data between the two networks, such as files, database traffic, and web traffic.

Here are some additional benefits of using StrongSwan server:

  It is open-source, so it is free to use and modify.
  It is highly scalable, so it can be used to support a large number of users.
  It is secure, using strong encryption to protect data from being intercepted or modified.
  It is easy to configure, even for non-technical users.

# Launch an Ubunutu instance using an EC2 Instance

Step 1: Go to the AWS Console

Log in to the AWS Console and navigate to the EC2 service.

Step 2: Click on Launch Instance

In the left navigation pane, click on Launch Instance.

Step 3: Choose an AMI

In the AMI section, select the Ubuntu Server 22.04 LTS AMI. This AMI is free tier eligible.

Step 4: Choose an instance type

In the Instance type section, select the t2.micro instance type. This instance type is also free tier eligible.

Step 5: Choose a key pair

In the Key pair section, do the following:

    If you do not have an existing key pair, create a new one. Make sure to download the private key file. You will need this file to connect to your EC2 instance.
    If you already have an existing key pair, select it from the list.

![Screenshot 2023-09-13 at 12 22 09 PM](https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/220a62e8-6b2c-4547-8088-2c932217b382)

Step 6: Configure network settings

In the Network settings section, do the following:

    Select the VPC that you want to launch the instance in. This should be the VPC that you created in Phase 3.
    Select a subnet in the VPC. This should be a public subnet.

Step 7: Create a security group

In the Security groups section, do the following:

    Click on Create new security group.
    In the Create security group dialog box, enter the following details:
        Name: StrongSwan Server Security Group
        Description: Allow SSH and ICMP
        Inbound Rules: SSH and ICMP
        Source Type: Anywhere
    Click on the Review and Launch button.

Step 8: Review the instance launch

Review the instance launch details and make sure that they are correct. If they are correct, click on the Launch button.

AWS will launch the EC2 instance. This may take a few minutes.

# Stop Source and Destination Checks

Source and destination checks are a security feature that is enabled by default on EC2 instances. These checks ensure that traffic only originates from and terminates at the instance. Since we will be allowing traffic to flow through this StrongSwan server, some of the traffic won't originate from here, therefore, we are stoping the source and destination checks. 

Step 1: Go to the AWS Console and navigate to the EC2 service.

Step 2: In the left navigation pane, click on Instances.

Step 3: Select the StrgonSwan Server EC2 instance.

Setp 4:Click on the Actions menu and select Instance settings.

Setp 5: In the Instance settings dialog box, scroll down to the "netowrking" section and expand the Source/destination checks section.

Step 6: Check the "Stop" checkbox.

Step 7: Click on the Save button.

<img width="1245" alt="Screenshot 2023-09-13 at 1 37 25 PM" src="https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/b0aac23c-707e-42f6-bcb1-552c57a11532">

<img width="612" alt="Screenshot 2023-09-13 at 1 37 34 PM" src="https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/a814f004-bf8c-4f89-b527-4b27b46f5bf3">

# OPTIONAL: Set up an Elastic IP for StrongSwan

An AWS Elastic IP address is a static IP address that can be assigned to an AWS resource, such as an EC2 instance or a load balancer. Elastic IP addresses are useful for applications that need to be accessible from the internet, such as a web server or a VPN server.

In the case of a StrongSwan server, it is recommended to use an Elastic IP address for the following reasons:

  It provides a static IP address for the StrongSwan server, which can be used to connect to it from the internet.
  It allows you to move the StrongSwan server to a different EC2 instance without having to change the IP address that is used to connect to it.
  It can be used to failover to a different EC2 instance in the event of an outage.

For this case, I am using an Elastic IP in case of a failover with the EC2 Instance. This allows me to configure the Customer Gateway in GovCloud using the Elastic IP associated with the instance and re-associate it with another StrongSwan server in caase of failover.

Step 1: Go to the AWS Console

Log in to the AWS Console and navigate to the EC2 service.

Step 2: Click on Elastic IPs

In the left navigation pane, click on Elastic IPs.

Step 3: Click on Allocate Elastic IP address

Click on the Allocate Elastic IP address button.

Step 4: Enter the Elastic IP address details

In the Allocate Elastic IP address dialog box, enter the following details:

    Elastic IP address: Select Amazon's pool of IPv4 addresses.

<img width="861" alt="Screenshot 2023-09-13 at 12 36 05 PM" src="https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/5b21f318-b7c2-40ee-aa29-b85d3fd16592">

Click on the Allocate button.

Step 5: Associate the Elastic IP address with the EC2 instance

In the Elastic IPs page, select the Elastic IP address that you just created.

Click on the Actions menu and select Associate Elastic IP address.

In the Associate Elastic IP address dialog box, do the following:

    Instance: Select the EC2 instance that is running the StrongSwan server.
    
<img width="829" alt="Screenshot 2023-09-13 at 12 37 08 PM" src="https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/e675fbc7-0f50-4b8c-b841-4248c444b07d">

Click on the Associate button.

Step 6: Verify the association

Once the Elastic IP address has been associated with the EC2 instance, you can verify the association by clicking on the Elastic IP address. The Instance field will show the EC2 instance that the Elastic IP address is associated with.

<img width="1228" alt="Screenshot 2023-09-13 at 12 40 09 PM" src="https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/8a2ccc0d-d4c9-4f5b-a11e-45a6f6f31f28">


That's it! You have successfully created an Elastic IP address and associated it with the StrongSwan Ubuntu server, selecting Amazon's pool of IPv4 addresses.
