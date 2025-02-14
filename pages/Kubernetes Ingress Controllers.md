- **Introduction to Ingress Controllers**
	- **Definition**:
		- An Ingress Controller is a Kubernetes component that manages external HTTP/S access to services inside a cluster.
		- Acts as a reverse proxy, routing traffic based on rules defined in an Ingress resource.
	- **Key Features**:
		- **Path-based Routing**: Directs requests to different services based on URL paths.
		- **Host-based Routing**: Routes requests based on domain names.
		- **TLS Termination**: Handles SSL encryption and decryption.
		- **Load Balancing**: Distributes traffic across multiple replicas.
- **How Ingress Works**
	- Requires both an **Ingress Resource** (YAML definition) and an **Ingress Controller** (the actual implementation).
	- Popular Ingress Controllers:
		- **NGINX Ingress Controller** (default in most clusters).
		- **Traefik** (lightweight, dynamic configuration).
		- **HAProxy** (high-performance, enterprise use).
		- **AWS ALB Ingress Controller** (for AWS cloud environments).
	- Default Kubernetes deployments do not include an Ingress Controller; it must be installed separately.
	- [Kubernetes Ingress Controllers](https://docs.google.com/spreadsheets/d/191WWNpjJ2za6-nbG4ZoUMXMpUK8KlCIosvQB0f-oq3k/edit?gid=907731238#gid=907731238)
- **Installing NGINX Ingress Controller (Common Approach)**
	- **Using Helm**:
	  ```bash
	  helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
	  helm repo update
	  helm install my-ingress ingress-nginx/ingress-nginx
	  ```
	- **Verify Installation**:
	  ```bash
	  kubectl get pods -n default
	  kubectl get svc -n default
	  ```
- **Creating an Ingress Resource**
	- Example of an Ingress YAML file:
	  ```yaml
	  apiVersion: networking.k8s.io/v1
	  kind: Ingress
	  metadata:
	    name: my-ingress
	    annotations:
	      nginx.ingress.kubernetes.io/rewrite-target: /
	  spec:
	    rules:
	      - host: myapp.local
	        http:
	          paths:
	            - path: /app1
	              pathType: Prefix
	              backend:
	                service:
	                  name: service-app1
	                  port:
	                    number: 80
	            - path: /app2
	              pathType: Prefix
	              backend:
	                service:
	                  name: service-app2
	                  port:
	                    number: 80
	  ```
	- **Apply the Ingress Resource**:
	  ```bash
	  kubectl apply -f ingress.yaml
	  ```
	- **Check Ingress Rules**:
	  ```bash
	  kubectl get ingress
	  kubectl describe ingress my-ingress
	  ```
- **Testing Ingress**
	- If using Minikube, enable Ingress addon:
	  ```bash
	  minikube addons enable ingress
	  ```
	- Get the Ingress Controller's IP:
	  ```bash
	  kubectl get svc -n default
	  ```
	- Add an entry to `/etc/hosts` for local resolution:
	  ```plaintext
	  192.168.49.2 myapp.local
	  ```
	- Test Ingress with `curl`:
	  ```bash
	  curl http://myapp.local/app1
	  ```
- **TLS with Ingress**
	- To enable HTTPS traffic, create a TLS secret:
	  ```bash
	  kubectl create secret tls my-tls-secret --cert=cert.pem --key=key.pem
	  ```
	- Modify the Ingress resource to use TLS:
	  ```yaml
	  spec:
	    tls:
	      - hosts:
	          - myapp.local
	        secretName: my-tls-secret
	  ```
- **Deleting Ingress Resources**
	- Remove an Ingress rule:
	  ```bash
	  kubectl delete ingress my-ingress
	  ```
	- Uninstall the Ingress Controller:
	  ```bash
	  helm uninstall my-ingress
	  ```
- **Best Practices**
	- Use annotations to configure advanced routing options (e.g., rate limiting, request rewrites).
	- Always use **TLS encryption** for production traffic.
	- Monitor Ingress logs for troubleshooting using:
	  ```bash
	  kubectl logs -l app.kubernetes.io/name=ingress-nginx -n default
	  ```
	- Use a **dedicated namespace** for Ingress controllers to separate concerns.