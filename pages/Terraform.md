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
- **Lifecycle, Outputs, and Depends_On**
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
- **Data Sources**
	- **Definition**:
		- Terraform **data sources** allow you to retrieve information from external systems to use within your Terraform configuration.
		- For example, you can fetch details about existing resources (e.g., VPCs, subnets) or AWS account info without manually hardcoding them.
		- This helps keep configurations dynamic, maintainable, and less prone to errors.
	- **Why Use Data Sources**:
		- Avoid hardcoding resource information.
		- Dynamically configure infrastructure based on existing [[AWS]] components.
		- Facilitate sharing information across multiple Terraform configurations or modules.
		- Improve maintainability by automatically updating resource references when they change.
	- **Common AWS Data Sources**:
	  1. **aws_availability_zones**
		- Retrieves a list of availability zones for a given region.
		- Useful for distributing resources across multiple AZs.
		  ```hcl
		  data "aws_availability_zones" "available" {
		  state = "available"
		  }
		  ```
		- You might use `data.aws_availability_zones.available.names` to loop over these zones for creating subnets.
		  
		  2. **aws_caller_identity**
		- Exposes information about the currently configured [[AWS]] account, including the account ID and ARN of the credentials.
		  ```hcl
		  data "aws_caller_identity" "current" {}
		  ```
		- Attributes:
			- `data.aws_caller_identity.current.account_id`
			- `data.aws_caller_identity.current.arn`
			- `data.aws_caller_identity.current.user_id`
		- Helps in tagging or naming resources automatically with the account ID.
		  
		  3. **aws_region**
		- Retrieves the current [[AWS]] region from the provider configuration.
		  ```hcl
		  data "aws_region" "current" {}
		  ```
		- Attributes:
			- `data.aws_region.current.name` (e.g., `us-east-1` or `eu-west-1`)
			  
			  4. **aws_vpcs**
		- Retrieves information about multiple VPCs in your account.
		  ```hcl
		  data "aws_vpcs" "all" {}
		  ```
		- Attributes:
			- `data.aws_vpcs.all.ids` → A list of VPC IDs.
			- You can filter or reference them dynamically for subsequent resource creation (e.g., subnets, routing tables).
	- **Auto-Lookup of AMI (Example of Data Source)**
		- Instead of hardcoding an AMI ID, you can use the **aws_ami** data source to automatically find the most recent or matching image.
		- **Example**: Find the latest Amazon Linux 2 AMI.
		  ```hcl
		  data "aws_ami" "amazon_linux" {
		    most_recent = true
		    owners      = ["amazon"]
		  
		    filter {
		      name   = "name"
		      values = ["amzn2-ami-kernel-*-x86_64"]
		    }
		  
		    filter {
		      name   = "state"
		      values = ["available"]
		    }
		  }
		  
		  resource "aws_instance" "example" {
		    ami           = data.aws_ami.amazon_linux.id
		    instance_type = "t2.micro"
		  }
		  ```
			- **Explanation**:
				- `most_recent = true` returns the newest AMI matching the filters.
				- `owners = ["amazon"]` ensures the AMI is provided by [[AWS]].
				- The `filter` blocks specify additional search criteria.
	- **Example Use-Case**:
		- In a typical scenario, you might combine multiple data sources in a single config:
		  ```hcl
		  provider "aws" {
		    region = "us-east-1"
		  }
		  
		  data "aws_caller_identity" "current" {}
		  
		  data "aws_vpcs" "all" {}
		  
		  data "aws_region" "current" {}
		  
		  data "aws_ami" "my_ami" {
		    most_recent = true
		    owners      = ["amazon"]
		  
		    filter {
		      name   = "name"
		      values = ["amzn2-ami-hvm-*-x86_64-gp2"]
		    }
		  
		    filter {
		      name   = "state"
		      values = ["available"]
		    }
		  }
		  
		  resource "aws_instance" "example" {
		    ami           = data.aws_ami.my_ami.id
		    instance_type = "t2.micro"
		    subnet_id     = element(data.aws_vpcs.all.ids, 0) # Example usage of the first VPC ID
		    tags = {
		      Name      = "ExampleInstance"
		      OwnerID   = data.aws_caller_identity.current.account_id
		      Region    = data.aws_region.current.name
		    }
		  }
		  ```
			- Here you can see how each data source provides dynamic input to resource creation.
	- **Summary**:
		- **Data Sources** enable you to dynamically fetch external information for use in your Terraform configs.
		- **aws_availability_zones**, **aws_caller_identity**, **aws_region**, and **aws_vpcs** are common built-in data sources for gathering environment details.
		- **Auto-lookup of AMI** using **aws_ami** ensures you’re always using the latest or most suitable image for your [[AWS]] infrastructure.
		- Overall, data sources help keep your infrastructure code clean, flexible, and up-to-date.
