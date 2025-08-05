## Step By Step Guide to the AWS Implementation:

## Overview:
This step by step guide is designed to assist in the deplolyment of our AWS infrastructure. The entire group must be able to understand and demonstrate these steps. 

## Step 1: Create VPC 
Services --> VPC

 - VPC only
 - Name Tage: Group1-VPC
 - IPV4 CIDR Manual input
 - IPv4 CIDR – 10.0.0.0/16
 - No IPv6 CIDR Block
 - Defaults for the rest
 - Create VPC

## Step 2: Create Public and Private Subnets in Group1-VPC
		VPC -->  Subnets

## Public Subnet
 - VPC ID: (Group1-VPC)
 - Subnet Name: Group1-Public-Subnet
 - Availability Zone: us-east-1a 
 - IPv4 VPC CIDR Block: 10.0.0.0/16
 - IPv4 Subnet CIDR block: 10.0.1.0/24
 - Create Subnet

## Private Subnet 
 - VPC ID: (Group1-VPC)
 - Subnet Name: Group1-Private-Subnet
 - Availability Zone: us-east-1b
 - IPv4 VPC CIDR block: 10.0.0.0/16 
 - IPv4 Subnet CIDR block: 10.0.2.0/24
 - Create Subnet

## Step 3: Create the Internet Gateway 
			Name tag: Group1-IGW
			Tags: Name, Value: Group1-IGW
	        Attach Internet Gateway to VPC
		VPC --> Internet gateways --> Attach to VPC
 - Attach to the Group1-VPC we created

## Step 4: Create Route Tables 
1.	Public route table for the web server
2.	Private route table for the database server

## Public Route Table
 - Name: Group1-Public-RouteTable
 - VPC (Group1-VPC) 
 - Create 

 Edit Routes 
 - Add route to internet Gateway using 0.0.0.0/0 to allow internet traffic to be routed
Edit subnet associations 
 - Associate Group1-public-subnet with this route table

## Private Route Table
Edit subnet associations 
 - Associate Group1-Private-Subnet with this route table

## Step 5: Create a NAT Gateway
Start by allocating an Elastic IP Address
EC2 --> Elastic IP Address (copy the address in case it doesn’t auto populate in the next step)

Create NAT gateway
VPC --> NAT gateway --> Create NAT gateway
 - Name: Group1-NAT-Gateway
 - Subnet: Group1-Public-Subnet
 - Connectivity Type: Public
 - Elastic IP: Add the elastic IP you allocated in the last part of step 5
 - Create
--> Group1-Private-RouteTable --> Edit Route --> Add Route to NAT gateway

## Step 6: Launch webserver and database EC2 instances 
EC2 --> Launch Instances 

1. EC2 Web Server
 - Name: Group1-WebServer
 - Application / OS: I left the default 
 - Instance Type: I left the default
 - Key pair (login): Create new key pair 
   - Name: Group1-Key
   - .ppk (for use with putty)

Edit Networking settings
 - VPC: Group1-VPC
 - Subnet: Group1-Public-Subnet
 - Auto-assign public IP: Enable

Create Security Group
 - Name: Group1-Web-SecurityGroup
 - Inbound Rules:
 - Allow SSH from my IP (port 22)
 - Allow web access (port 80)

Launch Instance

2. EC2 Database Server
 - Name: Group1- DatabaseServer
 - Application / OS: I left the default 
 - Instance Type: I left the default
 - Key pair (login): Group1-Key

Edit Networking settings
 - VPC: Group1-VPC
 - Subnet: Group1-Private-Subnet
 - Auto-assign public IP: Disable

Create Security Group
 - Name: Group1-DB-SecurityGroup
 - Inbound Rules:
 - Allow SSH from my IP (port 22)
 - Allow DB access from Web server only 
        - MYSQL/Aurora 
        - Source: Custom
        - Group1-Web-SecurityGroup 
        - Port: 3306

Launch Instance 

