- **Basics**
	- **What is Kubernetes (K8s)?**
		- **Definition**:
			- Kubernetes is an open-source container orchestration platform designed to automate deployment, scaling, and management of containerized applications.
			- Originally developed by Google, now maintained by the Cloud Native Computing Foundation (CNCF).
		- **Key Features**:
			- **Automated Deployment & Scaling**: Manages the lifecycle of containers.
			- **Self-Healing**: Detects and restarts failed containers.
			- **Load Balancing & Service Discovery**: Distributes traffic across multiple instances.
			- **Declarative Configuration**: Uses YAML/JSON manifests for defining workloads.
			- **Multi-Cloud & Hybrid Support**: Runs on any cloud provider, on-prem, or hybrid environment.
	- **Why Kubernetes?**
		- **Solves Challenges of Container Management**:
			- Containers (Docker, Podman, etc.) provide application isolation but require a way to manage them efficiently.
			- Kubernetes ensures that applications run reliably across different environments.
		- **Handles Microservices Architecture**:
			- Supports distributed applications where multiple services communicate dynamically.
		- **Efficient Resource Utilization**:
			- Manages compute resources to optimize costs and performance.
	- **Kubernetes Architecture Overview**
		- **Control Plane (Master Node)**:
			- **API Server** (`kube-apiserver`) → Central management interface.
			- **Controller Manager** (`kube-controller-manager`) → Monitors cluster state and manages resources.
			- **Scheduler** (`kube-scheduler`) → Assigns workloads to nodes.
			- **etcd** → Distributed key-value store for cluster state.
		- **Worker Nodes**:
			- **Kubelet** → Manages pods on the node.
			- **Container Runtime** (Docker, containerd) → Runs containers.
			- **Kube Proxy** → Handles networking between pods and external services.
	- **Basic Kubernetes Concepts**
		- **Pods**:
			- The smallest deployable unit in Kubernetes.
			- Can contain one or multiple containers.
		- **Nodes**:
			- Worker machines where pods are deployed.
		- **Deployments**:
			- Declarative way to manage application versions and updates.
		- **Services**:
			- Expose applications running in pods to the network.
		- **ConfigMaps & Secrets**:
			- Manage configuration and sensitive data.
	- **Comparison with Traditional Deployment**
		- **Without Kubernetes**:
			- Deploying applications requires manual scaling, networking, and monitoring.
			- Failures must be handled manually.
		- **With Kubernetes**:
			- Applications are automatically restarted, scaled, and balanced across nodes.
	- **Getting Started**
		- **Install Minikube (Local Kubernetes Cluster)**:
		  ```bash
		  minikube start
		  ```
		- **Check Cluster Status**:
		  ```bash
		  kubectl cluster-info
		  kubectl get nodes
		  ```
		- **Deploy a Simple Application**:
		  ```bash
		  kubectl create deployment nginx --image=nginx
		  ```
		- **Expose Deployment as a Service**:
		  ```bash
		  kubectl expose deployment nginx --port=80 --type=NodePort
		  ```
		- **View Running Resources**:
		  ```bash
		  kubectl get pods
		  kubectl get services
		  ```
	- **Best Practices**
		- Always define applications in declarative YAML manifests.
		- Use namespaces to organize workloads in large clusters.
		- Enable logging and monitoring for better visibility (`Prometheus`, `Grafana`).
		- Implement role-based access control (RBAC) for security.
