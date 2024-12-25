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