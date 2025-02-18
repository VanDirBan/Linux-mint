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
- **Deploying a Web Server with**  #NGINX
	- **Definition**:
		- Terraform can be used to deploy a web server (NGINX) on an AWS EC2 instance.
		- Supports handling **static files**, **dynamic files**, and **dynamic code blocks**.
	- **Key Features**:
		- Automates EC2 provisioning.
		- Configures NGINX with user data.
		- Uses external files for configuration.
	- **Terraform Configuration for NGINX**
		- **Defining EC2 Instance with NGINX**:
		  ```hcl
		  resource "aws_instance" "nginx_server" {
		    ami           = "ami-0c55b159cbfafe1f0"
		    instance_type = "t2.micro"
		    key_name      = "my-key"
		  
		    security_groups = [aws_security_group.nginx_sg.name]
		  
		    user_data = file("${path.module}/nginx-setup.sh")
		  
		    tags = {
		      Name = "TerraformWebServer"
		    }
		  }
		  ```
		- **Creating a Security Group for NGINX**:
		  ```hcl
		  resource "aws_security_group" "nginx_sg" {
		    name        = "nginx_sg"
		    description = "Allow HTTP and SSH"
		  
		    ingress {
		      from_port   = 80
		      to_port     = 80
		      protocol    = "tcp"
		      cidr_blocks = ["0.0.0.0/0"]
		    }
		  
		    ingress {
		      from_port   = 22
		      to_port     = 22
		      protocol    = "tcp"
		      cidr_blocks = ["0.0.0.0/0"]
		    }
		  
		    egress {
		      from_port   = 0
		      to_port     = 0
		      protocol    = "-1"
		      cidr_blocks = ["0.0.0.0/0"]
		    }
		  }
		  ```
	- **User Data Script for Installing NGINX**
		- **Using External Static File (`nginx-setup.sh`)**:
			- Create the `nginx-setup.sh` file:
			  ```bash
			  #!/bin/bash
			  sudo apt update
			  sudo apt install -y nginx
			  sudo systemctl enable nginx
			  sudo systemctl start nginx
			  
			  echo "<h1>Welcome to Terraform Web Server</h1>" | sudo tee /var/www/html/index.html
			  ```
			- Reference it in `main.tf`:
			  ```hcl
			  user_data = file("${path.module}/nginx-setup.sh")
			  ```
	- **Handling External Static Files**
		- **Using `file()` Function**:
			- Example of serving a static HTML file:
			  ```hcl
			  resource "aws_instance" "web_server" {
			    user_data = file("${path.module}/index.html")
			  }
			  ```
			- **Example Static HTML File (`index.html`)**:
			  ```html
			  <html>
			    <head><title>Terraform Web Server</title></head>
			    <body>
			      <h1>Hello from Terraform-managed NGINX Server</h1>
			    </body>
			  </html>
			  ```
	- **Handling Dynamic File Generation**
		- **Using `templatefile()` for Configuration Generation**:
			- Create a template file (`nginx.conf.tmpl`):
			  ```nginx
			  server {
			      listen 80;
			      server_name ${server_name};
			  
			      location / {
			          root /var/www/html;
			          index index.html;
			      }
			  }
			  ```
			- Use it in Terraform:
			  ```hcl
			  data "template_file" "nginx_config" {
			    template = file("${path.module}/nginx.conf.tmpl")
			  
			    vars = {
			      server_name = "my-terraform-server"
			    }
			  }
			  
			  resource "aws_instance" "nginx_server" {
			    user_data = data.template_file.nginx_config.rendered
			  }
			  ```
	- **Handling Dynamic Blocks in Terraform**
		- **Example: Defining Multiple Ingress Rules Dynamically**:
		  ```hcl
		  resource "aws_security_group" "nginx_sg" {
		    name = "nginx_sg"
		  
		    dynamic "ingress" {
		      for_each = [80, 443]
		  
		      content {
		        from_port   = ingress.value
		        to_port     = ingress.value
		        protocol    = "tcp"
		        cidr_blocks = ["0.0.0.0/0"]
		      }
		    }
		  }
		  ```
	- **Applying Terraform Configuration**
		- **Initialize and Apply**:
		  ```bash
		  terraform init
		  terraform apply -auto-approve
		  ```
		- **Check Instance Public IP**:
		  ```bash
		  terraform output instance_ip
		  ```
		- **Access Web Server**:
		  ```bash
		  curl http://<instance-public-ip>
		  ```
	- **Destroying Infrastructure**
		- **Delete Everything**:
		  ```bash
		  terraform destroy -auto-approve
		  ```
	- **Best Practices**
		- Use **remote state storage** for better management.
		- Store sensitive data (e.g., SSH keys) in **AWS Secrets Manager** instead of plain text.
		- Automate deployments with **Terraform Modules**.
		- Implement **load balancers** for high availability.