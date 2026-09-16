# Highly Available, Multi-Tier Cloud-Native Application on AWS
### (Design & Deployment using the AWS Well-Architected Framework)

---

## 1. Project Overview

A 3-tier web application deployed on AWS for high availability across two
Availability Zones:

- **Web Tier:** Application Load Balancer (public subnets, 2 AZs)
- **Application Tier:** EC2 instances in an Auto Scaling Group (private subnets, 2 AZs)
- **Database Tier:** Amazon RDS (MySQL/PostgreSQL) with Multi-AZ failover (private subnets)

See `AWS_Architecture_Diagram/` for the full architecture diagram.

---

## 2. Architecture Summary

```
Internet -> Application Load Balancer (public subnets, 2 AZs)
         -> Auto Scaling Group of EC2 app servers (private subnets, 2 AZs)
         -> RDS Multi-AZ database (private subnets)
```

Full design rationale and mapping to the 6 Well-Architected pillars
(Operational Excellence, Security, Reliability, Performance Efficiency,
Cost Optimization, Sustainability) is documented in `Case_Study_Report/`.

---

## 3. Prerequisites

- An AWS account (Free Tier / trial credit is sufficient)
- AWS CLI installed and configured (`aws configure`) 
- Git

---

## 4. Setup & Deployment Instructions


1. Clone this repository:
   ```
   git clone [PASTE YOUR PUBLIC GITHUB LINK HERE]
   cd [repo-folder-name]
   ```
2. **Network setup:** Create the VPC, public/private subnets (2 AZs), Internet
   Gateway, and NAT Gateway (see `Deployment_Files/` for exact CIDR blocks
   and settings used, or the console steps if built manually).
3. **Security groups:** Create `alb-sg`, `app-sg`, `db-sg` as described in
   `Deployment_Files/deployment-notes.md`.
4. **Database tier:** Launch the RDS instance with Multi-AZ enabled in the
   private subnets. Note the endpoint.
5. **Application tier:** Deploy the code in `Source_Code/` onto an EC2
   instance (or bake it into the Launch Template's user-data script — see
   `Deployment_Files/`). Create the Auto Scaling Group across both private
   subnets.
6. **Load balancer:** Create the ALB in the public subnets, pointing its
   target group at the Auto Scaling Group.
7. Update the application's database connection settings to point at the
   RDS endpoint from step 4.

---

## 5. Execution / How to Access the Application

1. Once deployed, open the ALB's DNS name in a browser:
   `http://<your-alb-dns-name>.elb.amazonaws.com`


---

## 6. Testing High Availability

Steps performed to verify HA (screenshots in `Deployment_Screenshots/`):

1. Confirmed the app responded correctly via the ALB with both AZs healthy.
2. Terminated one EC2 instance manually and observed:
   - The Auto Scaling Group launched a replacement automatically.
   - The application remained accessible throughout (served by the
     surviving instance in the other AZ).
3. Triggered an RDS "Reboot with failover" and confirmed the database
   reconnected via its endpoint without manual intervention.
4. Verified CloudWatch alarms and dashboards captured the events.

---

## 7. Cost Management

- Free-tier eligible instance sizes used throughout (`t3.micro` / `db.t3.micro`)
- AWS Budgets configured with alerts at $5 and $20
- Resources scaled to zero / stopped between work sessions to conserve credit
- Final estimated project cost: approximately $[X] of the $100 trial credit

---

## 8. Repository Structure

```
RollNumber_YourName/
├── Case_Study_Report/          - Full written case study (PDF)
├── Source_Code/                - Application source code
├── AWS_Architecture_Diagram/   - Architecture diagram
├── Deployment_Files/           - IaC / deployment configs & notes
├── Deployment_Screenshots/     - Evidence of working deployment & HA testing
└── README.md                   - This file
```

---

## 9. Author

[Harshit Gupta] — [2400290120111] — [KIET Group of Institutions]
