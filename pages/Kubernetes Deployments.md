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
- **Writing Deployment Manifests**
	- **Structure of a Deployment Manifest**
	  ```yaml
	  apiVersion: apps/v1
	  kind: Deployment
	  metadata:
	    name: my-deployment
	    labels:
	      app: my-app
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
	            image: nginx:1.21
	            ports:
	              - containerPort: 80
	            resources:
	              requests:
	                cpu: "100m"
	                memory: "128Mi"
	              limits:
	                cpu: "500m"
	                memory: "256Mi"
	            livenessProbe:
	              httpGet:
	                path: /
	                port: 80
	              initialDelaySeconds: 5
	              periodSeconds: 10
	            readinessProbe:
	              httpGet:
	                path: /
	                port: 80
	              initialDelaySeconds: 5
	              periodSeconds: 10
	        restartPolicy: Always
	  ```
	- **Explanation of Key Sections**
		- **`metadata`**:
			- Defines the Deployment name and labels used for Pod selection.
		- **`spec.replicas`**:
			- Defines the number of Pods the Deployment should maintain.
		- **`spec.selector.matchLabels`**:
			- Ensures the Deployment only manages Pods with matching labels.
		- **`template.spec.containers`**:
			- Defines the container image, ports, and resource limits.
		- **`resources.requests & limits`**:
			- Specifies resource allocations to prevent overuse.
		- **`livenessProbe & readinessProbe`**:
			- Ensures Kubernetes monitors container health and restarts failed instances.
	- **Apply the Deployment Manifest**
	  ```bash
	  kubectl apply -f deployment.yaml
	  ```
	- **View and Edit the Running Deployment**
	  ```bash
	  kubectl get deployments
	  kubectl edit deployment my-deployment
	  ```