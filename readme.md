# Django Application DevOps Solution

This repository contains the complete infrastructure and deployment solution for a Django web application with PostgreSQL database and React frontend, deployed on AWS using Infrastructure as Code (IaC) principles.

## Architecture Overview

The solution implements a highly available architecture with the following components:

- VPC with public and private subnets across two availability zones
- Application Load Balancer (ALB) in public subnets
- EC2 instances in private subnets running the Django application
- RDS PostgreSQL instance in private subnets with Multi-AZ deployment
- NAT Gateway for outbound internet access from private subnets
- S3 bucket for application logs
- CloudWatch for monitoring and alerting

### Security Features

- All resources are deployed in a custom VPC
- Private subnets for application and database servers
- Security groups with minimal required access
- IAM roles following least privilege principle
- TLS encryption for all data in transit
- Automated database backups
- Secrets management using AWS Secrets Manager

## Prerequisites

- AWS Account with appropriate permissions
- Terraform >= 1.0.0
- Ansible >= 2.9
- AWS CLI configured with appropriate credentials
- GitHub account for CI/CD pipeline

## Infrastructure Provisioning

1. Initialize Terraform:
```bash
terraform init
```

2. Review the infrastructure plan:
```bash
terraform plan
```

3. Apply the infrastructure:
```bash
terraform apply
```

## Application Deployment

1. Update the inventory file with your EC2 instance details:
```bash
echo "app_servers ansible_host=<EC2-IP> ansible_user=ubuntu" >> inventory
```

2. Run the Ansible playbook:
```bash
ansible-playbook -i inventory site.yml
```

## CI/CD Pipeline

The GitHub Actions workflow will:
1. Run tests on every push and pull request
2. Deploy to AWS on merges to main branch
3. Implement blue-green deployment strategy
4. Provide automatic rollback on deployment failures

### Setting up the Pipeline

1. Add the following secrets to your GitHub repository:
   - AWS_ACCESS_KEY_ID
   - AWS_SECRET_ACCESS_KEY
   - DATABASE_URL

2. Push code to trigger the pipeline:
```bash
git push origin main
```

## Monitoring and Alerting

CloudWatch alarms are configured for:
- CPU utilization > 80%
- Memory utilization > 80%
- Database connections > 80% of maximum
- 5xx errors > 1% of requests
- Response time > 2 seconds

## Disaster Recovery

### Backup Strategy

- Automated daily RDS snapshots
- S3 bucket versioning enabled
- Multi-AZ deployment for high availability

### Recovery Procedure

1. In case of failure, run the recovery script:
```bash
./scripts/disaster-recovery.sh
```