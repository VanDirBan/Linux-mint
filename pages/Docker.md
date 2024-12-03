- **Core Components of Docker**
	- **Containers**:
		- **Definition**:
			- Lightweight, standalone, and executable units of software that package an application and its dependencies.
			- Containers ensure consistency across multiple environments (development, testing, production).
		- **Key Features**:
			- Isolated environment for applications.
			- Uses the host OS kernel (unlike virtual machines).
			- Stateless by default (persistent storage requires volumes or bind mounts).
		- **Commands**:
			- List running containers:
			  ```bash
			  docker ps
			  ```
			- List all containers (including stopped ones):
			  ```bash
			  docker ps -a
			  ```
			- Start a container:
			  ```bash
			  docker start <container_name_or_id>
			  ```
			- Stop a container:
			  ```bash
			  docker stop <container_name_or_id>
			  ```
			- Remove a container:
			  ```bash
			  docker rm <container_name_or_id>
			  ```
			- Run a container from an image:
			  ```bash
			  docker run -d --name <container_name> <image_name>
			  ```
	- **Images**:
		- **Definition**:
			- Immutable templates used to create containers.
			- Built from a **Dockerfile**, which contains instructions to set up the environment and application.
		- **Key Features**:
			- Layers: Each instruction in a Dockerfile creates a layer, making images lightweight and efficient.
			- Reusable across multiple containers.
		- **Commands**:
			- List all images:
			  ```bash
			  docker images
			  ```
			- Pull an image from a repository:
			  ```bash
			  docker pull <image_name>
			  ```
			- Build an image from a Dockerfile:
			  ```bash
			  docker build -t <image_name> <path_to_dockerfile>
			  ```
			- Remove an image:
			  ```bash
			  docker rmi <image_name_or_id>
			  ```
	- **Repositories**:
		- **Definition**:
			- Locations (local or remote) where Docker images are stored and shared.
			- Images can be pulled from or pushed to repositories.
		- **Types**:
			- **Public Repositories**:
				- Accessible to anyone (e.g., Docker Hub).
			- **Private Repositories**:
				- Restricted access; requires authentication.
		- **Key Repositories**:
			- **Docker Hub** (default registry): `https://hub.docker.com`
			- Other registries: AWS ECR, Google Container Registry, GitHub Container Registry.
		- **Commands**:
			- Login to a repository:
			  ```bash
			  docker login
			  ```
			- Push an image to a repository:
			  ```bash
			  docker push <repository>/<image_name>:<tag>
			  ```
			- Pull an image from a repository:
			  ```bash
			  docker pull <repository>/<image_name>:<tag>
			  ```