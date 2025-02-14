- **Introduction to AWS EKS**
	- **Definition**:
		- Amazon Elastic Kubernetes Service (EKS) is a managed Kubernetes service that simplifies cluster deployment, scaling, and management in AWS.
	- **Key Features**:
		- **Fully Managed Control Plane**: AWS handles Kubernetes master node management.
		- **Integration with AWS Services**: Works seamlessly with IAM, VPC, CloudWatch, and ALB.
		- **Security**: Supports IAM roles, encryption, and networking policies.
		- **Auto Scaling**: Automatically scales worker nodes with EC2 Auto Scaling Groups.
- **Installing eksctl**
	- **eksctl** is a CLI tool for creating and managing AWS EKS clusters.
	- **Install eksctl on Linux**:
	  ```bash
	  curl -LO "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_Linux_amd64.tar.gz"
	  tar -xzf eksctl_Linux_amd64.tar.gz
	  sudo mv eksctl /usr/local/bin/
	  ```
	- **Verify Installation**:
	  ```bash
	  eksctl version
	  ```
- **Setting Up AWS CLI and IAM Permissions**
	- **Ensure AWS CLI is Installed**:
	  ```bash
	  aws --version
	  ```
	- **Configure AWS Credentials**:
	  ```bash
	  aws configure
	  ```
	- **IAM Permissions Required**:
		- The user must have permissions to create EKS clusters:
		  ```json
		  {
		    "Effect": "Allow",
		    "Action": [
		      "eks:*",
		      "ec2:*",
		      "iam:*",
		      "cloudformation:*",
		      "s3:*"
		    ],
		    "Resource": "*"
		  }
		  ```
- **Creating an EKS Cluster with eksctl**
	- **Basic Cluster Deployment**:
	  ```bash
	  eksctl create cluster --name my-cluster --region us-east-1 --nodes 2
	  ```
	- **Custom Cluster Configuration Using a YAML File**
		- File: `eks-cluster.yaml`
		  ```yaml
		  apiVersion: eksctl.io/v1alpha5
		  kind: ClusterConfig
		  
		  metadata:
		    name: my-cluster
		    region: us-east-1
		    version: "1.30"
		  
		  nodeGroups:
		    - name: worker-nodes
		      instanceType: t3.micro
		      desiredCapacity: 2
		      minSize: 1
		      maxSize: 3
		      volumeSize: 20
		      ssh:
		        allow: true
		  ```
		- Create the cluster:
		  ```bash
		  eksctl create cluster -f eks-cluster.yaml
		  ```
	- **Check Cluster Status**:
	  ```bash
	  eksctl get cluster --name my-cluster
	  ```
- **Connecting to the EKS Cluster**
	- **Update `kubeconfig` to Access Cluster**:
	  ```bash
	  aws eks --region us-east-1 update-kubeconfig --name my-cluster
	  ```
	- **Verify Connection**:
	  ```bash
	  kubectl get nodes
	  kubectl cluster-info
	  ```
- **Deploying a Simple Application**
	- **Deploy Nginx**:
	  ```bash
	  kubectl create deployment nginx --image=nginx
	  ```
	- **Expose the Application**:
	  ```bash
	  kubectl expose deployment nginx --type=LoadBalancer --port=80
	  ```
	- **Check Service Endpoint**:
	  ```bash
	  kubectl get services
	  ```
- **Deleting the EKS Cluster**
	- **Destroy the Cluster**:
	  ```bash
	  eksctl delete cluster --name my-cluster
	  ```
- **Best Practices**
	- Use **Managed Node Groups** for cost efficiency.
	- Implement **IAM Roles for Service Accounts (IRSA)** to securely provide permissions to applications.
	- Enable **Cluster Autoscaler** for efficient resource management.
	- Configure **CloudWatch Logging** to monitor cluster activity.
	- Use **Private Networking** for security-sensitive applications.