# AWS High Availability Project

## Overview
This Project demonstrates how to build reliable, fault-tolerant, and highly available systems in the cloud using Amazon Web Services (AWS). The focus is on integrating **Elastic Load Balancing** and **Auto Scaling groups** with Amazon EC2 instances and Amazon RDS. Understanding these concepts is crucial for AWS Solutions Architects to ensure systems can withstand failures while maintaining performance.

### Key Concepts
- **Fault Tolerance:** This refers to a system's ability to remain operational even when some components fail. In AWS, services like Auto Scaling and Elastic Load Balancers contribute to fault tolerance by automatically adjusting resources and distributing traffic.
  
- **High Availability:** This concept emphasizes the system's ability to recover quickly from failures rather than merely preventing them. High availability is achieved by deploying resources across multiple Availability Zones and using services like Amazon RDS with Multi-AZ deployments.

## Project Environment
The following AWS resources were provisioned through AWS CloudFormation, creating a baseline for the project:
- **Amazon VPC:** A virtual private cloud to host resources securely.
- **Public and Private Subnets:** Two subnets in different Availability Zones for high availability, with public subnets hosting load balancers and private subnets hosting application servers.
- **Internet Gateway:** Allows resources in the public subnets to communicate with the internet.
- **NAT Gateway:** Facilitates internet access for resources in private subnets.
- **Application Load Balancer:** Distributes incoming application traffic across multiple targets, such as EC2 instances.
- **EC2 Instance:** An application server running a basic inventory tracking application in a private subnet.
- **Aurora DB Cluster:** A highly available relational database with a single DB instance in a private subnet, storing inventory data.

## Project Architecture
![image_123650291 (1)](https://github.com/user-attachments/assets/196118bb-08bf-4aa2-8189-adce85719dca)

## Tasks

### Task 1: Inspect Existing Lab Environment
In this initial task, I reviewed the configuration of the existing AWS resources to understand the architecture and verify all components were provisioned correctly.

### Task 2: Create a Launch Template
1. **Open the AWS Management Console.**
2. **Navigate to Launch Templates:** Under **Instances**, I selected **Launch Templates**.
3. **Create Launch Template:** 
   - Clicked **Create launch template**.
   - **Launch template name:** Entered `LaunchTemplate-<NUMBER>`.
   - **Template version description:** Provided a brief description, e.g., `Initial version`.
4. **Amazon Machine Image (AMI) Selection:** Chose the **Quick Start** tab and selected **Amazon Linux 2023 AMI**.
5. **Instance Type:** Selected **t2.micro** for cost-effective computing.
6. **Network Settings:** 
   - **Security groups:** Assigned `Inventory-App` to ensure proper access controls.
   - **IAM instance profile:** Assigned `Inventory-App-Role` for the required permissions.
   - **User data:** Pasted initialization scripts to configure the instance upon launch.
7. **Create the Launch Template:** Clicked **Create launch template** to finalize.

### Task 3: Create an Auto Scaling Group
1. **Navigate to Auto Scaling Groups:** Selected **Auto Scaling Groups** from the left navigation pane.
2. **Create Auto Scaling Group:**
   - **Auto Scaling group name:** Entered `Inventory-App-ASG`.
   - **Launch template:** Selected the launch template created earlier.
3. **VPC and Subnets Configuration:**
   - **VPC:** Chose `Lab VPC`.
   - **Subnets:** Selected `Private Subnet 1` and `Private Subnet 2` for increased security.
4. **Attach to Existing Load Balancer:** 
   - Selected **Choose from your load balancer target groups** and chose `Inventory-App | HTTP`.
5. **Configure Health Check and Monitoring:**
   - Set the **Health check grace period** to allow instances time to initialize.
   - Enabled monitoring through **CloudWatch** for better visibility into the Auto Scaling group’s performance.
6. **Set Desired Capacity:** Defined the minimum, maximum, and desired number of instances based on expected load.
7. **Review and Create:** Verified the configuration and clicked **Create Auto Scaling group**.

### Task 4: Test the Application
1. **Target Groups Verification:** 
   - Navigated to **Target Groups**, selected `Inventory-App`, and confirmed the registered instances were healthy.
2. **Application Testing:**
   - Accessed the Application Load Balancer's DNS name in a web browser to verify that the application was reachable and functional.

### Task 5: Test High Availability of the Application Tier
1. **Simulate Instance Termination:** Terminated one of the Inventory-App instances to simulate a failure.
2. **Monitor Application Availability:** Continuously refreshed the application page to ensure it remained accessible.
3. **Auto Scaling Response:** Observed that Auto Scaling launched a replacement instance, maintaining the desired number of instances.

### Task 6: Configure High Availability of the Database Tier
1. **Database Instance Review:** 
   - Accessed **Databases** in the console to locate the `inventory-primary` instance.
2. **Availability Zone Confirmation:** Noted the Availability Zone of the primary database to plan for future Multi-AZ deployments.

### Task 7: Make the NAT Gateway Highly Available
1. **Launch Additional NAT Gateway:** Deployed another NAT gateway in the second Availability Zone to ensure internet access redundancy for resources in private subnets.

### Task 8: Force a Failover of the Aurora Database
1. **Verify Read Replica Status:** Confirmed that the `inventory-replica` was `Available` before proceeding.
2. **Initiate Failover:**
   - Selected `inventory-primary` and chose **Actions > Failover** to test failover capabilities.
3. **Monitor Failover Process:** Observed events in the **Events** section, noting the transition of roles from the primary to the read replica.

## Conclusion
I have successfully verified that my application is both highly available and fault-tolerant. By integrating Auto Scaling, Elastic Load Balancing, and AWS RDS with Multi-AZ capabilities, I have built a robust architecture suitable for production environments.
