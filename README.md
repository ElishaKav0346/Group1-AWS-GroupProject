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


