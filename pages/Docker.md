- **Key Components**
	- **Images**:
		- Definition: Read-only templates that contain the application and its dependencies.
		- Usage: Serve as a blueprint for creating containers.
		- Commands:
			- List images:
			  ```bash
			  docker images
			  ```
			- Pull an image:
			  ```bash
			  docker pull <image_name>
			  ```
	- **Containers**:
		- Definition: Lightweight, isolated runtime environments created from Docker images.
		- Usage: Running instances of Docker images.
		- Commands:
			- List running containers:
			  ```bash
			  docker ps
			  ```
			- Start a container:
			  ```bash
			  docker start <container_id>
			  ```
			- Stop a container:
			  ```bash
			  docker stop <container_id>
			  ```
	- **Repositories**:
		- Definition: Storage for Docker images, often hosted on Docker Hub or private registries.
		- Commands:
			- Push an image to a repository:
			  ```bash
			  docker push <repository>/<image_name>:<tag>
			  ```
			- Pull an image from a repository:
			  ```bash
			  docker pull <repository>/<image_name>:<tag>
			  ```
- **Basic Commands**
	- **System Info**:
		- Check Docker version:
		  ```bash
		  docker --version
		  ```
		- View Docker system information:
		  ```bash
		  docker info
		  ```
	- **Containers**:
		- Run a new container:
		  ```bash
		  docker run <image_name>
		  ```
			- Options:
				- `-d`: Run container in detached mode.
				- `--name`: Assign a name to the container.
				- `-it`: Run container interactively.
		- List running containers:
		  ```bash
		  docker ps
		  ```
		- List all containers:
		  ```bash
		  docker ps -a
		  ```
		- Remove a container:
		  ```bash
		  docker rm <container_id>
		  ```
	- **Images**:
		- Build an image from a Dockerfile:
		  ```bash
		  docker build -t <image_name> .
		  ```
		- Remove an image:
		  ```bash
		  docker rmi <image_name>
		  ```
- **Networking**
	- **Publishing Ports**:
		- Map container ports to host ports using the `-p` flag:
		  ```bash
		  docker run -d -p <host_port>:<container_port> <image_name>
		  ```
			- Example:
			  ```bash
			  docker run -d -p 8080:80 nginx
			  ```
			- Explanation:
				- `8080`: Port on the host machine.
				- `80`: Port inside the container.
	- **Volumes**
	- **Definition**:
		- Volumes are used to persist data generated or used by Docker containers.
	- **Mounting Volumes**:
		- Use the `-v` flag to mount a volume:
		  ```bash
		  docker run -d -v <host_path>:<container_path> <image_name>
		  ```
			- Example:
			  ```bash
			  docker run -d -v /data:/var/lib/mysql mysql
			  ```
			- Explanation:
				- `/data`: Host directory to store data.
				- `/var/lib/mysql`: Directory inside the container where MySQL stores its data.
	- **Listing Volumes**:
		- List all volumes:
		  ```bash
		  docker volume ls
		  ```
	- **Creating a Volume**:
		- Create a named volume:
		  ```bash
		  docker volume create <volume_name>
		  ```
		- Use the volume in a container:
		  ```bash
		  docker run -d -v <volume_name>:<container_path> <image_name>
		  ```