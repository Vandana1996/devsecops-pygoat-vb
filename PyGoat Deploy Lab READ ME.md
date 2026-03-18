**NOTE: this lesson will create AWS resources in your AWS account. Check current AWS Free Tier guidelines to determine if this will incur costs for you.

The "Clean Up AWS Resources" lab will help you shut down those resources when not needed.

The "Recreating AWS Lab Resources" lab will help you re-create the necessary resources when you need them.**

In this lesson, we will deploy the lab Pygoat application to our AWS free tier environment. By the end of this lesson you will understand how to:

-Create your EC2 keypair
-Create your lab AWS resources from Cloudformation stack
-Create IAM user and configure AWS access
- Configure GitHub secrets

#Lab Instructions

#Create EC2 Key Pair
Sign in to your AWS console

Navigate to the EC2 console (https://console.aws.amazon.com/ec2/)

In the navigation pane, under Network & Security, click Key Pairs

Click Create key pair.

For Name, enter something that you will remember

Select ED25519

Select .pem format

Click Create key pair

Save the private key file in a safe location on your system.

If you plan to use this key on a Mac or Linux system, find the .pem file you just created and run chmod 400 your-key-pair-name.pem

Create Cloudformation Stack
Sign in to your AWS console.

Navigate to the Cloudformation console. 

Click create stack.

Select Upload a template file. 

Download file from https://github.com/chad-butler-git/devsecops-with-github-actions/blob/main/aws/cf-ec2.yaml

Choose file and click Next.

Give the stack a descriptive name that you will recognize.

Select the Key Pair you created previously.

Enter the source IP address or network that you want to allow access from. If it is a single source IP address, make sure you add the /32 at the end (e.g. 12.34.56.78/32)

Find your Source IP address

Browser: https://ifconfig.me/

Comand Line: curl -4 ifconfig.me; echo

Click Next.

Confirm that the rollback options are acceptable.

Click Next.

Review creation options for accuracy.

Scroll to the bottom and acknowledge the IAM policy creation warning. 

Click Submit

Create IAM user
Sign in to AWS account.

Navigate to the IAM console.

In the Navigation tab, under Access management, click Users.

Click Create user.

Give your User a descriptive name.

Click Next.

Click Attach policies directly.

Click the Filter by Type drop-down and select Customer managed.

Find and select your managed policy. The name format is <Cloudformation_stack_name-PyGoatIAMPolicy-<unique_identifier>

Click Next.

Click Create user.

Click the name of the User you just created.

Click the Security credentials tab.

Find the Access keys section.

Click Create access key.

If you are asked for a Use case, select Command Line Interface (CLI).

Click Next.

Click Create access key.

Copy your Access key and Secret access key to a safe location.

Click Done.

Configure GitHub Secrets
Sign in to your GitHub account

Navigate to your forked Pygoat repository

Click Settings

In the Navigation pane, under Security, click Secrets and variables

Then click Actions

Click New repository secret

For Name, type AWS_ACCESS_KEY_ID

For Secret, paste the access key you created in the last step

Click Add secret

For each of the following secrets you will click Add secret and paste in the Name indicated below and the appropriate data in the Secret field.

Name

Secret Value

AWS_SECRET_ACCESS_KEY-secret access key from last step

HOST-IP address of EC2 Instance

USERNAME-ec2-user

KEY-SSH Keypair (private key)

PORT-22

                              

# Further Reading

The following resources explain the process of moving from IAM secret access keys to IAM roles to connect your GitHub Actions to your AWS account resources:

https://aws.amazon.com/blogs/security/use-iam-roles-to-connect-github-actions-to-actions-in-aws/

https://github.com/aws-actions/configure-aws-credentials
