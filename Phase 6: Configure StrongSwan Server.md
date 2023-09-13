# Configure StrongSwan Server

In this phase we will follow the Configuration file from the S2S VPN in GovCloud. However, we will make additional configuration that would enable a successful connection. 

The S2S Configuration files give you a step by step guide on how to configure your StrongSwan server such as configuring your ipsec tunnels. If you have followed the steps provided in the file in the past, you probably didn't establish a successful connection between GovCloud and AWS Commercial cloud or your on-premises network. In my case, I was not able to establish a connection between GovCloud and comemrcial for a few days. It took me several days to identified the cause of the problem and that is what I am intending to do here, save you some time and explain the problem.

First, we will start by making basic configurations to the StrongSwan server in commercial cloud. The following steps can also be found in the configuration file downloaded earleir.

To note: this instance has been deployed in a public subnet, has an Elastic IP associated, and has source and destination checks disabled. In addition, the Security Groups of this instance has SSH and ICMP as their inbound rule (0.0.0.0/0).

Step 1: Log into the EC2 instance (StrongSwan server)

step 2: Install StrongSwan

    sudo apt install StrongSwan -y

Step 3: Open /etc/sysctl.conf and uncomment the following:
    
    #net.ipv4.ip_forward = 1

    sudo nano /etc/sysctl.conf

  <img width="795" alt="Screenshot 2023-09-13 at 3 01 57 PM" src="https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/b9bed7aa-96a3-491a-988f-4dd5a71126de">

  Step 4: Save the changes

  Step 5: Apply the changes

      sudo sysctl -p

  Step 6: Edit the /etc/ipsec.conf file

      sudo nano /etc/ipsec.conf

  Step 7: Uncomment the following line:

      uniqueids=no

  Step 8: From the S2S VPN Configuration file, add the "conn Tunel1" configurations into the ipsec.conf file

      Example:
      conn Tunnel1
      	auto=start
      	left=%defaultroute
      	leftid=xxx.xxx.xxx.164 (This is the IP from your strongswan server, also the elastic IP created in Phase 4)
      	right=xxx.xxx.xxx.140
      	type=tunnel
      	leftauth=psk
      	rightauth=psk
      	keyexchange=ikev1
      	ike=aes128-sha1-modp1024
      	ikelifetime=8h
      	esp=aes128-sha1-modp1024
      	lifetime=1h
      	keyingtries=%forever
      	leftsubnet=0.0.0.0/0 
      	rightsubnet=0.0.0.0/0
      	dpddelay=10s
      	dpdtimeout=30s
      	dpdaction=restart
      	## Please note the following line assumes you only have two tunnels in your Strongswan configuration file. This "mark" value must be unique and may need to be changed based on other entries in your configuration file.
      	mark=100
      	## Uncomment the following line to utilize the script from the "Automated Tunnel Healhcheck and Failover" section. Ensure that the integer after "-m" matches the "mark" value above, and <VPC CIDR> is replaced with the CIDR of your VPC
      	#leftupdown="/etc/ipsec.d/aws-updown.sh -ln Tunnel1 -ll xxx.xxx.xxx.xxx/30 -lr xxx.xxx.xxx.xxx/30 -m 100 -r <VPC CIDR>"

Step 9: Make the following changes to this Conn Tunnel in the following sections:

    #Replace the IKE and ESP encryptions to the following:

    ike=aes256-sha256-modp2048,aes128-sha1-modp1024!
	  esp=aes128-sha256-modp2048,aes128-sha1-modp1024!

    #Edit the left and right subnet:
    leftsubnet=10.10.0.0/16 (Commercial Cloud VPC CIDR)
	  rightsubnet=10.0.0.0/16 (GovCloud VPC CIDR)

    #Uncomment the "leftupdown" Line

    #Replace the <VPC CIDR> with the CIDR from GovCloud 10.0.0.0/16

Step 10: Create a file at /etc/ipsec.secrets and add the PSK key provided in your configuration file:

    sudo nano /etc/ipsec.secrets 

    Add the PSK line provided in your configuration file, should be Step 4:

    xxx.xxx.xxx.164 xxx.xxxx.xxx.140 : PSK "xxxxxxxxxxxxxxxxx7jioJhVR"

Step 11: Repeat Step 8 and 9 to set up "Conn Tunnel2" (Optional)

![Screenshot 2023-09-13 at 3 24 06 PM](https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/650c99cd-fde8-47e5-af64-50d56bc0ebc7)
  
Step 12: Save the file.

# Create a Tunnel Heartbeat

The AWS configuration file states that it is recommended to have a heartbeat that periodically pings the remote endpoint's inside IP to keep the tunnel active.

Step 1: Create a file named "heartbeat.sh"

	sudo nano heartbeat.sh

Step 2: Input the following:

	#!/bin/bash

	while true; do
		ping -c 1 $1 &> /dev/null
		ping -c 1 $2 &> /dev/null
		sleep 5
	done

Step 3: Save the file

Step 4: Run the following command to execute the script

  	sudo chmod 744 heartbeat.sh

Step 5: insert:

   	./heartbeat.sh <REMOTE_INSIDE_IP_1> <REMOTE_INSIDE_IP_2> &

    Remote inside IP 1 & 2 can be found in the S2S VPN under "Tunnel Details"


<img width="1223" alt="Screenshot 2023-09-13 at 3 34 35 PM" src="https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/ba6465ad-ed05-4ac2-8116-36f4b16e97f1">


# Automate Tunnel Healthcheck and Failover



