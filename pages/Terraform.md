- **Introduction to Terraform**
	- **Definition**:
		- Terraform is an open-source **Infrastructure as Code (IaC)** tool developed by HashiCorp.
		- It allows declarative provisioning and management of cloud infrastructure.
	- **Key Features**:
		- **Multi-Cloud Support**: Works with AWS, Azure, GCP, Kubernetes, and more.
		- **State Management**: Keeps track of infrastructure changes.
		- **Immutable Infrastructure**: Ensures consistency by rebuilding rather than modifying in-place.
		- **Modular & Scalable**: Uses modules for reusable infrastructure components.
	- **Comparison with Other IaC Tools**:
		- **Ansible** → Configuration management, mainly procedural.
		- **CloudFormation** → AWS-specific alternative to Terraform.
		- **Pulumi** → Uses programming languages instead of declarative syntax.
- **Installing Terraform**
	- **Linux Installation**:
	  ```bash
	  sudo apt update && sudo apt install -y unzip
	  curl -fsSL https://releases.hashicorp.com/terraform/latest/terraform_$(curl -s https://api.github.com/repos/hashicorp/terraform/releases/latest | grep -oP '"tag_name": "\K(.*)(?=")').linux_amd64.zip -o terraform.zip
	  unzip terraform.zip
	  sudo mv terraform /usr/local/bin/
	  terraform -version
	  ```
- **First Terraform Configuration**
	- **Initializing a Terraform Project**
	  ```bash
	  mkdir terraform-project && cd terraform-project
	  ```
	- **Create `main.tf`**:
	  ```hcl
	  terraform {
	    required_providers {
	      aws = {
	        source  = "hashicorp/aws"
	        version = "~> 4.0"
	      }
	    }
	  }
	  
	  provider "aws" {
	    region = "us-east-1"
	  }
	  ```
	- **Initialize Terraform**:
	  ```bash
	  terraform init
	  ```
	- **Check Configuration**:
	  ```bash
	  terraform validate
	  ```
- **Creating an AWS EC2 Instance**
	- **Define an EC2 Instance in `main.tf`**:
	  ```hcl
	  resource "aws_instance" "example" {
	    ami           = "ami-0c55b159cbfafe1f0"
	    instance_type = "t2.micro"
	  
	    tags = {
	      Name = "TerraformInstance"
	    }
	  }
	  ```
	- **Plan Infrastructure Changes**:
	  ```bash
	  terraform plan
	  ```
	- **Apply Changes (Create Instance)**:
	  ```bash
	  terraform apply -auto-approve
	  ```
	- **View Created Resources**:
	  ```bash
	  terraform show
	  ```
	- **Check AWS Console**: Navigate to EC2 and verify the instance.
- **Modifying an EC2 Instance**
	- **Change Instance Type in `main.tf`**:
	  ```hcl
	  instance_type = "t3.micro"
	  ```
	- **Apply Changes**:
	  ```bash
	  terraform apply -auto-approve
	  ```
	- **Check Terraform State**:
	  ```bash
	  terraform state list
	  ```
	- **Manually Refresh the State**:
	  ```bash
	  terraform refresh
	  ```
- **Deleting Resources**
	- **Destroy Infrastructure**:
	  ```bash
	  terraform destroy -auto-approve
	  ```
	- **Remove Local State Files**:
	  ```bash
	  rm -rf .terraform terraform.tfstate*
	  ```
- **Terraform State and Locking**
	- **State File (`terraform.tfstate`)**:
		- Stores the current infrastructure state.
		- Should be stored securely (e.g., **S3 with DynamoDB locking**).
	- **Remote State Example (S3 Backend)**:
	  ```hcl
	  terraform {
	    backend "s3" {
	      bucket         = "my-terraform-state"
	      key            = "terraform.tfstate"
	      region         = "us-east-1"
	      dynamodb_table = "terraform-lock"
	    }
	  }
	  ```
	- **Initialize Remote State**:
	  ```bash
	  terraform init
	  ```
- **Best Practices**
	- Always use **`terraform plan`** before applying changes.
	- Store Terraform state remotely for team collaboration.
	- Use **variables** (`terraform.tfvars`) instead of hardcoded values.
	- Implement **IAM roles and security groups** for controlled access.
	- Use **Terraform modules** for reusable configurations.
- **Related Topics**
	- [[AWS]] – Understanding instances, networking, and security groups.
	- [[Kubernetes]] – Using Terraform to provision Kubernetes clusters.
	- [[Helm Charts]] – Managing Kubernetes applications with Helm and Terraform.