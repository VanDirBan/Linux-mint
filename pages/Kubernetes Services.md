- **Introduction to Services**
	- **Definition**:
		- A Kubernetes Service is an abstraction that defines a stable network endpoint to access a set of Pods.
		- Services ensure communication between application components, even if Pods are dynamically created or destroyed.
	- **Key Features**:
		- Provides a single access point (ClusterIP, NodePort, LoadBalancer, or ExternalName).
		- Uses **labels and selectors** to route traffic to the correct Pods.
		- Can expose services inside the cluster or to the external world.
- **Types of Services**
	- **ClusterIP (Default)**
		- Exposes the service inside the cluster only.
		- Example command:
		  ```bash
		  kubectl expose deployment my-app --type=ClusterIP --port=80
		  ```
		- Example YAML:
		  ```yaml
		  apiVersion: v1
		  kind: Service
		  metadata:
		    name: my-service
		  spec:
		    selector:
		      app: my-app
		    ports:
		      - protocol: TCP
		        port: 80
		        targetPort: 8080
		  ```
	- **NodePort**
		- Exposes the service on a static port across all nodes.
		- Accessible via `<NodeIP>:<NodePort>`.
		- Example command:
		  ```bash
		  kubectl expose deployment my-app --type=NodePort --port=80
		  ```
		- Example YAML:
		  ```yaml
		  apiVersion: v1
		  kind: Service
		  metadata:
		    name: my-service
		  spec:
		    type: NodePort
		    selector:
		      app: my-app
		    ports:
		      - protocol: TCP
		        port: 80
		        targetPort: 8080
		        nodePort: 30080
		  ```
	- **LoadBalancer**
		- Exposes the service externally using a cloud provider's load balancer.
		- Works in **AWS, GCP, Azure**, etc.
		- Example command:
		  ```bash
		  kubectl expose deployment my-app --type=LoadBalancer --port=80
		  ```
		- Example YAML:
		  ```yaml
		  apiVersion: v1
		  kind: Service
		  metadata:
		    name: my-service
		  spec:
		    type: LoadBalancer
		    selector:
		      app: my-app
		    ports:
		      - protocol: TCP
		        port: 80
		        targetPort: 8080
		  ```
	- **ExternalName**
		- Maps a service to an external domain (e.g., `example.com`).
		- Example YAML:
		  ```yaml
		  apiVersion: v1
		  kind: Service
		  metadata:
		    name: external-service
		  spec:
		    type: ExternalName
		    externalName: example.com
		  ```
- **Checking and Managing Services**
	- **List all services**:
	  ```bash
	  kubectl get services
	  ```
	- **Describe a specific service**:
	  ```bash
	  kubectl describe service my-service
	  ```
	- **Delete a service**:
	  ```bash
	  kubectl delete service my-service
	  ```
- **Testing a Service**
	- **Accessing a ClusterIP service from within the cluster**:
	  ```bash
	  kubectl run curl-test --image=radial/busyboxplus:curl -i --tty -- /bin/sh
	  curl http://my-service:80
	  ```
	- **Accessing a NodePort service externally**:
	  ```bash
	  curl http://<NodeIP>:30080
	  ```
	- **Checking LoadBalancer external IP**:
	  ```bash
	  kubectl get services my-service
	  ```
- **Best Practices**
	- Use **ClusterIP** for internal communication between services.
	- Use **NodePort** only when direct node access is needed.
	- Use **LoadBalancer** in cloud environments for external access.
	- Use **labels and selectors** properly to connect services to the correct Pods.
	- Secure services with **NetworkPolicies** and proper firewall rules.