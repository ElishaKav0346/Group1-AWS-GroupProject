# AWS Group Project - Group 1 – Designing a Secure and Scalable AWS Cloud Infrastructure Project

## Overview
This repository contains group 1s AWS project which involves designing and deploying a secure and scalable AWS infrastructure to host a basic web application backed by a private database.

We worked through each stage of the project collaboratively. Our aim was to follow AWS best practices while learning how cloud environments are built and secured in real-world scenarios.


## Task 1 – Architecture Design

We started by planning the cloud architecture:
- Created a custom VPC with both public and private subnets.
- Placed the EC2 Webserver instance in a public subnet.
- Placed the EC2 Database instance in a private subnet.
- Configured routing, an Internet Gateway, and a NAT Gateway for proper traffic flow.
- Set up security groups to control which parts could talk to each other.

Our architecture diagram can be found in the `task1_architecture/` folder.


## Task 2 – Manual Implementation (AWS Console)

In this step, we manually built the infrastructure using the AWS Management Console:
- Launched a EC2 instances for the web and database servers.
- Set up subnets, route tables, NAT Gateway, and Internet Gateway.
- Verified that the web server is publicly accessible.
- Confirmed that the web server can securely connect to the private database.

Screenshots and documentation are located in `task2_implementation/`.

## AWS Implementation Screenshot Links:

### 1. We started by creating our Group1-Virtual Private Cloud
![VPC](Screenshots2/screenshot1.png)

### 2. Next, we created a public and private subnet 
![Public Subnet](Screenshots2/screenshot2.png)

### 3. Private Subnet
![Private Subnet](Screenshots2/screenshot3.png)

### 4. Both of our Subnets:
![Group1-Subnets](Screenshots2/screenshot4.png)

### 5. We then created an internet gateway 
![IGW](Screenshots2/screenshot5.png)

### 6. Next, we configured route tables
![Public Route Table](Screenshots2/screenshot6.png)

### 7. Private Route Table
![Private Route Table](Screenshots2/screenshot7.png)

### 8. NAT gateway configuration 
![NAT Gateway](Screenshots2/screenshot8.png)

### 9. Then, we configured security groups to control the flow of traffic to our EC2 instances
![Our Web Server Security Group](Screenshots2/screenshot9.png)

### 10. Database Security Group 
![DB Security Group](Screenshots2/screenshot10.png)

### 11. Finally we deployed a webserver and a database EC2 instance 
![Web Server EC2 Instance](Screenshots2/screenshot11.png)

### 12. Database EC2 Instance 
![Database EC2 Instance](Screenshots2/screenshot12.png)



## Task 3 – Infrastructure as Code (CloudFormation)

We translated our manual setup into code using CloudFormation:
- Templates were created to automate creation of the VPC, subnets, EC2 instances, and networking components.
- This allowed us to quickly redeploy the infrastructure without using the console.

CloudFormation templates and setup instructions are in `task3_automation/`.


## Task 4 – Security Considerations

In this final step, we reviewed and documented the security of our system:
- Database is not exposed to the internet, only the web server can access it.
- We used private key authentication for SSH instead of passwords.
- We used isolated subnets and security groups to tightly control traffic.

Our security design and notes are in the `task4_security/` folder.

## Summary

This project helped us learn how to:
- We designed a secure cloud architecture.
- We used both the AWS Console and Infrastructure-as-Code tools.
- Used best practices for public and private subnet usage.
- Applied real-world security configurations in AWS.

We’re Group 1, and this is our AWS cloud infrastructure project, everything is organized by task inside this repository.


