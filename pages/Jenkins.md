- **Jenkins**
	- **Definition**:
		- An open-source automation server for building, testing, and deploying applications.
		- Supports continuous integration and continuous delivery workflows.
	- **Installation**:
		- **Prerequisites**:
			- Install #OpenJDK 17 or 21:
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
			- Examples: [[Git]], [[Docker]], Pipeline.
		- **Pipelines**:
			- Define [[CI/CD]] workflows as code using Jenkinsfile.
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
			- [See [[CI/CD]] Section].
	- **Basic Commands**:
		- Restart Jenkins:
		  ```bash
		  sudo systemctl restart jenkins
		  ```
		- View logs:
		  ```bash
		  sudo journalctl -u jenkins
		  ```
- **Server Configuration**
	- **Initial Setup**:
		- After installation, Jenkins requires an initial admin password to unlock:
		  ```bash
		  sudo cat /var/lib/jenkins/secrets/initialAdminPassword
		  ```
			- Enter the password in the Jenkins web interface.
		- Complete the setup wizard:
			- **Install Suggested Plugins** or choose specific plugins to install.
			- Create an admin user.
		- Access Jenkins via:
		  ```
		  http://<your_server_ip>:8080
		  ```
	- **Global Configuration**:
		- Access global configuration:
			- Go to **Manage Jenkins** > **Configure System**.
		- Key settings:
			- **Jenkins URL**: Ensure the correct URL is configured (important for webhook triggers).
			- **Environment Variables**: Add global environment variables for all builds.
				- Example:
					- Variable: `JAVA_HOME`
					- Value: `/usr/lib/jvm/java-17-openjdk-amd64`
- **Plugins Management**
	- **Plugins Overview**:
		- Plugins extend Jenkins' functionality (e.g., Git, Docker, Slack).
	- **Install Plugins**:
		- Navigate to **Manage Jenkins** > **Plugin Manager**.
		- Tabs:
			- **Available**: Browse and install plugins.
			- **Installed**: View installed plugins and update them.
			- **Updates**: Check for updates for installed plugins.
			- **Advanced**: Upload custom `.hpi` plugin files.
	- **Recommended Plugins**:
		- **Git Plugin**: For integrating with Git repositories.
		- **Pipeline Plugin**: For creating CI/CD pipelines.
		- **Blue Ocean**: Provides an enhanced UI for pipelines.
		- **Slack Notifications**: Sends build notifications to Slack channels.
		- **Credentials Binding**: Securely manage credentials for builds.
		- **Docker Pipeline**: Supports Docker-based pipelines.
	- **Installing Plugins via CLI**:
		- Use the Jenkins CLI to install plugins:
		  ```bash
		  java -jar jenkins-cli.jar -s http://localhost:8080/ install-plugin <plugin_name>
		  ```
		- Example:
		  ```bash
		  java -jar jenkins-cli.jar -s http://localhost:8080/ install-plugin git
		  ```
- **Global Tools Configuration**
	- **Definition**:
		- Global tools are external software (e.g., JDK, Maven, Git) required for Jenkins jobs.
	- **Configuration Steps**:
		- Go to **Manage Jenkins** > **Global Tool Configuration**.
		- Common tools:
			- **JDK**:
				- Ensure Java is installed on the system (e.g., OpenJDK 17 or 21).
				- Add a JDK installation:
					- **Name**: `JDK17`
					- **Path to Java**: `/usr/lib/jvm/java-17-openjdk-amd64`
			- **Git**:
				- Ensure Git is installed on the system:
				  ```bash
				  sudo apt install git
				  ```
				- Add Git installation:
					- **Name**: `Default Git`
					- Jenkins will automatically detect the path.
			- **Maven**:
				- Install Maven:
				  ```bash
				  sudo apt install maven
				  ```
				- Add Maven installation:
					- **Name**: `Maven3`
					- Path: `/usr/share/maven`
			- **NodeJS (Optional)**:
				- Install the NodeJS plugin.
				- Configure NodeJS under Global Tool Configuration.
	- **Best Practices**:
		- Use descriptive names for tools to avoid confusion.
		- Verify paths after configuration to ensure tools are properly installed.
		- Use environment variables (e.g., `$JAVA_HOME`) for portability.
- **Security and Access Control**
	- **Configure Credentials**:
		- Go to **Manage Jenkins** > **Manage Credentials**.
		- Types of credentials:
			- **Username and Password**: For authenticating with external services.
			- **SSH Keys**: For accessing Git repositories or servers.
			- **API Tokens**: For integration with third-party tools.
		- Add credentials:
			- Scope: Global (available to all jobs) or specific (restricted to specific jobs).
			- ID: Unique identifier for referencing credentials.
	- **Restrict Access**:
		- Configure access control under **Manage Jenkins** > **Configure Global Security**.
		- Authentication options:
			- **Matrix-based Security**: Fine-grained access control.
			- **Project-based Authorization**: Access control per job.