- **Zero Downtime Web Server with Terraform**
	- **Definition**:
		- **Zero Downtime** refers to updating or redeploying your application or infrastructure without causing any interruptions or outages in the service.
		- This approach is often required for production environments where a service must remain accessible at all times.
	- **Key Components**:
		- **[[AWS]] ELB (Classic ELB)**:
			- Distributes incoming traffic across multiple instances in different availability zones.
			- Health checks ensure traffic is only routed to healthy instances.
		- **Auto Scaling Group (ASG)**:
			- Manages and scales a group of instances automatically.
			- Ensures minimum and maximum capacity, and replaces unhealthy instances.
		- **Launch Template**:
			- Defines the configuration (AMI, instance type, security groups, etc.) for instances in the ASG.
			- Supports versioning, allowing updates without downtime.
		- **Security Group**:
			- Configures inbound (TCP 80) and outbound rules.
			- Ensures controlled network access to and from the instances.
		- **AWS Default Subnets**:
			- Subnets that exist by default in each AZ, used here for simplicity.
			- Distributing instances across multiple AZs increases fault tolerance.
	- **Conceptual Flow**:
	  1. **User Requests** → [[AWS]] **ELB** → Healthy Instances in **Auto Scaling Group** → Serve Web Content.
	  2. **ASG** ensures there are always at least 2 instances running (min_size = 2).
	  3. **Lifecycle** settings in Terraform are used to create new resources before destroying old ones (`create_before_destroy`), helping to maintain zero downtime.
	  4. **Health Checks** in the ELB continuously verify instance health. Unhealthy instances are removed from rotation automatically.
	- **Terraform Configuration Walkthrough**:
		- **Provider and Data Sources**:
		  ```hcl
		  provider "aws" {}
		  
		  data "aws_availability_zones" "avaiblible" {}
		  
		  data "aws_ami" "latest_ubuntu" {
		    owners      = ["099720109477"]
		    most_recent = true
		    filter {
		      name   = "name"
		      values = ["ubuntu/images/hvm-ssd/ubuntu-jammy-22.04-amd64-server*"]
		    }
		  }
		  ```
			- **`aws_availability_zones`**: Retrieves the available AZs for your region.
			- **`aws_ami`**: Dynamically fetches the latest Ubuntu 22.04 (Jammy) image owned by Canonical.
		- **Security Group**:
		  ```hcl
		  resource "aws_security_group" "web" {
		    name = "My Security Group"
		  
		    dynamic "ingress" {
		      for_each = ["80"]
		      content {
		        from_port   = ingress.value
		        to_port     = ingress.value
		        protocol    = "tcp"
		        cidr_blocks = ["0.0.0.0/0"]
		      }
		    }
		  
		    egress {
		      from_port   = 0
		      to_port     = 0
		      protocol    = "-1"
		      cidr_blocks = ["0.0.0.0/0"]
		    }
		  
		    tags = {
		      Name  = "My Security Group"
		      Onwer = "Van"
		    }
		  }
		  ```
			- Allows HTTP access on port 80 from anywhere.
			- Allows outbound traffic on all ports.
			- Minimal config for a simple web server.
		- **Launch Template**:
		  ```hcl
		  resource "aws_launch_template" "as_lt" {
		    name_prefix   = "web-server-lt-"
		    image_id      = data.aws_ami.latest_ubuntu.id
		    instance_type = "t3.micro"
		  
		    vpc_security_group_ids = [aws_security_group.web.id]
		  
		    user_data = filebase64("user_data.sh")
		  
		    tag_specifications {
		      resource_type = "instance"
		      tags = {
		        Name  = "WebServer-instance"
		        Owner = "Van"
		      }
		    }
		  
		    lifecycle {
		      create_before_destroy = true
		      prevent_destroy       = false
		    }
		  }
		  ```
			- References `data.aws_ami.latest_ubuntu.id` to always use the latest Ubuntu image.
			- `user_data` can include commands to install and configure your web server automatically.
		- **Auto Scaling Group (ASG)**:
		  ```hcl
		  resource "aws_autoscaling_group" "web" {
		    name = "ASG-${aws_launch_template.as_lt.latest_version}"
		  
		    launch_template {
		      id      = aws_launch_template.as_lt.id
		      version = "$Latest"
		    }
		  
		    min_size            = 2
		    max_size            = 4
		    min_elb_capacity    = 2
		    vpc_zone_identifier = [aws_default_subnet.default_az1.id, aws_default_subnet.default_az2.id]
		    health_check_type   = "ELB"
		    load_balancers      = [aws_elb.web.name]
		  
		    dynamic "tag" {
		      for_each = {
		        Name  = "WebServer-in-ASG"
		        Owner = "Van"
		      }
		      content {
		        key                 = tag.key
		        value               = tag.value
		        propagate_at_launch = true
		      }
		    }
		  
		    lifecycle {
		      create_before_destroy = true
		    }
		  }
		  ```
			- Maintains 2 to 4 instances, ensuring there are always at least 2 to handle traffic.
			- Uses the `load_balancers` argument to associate with the ELB.
			- `create_before_destroy` helps ensure no downtime when updating or changing the ASG.
		- **Elastic Load Balancer (ELB)**:
		  ```hcl
		  resource "aws_elb" "web" {
		    name               = "WebServer-HA-ELB"
		    availability_zones = [data.aws_availability_zones.avaiblible.names[0], data.aws_availability_zones.avaiblible.names[1]]
		    security_groups    = [aws_security_group.web.id]
		  
		    listener {
		      lb_port           = 80
		      lb_protocol       = "http"
		      instance_port     = 80
		      instance_protocol = "http"
		    }
		  
		    health_check {
		      healthy_threshold   = 2
		      unhealthy_threshold = 2
		      timeout             = 3
		      target              = "HTTP:80/"
		      interval            = 10
		    }
		  
		    tags = {
		      Name = "Web_server_Highly_Availible_ELB"
		    }
		  }
		  ```
			- Classic ELB listening on port 80.
			- Health checks ensure only healthy instances receive traffic.
		- **Default Subnets**:
		  ```hcl
		  resource "aws_default_subnet" "default_az1" {
		    availability_zone = data.aws_availability_zones.avaiblible.names[0]
		  }
		  
		  resource "aws_default_subnet" "default_az2" {
		    availability_zone = data.aws_availability_zones.avaiblible.names[1]
		  }
		  ```
			- Provide subnets in different AZs to improve availability.
		- **Output**:
		  ```hcl
		  output "web_loadbalabcer_url" {
		    value = aws_elb.web.dns_name
		  }
		  ```
			- Shows the ELB’s DNS name after deployment.
			- You can directly access your web server at this endpoint.
	- **Zero Downtime Strategy**:
		- **Multi-AZ**: By using at least two availability zones, you ensure that if one AZ experiences issues, the other can still serve traffic.
		- **Rolling Updates via ASG**: With `create_before_destroy` and health checks, Terraform will create new instances, attach them to the ELB, and only then remove old instances. This ensures uninterrupted service.
		- **Elastic Load Balancer**: The ELB automatically routes traffic to healthy instances and seamlessly handles scaling events.
	- **Conclusion**:
		- This Terraform configuration sets up a highly available web server using an **Auto Scaling Group** and **ELB** in multiple availability zones.
		- **Zero Downtime** is achieved through careful use of Terraform’s lifecycle rules, health checks, and multi-AZ redundancy.
		- For advanced configurations, consider exploring [[Terraform Modules]], containerization with [[Docker]] or [[Kubernetes]] for container-based deployments, or #ssh for remote instance management.
