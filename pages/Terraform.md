- [[Terraform Basics]]
	- **Introduction to Terraform (v1.1+)**
		- **Definition**:
			- Terraform is an open-source Infrastructure as Code (IaC) tool by HashiCorp.
			- Allows declarative provisioning of cloud resources across multiple providers (AWS, Azure, GCP, etc.).
		- **Key Features**:
			- **Declarative Syntax**: Describes the desired state of infrastructure in `.tf` files.
			- **Provider-based Architecture**: Uses plugins (e.g., AWS provider) to manage resources.
			- **State Management**: Tracks resource changes in a state file for consistent updates.
			- **Modules**: Organizes and reuses configurations.
	- **Installing Terraform**
		- **Linux Installation (Example)**:
		  ```bash
		  sudo apt update && sudo apt install -y curl unzip
		  curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
		  sudo apt-add-repository "deb https://apt.releases.hashicorp.com $(lsb_release -cs) main"
		  sudo apt update && sudo apt install terraform
		  terraform -version
		  ```
		- **Verify Installation**:
		  ```bash
		  terraform version
		  ```
		- **Related**: See [[Ansible]] for configuration management vs IaC.
	- **Terraform Configuration**
		- **Basic `main.tf` Structure**:
		  ```hcl
		  terraform {
		    required_version = ">= 1.1.0"
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
		- **Initialize the Project**:
		  ```bash
		  terraform init
		  ```
		- **Validate Syntax**:
		  ```bash
		  terraform validate
		  ```
		- **Format Code** (optional but recommended):
		  ```bash
		  terraform fmt
		  ```
	- **Creating an AWS EC2 Instance**
		- **Resource Definition**:
		  ```hcl
		  resource "aws_instance" "example" {
		    ami           = "ami-0c55b159cbfafe1f0"
		    instance_type = "t2.micro"
		  
		    tags = {
		      Name = "TerraformInstance"
		    }
		  }
		  ```
		- **Plan the Infrastructure**:
		  ```bash
		  terraform plan
		  ```
		- **Apply (Create Resources)**:
		  ```bash
		  terraform apply -auto-approve
		  ```
		- **Check State**:
		  ```bash
		  terraform state list
		  ```
		- **View Resource Details**:
		  ```bash
		  terraform show
		  ```
	- **Modifying Resources**
		- **Example**: Changing the instance type
		  ```hcl
		  instance_type = "t3.micro"
		  ```
		- **Apply Changes**:
		  ```bash
		  terraform apply -auto-approve
		  ```
		- **Inspect Differences**:
		  ```bash
		  terraform plan
		  ```
	- **Deleting Resources**
		- **Destroy Infrastructure**:
		  ```bash
		  terraform destroy -auto-approve
		  ```
		- **Remove Local Files** (if needed):
		  ```bash
		  rm -rf .terraform/ terraform.tfstate*
		  ```
	- **Managing State**
		- **Local State File**:
			- Stored as `terraform.tfstate`.
			- Keep it secure and versioned if possible.
		- **Remote State (S3, etc.)**:
			- Example backend configuration:
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
		- **Initialize or Reconfigure**:
		  ```bash
		  terraform init -reconfigure
		  ```
	- **Modules**
		- **Purpose**:
			- Reusable configurations for complex infrastructures.
			- Example usage in `main.tf`:
			  ```hcl
			  module "vpc" {
			    source = "./modules/vpc"
			    cidr_block = "10.0.0.0/16"
			  }
			  ```
		- **Related**: See [[Terraform Modules]] for advanced usage.
	- **Best Practices**
		- Always run `terraform plan` before `terraform apply`.
		- Use **variables** and **tfvars** files for environment-specific data.
		- Store state remotely for collaboration (e.g., S3 with DynamoDB lock).
		- Implement version control (e.g., Git) for `.tf` files.
		- Use **terraform fmt** and **terraform validate** for consistent code style and syntax checks.
		- Tag resources for easier identification and cost tracking.
		- Combine with other IaC or config management tools if needed (e.g., [[Ansible]], [[Helm Charts]]).
- [[Web Server Deployment]]
	- **Deploying a Web Server with Terraform and NGINX (v1.1+)**
		- **Definition**:
			- Terraform can automate the deployment of a web server with NGINX on AWS.
			- Involves using **static external files**, **dynamic external files**, and **dynamic blocks**.
		- **Key Features**:
			- Infrastructure as Code (IaC) for consistent and repeatable deployments.
			- Dynamic file templates for flexible configuration.
			- Modular and scalable infrastructure with reusable code.
	- **Terraform Configuration for NGINX**
		- **Basic Infrastructure Setup (main.tf)**:
		  ```hcl
		  terraform {
		    required_version = ">= 1.1.0"
		    required_providers {
		      aws = {
		        source  = "hashicorp/aws"
		        version = "~> 5.0"
		      }
		    }
		  }
		  
		  provider "aws" {
		    region = "us-east-1"
		  }
		  ```
	- **Creating the AWS Resources**
		- **EC2 Instance for NGINX**:
		  ```hcl
		  resource "aws_instance" "nginx_server" {
		    ami           = "ami-0c55b159cbfafe1f0"
		    instance_type = "t2.micro"
		    key_name      = var.key_name
		  
		    security_groups = [aws_security_group.nginx_sg.name]
		  
		    # Using external file for user_data
		    user_data = file("${path.module}/nginx-setup.sh")
		  
		    tags = {
		      Name = "TerraformNGINXServer"
		    }
		  }
		  ```
		- **Security Group Configuration**:
		  ```hcl
		  resource "aws_security_group" "nginx_sg" {
		    name        = "nginx_sg"
		    description = "Allow HTTP and SSH access"
		  
		    # Dynamic block to define ingress rules for ports 80 and 22
		    dynamic "ingress" {
		      for_each = toset([80, 22])
		      content {
		        from_port   = ingress.value
		        to_port     = ingress.value
		        protocol    = "tcp"
		        cidr_blocks = ["0.0.0.0/0"]
		      }
		    }
		  
		    # Egress rule for all outbound traffic
		    egress {
		      from_port   = 0
		      to_port     = 0
		      protocol    = "-1"
		      cidr_blocks = ["0.0.0.0/0"]
		    }
		  }
		  ```
	- **User Data Script for NGINX Setup**
		- **Static External File (`nginx-setup.sh`)**:
		  ```bash
		  #!/bin/bash
		  sudo apt update
		  sudo apt install -y nginx
		  sudo systemctl enable nginx
		  sudo systemctl start nginx
		  
		  # Deploy a simple static page
		  echo "<h1>Welcome to the Terraform-powered NGINX Server!</h1>" | sudo tee /var/www/html/index.html
		  ```
		- **Explanation**:  
		  Используемая команда `file("${path.module}/nginx-setup.sh")` позволяет Terraform подключить внешний скрипт без дублирования кода в `main.tf`.
	- **Dynamic Configuration File**
		- **Template for NGINX Config (`nginx.conf.tmpl`)**:
		  ```nginx
		  server {
		      listen 80;
		      server_name ${server_name};
		  
		      location / {
		          root /var/www/html;
		          index index.html;
		      }
		  
		      location /api {
		          proxy_pass http://localhost:8080;
		      }
		  }
		  ```
		- **Terraform Code Using `templatefile()`**:
		  ```hcl
		  data "template_file" "nginx_config" {
		    template = file("${path.module}/nginx.conf.tmpl")
		  
		    vars = {
		      server_name = "my-terraform-nginx"
		    }
		  }
		  
		  resource "aws_instance" "nginx_server" {
		    ami           = "ami-0c55b159cbfafe1f0"
		    instance_type = "t2.micro"
		    key_name      = var.key_name
		  
		    user_data = data.template_file.nginx_config.rendered
		  
		    tags = {
		      Name = "TerraformNGINXServer"
		    }
		  }
		  ```
	- **Terraform Variables for Flexibility**
		- **`variables.tf`**:
		  ```hcl
		  variable "key_name" {
		    description = "SSH key name for EC2 access"
		    type        = string
		  }
		  
		  variable "server_name" {
		    description = "Name for the NGINX server"
		    type        = string
		    default     = "my-terraform-nginx"
		  }
		  ```
		- **Applying with Custom Variables**:
		  ```bash
		  terraform apply -var="key_name=my-key"
		  ```
	- **Deploying the Infrastructure**
		- **Initialize the Terraform Project**:
		  ```bash
		  terraform init
		  ```
		- **Validate the Configuration**:
		  ```bash
		  terraform validate
		  ```
		- **Plan the Deployment**:
		  ```bash
		  terraform plan -var="key_name=my-key"
		  ```
		- **Apply Changes**:
		  ```bash
		  terraform apply -var="key_name=my-key" -auto-approve
		  ```
	- **Accessing the Web Server**
		- **Retrieve Public IP**:
		  ```bash
		  aws ec2 describe-instances --query "Reservations[*].Instances[*].PublicIpAddress" --output text
		  ```
		- **Access the Server**:
		  ```bash
		  curl http://<instance-public-ip>
		  ```
			- Should return the message:  
			  **"Welcome to the Terraform-powered NGINX Server!"**
	- **Modifying and Updating**
		- **Updating the Welcome Message**:
			- Modify the `nginx-setup.sh` file:
			  ```bash
			  echo "<h1>Updated: Terraform Web Server!</h1>" | sudo tee /var/www/html/index.html
			  ```
			- **Reapply the Changes**:
			  ```bash
			  terraform apply -var="key_name=my-key" -auto-approve
			  ```
	- **Tearing Down the Infrastructure**
		- **Destroy the Deployed Resources**:
		  ```bash
		  terraform destroy -auto-approve
		  ```
	- **Best Practices**
		- Use **remote state storage** (e.g., S3 with DynamoDB locking) for production environments.
		- Implement **variables** instead of hardcoded values for flexibility.
		- Avoid hardcoding sensitive information; use **AWS Secrets Manager** or **Terraform Vault**.
		- Structure code into **modules** for better maintainability.
		- Regularly update the **AWS provider** for compatibility with new features.
	- **Related Topics**
		- [[Terraform Basics]] – Overview of Terraform's core principles.
		- [[AWS]] – AWS instance configuration.
		- [[Terraform Variables]] – Managing dynamic input values.
		- [[Terraform Modules]] – Reusable infrastructure patterns.
		- [[Helm Charts]] – Kubernetes application management with Terraform.
- **Terraform: Lifecycle, Outputs, and Depends_On**
	- **Overview**:
		- Terraform is an Infrastructure as Code (IaC) tool used to provision and manage cloud infrastructure on various platforms (e.g., [[AWS]], [[GCP]], [[Azure]]).
		- Key features include state management, dependency graphing, and repeatable, version-controlled provisioning.
		- Understanding how Terraform handles resource lifecycles, outputs, and dependencies helps ensure a predictable and maintainable setup.
	- ## Lifecycle
		- **Definition**:
			- The `lifecycle` meta-argument is placed inside a resource block.
			- It modifies resource behavior during create, update, or delete actions.
		- **Common Lifecycle Arguments**:
		  1. **`create_before_destroy`**
			- Ensures a new resource is created before the old one is destroyed.
			- Useful when you want to minimize downtime (e.g., updating an [[AWS]] instance with zero downtime).
		- 2. **`prevent_destroy`**
			- Prevents a resource from being destroyed by throwing an error if a plan attempts to destroy it.
			- Ideal for critical resources like production databases where accidental deletion is unacceptable.
		- 3. **`ignore_changes`**
			- Instructs Terraform to ignore changes in certain attributes (e.g., ephemeral fields set by an external process like [[Docker]] containers or #ssh key rotation).
		- **Example Usage**:
		  ```hcl
		  resource "aws_instance" "example" {
		    ami           = "ami-0c55b159cbfafe1f0"
		    instance_type = "t2.micro"
		  
		    lifecycle {
		      create_before_destroy = true
		      prevent_destroy       = false
		      ignore_changes        = [
		        tags
		      ]
		    }
		  }
		  ```
	- ## Outputs
		- **Definition**:
			- Outputs allow you to expose or print resource attributes after a Terraform apply.
			- Often used to pass values to parent modules or display critical information (IP addresses, connection strings) in the CLI.
		- **Why Use Outputs?**:
			- Provide visibility into key resource attributes (e.g., instance IP).
			- Pass information between modules or to external tools (e.g., a script configuring [[Docker]] containers).
		- **Example Usage**:
		  ```hcl
		  output "instance_ip" {
		    description = "Public IP address of the instance"
		    value       = aws_instance.example.public_ip
		  }
		  
		  output "db_connection_string" {
		    description = "Database connection string"
		    value       = module.database.connection_string
		    sensitive   = true
		  }
		  ```
			- **`description`**: Adds clarity about the purpose of the output.
			- **`value`**: Typically references a resource or module attribute.
			- **`sensitive`**: Hides the output from logs; helpful for secrets or tokens.
	- ## depends_on
		- **Definition**:
			- `depends_on` is a meta-argument within resource or module blocks to explicitly define dependencies.
			- Ensures Terraform creates, updates, or destroys resources in a specific order when automatic inference isn’t enough.
		- **When to Use `depends_on`**:
			- **Indirect dependencies**: When resources rely on each other but no direct reference exists in the code.
			- **Complex service requirements**: For example, waiting for a [[Kubernetes]] cluster to be ready before deploying pods.
			- **Explicit orchestration**: If external or partial references that Terraform can’t infer are needed (like #ssh key setup scripts).
		- **Example Usage**:
		  ```hcl
		  resource "aws_s3_bucket" "my_bucket" {
		    bucket = "my-bucket"
		  }
		  
		  resource "aws_s3_bucket_policy" "my_bucket_policy" {
		    bucket = aws_s3_bucket.my_bucket.bucket
		    policy = data.aws_iam_policy_document.bucket_policy.json
		  
		    depends_on = [
		      aws_s3_bucket.my_bucket
		    ]
		  }
		  ```
			- Ensures `aws_s3_bucket_policy` is only created after `aws_s3_bucket`.
	- **Additional Notes**:
		- **Terraform Version**: Examples align with Terraform v1.1+ syntax; older versions may behave differently.
		- For advanced configuration, see [[Terraform Modules]].
		- For references on SSH automation, check [#ssh](#) in your notes.
		- For containerized infrastructure, explore [[Docker]] or [[Kubernetes]] references.
	- **Summary**:
		- **Lifecycle**: Use to control creation, update, and destruction patterns of resources.
		- **Outputs**: Useful for exposing or sharing resource information.
		- **Depends_On**: Essential for manually defining dependencies beyond Terraform’s automatic inference.
	- **Conclusion**:
		- By leveraging `lifecycle`, `outputs`, and `depends_on`, you can create a robust, predictable workflow for managing infrastructure with Terraform, whether on [[AWS]], on-prem, or in container environments with [[Docker]] and [[Kubernetes]].