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