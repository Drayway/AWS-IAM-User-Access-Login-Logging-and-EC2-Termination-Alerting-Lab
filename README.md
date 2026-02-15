# 🛡️ AWS IAM Login Monitoring & EC2 Termination Detection Lab


## Overview
This lab demonstrates how to implement AWS identity monitoring and infrastructure incident detection by logging login activity and detecting EC2 termination attempts using native AWS security services.

---

## Objectives

- Create IAM users with role-based permissions
- Monitor AWS console login activity
- Enable CloudTrail logging
- Stream logs into CloudWatch
- Create metric filters for termination attempts
- Trigger real-time alerts using SNS

---

## Step 1 — Create IAM Users

1. Go to **IAM → Users**
2. Create or confirm the following users exist:
   - CloudAdmin (Administrator access)
   - Helpdesk_User (Limited access)

<img width="1890" height="797" alt="Screenshot 2026-02-15 110244" src="https://github.com/user-attachments/assets/fafe7425-be0c-46cb-80d1-756db1c32a3c" />


---

## Step 2 — Log in as Helpdesk_User

1. Use the IAM login URL
2. Sign in as **Helpdesk_User**
3. Confirm login in top-right console corner

Screenshot:  
<img width="1897" height="871" alt="Screenshot 2026-02-15 161437" src="https://github.com/user-attachments/assets/cad6dfb6-9079-46fc-a870-0351026ba723" />



---

## Step 3 — Launch EC2 Instance

1. Go to **EC2 → Instances → Launch instance**
2. Name: IAM-Deny-Test
3. Instance type: t3.micro
4. Use default network settings
5. Launch instance

Screenshot: 
<img width="1896" height="778" alt="Screenshot 2026-02-15 122434" src="https://github.com/user-attachments/assets/94102204-0fb5-45f9-80ed-5566c186fc4b" />


---

## Step 4 — Attempt Termination (Denied)

1. While logged in as Helpdesk_User
2. Select the instance
3. Click **Instance state → Terminate**
4. Confirm action

Expected Result: Access Denied error

Screenshot:  
<img width="1890" height="706" alt="Screenshot 2026-02-15 162052" src="https://github.com/user-attachments/assets/b74ec991-dcc7-4dfc-b615-400ed08ab894" />


---

## Step 5 — Verify IAM Deny Policy

1. Switch to CloudAdmin
2. Go to **IAM → Policies**
3. Open policy attached to Helpdesk_User

Policy JSON includes:

- Allow: ec2:Describe*
- Deny: ec2:TerminateInstances

Screenshot:  
<img width="1878" height="707" alt="Screenshot 2026-02-15 124815" src="https://github.com/user-attachments/assets/98284013-8b58-45a2-9c35-fb840253b644" />


---

## Step 6 — Enable CloudTrail Logging

1. Go to **CloudTrail → Trails**
2. Create trail named: Security-Trail
3. Enable multi-region logging
4. Confirm logging is active

Screenshot:  
<img width="1903" height="840" alt="Screenshot 2026-02-15 162942" src="https://github.com/user-attachments/assets/0555dbc7-2ab2-4201-be4a-94c36237e6fc" />


---

## Step 7 — Verify Event Logging

1. Go to **CloudTrail → Event History**
2. Search: TerminateInstances
3. Confirm event logged for Helpdesk_User

Screenshot:  
<img width="1880" height="785" alt="Screenshot 2026-02-15 163259" src="https://github.com/user-attachments/assets/39c1189b-70b8-4eb8-92e3-9dc9d46881e1" />


---

## Step 8 — Send Logs to CloudWatch

1. Open trail → Edit
2. Enable CloudWatch Logs
3. Create/select log group
4. Assign IAM role

Screenshot:  
<img width="1907" height="811" alt="Screenshot 2026-02-15 163924" src="https://github.com/user-attachments/assets/4e1bac30-fa65-42a1-ad3b-eaf282de21c8" />


---

## Step 9 — Create Metric Filter

Filter pattern:


Metric settings:

- Namespace: Security
- Metric name: TerminateInstances
- Value: 1

Screenshot:  
<img width="1888" height="827" alt="Screenshot 2026-02-15 164105" src="https://github.com/user-attachments/assets/bb7be8ff-256c-48c7-b55b-d5f6d899a0d2" />


---

## Step 10 — Create Alarm

1. Go to **CloudWatch → Metrics**
2. Select Security → TerminateInstances
3. Create alarm

Settings:

- Threshold: > 0
- Period: 5 minutes
- Statistic: Sum or Average

Screenshot:  
<img width="1855" height="716" alt="Screenshot 2026-02-15 143133" src="https://github.com/user-attachments/assets/1f0f03e6-cc14-451a-aceb-7e6f71bef785" />


---

## Step 11 — Configure SNS Alerts

1. Create SNS topic
2. Add email subscription
3. Confirm email

Screenshot:  
<img width="1882" height="823" alt="Screenshot 2026-02-15 165109" src="https://github.com/user-attachments/assets/bfc03bde-e6d8-463e-b1cc-e0808884ce9a" />


---

## Step 12 — Test Detection

1. Attempt termination again
2. Alarm triggers
3. SNS email received

Screenshot:  
<img width="1107" height="620" alt="Screenshot 2026-02-15 152331" src="https://github.com/user-attachments/assets/8f286e79-8f0d-4852-a912-c65246a62f5a" />


---

## Outcome

This lab successfully implemented:

- IAM explicit deny controls
- CloudTrail audit logging
- CloudWatch log monitoring
- Metric-based detection
- Alarm alerting
- SNS email notifications

---

## Skills Demonstrated

- AWS IAM
- CloudTrail
- CloudWatch Logs
- Detection Engineering
- Security Monitoring
- Incident Alerting
