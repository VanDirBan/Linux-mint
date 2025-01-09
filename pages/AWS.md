- #AWS
	- **Creating an AWS Account**
		- **Steps**:
		  1. Go to the [AWS Registration Page](https://aws.amazon.com/).
		  2. Enter your email address, choose a root account password, and create the account.
		  3. Provide billing information (credit/debit card required).
		  4. Choose a support plan (Free Tier is sufficient for learning).
		  5. Verify your identity via SMS or call.
		  6. Log in to the AWS Management Console using the root account.
		- **Best Practices**:
			- Use the root account only for billing and account management.
			- Enable Multi-Factor Authentication (MFA) for the root account.
	- **Creating Users and Groups in AWS IAM**
		- **Definition**:
			- IAM (Identity and Access Management) manages access to AWS services and resources securely.
		- **Steps**:
		  1. Go to the AWS Management Console > **IAM**.
		  2. **Create a Group**:
			- Name the group (e.g., `Admins`, `Developers`).
			- Attach permissions (e.g., `AdministratorAccess` for full control).
			  3. **Create Users**:
			- Add a username (e.g., `developer1`).
			- Select the **Access Type**:
				- **Programmatic Access**: Generates an Access Key ID and Secret Access Key for CLI/API use.
				- **Console Access**: Allows login to the AWS Management Console.
			- Assign the user to the previously created group.
			  4. Download the **credentials.csv** file for secure storage.
		- **Best Practices**:
			- Create separate users for each individual or application.
			- Use groups to assign permissions instead of assigning them directly to users.
			- Enable MFA for critical users.
	- **Setting Up AWS CLI on Linux**
		- **Install AWS CLI**:
			- Use the package manager to install AWS CLI:
			  ```bash
			  sudo apt update
			  sudo apt install awscli -y
			  ```
			- Verify the installation:
			  ```bash
			  aws --version
			  ```
		- **Configure AWS CLI**:
			- Use the `aws configure` command:
			  ```bash
			  aws configure
			  ```
				- Enter the following details:
					- Access Key ID: (from IAM user)
					- Secret Access Key: (from IAM user)
					- Default region: (e.g., `us-east-1`)
					- Default output format: (e.g., `json`, `table`, or `text`)
		- **Environment Variables for AWS CLI**:
			- Set the environment variables to avoid re-entering credentials:
			  ```bash
			  export AWS_ACCESS_KEY_ID=your-access-key-id
			  export AWS_SECRET_ACCESS_KEY=your-secret-access-key
			  export AWS_DEFAULT_REGION=us-east-1
			  ```
			- Add these variables to the `.bashrc` or `.zshrc` file for persistence:
			  ```bash
			  echo "export AWS_ACCESS_KEY_ID=your-access-key-id" >> ~/.bashrc
			  echo "export AWS_SECRET_ACCESS_KEY=your-secret-access-key" >> ~/.bashrc
			  echo "export AWS_DEFAULT_REGION=us-east-1" >> ~/.bashrc
			  source ~/.bashrc
			  ```
		- **Testing the Configuration**:
			- Verify the configuration using:
			  ```bash
			  aws sts get-caller-identity
			  ```
				- This command returns the ARN of the user whose credentials are being used.
		- **Best Practices for AWS CLI**:
			- Use IAM roles and temporary credentials whenever possible instead of storing access keys locally.
			- Limit permissions for IAM users to the minimum required.
			- Regularly rotate Access Key IDs and Secret Access Keys.
	- **IAM Identity Center**
		- **Definition**:
			- AWS IAM Identity Center (formerly AWS SSO) provides centralized access management to AWS accounts, cloud applications, and custom apps.
		- **Key Features**:
			- Single Sign-On (SSO): Access multiple AWS accounts and applications with one login.
			- Centralized user management through AWS Directory Service or external identity providers (e.g., Microsoft AD, Google Workspace).
			- Supports integration with IAM roles for managing permissions.
		- **Setup Steps**:
		  1. Go to **IAM Identity Center** in the AWS Management Console.
		  2. Configure an identity source:
			- AWS Managed Directory.
			- External identity provider (via SAML).
			  3. Assign users/groups to AWS accounts and permissions sets.
		- **Usage**:
			- Assign permissions sets to users/groups for access control.
			- Users can log in via the AWS SSO portal.
		- **Best Practices**:
			- Use centralized identity management for teams with multiple AWS accounts.
			- Leverage MFA for additional security.
	- **IAM Policies**
		- **Definition**:
			- IAM policies are JSON documents that define permissions for AWS resources.
		- **Policy Structure**:
			- **Version**: Specifies the policy language version (e.g., `"2012-10-17"`).
			- **Statement**: Contains the permission rules.
				- **Effect**: `Allow` or `Deny`.
				- **Action**: AWS service actions (e.g., `s3:ListBucket`).
				- **Resource**: ARN of the resource (e.g., `arn:aws:s3:::my-bucket`).
				- **Condition**: Optional, adds conditions for the policy.
		- **Example Policy**:
		  ```json
		  {
		    "Version": "2012-10-17",
		    "Statement": [
		      {
		        "Effect": "Allow",
		        "Action": "s3:ListBucket",
		        "Resource": "arn:aws:s3:::my-bucket"
		      },
		      {
		        "Effect": "Deny",
		        "Action": "s3:DeleteObject",
		        "Resource": "arn:aws:s3:::my-bucket/*"
		      }
		    ]
		  }
		  ```
		- **Best Practices**:
			- Follow the principle of least privilege.
			- Test policies using the Policy Simulator.
	- **Switching IAM Roles**
		- **Definition**:
			- Switching roles allows users or applications to assume different permissions temporarily.
		- **Setup Steps**:
		  1. Create a role with permissions to access specific resources.
		  2. Add trusted entities that can assume the role (e.g., other AWS accounts or services).
		  3. Use the AWS Management Console, CLI, or SDK to switch roles.
		- **Example**:
			- Assume a role using the CLI:
			  ```bash
			  aws sts assume-role --role-arn arn:aws:iam::123456789012:role/MyRole --role-session-name MySession
			  ```
		- **Best Practices**:
			- Use IAM roles for cross-account access.
			- Enable role-based access for temporary tasks.
	- **Permissions Boundary**
		- **Definition**:
			- A permissions boundary is an advanced IAM feature that restricts the maximum permissions a user or role can have.
		- **Usage**:
			- Define a policy as the permissions boundary for IAM entities.
		- **Example**:
			- Policy to allow only read access:
			  ```json
			  {
			    "Version": "2012-10-17",
			    "Statement": [
			      {
			        "Effect": "Allow",
			        "Action": "s3:GetObject",
			        "Resource": "arn:aws:s3:::my-bucket/*"
			      }
			    ]
			  }
			  ```
			- Attach this policy as a permissions boundary to a user or role.
		- **Best Practices**:
			- Use permissions boundaries to enforce organizational security policies.
			- Combine with other IAM policies for granular control.
	- **CloudTrail**
		- **Definition**:
			- AWS CloudTrail records all API calls and user activity in your AWS account.
		- **Features**:
			- Tracks actions taken by users, roles, and services.
			- Stores logs in an S3 bucket for auditing and troubleshooting.
		- **Setup Steps**:
		  1. Enable CloudTrail in the AWS Management Console.
		  2. Choose whether to create a new trail or use the default trail.
		  3. Configure an S3 bucket to store logs.
		  4. Enable encryption and log file validation for security.
		- **Best Practices**:
			- Enable CloudTrail across all AWS regions.
			- Integrate with CloudWatch for real-time monitoring.
			- Use CloudTrail Insights to detect unusual activity.
	- **Policy Simulator**
		- **Definition**:
			- The AWS IAM Policy Simulator tests and validates IAM policies before applying them.
		- **Usage**:
		  1. Go to the [Policy Simulator Console](https://policysim.aws.amazon.com/).
		  2. Load the policy to test.
		  3. Select the actions, resources, and conditions.
		  4. Review the results to ensure the policy works as intended.
		- **Best Practices**:
			- Test new policies before applying them to users or roles.
			- Use the simulator to debug permission issues.