- **Comparison: Zero Downtime Web Server (Version 1 vs. Improved Version)**
	- **Overview**:
		- In the first configuration, a **Classic ELB** (`aws_elb`) was used, while in the improved version we’ve migrated to an **Application Load Balancer (ALB)** (`aws_lb`).
		- Default VPC and subnets are explicitly defined, making network configuration more intentional.
		- **Default tags** are applied at the provider level, removing the need to repeat them in multiple resources.
		- An additional port (443) is now opened, hinting at potential HTTPS usage or future SSL offloading.
		  
		  ---
	- **1. Provider & Default Tags**:
		- **Previous**:
			- No default tags were defined at the provider level; tags had to be set manually for each resource.
		- **Improved Version**:
		  ```hcl
		  provider "aws" {
		    default_tags {
		      tags = {
		        Owner     = "Van"
		        CreatedBy = "Terraform"
		      }
		    }
		  }
		  ```
			- Simplifies tagging across all resources.
			- Ensures consistency, reduces repetitive code, and helps with cost allocation or resource tracking in the AWS console.
			  
			  ---
	- **2. Network Configuration**:
		- **Previous**:
			- Used `aws_default_subnet` but didn’t explicitly reference a VPC resource.
			- Relied on the default AWS VPC implicitly.
		- **Improved Version**:
		  ```hcl
		  resource "aws_default_vpc" "default" {}
		  
		  resource "aws_default_subnet" "default_az1" {
		    availability_zone = data.aws_availability_zones.avaiblible.names[0]
		  }
		  
		  resource "aws_default_subnet" "default_az2" {
		    availability_zone = data.aws_availability_zones.avaiblible.names[1]
		  }
		  ```
			- Explicitly declares a `aws_default_vpc.default` resource.
			- Confirms the subnet placement within that VPC.
			- Improves clarity regarding which VPC is being used.
			  
			  ---
	- **3. Security Group**:
		- **Previous**:
			- Allowed only port 80 inbound.
			- Did not specify `vpc_id`, assuming default behavior.
		- **Improved Version**:
		  ```hcl
		  resource "aws_security_group" "web" {
		    name   = "My Security Group"
		    vpc_id = aws_default_vpc.default.id
		  
		    dynamic "ingress" {
		      for_each = ["80", "443"]
		      content {
		        from_port   = ingress.value
		        to_port     = ingress.value
		        protocol    = "tcp"
		        cidr_blocks = ["0.0.0.0/0"]
		      }
		    }
		  
		    ...
		  }
		  ```
			- Now opens both **port 80 and port 443**, indicating readiness for HTTPS traffic or SSL/TLS termination in the future.
			- Explicitly assigns the security group to the default VPC, increasing transparency.
			  
			  ---
	- **4. Load Balancer**:
		- **Previous**:
			- Used `aws_elb.web` (Classic Load Balancer).
			- Configured with listeners, health checks, and attached to the ASG via `load_balancers` argument.
		- **Improved Version**:
		  ```hcl
		  resource "aws_lb" "web" {
		    name               = "WebServer-Highly-Available-LB"
		    load_balancer_type = "application"
		    security_groups    = [aws_security_group.web.id]
		    subnets            = [
		      aws_default_subnet.default_az1.id,
		      aws_default_subnet.default_az2.id
		    ]
		  }
		  ```
			- Upgrades to an **Application Load Balancer (ALB)** (`aws_lb`).
			- **ALB** is often preferred for advanced routing, path-based or host-based rules, and improved metrics.
			- The security groups and subnets are explicitly declared, clarifying the LB’s networking setup.
		- **Target Group & Listener**:
		  ```hcl
		  resource "aws_lb_target_group" "web" {
		    name       = "WebServer-Highly-Available-TG"
		    vpc_id     = aws_default_vpc.default.id
		    port       = 80
		    protocol   = "HTTP"
		    ...
		  }
		  
		  resource "aws_lb_listener" "http" {
		    load_balancer_arn = aws_lb.web.arn
		    port              = 80
		    protocol          = "HTTP"
		  
		    default_action {
		      type             = "forward"
		      target_group_arn = aws_lb_target_group.web.arn
		    }
		  }
		  ```
			- **aws_lb_target_group** and **aws_lb_listener** replace the previous single resource configuration of `aws_elb`.
			- Allows for more flexible and modern load balancing features, including **deregistration delay** for graceful shutdowns (set to 10 seconds).
			  
			  ---
	- **5. Auto Scaling Group (ASG)**:
		- **Previous**:
			- Associated an ASG with the Classic ELB using the `load_balancers` argument.
		- **Improved Version**:
		  ```hcl
		  resource "aws_autoscaling_group" "web" {
		    ...
		    target_group_arns = [aws_lb_target_group.web.arn]
		  
		    launch_template {
		      id      = aws_launch_template.web.id
		      version = aws_launch_template.web.latest_version
		    }
		  
		    dynamic "tag" {
		      for_each = {
		        Name = "WebServer-in-ASG-v${aws_launch_template.web.latest_version}"
		      }
		      content {
		        key                 = tag.key
		        value               = tag.value
		        propagate_at_launch = true
		      }
		    }
		    ...
		  }
		  ```
			- **`target_group_arns`** is used instead of the `load_balancers` argument (Classic ELB).
			- **Launch Template** references a single version (no name prefix needed, because `aws_launch_template` explicitly uses `name`).
			- The **dynamic tag** key now includes the version from the launch template (`v${aws_launch_template.web.latest_version}`), improving traceability.
			- Retains **create_before_destroy** for zero-downtime updates.
			  
			  ---
	- **6. Launch Template**:
		- **Previous**:
			- `name_prefix` was used, user data was read from `filebase64("user_data.sh")`.
		- **Improved Version**:
		  ```hcl
		  resource "aws_launch_template" "web" {
		    name                   = "WebServer-Highly-Available-LT"
		    image_id               = data.aws_ami.latest_ubuntu.id
		    instance_type          = "t3.micro"
		    vpc_security_group_ids = [aws_security_group.web.id]
		    user_data              = filebase64("${path.module}/user_data.sh")
		  }
		  ```
			- Provides a **fixed name** (`WebServer-Highly-Available-LT`) rather than a prefix.
			- Uses `${path.module}` to make the path more explicit for user_data.
			- Overall cleaner approach, though functionally similar to the prior version for setting up the instance configuration.
			  
			  ---
	- **7. Summary of Improvements**:
	  1. **Default Tags**: Centralized tagging logic in the provider for consistent resource tagging.
	  2. **Default VPC & Subnets**: Explicitly declared, ensuring clarity about the environment.
	  3. **Security Group Enhancements**: Inbound rules for both HTTP (80) and HTTPS (443).
	  4. **Shift to Application Load Balancer**:
		- Adds advanced load balancing features.
		- Improves modern best practices over Classic ELB.
		  5. **Target Group & Listener**: Allows fine-grained control of how traffic is routed.
		  6. **Launch Template**: Simplified naming and explicit user_data reference.
		  7. **ASG with target_group_arns**: Aligns with ALB usage, better logging, and deregistration.
	- **Conclusion**:
		- The **improved configuration** offers a more flexible and maintainable setup, leveraging Application Load Balancing, explicit network references, and centralized tagging.
		- These changes make the infrastructure more **scalable, future-proof**, and **easier to manage** compared to the original version.
-