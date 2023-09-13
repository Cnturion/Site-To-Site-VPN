# AWS GovCloud or AWS Cloud connecting to on-prem infrastructure

# Setting up a VPC

In the AWS GovCloud I will create a Virtual Private Cloud (VPC) with public and private subnet. 

1. Log in to your AWS Console
2. Select VPC
3. Create VPC
4. Select VPC and more
<img width="2110" alt="Screenshot 2023-09-12 at 4 28 02 PM" src="https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/90669f2d-b275-4516-8610-bbccc074af66">

5. Enter the name of your VPC
6. Enter the IPv4 CIDR Block
7. Feel free to adjust as needed. For my use I created 2 public and private subnets and left DNS options as default (enabled).

When your VPC is created you should have as many private and public subnets as your chose, an Internet Gateway, a main Route Table and route tables for your public and private subnets. 

# Setting up a Test-Instance

This EC2 Intance will be used to tst the connectivity of the two networks, the GovCloud and AWS commercial cloud. 

1. Go to EC2
2. Select Launch Instance
3. For name "Test-Instance"
4. Under Application and OS Images (Amazon Machine Image) select "Amazon Linux 2023 AMI" (Free tier eligible)
5. Instance Type: t2.micro
6. Keypair: Select a preffered key pair or create one if one doesn't exist.
   ![Screenshot 2023-09-12 at 4 33 45 PM](https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/c206c64b-18ca-41df-a1e1-2fb782e8a81f)

7. Under Network settings, select the VPC created earlier and a public subnet.
8. Create a new Security group allowing SSH and ICMP from 0.0.0.0/0 source.
![Screenshot 2023-09-12 at 4 43 01 PM](https://github.com/Cnturion/Site-To-Site-VPN/assets/98136077/6489c2b8-237c-4858-8759-2dd2c3c5522f)

9. Launch Instance

# Virtual Private Network: Customer Gateway

