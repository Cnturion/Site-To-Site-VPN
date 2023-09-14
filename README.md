# Site-To-Site-VPN

Recently, I was presented with a challenge that initially seemed like an easy task but ended up taking me 5-7 days to figure out. In this repository, I want to demonstrate the process of setting up a Site-to-Site VPN from AWS Cloud to AWS GovCloud. This demo can also be applied to those who would like to establish a Site-to-Site VPN connection between AWS Cloud and their on-premises infrastructure. Additionally, and most importantly, I would like to highlight some common errors that you may encounter when trying to establish a Site-to-Site VPN connection. Personally, these errors took me days to troubleshoot because they were not easy to find solutions for.

First, I would like to thank the AWS community for their participation in helping me find a solution to this challenge and their mentorship.

This demo is broken down into six phases and it take approximately 1.5 hours to complete.

Phase 1: VPC in GovCloud

In this phase I go over building up the necessary cloud infrastructurein GovCloud (where we are initiating the VPN connection from). For those who are looking to set up a S2S VPN connection between AWS Cloud and their on-premises network, this section is also applicable and can be set up in the AWS commercial cloud.

Phase 2: Create a Test Instance

In this phase I demonstrate how to create an EC2 instance to test the VPN connection between the GovCloud and AWS commercial cloud.

Phase 3: VPC in Commercial Cloud

In this phase I demonstrate how to set up our cloud environemnt to connect to the S2S VPN.

Phase 4: StrongSwan Server

In this phase I set up the StrongSwan server using an EC2 instance running Ubuntu 20.04. 

Phase 5: Set up S2S VPN

In this phase I start creating and deploying the S2S VPN dependencies such as the customer gateway and virtual private gateway to create the S2S VPN in GovCloud.

Phase 6: Configure StrongSwan

In this phase I demonstrate how to configure the StrongSwan server. I make additional modifications the the S2S VPN configuration files from GovCloud. 

Phase 7: Route Table configurations and Testing

In this phase I add a route table from the commercial cloud to route all traffic destined to GovCloud to be routed through the strongswan server. Lastly, I test the connection by pinging both networks, demonstrating the ipsec tunnels are working properly. 



![S2S VPN Demo](https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/e0f62df2-491e-4c89-aeb5-81317a3fe53c)
