### CloudFormation YAML Setup Guide – Group1-AWS-GroupProject

### Overview:
This CloudFormation guide explains the steps in out cloudformation-template.yaml file which is designed to automate the setup of our AWS infrasturcure. It deploys a VPC with both public and private subnets in different availability zones for high availability, an internet gateway, NAT gateway, a webserver with internet access and a database server that is private and cannot be reached by internet traffic.

### Resources:
- The Resources section is the core of the template. This is where AWS components like VPCs, subnets, gateways, etc. are defined.

### VPC – Virtual Private Cloud Automation Setup 
    Group1VPC:
        Type: AWS::EC2::VPC
        Properties:
            CidrBlock: 10.0.0.0/16
            EnableDnsSupport: true
            EnableDnsHostnames: true
        Tags:
            Key: Name
            Value: Group1-VPC

- Creates our virtual network in AWS.
- Type: Declears the resource we are deploying in this case a VPC.
- CidrBlock: Defines the IP range used in our network.
- DNS Support: Enables internal DNS for name resolution.
- Tag "Name": Labels this VPC as "Group1-VPC" in the AWS Console.

 ### Public Subnet
    Group1PublicSubnet:
        Type: AWS::EC2::Subnet
        Properties:
            VpcId: !Ref Group1VPC
            CidrBlock: 10.0.1.0/24
            AvailabilityZone: us-east-1a
            MapPublicIpOnLaunch: true
      Tags:
            Key: Name
            Value: Group1-Public-Subnet

- Availibility Zone: Located in us-east-1a.
- MapPublicIpOnLaunch: Automatically assigns public IPs to EC2s.
- Allows our web server to be publically accessible.

### Private Subnet Automation
    Group1PrivateSubnet:
        Type: AWS::EC2::Subnet
        Properties:
            VpcId: !Ref Group1VPC
            CidrBlock: 10.0.2.0/24
            AvailabilityZone: us-east-1b
            MapPublicIpOnLaunch: false
      Tags:
            Key: Name
            Value: Group1-Private-Subnet

- Creates a private subnet in the us-east-1b availibility zone for high availability.
- Doesn’t map a public IP to instances in this subnet, and won't be routed through an IGW.

### Public Route Table Automation
    Group1PublicRouteTable:
        Type: AWS::EC2::RouteTable
        Properties:
            VpcId: !Ref Group1VPC
        Tags:
                Key: Name
                Value: Group1-Public-RouteTable

- Sets up our public route table

    AddPublicRoute:
        Type: AWS::EC2::Route
        DependsOn: AttachIGWToVPC
        Properties:
            RouteTableId: !Ref Group1PublicRouteTable
            DestinationCidrBlock: 0.0.0.0/0
            GatewayId: !Ref Group1IGW

- Sends traffic destined for the internet (0.0.0.0/0) to the IGW.

    AssociatePublicSubnetWithRouteTable:
        Type: AWS::EC2::SubnetRouteTableAssociation
        Properties:
            SubnetId: !Ref Group1PublicSubnet
            RouteTableId: !Ref Group1PublicRouteTable

- A set of rules that determine where traffic goes from our subnets.

### Private Route Table Automation
    Group1PrivateRouteTable:
        Type: AWS::EC2::RouteTable
        Properties:
            VpcId: !Ref Group1VPC
        Tags:
            Key: Name
          Value: Group1-Private-RouteTable

    AssociatePrivateSubnetWithRouteTable:
        Type: AWS::EC2::SubnetRouteTableAssociation
        Properties:
            SubnetId: !Ref Group1PrivateSubnet
            RouteTableId: !Ref Group1PrivateRouteTable

- Controls how resources inside the private subnet communicate.
- Essential for keeping resources private from the public internet.

### Internet Gateway Automation
    Group1IGW:
        Type: AWS::EC2::InternetGateway
        Properties:
        Tags:
            Key: Name
          Value: Group1-IGW
 
- Allows communication between resources in our VPC and the internet.

    AttachIGWToVPC:
        Type: AWS::EC2::VPCGatewayAttachment
        Properties:
            VpcId: !Ref Group1VPC
            InternetGatewayId: !Ref Group1IGW

- Connects the IGW to the VPC so public subnets can access the internet.
- !Ref: Returns the logical name of the resource.

### Elastic IP for NAT Gateway Automation
    Group1NATEIP:
        Type: AWS::EC2::EIP
        Properties:
            Domain: vpc

