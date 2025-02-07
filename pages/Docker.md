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
				- `/var/lib/mysql`: Directory inside the container where [[MySQL]] stores its data.
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
- **Building Custom Images with Dockerfile**
	- **What is a Dockerfile?**
		- A Dockerfile is a script containing a series of instructions to assemble a Docker image.
		- It defines the base image, dependencies, configuration, and commands to set up the environment.
	- **Basic Structure of a Dockerfile**:
		- **FROM**: Specifies the base image.
		- **RUN**: Executes commands to install dependencies or configure the environment.
		- **COPY** or **ADD**: Copies files from the host to the image.
		- **CMD** or **ENTRYPOINT**: Defines the command to run when the container starts.
		- **EXPOSE**: Declares the port the container will listen on.
	- **Example Dockerfile**:
	  ```Dockerfile
	  # Use an official Node.js image as the base
	  FROM node:14
	  
	  # Set the working directory inside the container
	  WORKDIR /app
	  
	  # Copy package.json and package-lock.json to the container
	  COPY package*.json ./
	  
	  # Install dependencies
	  RUN npm install
	  
	  # Copy the application code
	  COPY . .
	  
	  # Expose the application port
	  EXPOSE 3000
	  
	  # Start the application
	  CMD ["npm", "start"]
	  ```
	- **Building an Image**:
		- **Syntax**:
		  ```bash
		  docker build -t <image_name>:<tag> <path_to_dockerfile>
		  ```
		- **Example**:
		  ```bash
		  docker build -t my-node-app:1.0 .
		  ```
			- `-t`: Assigns a name and optional tag to the image.
			- `.`: Indicates the context directory containing the Dockerfile.
	- **Best Practices**:
		- Use a minimal base image (e.g., `alpine`) to reduce image size:
		  ```Dockerfile
		  FROM alpine
		  ```
		- Combine multiple `RUN` instructions to minimize the number of layers:
		  ```Dockerfile
		  RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
		  ```
		- Use `.dockerignore` to exclude unnecessary files and directories from the build context (e.g., logs, `.git`, `node_modules`):
		  ```
		  node_modules
		  *.log
		  .git
		  ```
	- **Dockerfile Instructions**:
		- **FROM**: Sets the base image for the new image.
		- **WORKDIR**: Sets the working directory inside the container.
		- **RUN**: Executes commands during image build.
		- **COPY**: Copies files/directories from the host to the container.
		- **ADD**: Similar to COPY but supports remote URLs and extraction of archives.
		- **CMD**: Specifies the default command to run when the container starts.
		- **ENTRYPOINT**: Similar to CMD but allows arguments to be passed during container runtime.
		- **EXPOSE**: Documents the port on which the container listens (for informational purposes only).
		- **ENV**: Sets environment variables.
		  ```Dockerfile
		  ENV NODE_ENV=production
		  ```
		- **ARG**: Defines build-time variables.
		  ```Dockerfile
		  ARG APP_VERSION=1.0
		  ```
	- **Common Commands for Managing Images**:
		- List built images:
		  ```bash
		  docker images
		  ```
		- Remove an image:
		  ```bash
		  docker rmi <image_name>
		  ```
		- Run a container from the custom image:
		  ```bash
		  docker run -d -p 3000:3000 my-node-app:1.0
		  ```
	- **Debugging Builds**:
		- Use the `--no-cache` flag to force rebuild without using the cache:
		  ```bash
		  docker build --no-cache -t my-node-app:1.0 .
		  ```
		- Inspect intermediate build layers:
		  ```bash
		  docker history <image_id>
		  ```
- **Docker Compose**
	- **Definition**:
		- Docker Compose is a tool for defining and managing multi-container Docker applications.
		- Applications are defined using a `docker-compose.yml` file, which specifies the services, networks, and volumes needed for the application.
	- **Basic Workflow**:
	  1. Create a `docker-compose.yml` file.
	  2. Use `docker-compose` commands to manage services (e.g., start, stop, restart).
	- **Key Benefits**:
		- Simplifies running multi-container setups (e.g., web app + database).
		- Allows for easy scaling and orchestration of services.
	- **Basic Commands**:
		- Start services defined in `docker-compose.yml`:
		  ```bash
		  docker-compose up
		  ```
		- Start services in detached mode (background):
		  ```bash
		  docker-compose up -d
		  ```
		- Stop services:
		  ```bash
		  docker-compose down
		  ```
		- View running services:
		  ```bash
		  docker-compose ps
		  ```
		- Scale services:
		  ```bash
		  docker-compose up -d --scale <service>=<count>
		  ```
		- Restart services:
		  ```bash
		  docker-compose restart <service>
		  ```
	- **Example `docker-compose.yml` File**:
		- Example: Running a web application with a [[PostgreSQL]] database:
		  ```yaml
		  version: '3.8'
		  
		  services:
		    app:
		      image: my-web-app:1.0
		      ports:
		        - "8080:8080"
		      volumes:
		        - ./app:/usr/src/app
		      environment:
		        - DATABASE_URL=postgres://user:password@db:5432/mydb
		      depends_on:
		        - db
		  
		    db:
		      image: postgres:13
		      volumes:
		        - db-data:/var/lib/postgresql/data
		      environment:
		        POSTGRES_USER: user
		        POSTGRES_PASSWORD: password
		        POSTGRES_DB: mydb
		  
		  volumes:
		    db-data:
		  ```
	- **Explanation of Example**:
		- **Version**: Specifies the Docker Compose file format version.
		- **Services**:
			- **app**: Defines the web application service.
				- **image**: The Docker image for the service.
				- **ports**: Maps port 8080 on the host to port 8080 in the container.
				- **volumes**: Mounts the local `./app` directory into the container.
				- **environment**: Sets environment variables for the service.
				- **depends_on**: Ensures the database service starts before the app.
			- **db**: Defines the PostgreSQL database service.
				- **volumes**: Mounts a named volume for persisting database data.
				- **environment**: Sets PostgreSQL-specific environment variables.
		- **Volumes**:
			- `db-data`: A named volume for persistent data storage.
	- **Best Practices**:
		- Use `.env` files to store sensitive environment variables and load them into your `docker-compose.yml`:
		  ```yaml
		  environment:
		    - POSTGRES_USER=${DB_USER}
		    - POSTGRES_PASSWORD=${DB_PASSWORD}
		  ```
			- `.env` file example:
			  ```
			  DB_USER=user
			  DB_PASSWORD=securepassword
			  ```
			- Load with:
			  ```bash
			  docker-compose --env-file .env up
			  ```
		- Always specify versions explicitly to avoid unexpected behavior.
		- Use named volumes to persist important data (e.g., databases).
	- **Scaling Services**:
		- To scale services, add the `--scale` flag:
		  ```bash
		  docker-compose up -d --scale app=3
		  ```
			- This example creates three instances of the `app` service.
	- **Logs and Debugging**:
		- View logs for all services:
		  ```bash
		  docker-compose logs
		  ```
		- View logs for a specific service:
		  ```bash
		  docker-compose logs <service>
		  ```
		- Follow logs in real-time:
		  ```bash
		  docker-compose logs -f
		  ```
	- **Removing Containers, Networks, and Volumes**:
		- Stop and remove all containers, networks, and volumes:
		  ```bash
		  docker-compose down --volumes
		  ```