- **Setting Up a Local Kubernetes Cluster on Linux x64**
	- **Definition**:
		- Running a Kubernetes cluster locally allows testing, development, and learning without using cloud resources.
		- Tools like `minikube` and `kind` (Kubernetes in Docker) are commonly used.
	- **Choosing a Local Kubernetes Solution**
		- **Minikube**:
			- A lightweight Kubernetes cluster designed for local development.
			- Runs a single-node cluster using a VM or container runtime.
		- **kind (Kubernetes-in-Docker)**:
			- Uses Docker to simulate a multi-node Kubernetes cluster.
			- More lightweight than Minikube for advanced use cases.
	- **Installing Minikube (Recommended for Beginners)**
		- **Prerequisites**:
			- Virtualization must be enabled (e.g., KVM, VirtualBox, Docker).
			- Install dependencies:
			  ```bash
			  sudo apt update && sudo apt install -y curl conntrack
			  ```
		- **Install Minikube**:
		  ```bash
		  curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
		  sudo install minikube-linux-amd64 /usr/local/bin/minikube
		  ```
		- **Start Minikube Cluster**:
		  ```bash
		  minikube start --driver=docker
		  ```
		- **Verify Installation**:
		  ```bash
		  minikube status
		  kubectl get nodes
		  ```
		- **Test Deployment**:
		  ```bash
		  kubectl create deployment nginx --image=nginx
		  kubectl expose deployment nginx --type=NodePort --port=80
		  ```
		- **Access the Application**:
		  ```bash
		  minikube service nginx --url
		  ```
	- **Installing kind (Alternative)**
		- **Install Docker (Required for kind)**:
		  ```bash
		  sudo apt install -y docker.io
		  ```
		- **Install kind**:
		  ```bash
		  curl -Lo ./kind https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64
		  chmod +x ./kind
		  sudo mv ./kind /usr/local/bin/kind
		  ```
		- **Create a kind Cluster**:
		  ```bash
		  kind create cluster --name my-cluster
		  ```
		- **Check Cluster Status**:
		  ```bash
		  kubectl get nodes
		  ```
		- **Delete Cluster**:
		  ```bash
		  kind delete cluster --name my-cluster
		  ```
	- **Basic Kubernetes Commands**
		- **View Cluster Information**:
		  ```bash
		  kubectl cluster-info
		  ```
		- **View Running Pods and Services**:
		  ```bash
		  kubectl get pods
		  kubectl get services
		  ```
		- **Stop Minikube**:
		  ```bash
		  minikube stop
		  ```
		- **Delete Minikube Cluster**:
		  ```bash
		  minikube delete
		  ```
	- **Best Practices**
		- Use Minikube for learning Kubernetes fundamentals.
		- Use kind if you need multi-node testing with a lightweight setup.
		- Ensure `kubectl` is correctly configured to communicate with the local cluster.
		- Monitor resource usage, as local clusters can consume significant CPU and RAM.
- [[AWS EKS]]
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
- [[Pods]]
	- **Introduction to Pods**
		- **Definition**:
			- A Pod is the smallest deployable unit in Kubernetes.
			- It represents a single instance of a running process in the cluster.
		- **Key Features**:
			- Can contain one or multiple containers.
			- Containers in a Pod share the same network namespace (IP address).
			- Pods are ephemeral and managed by higher-level controllers like Deployments.
	- **Creating a Pod**
		- **Using `kubectl run` (Imperative Method)**:
		  ```bash
		  kubectl run my-pod --image=nginx --restart=Never
		  ```
		- **Using a YAML Manifest (Declarative Method)**:
			- `pod.yaml`:
			  ```yaml
			  apiVersion: v1
			  kind: Pod
			  metadata:
			    name: my-pod
			  spec:
			    containers:
			      - name: nginx-container
			        image: nginx
			        ports:
			          - containerPort: 80
			  ```
			- Apply the configuration:
			  ```bash
			  kubectl apply -f pod.yaml
			  ```
	- **Viewing Pod Status**
		- **List all Pods**:
		  ```bash
		  kubectl get pods
		  ```
		- **Describe a Pod in Detail**:
		  ```bash
		  kubectl describe pod my-pod
		  ```
		- **Check Pod Logs**:
		  ```bash
		  kubectl logs my-pod
		  ```
		- **Stream Logs in Real-Time**:
		  ```bash
		  kubectl logs -f my-pod
		  ```
	- **Interacting with Running Pods**
		- **Execute a Command Inside a Pod**:
		  ```bash
		  kubectl exec my-pod -- ls /
		  ```
		- **Start an Interactive Shell Inside a Pod**:
		  ```bash
		  kubectl exec -it my-pod -- /bin/sh
		  ```
	- **Deleting Pods**
		- **Delete a Specific Pod**:
		  ```bash
		  kubectl delete pod my-pod
		  ```
		- **Delete All Pods in a Namespace**:
		  ```bash
		  kubectl delete pods --all
		  ```
	- **Multi-Container Pods**
		- **Example of a Pod with Multiple Containers**:
		  ```yaml
		  apiVersion: v1
		  kind: Pod
		  metadata:
		    name: multi-container-pod
		  spec:
		    containers:
		      - name: app-container
		        image: my-app
		      - name: sidecar-container
		        image: log-collector
		  ```
	- **Pod Lifecycle**
		- **Pod Phases**:
			- `Pending` → Pod created but not scheduled yet.
			- `Running` → At least one container is running.
			- `Succeeded` → All containers have completed successfully.
			- `Failed` → At least one container failed.
			- `Unknown` → Pod status cannot be determined.
		- **Restart Policies**:
			- `Always` (default): Restart container if it exits.
			- `OnFailure`: Restart only if the container exits with a non-zero status.
			- `Never`: Do not restart containers.
	- **Best Practices**
		- Use Deployments instead of standalone Pods for high availability.
		- Assign resource limits (`cpu`, `memory`) to prevent resource exhaustion.
		- Use Labels and Selectors to organize and manage Pods efficiently.
		- Monitor Pods using `kubectl logs` and `kubectl describe` for troubleshooting.
