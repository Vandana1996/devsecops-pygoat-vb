**NOTE: this lesson will create AWS resources in your AWS account. Check current AWS Free Tier guidelines to determine if this will incur costs for you.

The "Clean Up AWS Resources" lab will help you shut down those resources when not needed.

The "Recreating AWS Lab Resources" lab will help you re-create the necessary resources when you need them.**

In this lesson, we will deploy the lab Pygoat application to our AWS free tier environment. By the end of this lesson you will understand how to:

-Create your EC2 keypair
-Create your lab AWS resources from Cloudformation stack
-Create IAM user and configure AWS access
- Configure GitHub secrets

# Lab Instructions

  # Create EC2 Key Pair
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

  # Create Cloudformation Stack
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

  # Create IAM user
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

  # Configure GitHub Secrets
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


  # Lab Instructions to Test with ZAP
Download deploy Action: https://github.com/chad-butler-git/devsecops-with-github-actions/blob/main/git/workflows/deploy.yaml 

Add the file to your repo/git/workflows/ directory

Edit the file

Find the following and add your AWS region (e.g. us-west-2, us-east-1, etc.) echo "AWS_DEFAULT_REGION=<YOUR REGION HERE>" >> "$GITHUB_ENV"

Find the following and add your repository url: echo "REPO_LOCATION=<YOUR REPO HERE>" >> "$GITHUB_ENV"

Save the workflow

Run the workflow

Visit the Pygoat URL to ensure it is working

Explore Pygoat



  # Troubleshooting Tips:
Ensure your AWS security group allows inbound TCP/80 from your IP address 

If ZAP is timing out, try increasing the timeout (Settings -> Network -> Connection -> Timeout (in seconds): set to 45 seconds and retry

SSH into your AWS EC2 Instance and restart and check service states. 

sudo systemctl restart gunicorn.service ; sudo systemctl status gunicorn.service

sudo systemctl restart nginx ; sudo systemctl status nginx

Check GitHub Action logs and output for errors

                              

# Further Reading

The following resources explain the process of moving from IAM secret access keys to IAM roles to connect your GitHub Actions to your AWS account resources:

https://aws.amazon.com/blogs/security/use-iam-roles-to-connect-github-actions-to-actions-in-aws/

https://github.com/aws-actions/configure-aws-credentials

  # AWS Lab Resources Clean Up
Log in to your AWS Console

Click CloudFormation

Select your DevSecOps lab stack

Click Delete

If the delete fails the first time, this is likely due to the fact that the IAM policy has a user attached to it.

Click the Retry delete button

Carefully confirm that you are deleting the correct stack and select “Force delete this entire stack”


# Recreating AWS lba Resources

This is only needed if you followed the steps to delete your AWS Cloud Formation stack in the Clean Up AWS Lab Resources lesson

# Create Cloud Formation Stack
Log in to your AWS account

Click CloudFormation

Click Create stack

Select "With new resources (standard)

Select "Upload a template file"

Download the template file from: https://github.com/chad-butler-git/devsecops-with-github-actions/blob/main/aws/cf-ec2.yaml

Click Choose file

Select the cf-ec2.yaml file you downloaded

Click next

Provide a stack name (e.g. devsecops)

Select the keypair you created in lesson 3.1.5

Enter your Source IP address

Browse to https://ifconfig.me/

Copy and paste the IP address from ifconfig.me and append a /32

Click Next

Click the checkbox to confirm "I acknowledge that AWS CloudFormation might create IAM resources."

Click Next

Click Submit

Wait for stack creation process to complete.

If the Creation fails, check the events log. If the error is related to the PyGoatSecurityGroup, do the following

Click EC2 console

Under Network & Security, Click Security Groups

Select the PyGoatSG security group

Click Actions and Delete security groups

Return to the CloudFormation console

Delete the devsecops stack and start over from Step 3

# Re-Link IAM User
Click the IAM console

Click Users

Select the user you created in lesson 3.1.5

Click Add permission > Add permission

Select "Attach policies directly"

Search for Pygoat

Select the policy named devsecops-PyGoatIAMPolicy-<stackID>

Click Next

Review details and Click Add permission

# Update EC2 Hostname
Click the EC2 console

Click Instances

Select your running instance created by CloudFormation

Copy the Public IPv4 DNS address

Login to GitHub

Select your Pygoat forked repository

Click Settings

Under Security, click Secrets and variables

Click Actions

Find the repository secret named HOST and click the edit button

Paste the Public IPv4 DNS address you copied into the Value box.

Click Update secret

# Re-deploy
From GitHub.com, click the Actions tab

Find and click the Pygoat Deploy CD action (we create this in lab )

Click Run workflow, select Run workflos

Confirm that Pygoat deployed successfully by browsing to the Public IPv4 DNS address of your EC2 instance.


# ZAP Automation iwth GitHub Workflows

By the end of this lesson you will understand:

ZAP automation options and their strengths and weaknesses

ZAP Docker packaged scans and GitHub Action

How to implement and configure automated scans through GitHub Actions

# Lab Instructions
Download DAST Action: https://github.com/chad-butler-git/devsecops-with-github-actions/blob/main/git/workflows/DAST.yaml 

Add the file to your repo/git/workflows/ directory

Edit the file

Find the target line and add your EC2 public DNS name.

Save the workflow

Run the workflow

Analyze workflow to ensure it ran successfully

Download zap report from Workflow summary page -OR- check the Issues tab for the ZAP report issue.



# Troubleshooting Tips
Confirm that your EC2 public DNS name in the action file includes http://

SSH into your AWS EC2 Instance and restart and check service states. 

sudo systemctl restart gunicorn.service ; sudo systemctl status gunicorn.service

sudo systemctl restart nginx ; sudo systemctl status nginx

Check GitHub Action logs and output for errors

If you get an error regarding the rules.tsv file (“Error when reading the rules file”) confirm that the rules.tsv file is using tabs instead of spaces

Post questions in the community

# ZAP Authentication
In this lesson we will explore ZAP authentication contexts and use them in a lab to add authentication to our automated baseline scans. By the end of this lesson you will understand:

ZAP authentication contexts and verification strategies

How to enable authentication in GitHub Action scan workflows

How to determine if authentication is successful

How to test authentication

# Lab Instructions
Open ZAP

Start a manual explore of Pygoat

Click a link you don’t have access to (before authenticating)

Login with your test user account

Click the same link 

Run Authentication Tester

Provide your test user password

Click test and observe

Wait until authentication tester finishes and close

Open the Authentication Context 

Explore the settings

Export Context

Right-click the context and select Export Context

Save it to a location on your system

Upload to the .zap directory in your Pygoat repository

Modify DAST.yaml workflow

Find the cmd_options: section and add -n .zap/<your-context-file.context>

Re-run the DAST scan

Check Action log output to determine if it ran successfully.

# Troubleshooting Tips
Confirm that your EC2 public DNS name in the action file includes http://

SSH into your AWS EC2 Instance and restart and check service states. 

sudo systemctl restart gunicorn.service ; sudo systemctl status gunicorn.service

sudo systemctl restart nginx ; sudo systemctl status nginx

Check GitHub Action logs and output for errors

If you get an error regarding the rules.tsv file (“Error when reading the rules file”) confirm that the rules.tsv file is using tabs instead of spaces

Post questions in the community
