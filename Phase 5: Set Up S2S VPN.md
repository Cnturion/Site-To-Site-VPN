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

    Name: StrongSwan Server-CGW
    IP address: xxx.xxx.xxx.164 (Elastic IP in commercial cloud)
    BGP ASN: 65000 (Default)

![Screenshot 2023-09-13 at 12 50 06 PM](https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/90f42043-bf03-4ad9-a33a-fc41ea31f9e3)

Leave all other settings as default.

Step 5: Click on Create

Click on the Create button.

AWS will create the customer gateway. This may take a few minutes.

Step 6: Verify the customer gateway

Once the customer gateway is created, you can verify it by clicking on the customer gateway name. The customer gateway details will be displayed.

# Virtual Private Gateway

An Amazon VGW is a virtual appliance that connects your VPC to your on-premises network over a Site-to-Site VPN connection. It's a gateway that allows you to securely connect your AWS resources to your on-premises network.


Step 1: Go to the AWS Console and navigate to the VPC service.

Step 2: In the left navigation pane, click on Virtual Private Gateways.

Step 3: Click on the Create Virtual Private Gateway button.

Step 4: In the Create Virtual Private Gateway dialog box, enter the following details:

    Name: AWS VPGW
    Amazon's default ASN: Select the Use Amazon's default ASN checkbox.
    
<img width="825" alt="Screenshot 2023-09-13 at 1 00 42 PM" src="https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/81323e69-a399-4ac2-b292-12561d120fdd">

Step 5: Click on the Create button.

Once the VGW has been created, you can associate it with your VPC.


Step 1: In the left navigation pane, click on Virtual Private Gateways.

Step 2: Select the VGW that you want to associate with your VPC.

Step 3: Click on the Actions menu and select Attach to VPC.

Step 4: In the Associate with VPC dialog box, do the following:

        VPC: Select the VPC that you want to associate the VGW with, this should be the VPC created in Phase 1.

Step 5: Click on the Attach to VPC button.

<img width="1201" alt="Screenshot 2023-09-13 at 12 59 01 PM" src="https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/07322c88-31f4-4d76-8b00-a9c36cedd655">

Once the VGW has been attached to the VPC, you can verify the association by clicking on the VGW. The VPC field will show the VPC that the VGW is associated with.

# Create a Site-to-Site VPN connection

Step 1: Go to the AWS Console and navigate to the Site-to-Site VPN service.

Step 2: In the left navigation pane, click on Site-to-Site VPN Connections.

Step 3: Click on the Create Site-to-Site VPN Connection button.

Step 4: In the Create Site-to-Site VPN Connection dialog box, enter the following details:
        
        Name: AWS GovCloud S2SVPN
        Target: Select the VGW that you created in the previous step.
        Customer gateway: Select the customer gateway that you created in the previous step.
        Routing options: Select Static.
        Static IP prefixes: Enter the following CIDR blocks:
            10.0.0.0/16 (GovCloud VPC)
            10.10.0.0/16 (Commercial cloud)
        Local IPv4 network CIDR: 10.10.0.0/16 (commercial cloud VPC CIDR)
        Remote IPv4 CIDR: 10.0.0.0/16 (GovCloud VPC CIDR)
        
![Screenshot 2023-09-13 at 1 34 29 PM](https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/3355ad48-8a49-45fc-a19c-344d6b9ee84e)

Step 5: Click on the Create button.

Once the Site-to-Site VPN connection has been created, you can verify the connection by connecting to the StrongSwan server from your on-premises network.
