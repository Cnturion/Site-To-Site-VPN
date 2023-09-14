  # Route Table Configurations

  Now that we have both VPN tunnels UP, we still need to configure the routing from one VPC to be able to communicate and pass traffic to the other. 

  In the next steps we will add a route table (RT) to the RT associated with the public subnets of the commercial cloud. We want the traffic from this subnet to be routed through the StrongSwan Server.

  
Step 1: Go to the AWS Console and navigate to the VPC service.

Step 2: In the left navigation pane, click on Route tables.

Step 3: Select the route table associated with the public subnets.

Step 4: Click on the Actions menu and select Edit routes.

Step 5: In the Edit routes dialog box, do the following:

    In the Destination field, enter the CIDR block of the GovCloud VPC: 10.0.0.0/16.
    In the Target field, select "Instance" and select the StrongSwan Server Instance "eni-xxxxxxx.
        
<img width="1482" alt="Screenshot 2023-09-13 at 8 02 05 PM" src="https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/df77e16c-d1ac-4b8d-938d-c11e22b01b22">

Step 6: Click on the Save changes button.

Once you have edited the route table, all traffic destined for the CIDR block of the GovCloud server will be routed through the StrongSwan server.

To test the network connection you can ping the StrongSwan server from the "Test Instance" created in Phase 2 and vice versa.

<img width="1347" alt="Screenshot 2023-09-13 at 8 06 11 PM" src="https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/13798d84-b4ec-408c-8c15-c8953569aafc">
