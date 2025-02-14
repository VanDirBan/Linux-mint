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
- **Horizontal Pod Autoscaler (HPA)**
	- **Definition**:
		- The Horizontal Pod Autoscaler (HPA) automatically scales the number of Pods in a Deployment based on CPU or memory usage.
		- Ensures efficient resource utilization and optimal application performance.
	- **Key Features**:
		- Monitors CPU/Memory usage using the Metrics API.
		- Adjusts the number of Pods dynamically.
		- Works with Deployments, ReplicaSets, and StatefulSets.
	- **Enable Metrics Server (Required for HPA)**
		- Check if Metrics Server is installed:
		  ```bash
		  kubectl get deployment metrics-server -n kube-system
		  ```
		- If missing, install it:
		  ```bash
		  kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
		  ```
	- **Creating an HPA for a Deployment**
		- Example command to create an HPA:
		  ```bash
		  kubectl autoscale deployment my-deployment --cpu-percent=50 --min=2 --max=10
		  ```
		- Example YAML manifest:
		  ```yaml
		  apiVersion: autoscaling/v2
		  kind: HorizontalPodAutoscaler
		  metadata:
		    name: my-deployment-hpa
		  spec:
		    scaleTargetRef:
		      apiVersion: apps/v1
		      kind: Deployment
		      name: my-deployment
		    minReplicas: 2
		    maxReplicas: 10
		    metrics:
		      - type: Resource
		        resource:
		          name: cpu
		          target:
		            type: Utilization
		            averageUtilization: 50
		  ```
		- Apply the configuration:
		  ```bash
		  kubectl apply -f hpa.yaml
		  ```
	- **View HPA Status**
	  ```bash
	  kubectl get hpa
	  ```
	- **Delete HPA**
	  ```bash
	  kubectl delete hpa my-deployment-hpa
	  ```
	  
	  ---
	- **Best Practices**
		- Always use **Deployments** instead of standalone Pods for high availability.
		- Assign **resource limits** (`cpu`, `memory`) to avoid excessive resource usage.
		- Use **readiness and liveness probes** to monitor application health.
		- Manage configurations with **ConfigMaps and Secrets**.
		- Implement **auto-scaling** (`HorizontalPodAutoscaler`) for efficient resource utilization.
		- Always set resource requests and limits.
		- Use `livenessProbe` and `readinessProbe` for better application stability.
		- Store environment-specific configurations in **ConfigMaps** and **Secrets**.
		- Use **Rolling Updates** for smooth deployments.