# AWS VPC Flow Logs Project with CloudWatch and Custom Trust Policy

This project demonstrates the setup of VPC Flow Logs for monitoring network traffic within an AWS Virtual Private Cloud (VPC). It includes configuring VPC subnets, security groups, a custom IAM role with an attached policy, and a custom trust policy to allow VPC Flow Logs to interact with AWS services.

---

## Features

- **VPC Setup**: Create a VPC with subnets and security groups.
- **Custom Trust Policy**: Define a trust relationship for the custom IAM role.
- **VPC Flow Logs**: Capture and analyze network traffic.
- **CloudWatch Logs**: Store and visualize flow logs.
- **IAM Policy and Role**: Assign policies to enable logging.
- **Traffic Verification**: Test with ping to an external IP address.

---

## Steps to Recreate

### 1. **Create a VPC**
   - Navigate to **VPC** in the AWS Management Console.
   - Create a new VPC:
     - **IPv4 CIDR block**: `10.0.0.0/16`.
   - Create a public subnet:
     - **CIDR block**: `10.0.1.0/24`.

### 2. **Set Up a Security Group**
   - Create a security group for your VPC:
     - **Inbound Rules**: Allow ICMP traffic for `0.0.0.0/0` (to allow ping).
     - **Outbound Rules**: Allow all traffic (default).

### 3. **Launch an EC2 Instance**
   - Launch an EC2 instance in the public subnet.
   - Attach the previously created security group to the instance.
   - Connect to the instance via SSH.

### 4. **Enable VPC Flow Logs**
   - Go to the **VPC Dashboard** and select your VPC.
   - Enable **Flow Logs**:
     - **Destination**: CloudWatch Logs.
     - **Log Group**: Create a new log group (e.g., `/vpc-flow-logs`).
     - **IAM Role**: Assign a custom IAM role (see Steps 5 and 6).

### 5. **Create a Custom IAM Role**
   - Navigate to **IAM** in the AWS Management Console and create a new **IAM Role**.
   - Select **Custom Trust Policy** as the trusted entity and define the following policy:
     ```json
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Principal": {
             "Service": "vpc-flow-logs.amazonaws.com"
           },
           "Action": "sts:AssumeRole"
         }
       ]
     }
     ```
   - This trust policy allows the VPC Flow Logs service to assume the IAM role.

### 6. **Attach a Policy to the IAM Role**
   - Attach the following policy to the custom IAM role:
     ```json
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Action": [
             "logs:CreateLogGroup",
             "logs:CreateLogStream",
             "logs:PutLogEvents"
           ],
           "Resource": "*"
         }
       ]
     }
     ```
   - Save the IAM role (e.g., `custom-vpc-flowlogs-role`).

### 7. **Assign the Role to VPC Flow Logs**
   - While enabling flow logs for your VPC, select the **custom IAM role** created in Steps 5 and 6.

### 8. **Verify Traffic with Ping**
   - SSH into the EC2 instance and run:
     ```bash
     ping <website-domain>
     ```
   - Example:
     ```bash
     ping google.com
     ```
   - Note the external website's IP address.

### 9. **Check VPC Flow Logs**
   - Navigate to **CloudWatch Logs** and select the created log group.
   - Search for traffic logs corresponding to the noted IP address to verify that flow logs are working.

---

## How It Works

1. **Custom Trust Policy**: Grants the VPC Flow Logs service permission to assume the custom IAM role.
2. **IAM Role and Policy**: Enables the logging of network traffic in CloudWatch Logs.
3. **VPC Flow Logs**: Captures metadata for analysis.
4. **Ping Test**: Simulates network traffic to validate the setup.

---

## Folder Structure

```plaintext
aws-vpc-flowlogs-monitoring-with-custom-trust-policy/
├── README.md                # Project documentation
├── Screenshots/             # Screenshots for VPC, IAM, and CloudWatch configurations
└── Terraform/               # Optional folder for infrastructure automation

Requirements
AWS Free Tier Account or equivalent.
Basic Knowledge of AWS services (VPC, CloudWatch, IAM).
SSH access to the EC2 instance for testing.

Screenshots
VPC and Subnet Configuration
Security Group Rules
Custom Trust Policy
IAM Role and Policy Setup
CloudWatch Log Group with Flow Logs
Ping Traffic Verification

Future Enhancements
Automate the setup using Terraform or CloudFormation.
Integrate with Amazon Athena for detailed traffic analysis.
Add CloudWatch Alarms to monitor specific patterns.
