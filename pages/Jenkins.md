- **Jenkins**
	- **Definition**:
		- An open-source automation server for building, testing, and deploying applications.
		- Supports continuous integration and continuous delivery workflows.
	- **Installation**:
		- **Prerequisites**:
			- Install OpenJDK 17 or 21:
			  ```bash
			  sudo apt update
			  sudo apt install openjdk-17-jdk
			  ```
		- **Install Jenkins**:
			- Add the Jenkins repository and key:
			  ```bash
			  curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
			  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
			  echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
			  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
			  /etc/apt/sources.list.d/jenkins.list > /dev/null
			  ```
			- Install Jenkins:
			  ```bash
			  sudo apt update
			  sudo apt install jenkins
			  ```
		- **Start and Enable Jenkins**:
		  ```bash
		  sudo systemctl start jenkins
		  sudo systemctl enable jenkins
		  ```
		- **Access Jenkins**:
			- Open `http://<your_server_ip>:8080` in your web browser.
			- Unlock Jenkins:
				- Retrieve the initial admin password:
				  ```bash
				  sudo cat /var/lib/jenkins/secrets/initialAdminPassword
				  ```
			- Complete the setup wizard.
	- **Basic Features**:
		- **Plugins**:
			- Jenkins has a rich plugin ecosystem to extend its capabilities.
			- Examples: Git, Docker, Pipeline.
		- **Pipelines**:
			- Define CI/CD workflows as code using Jenkinsfile.
			- Two types: Declarative and Scripted.
		- **Web Interface**:
			- Intuitive UI for managing jobs, builds, and logs.
		- **Job Management**:
			- Jobs define what Jenkins will execute (e.g., build, test, deploy).
	- **Key Concepts**:
		- **Master-Agent Architecture**:
			- Master: Orchestrates builds and provides the web interface.
			- Agents: Execute builds.
		- **Pipeline**:
			- Automated sequence of stages for CI/CD.
			- [See CI/CD Section](#CI/CD).
	- **Basic Commands**:
		- Restart Jenkins:
		  ```bash
		  sudo systemctl restart jenkins
		  ```
		- View logs:
		  ```bash
		  sudo journalctl -u jenkins
		  ```