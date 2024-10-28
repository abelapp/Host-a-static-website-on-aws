---
# Hosting a Static HTML Website on AWS

This project demonstrates hosting a static HTML website on AWS using various AWS services and best practices to ensure high availability, scalability, and security. The infrastructure was designed to support a DevOps deployment pipeline and provide fault tolerance across multiple Availability Zones (AZs). 

## Architecture Overview

The static website is deployed within a Virtual Private Cloud (VPC) across two different AZs to achieve availability and fault tolerance. Key AWS services and components were configured to establish a resilient and secure environment.

### Resources and Components Used
1. **VPC Configuration**:
   - Set up a custom VPC with both public and private subnets across two AZs to ensure fault tolerance.
   
2. **Internet Gateway**:
   - Configured to facilitate connectivity for VPC instances to the internet.
   
3. **Availability Zones**:
   - Deployed resources in two AZs to enhance the availability of the web application.

4. **Security Groups**:
   - Created and attached security groups as firewalls for granular control over inbound and outbound traffic.

5. **Subnet Usage**:
   - Public subnets were used for NAT Gateway and Application Load Balancer (ALB).
   - Private subnets were used to host EC2 instances for web serving, enhancing security by limiting direct internet access.

6. **Route Tables**:
   - Configured route tables to route traffic from instances in private subnets to the internet through the NAT Gateway.

7. **EC2 Instance Connect Endpoint**:
   - Set up to enable secure and efficient connections to resources within both public and private subnets.

8. **EC2 Instances**:
   - Deployed EC2 instances in private subnets for serving the website content securely.
   
9. **Autoscaling Group**:
   - Configured to automatically scale the EC2 instances based on traffic, ensuring cost-effective scalability.

10. **GitHub for Version Control**:
    - Web files are stored on GitHub to manage version control and collaboration.

11. **AWS Certificate Manager (ACM)**:
    - Configured to secure communication between components using SSL/TLS certificates.

12. **Simple Notification Service (SNS)**:
    - Used to alert about autoscaling activities to monitor and respond to scaling events.

13. **Route 53**:
    - Registered a domain name and configured DNS records for simplified access to the website.

## Prerequisites

- AWS account with permissions for VPC, EC2, S3, IAM, Route 53, ACM, and SNS.
- Git installed locally or on EC2 instances.
- Domain name registered via Route 53 (or another DNS provider, with Route 53 handling DNS records).

## Deployment Instructions

### Step 1: Set Up Infrastructure
1. Configure a VPC with both public and private subnets across two AZs.
2. Deploy an Internet Gateway for VPC connectivity.
3. Set up Security Groups for controlled access to EC2 instances and ALB.
4. Establish Route Tables for routing private subnet traffic to the internet via NAT Gateway.
5. Configure EC2 Instance Connect Endpoint to securely connect to instances in both public and private subnets.

### Step 2: Configure Application Load Balancer
1. Deploy an Application Load Balancer (ALB) in public subnets.
2. Use ALB to distribute traffic across EC2 instances in the private subnets.

### Step 3: Set Up EC2 Instances
1. Create an autoscaling group to automatically manage the EC2 instances.
2. Configure the instances with the following bootstrapping script to install Apache, pull the website files from GitHub, and start the web server:

   ```bash
   #!/bin/bash 
   sudo su 
   yum update -y 
   yum install -y httpd 
   cd /var/www/html 
   yum install git -y 
   git clone https://github.com/abelapp/Host-a-static-website-on-aws.git
   cp -R host-a-statc-website-on-aws/. /var/www/html/ 
   rm -rf host-a-statc-website-on-aws 
   systemctl enable httpd  
   systemctl start httpd
   ```

### Step 4: Secure the Application
1. Use AWS Certificate Manager to issue and attach SSL/TLS certificates to the ALB.
2. Configure Route 53 with DNS records to direct domain traffic to the ALB.

### Step 5: Monitor and Notify with SNS
1. Set up an SNS topic to alert for autoscaling events.
2. Subscribe to notifications to monitor instance scaling activities and potential issues.

## Diagram

For a visual overview of the architecture, refer to the attached `Architecture Diagram`.

## Repository Structure

- **Scripts**: Includes the deployment script to install dependencies and start the Apache web server on EC2 instances.
- **Architecture Diagram**: `ArchitectureDiagram.drawio` provides a flowchart of the AWS resources and data flow.

## Security Considerations

- EC2 instances are placed in private subnets to limit direct internet access.
- Secure communication between components is ensured with AWS Certificate Manager (ACM) SSL certificates.
- Security Groups and IAM roles are configured with least privilege access.

## Future Enhancements

- Use CloudFront as a content delivery network (CDN) to cache content and reduce latency.
- Implement CI/CD for automated deployments to the website.
- Add CloudWatch for detailed monitoring and logging.

## License

This project is licensed under the MIT License.

---
