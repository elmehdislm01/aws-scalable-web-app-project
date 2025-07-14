# Project 1: Scalable Web Application with ALB and Auto Scaling
This repository contains the architecture and implementation details for a scalable web application deployed on Amazon Web Services (AWS). The core of this project involves utilizing EC2 instances to host the web application, leveraging an Application Load Balancer (ALB) to efficiently distribute incoming traffic, and an Auto Scaling Group (ASG) to dynamically adjust computing capacity based on demand. This setup ensures continuous availability, resilience, and optimized resource utilization for the web application.



![Solution Architecture Diagram](Blank%20diagram.png)

Here's a breakdown of the key components and their interactions:

Virtual Private Cloud (VPC):

My Web App VPC serves as the isolated virtual network within AWS where all our resources are deployed. This provides a secure and controlled environment for the application.

Availability Zones (AZs) and Subnets:

To achieve high availability and disaster recovery capabilities, the VPC is configured across two Availability Zones (AZ1 and AZ2).

Within each AZ, there are:

Public Subnets: These subnets contain resources that require direct internet access. Our Application Load Balancer (ALB) is deployed here.

Private Subnets: These subnets host our EC2 instances running the web application. They do not have direct internet access, enhancing security.

Internet Gateway:

The Internet Gateway is attached to the VPC and allows communication between resources in the public subnets and the internet. This is how users can access the web application.

Application Load Balancer (ALB):

The Web App ALB is an internet-facing load balancer deployed across the public subnets in both AZ1 and AZ2.

Function: It receives incoming HTTP/HTTPS requests from the internet and intelligently distributes this traffic across the healthy EC2 instances registered to its target group. This ensures no single instance becomes a bottleneck and provides resilience against instance failures.

Auto Scaling Group (ASG):

The Web App ASG is configured to manage the deployment and scaling of our EC2 instances across the private subnets in AZ1 and AZ2.

Function:

Scalability: Based on predefined scaling policies (e.g., CPU utilization thresholds), the ASG automatically launches new EC2 instances when demand increases (scaling out) and terminates instances when demand decreases (scaling in).

High Availability & Self-Healing: The ASG continuously monitors the health of its instances. If an instance becomes unhealthy or terminates for any reason, the ASG automatically replaces it, ensuring the desired number of healthy instances is always maintained.

EC2 Instance 1 and EC2 Instance 2 (and more as scaled out) are the web servers running our application, managed by this ASG within the private subnets.

Security Groups:

ALB Security Group (ALB SG): This security group is associated with the Application Load Balancer.

Rule: It is configured to allow inbound HTTP (Port 80) and/or HTTPS (Port 443) traffic from anywhere on the internet (0.0.0.0/0). This makes the web application accessible to users.

EC2 Security Group (EC2 SG): This security group is associated with the EC2 instances.

Rule: It is strictly configured to only allow inbound HTTP (Port 80) and/or HTTPS (Port 443) traffic from the ALB Security Group (ALB SG). This is a critical security measure, ensuring that the EC2 instances cannot be directly accessed from the internet, only via the load balancer.

Traffic Flow Summary:

User requests from the Internet first hit the Internet Gateway and are then routed to the Web App ALB (in the public subnets). The ALB, secured by the ALB SG, forwards these requests to the healthy EC2 Instances (in the private subnets). These instances are managed by the Web App ASG and are protected by the EC2 SG, which only permits traffic originating from the ALB. This architecture ensures a secure, highly available, and dynamically scalable web application.
