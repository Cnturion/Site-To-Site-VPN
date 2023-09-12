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