- This part of our CloudFormation script allocates an Elastic IP address (EIP), which will later be attached to our NAT Gateway.

### NAT Gateway Automation
    Group1NATGateway:
        Type: AWS::EC2::NatGateway
        Properties:
            AllocationId: !GetAtt Group1NATEIP.AllocationId
            SubnetId: !Ref Group1PublicSubnet
      Tags:
            Key: Name
          Value: Group1-NAT-Gateway

    AddPrivateRouteToNAT:
        Type: AWS::EC2::Route
        Properties:
            RouteTableId: !Ref Group1PrivateRouteTable
            DestinationCidrBlock: 0.0.0.0/0
            NatGatewayId: !Ref Group1NATGateway

- This section creates a NAT Gateway and attaches the EIP we allocated in the last step to allow the instances in our private subnet to access the internet, but prevents the internet from initiating a connection to those instances. (So the Database can minimize vulnerabilities by staying up-to-date on updates.)

### Automation of our Security Group for our Web Server
    Group1WebSG:
        Type: AWS::EC2::SecurityGroup
        Properties:
            GroupDescription: Allow SSH and HTTP access
            VpcId: !Ref Group1VPC
            SecurityGroupIngress:
                IpProtocol: tcp
                FromPort: 22
                ToPort: 22
                CidrIp: 0.0.0.0/0
                IpProtocol: tcp
                FromPort: 80
                ToPort: 80
                CidrIp: 0.0.0.0/0
                IpProtocol: tcp
                FromPort: 443
                ToPort: 443
                CidrIp: 0.0.0.0/0     
      Tags:
            Key: Name
          Value: Group1-Web-SecurityGroup

This security group for our web server allows:
            - SSH (port 22) – for administrators to log in remotely.
            - HTTP (port 80) – for users to access the web server.
            - HTTPS (port 443) - Allows encrypted web traffic (secure communication).

### Automation for our DB Server Security Group 
    Group1DBSG:
        Type: AWS::EC2::SecurityGroup
        Properties:
            GroupDescription: Allow MySQL from Web Server SG
            VpcId: !Ref Group1VPC
            SecurityGroupIngress:
                IpProtocol: tcp
                FromPort: 3306
                ToPort: 3306
                SourceSecurityGroupId: !Ref Group1WebSG
      Tags:
            Key: Name
          Value: Group1-DB-SecurityGroup

- Group1DBSG defines a security group for the database server. 
- It only allows MySQL traffic (port 3306) from the web server's security group (Group1WebSG)
- This improves security by not exposing the database to the public internet.

### EC2 Web Server Automation
    Group1WebInstance:
        Type: AWS::EC2::Instance
        Properties:
            InstanceType: t2.micro
            KeyName: !Ref KeyName
            ImageId: ami-0c02fb55956c7d316  # Amazon Linux 2 (us-east-1)
            SubnetId: !Ref Group1PublicSubnet
        SecurityGroupIds:
            !Ref Group1WebSG
      Tags:
            Key: Name
          Value: Group1-WebServer
      UserData:
            Fn::Base64: !Sub |
                #!/bin/bash
                yum update -y
                yum install -y httpd
                systemctl enable httpd
                systemctl start httpd
                echo "<h1>Group1 Web Server</h1>" > /var/www/html/index.html

- Group1WebInstance automates the launch of a web server EC2 instance. 
- It uses the Amazon Linux 2 AMI in a public subnet with the associated Group1WebSG security group. 
- The UserData script installs and starts Apache (httpd), and creates a simple HTML page with the content "Group1 Web Server".

### EC2 DB Server Automation 
    Group1DBInstance:
        Type: AWS::EC2::Instance
        Properties:
            InstanceType: t2.micro
            KeyName: !Ref KeyName
            ImageId: ami-0c02fb55956c7d316  # Amazon Linux 2 (us-east-1)
            SubnetId: !Ref Group1PrivateSubnet
      SecurityGroupIds:
            !Ref Group1DBSG
      Tags:
            Key: Name
          Value: Group1-DatabaseServer
      UserData:
            Fn::Base64: !Sub |
                #!/bin/bash
                yum update -y
                yum install -y mariadb-server
                systemctl enable mariadb
                systemctl start mariadb

- Group1DBInstance automates the deployment of a database EC2 instance in a private subnet using the Group1DBSG security group. 
- It launches an Amazon Linux 2 server. 
- The UserData script is designed to install and start MariaDB. 
- This instance is secured and isolated from public access.