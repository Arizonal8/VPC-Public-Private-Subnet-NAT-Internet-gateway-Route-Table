# AWS Cloud Infrastructure Project

## Project Overview

This project demonstrates how to architect and deploy a secure and scalable AWS cloud infrastructure using Infrastructure as Code (IaC) principles. The deployment includes:
- A custom Virtual Private Cloud (VPC)
- Public and Private Subnets
- Internet Gateway and NAT Gateway for connectivity
- Route Tables for routing configuration
- An EC2 instance running Ubuntu (Linux)

## Architecture Components

1. **VPC**: Custom VPC with a CIDR block (e.g., 10.0.0.0/16)
2. **Subnets**:
    - Public Subnet (e.g., 10.0.1.0/24)
    - Private Subnet (e.g., 10.0.2.0/24)
3. **Internet Gateway**: Attached to the VPC to enable internet access for public subnet
4. **NAT Gateway**: Allows instances in the private subnet to access the internet
5. **Route Tables**: Configured to route traffic appropriately between subnets and gateways
6. **EC2 Instance**: Launched in the public subnet and accessed using SSH via a .pem key

## Setup Instructions

### Step 1: Configure the VPC and Networking

1. Create a VPC:
   ```bash
   aws ec2 create-vpc --cidr-block 10.0.0.0/16
   ```

2. Create Public and Private Subnets:
   ```bash
   aws ec2 create-subnet --vpc-id <vpc-id> --cidr-block 10.0.1.0/24 --availability-zone <zone>
   aws ec2 create-subnet --vpc-id <vpc-id> --cidr-block 10.0.2.0/24 --availability-zone <zone>
   ```

3. Create and Attach Internet Gateway:
   ```bash
   aws ec2 create-internet-gateway
   aws ec2 attach-internet-gateway --vpc-id <vpc-id> --internet-gateway-id <igw-id>
   ```

4. Create and Associate Route Table:
   ```bash
   aws ec2 create-route-table --vpc-id <vpc-id>
   aws ec2 associate-route-table --subnet-id <subnet-id> --route-table-id <rtb-id>
   aws ec2 create-route --route-table-id <rtb-id> --destination-cidr-block 0.0.0.0/0 --gateway-id <igw-id>
   ```

5. Create NAT Gateway (in public subnet):
   ```bash
   aws ec2 allocate-address
   aws ec2 create-nat-gateway --subnet-id <public-subnet-id> --allocation-id <eip-alloc-id>
   ```

6. Update Private Route Table with NAT Gateway route:
   ```bash
   aws ec2 create-route --route-table-id <private-rtb-id> --destination-cidr-block 0.0.0.0/0 --nat-gateway-id <nat-id>
   ```

### Step 2: Launch and Connect to EC2

1. Launch Ubuntu EC2 in Public Subnet with security group allowing port 22 (SSH).
2. SSH into EC2 instance using:
   ```bash
   ssh -i "your-key.pem" ubuntu@<your-ec2-public-ip>
   ```

## Tools Used

- AWS Management Console
- AWS CLI (Linux)
- EC2 with Ubuntu 24.04
- Command Prompt (for SSH from Windows)