- **Connecting Slave Agents (via SSH)**
	- **Definition**:
		- Slave agents (now called "nodes") are machines that Jenkins uses to run builds.
		- Using SSH is a secure and efficient way to connect slave agents.
	- **Steps to Configure an SSH Slave**:
	  1. **Prepare the Slave Machine**:
		- Install Java (compatible version for Jenkins):
		  ```bash
		  sudo apt update
		  sudo apt install openjdk-17-jdk
		  ```
		- Create a Jenkins user on the slave machine:
		  ```bash
		  sudo adduser jenkins
		  sudo usermod -aG sudo jenkins
		  ```
		- Enable SSH access for the Jenkins user.
	- 2. **Add the Node in Jenkins**:
		- Go to **Manage Jenkins** > **Manage Nodes and Clouds** > **New Node**.
		- Enter the node name, select **Permanent Agent**, and configure:
			- **Remote Root Directory**: Directory on the slave machine where Jenkins files will be stored (e.g., `/home/jenkins`).
			- **Usage**: Choose "Use this node as much as possible" or other options based on your needs.
			- **Launch Method**: Select **Launch agent via SSH**.
	- 3. **Configure SSH Credentials**:
		- In the node configuration, under **Credentials**, click **Add** to create SSH credentials:
			- **Username**: `jenkins` (or the user created on the slave machine).
			- **Private Key**: Upload the private key for SSH access.
		- Test the connection to ensure proper setup.
	- 4. **Verify the Connection**:
		- Once saved, Jenkins will attempt to connect to the slave.
		- Check the status of the node in **Manage Nodes**.
- **Working with Credentials**
	- **Definition**:
		- Credentials in Jenkins are used to securely store and manage sensitive data (e.g., SSH keys, passwords, API tokens).
	- **Types of Credentials**:
		- **Username and Password**: For basic authentication.
		- **SSH Key**: For connecting to remote servers or repositories.
		- **Secret Text**: For API tokens or other sensitive strings.
		- **Certificate**: For authentication using certificates.
	- **Adding Credentials**:
		- Navigate to **Manage Jenkins** > **Manage Credentials**.
		- Select a credentials store (e.g., **Global** or **Folder-Specific**).
		- Click **Add Credentials** and fill in the details:
			- **Scope**:
				- **Global**: Available for all jobs and nodes.
				- **System**: Restricted to Jenkins system use only.
				- **Folder/Job**: Available only within a specific folder or job.
			- **ID**: Unique identifier for referencing the credentials in pipelines.
		- Example:
			- Add an SSH private key:
				- **Kind**: SSH Username with private key.
				- **Username**: `jenkins`.
				- **Private Key**: Paste or upload the private key file.
	- **Using Credentials in Jobs**:
		- Credentials can be referenced in pipelines or freestyle jobs:
		  ```groovy
		  pipeline {
		    agent any
		    environment {
		      GIT_CREDENTIALS = credentials('git-ssh-credentials')
		    }
		    stages {
		      stage('Checkout') {
		        steps {
		          git url: 'git@github.com:user/repo.git',
		              credentialsId: GIT_CREDENTIALS
		        }
		      }
		    }
		  }
		  ```
- **Access Control and Role-Based Access**
	- **Global Security Configuration**:
		- Navigate to **Manage Jenkins** > **Configure Global Security**.
		- Enable **Authorization** and choose a strategy:
			- **Matrix-based Security**:
				- Allows granular permissions for users/groups.
				- Common roles:
					- **Admin**: Full control over Jenkins.
					- **Developer**: Can create and manage jobs.
					- **Viewer**: Can only view job configurations and results.
			- **Project-based Authorization**:
				- Configure permissions at the job or folder level.
	- **Setting Up Roles with a Plugin**:
		- Install the **Role-Based Authorization Strategy** plugin.
		- Go to **Manage Jenkins** > **Manage and Assign Roles**:
		  1. **Create Roles**:
			- Define roles (e.g., admin, developer, viewer) and assign permissions.
			  2. **Assign Roles**:
			- Assign roles to users or groups at the global or folder level.
		- Example Role Permissions:
			- **Admin**:
				- Full permissions.
			- **Developer**:
				- Build and configure jobs.
				- No access to global configuration.
			- **Viewer**:
				- Read-only access to jobs and builds.
	- **Best Practices**:
		- Grant minimal permissions required for users to perform their tasks.
		- Regularly review and audit permissions.