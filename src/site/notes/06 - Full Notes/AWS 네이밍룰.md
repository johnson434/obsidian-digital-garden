---
{"dg-publish":true,"permalink":"/06-full-notes/aws/","noteIcon":""}
---


## EC2 Instances:

- **Format:** `[project]-[env]-[role]-[identifier]`
- **Example:** `myapp-prod-web-01`
- **Explanation:** Clearly indicates this is the first web server instance for the production environment of the ‘myapp’ project.

## S3 Buckets:

- **Format:** `[project]-[env]-[data-type]-[identifier]`
- **Example:** `myapp-prod-logs-backup`
- **Explanation:** Shows this bucket is for backup logs in the production environment for ‘myapp’.

## IAM Roles:

- **Format:** `[role-type]-[project]-[env]`
- **Example:** `admin-myapp-prod`
- **Explanation:** Denotes an admin role for the ‘myapp’ project in production.

## RDS Instances:

- **Format:** `[project]-[env]-[database-type]-[identifier]`
- **Example:** `myapp-prod-mysql-db01`
- **Explanation:** Identifies this as the first MySQL database for ‘myapp’ in production.

## Lambda Functions:

- **Format:** `[project]-[env]-[function-purpose]`
- **Example:** `myapp-prod-process-upload`
- **Explanation:** Indicates a Lambda function in production for processing uploads in ‘myapp’.

## CloudFormation Stacks:

- **Format:** `[project]-[env]-[stack-purpose]`
- **Example:** `myapp-prod-network-stack`
- **Explanation:** Designates a CloudFormation stack for the network setup in the production environment of ‘myapp’.

## VPC:

- **Format:** `[project]-[env]-vpc`
- **Example:** `myapp-prod-vpc`
- **Explanation:** Clearly indicates this VPC belongs to the production environment of ‘myapp’.
## Route Table:
- **Format:** `[project]-[env]-rtb-[purpose]`
- **Example:** `myapp-prod-rtb-nat`
## Subnets:
- **Format:** `[project]-[env]-subnet-[purpose]-[zone]`
- **Example:** `myapp-prod-subnet-web-a`
- **Explanation:** Identifies this as a subnet in availability zone ‘a’ for the production environment of ‘myapp’.
## IGW:
- Format: [project]-[env]-igw

## NACL:
- **형식:** [projcet]-[env]-nacl-[목적]
- **예시:** myapp-dev-nacl-web
## Security Groups:

- **Format:** `[project]-[env]-sg-[purpose]`
- **Example:** `myapp-prod-sg-web`
- **Explanation:** Shows this security group is for web servers in the production environment of ‘myapp’.

## Elastic Load Balancers:

- **Format:** `[project]-[env]-elb-[purpose]`
- **Example:** `myapp-prod-elb-web`
- **Explanation:** Denotes this ELB is for web traffic in the production environment of ‘myapp’.

# Additional Tips:

- **Consistency is Key:** Apply the naming conventions uniformly across all your AWS resources to avoid confusion.
- **Document Your Conventions:** Maintain a shared document outlining your naming conventions for team reference and onboarding new team members.
- **Automate Where Possible:** Use Infrastructure as Code (IaC) tools like AWS CloudFormation or Terraform to automate resource creation with standardized naming.
# 참고자료
https://vitaltech.medium.com/mastering-aws-cloud-best-practices-for-naming-conventions-8684d53e3d95