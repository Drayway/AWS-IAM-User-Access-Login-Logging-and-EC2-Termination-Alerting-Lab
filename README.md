Build a security monitoring workflow that detects EC2 termination attempts (even failed ones) and sends an email alert.

This lab uses:

IAM (role-based access)

EC2 (test resource)

CloudTrail (audit logs)

CloudWatch Logs + Metric Filters (detection)

CloudWatch Alarm (alert trigger)

SNS Email Notifications (alert delivery)

✅ Step 1 — Create IAM Users (CloudAdmin + Helpdesk_User)

Go to IAM → Users

Confirm you have:

CloudAdmin (admin privileges)

Helpdesk_User (limited privileges)

📸 Screenshot:
/screenshots/01-iam-users.png

✅ Step 2 — Sign in as Helpdesk_User

Log in using the IAM user sign-in page

Verify top-right shows Helpdesk_User

📸 Screenshot:
/screenshots/02-helpdesk-login.png

✅ Step 3 — Launch an EC2 instance (Test Resource)

Go to EC2 → Instances → Launch instance

Name example: IAM-Deny-Test

Select instance type: t3.micro

Keep default VPC/subnet

Launch the instance

📸 Screenshot:
/screenshots/03-ec2-running.png

✅ Step 4 — Attempt to terminate instance (Expected: Denied)

While logged in as Helpdesk_User

Select the EC2 instance

Click Instance state → Terminate

Confirm termination

You should receive an Access Denied style error indicating the user is blocked.

📸 Screenshot:
/screenshots/04-terminate-denied.png

✅ Step 5 — Confirm explicit deny policy (CloudAdmin)

Switch to CloudAdmin

Go to IAM → Policies

Find the policy attached to Helpdesk_User (example: EC2_ReadOnly_NoTerminate)

Confirm the JSON includes:

Allow: ec2:Describe*

Deny: ec2:TerminateInstances

📸 Screenshot:
/screenshots/05-policy-json.png

✅ Step 6 — Enable CloudTrail Trail Logging

Go to CloudTrail → Trails

Confirm trail is created (example: Security-Trail)

Confirm status shows Logging

Confirm an S3 bucket is assigned for logs

📸 Screenshot:
/screenshots/06-cloudtrail-trail.png

✅ Step 7 — Verify TerminateInstances event appears in Event History

Go to CloudTrail → Event history

Filter/search for TerminateInstances

Confirm the event shows:

Event name: TerminateInstances

Username: Helpdesk_User

Event source: ec2.amazonaws.com

📸 Screenshot:
/screenshots/07-cloudtrail-event.png

✅ Step 8 — Send CloudTrail logs to CloudWatch Logs

Open CloudTrail trail → Edit

Enable CloudWatch Logs

Select / create:

Log group: aws-cloudtrail-logs-<account-id>

IAM Role: CloudTrail_CloudWatchLogs_Role

📸 Screenshot:
/screenshots/08-cloudwatch-log-group.png

✅ Step 9 — Create a Metric Filter for EC2 Termination Attempts

Go to CloudWatch → Log groups

Open the CloudTrail log group

Create a Metric filter

Use filter pattern:

{ ($.eventName = "TerminateInstances") }


Metric settings example:

Filter name: EC2-Terminate-Attempt

Namespace: Security

Metric name: TerminateInstances

Value: 1

📸 Screenshot:
/screenshots/09-metric-filter.png

✅ Step 10 — Create CloudWatch Alarm (Trigger Email Alert)

Go to CloudWatch → Metrics

Find namespace: Security

Select metric: TerminateInstances

Click Create alarm

Recommended settings:

Threshold: > 0

Period: 5 minutes

Statistic: Sum (preferred) or Average

📸 Screenshot:
/screenshots/10-alarm-created.png

✅ Step 11 — Create SNS Topic + Email Notification

During alarm creation (Actions step)

Choose Create new topic

Add your email address

Confirm SNS subscription from your inbox

📸 Screenshot:
/screenshots/11-sns-email.png

✅ Step 12 — Test Alert Trigger (Proof)

Attempt termination again as Helpdesk_User OR use the already logged TerminateInstances event

Confirm the alarm changes to ALARM

Confirm email notification arrives

📸 Screenshot:
/screenshots/12-alarm-in-alarm.png

✅ Result

This lab successfully:

Prevented unauthorized termination via IAM explicit deny

Logged the attempt via CloudTrail

Streamed logs into CloudWatch Logs

Detected termination attempts via metric filter

Triggered a CloudWatch alarm

Sent email alerts via SNS

🧠 What I learned

IAM explicit deny overrides allow permissions

CloudTrail records both successful and failed API actions

CloudWatch metric filters enable detection engineering using event patterns

Alarms + SNS make real-time security alerting possible