- [[Deployments]]
	- **Introduction to Deployments**
		- **Definition**:
			- A Deployment is a Kubernetes resource that manages the lifecycle of Pods.
			- It ensures that the desired number of Pod replicas are running and handles rolling updates and rollbacks.
		- **Key Features**:
			- **Self-healing**: Automatically restarts failed Pods.
			- **Scaling**: Adjusts the number of running Pods dynamically.
			- **Rolling Updates**: Ensures zero downtime when deploying new versions.
			- **Rollback Support**: Reverts to a previous version in case of failure.
	- **Creating a Deployment**
		- **Using `kubectl create deployment` (Imperative Method)**:
		  ```bash
		  kubectl create deployment my-deployment --image=nginx
		  ```
		- **Using a YAML Manifest (Declarative Method)**:
			- `deployment.yaml`:
			  ```yaml
			  apiVersion: apps/v1
			  kind: Deployment
			  metadata:
			    name: my-deployment
			  spec:
			    replicas: 3
			    selector:
			      matchLabels:
			        app: my-app
			    template:
			      metadata:
			        labels:
			          app: my-app
			      spec:
			        containers:
			          - name: nginx-container
			            image: nginx
			            ports:
			              - containerPort: 80
			  ```
			- Apply the configuration:
			  ```bash
			  kubectl apply -f deployment.yaml
			  ```
	- **Viewing Deployment Status**
		- **List all Deployments**:
		  ```bash
		  kubectl get deployments
		  ```
		- **Describe a Specific Deployment**:
		  ```bash
		  kubectl describe deployment my-deployment
		  ```
		- **List All Pods Managed by a Deployment**:
		  ```bash
		  kubectl get pods -l app=my-app
		  ```
	- **Scaling Deployments**
		- **Manually Scale a Deployment**:
		  ```bash
		  kubectl scale deployment my-deployment --replicas=5
		  ```
		- **Update a Deployment Manifest for Scaling**:
			- Modify `replicas: 5` in `deployment.yaml`, then apply the changes:
			  ```bash
			  kubectl apply -f deployment.yaml
			  ```
	- **Rolling Updates**
		- **Update the Image Version in Deployment**:
		  ```bash
		  kubectl set image deployment/my-deployment nginx-container=nginx:1.21
		  ```
		- **Check Rollout Status**:
		  ```bash
		  kubectl rollout status deployment my-deployment
		  ```
		- **View Deployment Revision History**:
		  ```bash
		  kubectl rollout history deployment my-deployment
		  ```
	- **Rolling Back a Deployment**
		- **Undo the Last Update**:
		  ```bash
		  kubectl rollout undo deployment my-deployment
		  ```
		- **Rollback to a Specific Revision**:
		  ```bash
		  kubectl rollout undo deployment my-deployment --to-revision=2
		  ```
	- **Deleting Deployments**
		- **Delete a Specific Deployment**:
		  ```bash
		  kubectl delete deployment my-deployment
		  ```
		- **Delete All Deployments in a Namespace**:
		  ```bash
		  kubectl delete deployments --all
		  ```
	- **Best Practices**
		- Always use **Deployments** instead of standalone Pods for high availability.
		- Assign **resource limits** (`cpu`, `memory`) to avoid excessive resource usage.
		- Use **readiness and liveness probes** to monitor application health.
		- Manage configurations with **ConfigMaps and Secrets**.
		- Implement **auto-scaling** (`HorizontalPodAutoscaler`) for efficient resource utilization.