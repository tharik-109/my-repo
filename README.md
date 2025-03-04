**# Assignment-01: Load Balancer & Auto Scaling Group (H1)**

Objective

The goal of this assignment is to design and implement a cloud infrastructure that supports the deployment of a Spring 3 Hibernate application. The infrastructure will be designed for high availability, scalability, and security. The application will be deployed on private servers to ensure a secure environment.

Spring 3 Hibernate Repository:

Spring 3 Hibernate GitHub Repository

Task Overview

1. Infrastructure Design and Diagram Submission

Design a comprehensive infrastructure diagram.

The diagram should include all key components, such as:

Networking (VPC, Subnets, NAT Gateway, Security Groups)

Compute (App Servers in Private Subnets, Auto Scaling Group)

Load Balancer (ALB for distributing traffic to app servers)

Submit the diagram for review and feedback.

After receiving feedback, proceed with implementation.

2. Final Implementation Code

Implement the infrastructure design using AWS.

Deploy the Spring 3 Hibernate application in a secure private environment.

Ensure high availability, scalability, and security best practices.

Infrastructure Components & Design

1. Virtual Private Cloud (VPC)

Create a dedicated VPC to host all resources.

2. Subnets

Public Subnets: Host the Load Balancer.

Private Subnets: Host the application servers.

3. Load Balancer (ALB)

Application Load Balancer (ALB) will distribute traffic across multiple instances in private subnets.

Ensures high availability and failover.

4. Auto Scaling Group (ASG)

Auto Scaling Group (ASG) will dynamically scale application servers based on traffic and resource utilization.

Ensures scalability and fault tolerance.

5. Security Groups

Load Balancer Security Group: Allows HTTP/HTTPS traffic from the internet.

Application Security Group: Allows traffic only from the Load Balancer.

Database Security Group: Allows traffic only from the application servers.

6. NAT Gateway

NAT Gateway allows application servers in private subnets to access the internet for software updates without exposing them publicly.

Implementation Steps

Create a VPC with appropriate CIDR blocks.

Set up Public and Private Subnets within the VPC.

Deploy an Application Load Balancer (ALB) in public subnets.

Launch EC2 instances in Private Subnets to host the Spring 3 Hibernate application.

Set up an Auto Scaling Group (ASG) to manage instance scaling.

Configure Security Groups to control network traffic.

Deploy a NAT Gateway to provide internet access for private subnets.

Test the deployment by accessing the application through the Load Balancer.


day-1
![Screenshot-18](https://github.com/user-attachments/assets/657b6e1f-25a1-4d5d-945d-3b2700ad033a)
day-2
![Screenshot-32](https://github.com/user-attachments/assets/c57b623c-0245-4ad5-9ffc-578b2eafe29d)
