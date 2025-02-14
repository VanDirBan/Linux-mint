- **Introduction to Helm**
	- **Definition**:
		- Helm is a package manager for Kubernetes that simplifies the deployment and management of applications.
		- Uses **Helm Charts**, which are predefined configurations for deploying applications.
	- **Key Features**:
		- **Templating**: Uses YAML templates to manage complex deployments.
		- **Versioning**: Allows rollback to previous configurations.
		- **Dependency Management**: Handles application dependencies within Kubernetes.
		- **Reusability**: Charts can be reused and shared via Helm repositories.
- **Installing Helm**
	- **Linux Installation**:
	  ```bash
	  curl -fsSL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
	  ```
	- **Verify Installation**:
	  ```bash
	  helm version
	  ```
- **Helm Commands**
	- **Search for a Chart**:
	  ```bash
	  helm search repo nginx
	  ```
	- **Add a Helm Repository**:
	  ```bash
	  helm repo add stable https://charts.helm.sh/stable
	  ```
	- **Update Repository Index**:
	  ```bash
	  helm repo update
	  ```
	- **Install a Chart**:
	  ```bash
	  helm install my-release stable/nginx
	  ```
	- **List Installed Releases**:
	  ```bash
	  helm list
	  ```
	- **Uninstall a Release**:
	  ```bash
	  helm uninstall my-release
	  ```
- **Creating a Custom Helm Chart**
	- **Initialize a New Chart**:
	  ```bash
	  helm create my-chart
	  ```
	- **Chart Directory Structure**:
	  ```plaintext
	  my-chart/
	  ├── charts/          # Dependencies (if any)
	  ├── templates/       # YAML templates for Kubernetes resources
	  │   ├── deployment.yaml
	  │   ├── service.yaml
	  │   ├── _helpers.tpl
	  ├── values.yaml      # Default configuration values
	  ├── Chart.yaml       # Metadata about the chart
	  ├── README.md        # Documentation
	  ```
	- **Defining Deployment in `templates/deployment.yaml`**:
	  ```yaml
	  apiVersion: apps/v1
	  kind: Deployment
	  metadata:
	    name: {{ .Release.Name }}-nginx
	  spec:
	    replicas: {{ .Values.replicaCount }}
	    selector:
	      matchLabels:
	        app: {{ .Release.Name }}-nginx
	    template:
	      metadata:
	        labels:
	          app: {{ .Release.Name }}-nginx
	      spec:
	        containers:
	          - name: nginx
	            image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
	            ports:
	              - containerPort: 80
	  ```
	- **Defining Values in `values.yaml`**:
	  ```yaml
	  replicaCount: 2
	  image:
	    repository: nginx
	    tag: latest
	  ```
- **Deploying a Custom Helm Chart**
	- **Package the Chart**:
	  ```bash
	  helm package my-chart
	  ```
	- **Deploy the Chart**:
	  ```bash
	  helm install my-app my-chart
	  ```
	- **Verify Deployment**:
	  ```bash
	  kubectl get pods
	  ```
	- **Upgrade a Release**:
	  ```bash
	  helm upgrade my-app my-chart
	  ```
	- **Rollback to a Previous Release**:
	  ```bash
	  helm rollback my-app 1
	  ```
	- **Delete a Release**:
	  ```bash
	  helm uninstall my-app
	  ```
- **Best Practices**
	- Always update `values.yaml` to customize deployments.
	- Use **`_helpers.tpl`** for reusable template logic.
	- Version control Helm charts in a Git repository.
	- Enable **Helm secrets** for managing sensitive data.
	- Utilize **Helm repositories** to store and share charts.