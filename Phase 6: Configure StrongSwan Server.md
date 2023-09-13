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

Step 13: Repeat Step 10 and add the PSK key for tunnel2. This key is also provided in the S2S configuration file. 

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

Comment: The tunnels are supposed to be down. This image was taken after I configured the tunnel routes. If the status of your tunnels are "Down" that is normal.

# Automate Tunnel Healthcheck and Failover

Step 1: Create a file at /etc/ipsec.d/aws-updown.sh

	sudo nano /etc/ipsec.d/aws-updown.sh

Step 2: AWS gives you the following script to automate healthchecks and failover. You should have something very similar.

	#!/bin/bash

	while [[ $# > 1 ]]; do
		case ${1} in
			-ln|--link-name)
				TUNNEL_NAME="${2}"
				TUNNEL_PHY_INTERFACE="${PLUTO_INTERFACE}"
				shift
				;;
			-ll|--link-local)
				TUNNEL_LOCAL_ADDRESS="${2}"
				TUNNEL_LOCAL_ENDPOINT="${PLUTO_ME}"
				shift
				;;
			-lr|--link-remote)
				TUNNEL_REMOTE_ADDRESS="${2}"
				TUNNEL_REMOTE_ENDPOINT="${PLUTO_PEER}"
				shift
				;;
			-m|--mark)
				TUNNEL_MARK="${2}"
				shift
				;;
			-r|--static-route)
				TUNNEL_STATIC_ROUTE="${2}"
				shift
				;;
			*)
				echo "${0}: Unknown argument \"${1}\"" >&2
				;;
		esac
		shift
	done
	
	command_exists() {
		type "$1" >&2 2>&2
	}
	
	create_interface() {
		ip link add ${TUNNEL_NAME} type vti local ${TUNNEL_LOCAL_ENDPOINT} remote ${TUNNEL_REMOTE_ENDPOINT} key ${TUNNEL_MARK}
		ip addr add ${TUNNEL_LOCAL_ADDRESS} remote ${TUNNEL_REMOTE_ADDRESS} dev ${TUNNEL_NAME}
		ip link set ${TUNNEL_NAME} up mtu 1419
	}
	
	configure_sysctl() {
		sysctl -w net.ipv4.ip_forward=1
		sysctl -w net.ipv4.conf.${TUNNEL_NAME}.rp_filter=2
		sysctl -w net.ipv4.conf.${TUNNEL_NAME}.disable_policy=1
		sysctl -w net.ipv4.conf.${TUNNEL_PHY_INTERFACE}.disable_xfrm=1
		sysctl -w net.ipv4.conf.${TUNNEL_PHY_INTERFACE}.disable_policy=1
	}
	
	add_route() {
		IFS=',' read -ra route <<< "${TUNNEL_STATIC_ROUTE}"
	    	for i in "${route[@]}"; do
		    ip route add ${i} dev ${TUNNEL_NAME} metric ${TUNNEL_MARK}
		done
		iptables -t mangle -A FORWARD -o ${TUNNEL_NAME} -p tcp --tcp-flags SYN,RST SYN -j TCPMSS --clamp-mss-to-pmtu
		iptables -t mangle -A INPUT -p esp -s ${TUNNEL_REMOTE_ENDPOINT} -d ${TUNNEL_LOCAL_ENDPOINT} -j MARK --set-xmark ${TUNNEL_MARK}
		ip route flush table 220
	}
	
	cleanup() {
	        IFS=',' read -ra route <<< "${TUNNEL_STATIC_ROUTE}"
	        for i in "${route[@]}"; do
	            ip route del ${i} dev ${TUNNEL_NAME} metric ${TUNNEL_MARK}
	        done
		iptables -t mangle -D FORWARD -o ${TUNNEL_NAME} -p tcp --tcp-flags SYN,RST SYN -j TCPMSS --clamp-mss-to-pmtu
		iptables -t mangle -D INPUT -p esp -s ${TUNNEL_REMOTE_ENDPOINT} -d ${TUNNEL_LOCAL_ENDPOINT} -j MARK --set-xmark ${TUNNEL_MARK}
		ip route flush cache
	}
	
	delete_interface() {
		ip link set ${TUNNEL_NAME} down
		ip link del ${TUNNEL_NAME}
	}
	
	# main execution starts here
	
	command_exists ip || echo "ERROR: ip command is required to execute the script, check if you are running as root, mostly to do with path, /sbin/" >&2 2>&2
	command_exists iptables || echo "ERROR: iptables command is required to execute the script, check if you are running as root, mostly to do with path, /sbin/" >&2 2>&2
	command_exists sysctl || echo "ERROR: sysctl command is required to execute the script, check if you are running as root, mostly to do with path, /sbin/" >&2 2>&2
	
	case "${PLUTO_VERB}" in
		up-client)
			create_interface
			configure_sysctl
			add_route
			;;
		down-client)
			cleanup
			delete_interface
			;;
	esac

Step 3: Make the following configuration under add_route() line

	Change it from:
 
	add_route() {
	IFS=',' read -ra route <<< "${TUNNEL_STATIC_ROUTE}"
    	for i in "${route[@]}"; do
	    ip route add ${i} dev ${TUNNEL_NAME} metric ${TUNNEL_MARK}

     	To:

      	add_route() {
	IFS=',' read -ra route <<< "${TUNNEL_STATIC_ROUTE}"
    	for i in "${route[@]}"; do
	    ip route add ${i} dev ${TUNNEL_NAME} metric ${TUNNEL_MARK} src <StrongSwan Server Private IP>

<img width="1212" alt="Screenshot 2023-09-13 at 4 55 54 PM" src="https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/ae2b56ed-7927-403e-86ae-036305ee98cf">

Step 4: Make the file executable

	sudo chmod 744 /etc/ipsec.d/aws-updown.sh

Step 5: Restart StrongSwan

	sudo ipsec restart

# Verify S2S VPN Tunnels are UP

You can veify if the tunnels you configure are working by going to the GovCloud VPC and select the S2S VPN you created.

Under Tunnel details you can see the "Status" as "Up"

<img width="1223" alt="Screenshot 2023-09-13 at 3 34 35 PM" src="https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/b70e0bbd-a500-4c8b-9955-8fa6b95286f2">

Congratulations! You have successfully set up a Site-to-Site VPN connection!

# Common Errors

If your tunnels are down check the following:

1. Verify your tunnel configurations listen in /etc/ipsec.conf have the proper IKE and ESP encryption algorithms

		ike=aes256-sha256-modp2048,aes128-sha1-modp1024!
		esp=aes128-sha256-modp2048,aes128-sha1-modp1024!

3. Verify your left and right subent is your VPC IPv4 CIDR of your commercial cloud and GovCloud respectively.

		leftsubnet=10.10.0.0/16
        	rightsubnet=10.0.0.0/16

4. Verify your leftupdown line is uncommented and has the subnet CIDR of your VPC cloud (GovCloud)

   		leftupdown="/etc/ipsec.d/aws-updown.sh -ln Tunnel1 -ll xxx.xxx.xxx.xxx/30 -lr xxx.xxx.xxx.xxx/30 -m 100 -r 10.0.0.0/16"

5. Verify your configuration for Tunnel2 are also configured properly.

6. Verify both if your tunnels are ESTABLISHED

   		sudo ipsec status

7. Verify your Source and Destination check configuration in the StrongSwan EC2 Instance is set to "Stop"

Changes made in these previous steps can take a few minutes to apply and for your tunnels to change status.
