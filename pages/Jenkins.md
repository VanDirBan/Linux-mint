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
- **Job Types in Jenkins**
	- **Freestyle Project**:
		- **Definition**:
			- The simplest type of Jenkins job for building, testing, and deploying applications.
		- **Use Case**:
			- Suitable for simple projects without complex workflows.
		- **Features**:
			- Integration with version control (Git, SVN).
			- Build triggers (e.g., scheduled, webhook, manual).
			- Build steps (e.g., shell commands, build tools like Maven).
		- **Configuration**:
			- Go to **New Item** > **Freestyle project**.
			- Configure source code management, build steps, and post-build actions.
	- **Pipeline**:
		- **Definition**:
			- Automates complex CI/CD workflows as code using a `Jenkinsfile`.
		- **Benefits**:
			- Pipeline as code: Version-controlled and reusable.
			- Resilience: Supports resuming builds after a crash.
			- Visualization: Displays stages and steps clearly in the Jenkins UI.
		- **Types of Pipelines**:
			- **Declarative Pipeline**:
				- High-level, structured syntax.
				- Easy to read and maintain.
				- Example:
				  ```groovy
				  pipeline {
				      agent any
				      stages {
				          stage('Build') {
				              steps {
				                  echo "Building application..."
				                  sh 'mvn clean package'
				              }
				          }
				          stage('Test') {
				              steps {
				                  echo "Running tests..."
				                  sh 'mvn test'
				              }
				          }
				          stage('Deploy') {
				              steps {
				                  echo "Deploying to production..."
				              }
				          }
				      }
				  }
				  ```
			- **Scripted Pipeline**:
				- Flexible, uses Groovy scripts.
				- Offers full control for advanced use cases.
				- Example:
				  ```groovy
				  node {
				      stage('Build') {
				          sh 'mvn clean package'
				      }
				      stage('Test') {
				          sh 'mvn test'
				      }
				      stage('Deploy') {
				          sh 'scp target/*.jar user@server:/app/'
				      }
				  }
				  ```
	- **Multibranch Pipeline**:
		- **Definition**:
			- Automatically creates pipelines for branches and pull requests in a repository.
		- **Use Case**:
			- Projects with multiple branches (e.g., feature branches, main branch).
		- **Configuration**:
			- Go to **New Item** > **Multibranch Pipeline**.
			- Link to a Git repository and configure the branch discovery strategy.
	- **Folder**:
		- **Definition**:
			- Groups multiple jobs or pipelines into a folder for better organization.
		- **Use Case**:
			- Organize jobs by project, team, or environment.
	- **Other Job Types**:
		- **Maven Project**: A job type specifically designed for Maven builds.
		- **External Job**: Executes jobs outside of Jenkins, useful for monitoring processes.
		- **Pipeline Script from SCM**:
			- Fetches `Jenkinsfile` from version control, making it easy to integrate with Git.
- **Approaches to Writing Pipelines**
	- **Declarative vs Scripted**:
		- **Declarative**:
			- Structured and easier to read.
			- Recommended for most use cases.
		- **Scripted**:
			- More flexible for advanced workflows.
			- Requires deeper understanding of Groovy syntax.
	- **Best Practices**:
		- **Store Jenkinsfile in Version Control**:
			- Keep the `Jenkinsfile` alongside the project code for better traceability.
		- **Parameterize Builds**:
			- Use input parameters to customize builds dynamically.
			  ```groovy
			  parameters {
			    string(name: 'ENVIRONMENT', defaultValue: 'staging', description: 'Target environment')
			  }
			  ```
		- **Use Shared Libraries**:
			- Store reusable pipeline scripts in a shared library.
			- Example import:
			  ```groovy
			  @Library('my-shared-library') _
			  ```
		- **Split Pipelines into Stages**:
			- Use clear stage names to visualize the CI/CD process.
		- **Fail Fast**:
			- Run tests and validations early to catch errors as soon as possible.
	- **Pipeline Examples**:
		- **Pipeline with Environment Variables**:
		  ```groovy
		  pipeline {
		      agent any
		      environment {
		          APP_ENV = 'production'
		          JAVA_HOME = '/usr/lib/jvm/java-17-openjdk'
		      }
		      stages {
		          stage('Build') {
		              steps {
		                  echo "Building in ${APP_ENV} environment..."
		              }
		          }
		      }
		  }
		  ```
		- **Parallel Stages**:
			- Run multiple tasks simultaneously to save time.
			  ```groovy
			  pipeline {
			    agent any
			    stages {
			        stage('Parallel Tests') {
			            parallel {
			                stage('Unit Tests') {
			                    steps {
			                        echo "Running unit tests..."
			                    }
			                }
			                stage('Integration Tests') {
			                    steps {
			                        echo "Running integration tests..."
			                    }
			                }
			            }
			        }
			    }
			  }
			  ```
- **Declarative Pipeline**
	- **Definition**:
		- Declarative Pipeline is a high-level syntax for defining Jenkins pipelines as code.
		- It is easier to read, structured, and requires minimal Groovy knowledge.
	- **Basic Structure**:
	  ```groovy
	  pipeline {
	      agent any
	      stages {
	          stage('Stage 1') {
	              steps {
	                  echo 'Executing Stage 1...'
	              }
	          }
	          stage('Stage 2') {
	              steps {
	                  echo 'Executing Stage 2...'
	              }
	          }
	      }
	  }
	  ```
	- **Key Elements**:
		- **pipeline**: The root block that contains the entire pipeline definition.
		- **agent**: Specifies where the pipeline or a stage will run.
			- **any**: Use any available node.
			- **none**: No global agent; each stage must define its agent.
			- Example:
			  ```groovy
			  agent { label 'docker' }
			  ```
		- **stages**: Contains multiple `stage` blocks that define steps.
		- **stage**: Represents a single phase of the pipeline.
		- **steps**: A sequence of actions to execute within a stage.
		- **post**: Defines actions that run after the pipeline or a stage completes.
- **Creating a Basic Declarative Pipeline**
	- **Example Pipeline**:
		- Build, Test, and Deploy a simple application:
		  ```groovy
		  pipeline {
		      agent any
		  
		      environment {
		          APP_ENV = 'development'
		          BUILD_VERSION = '1.0'
		      }
		  
		      stages {
		          stage('Checkout Code') {
		              steps {
		                  echo "Checking out source code..."
		                  git url: 'https://github.com/user/repository.git', branch: 'main'
		              }
		          }
		  
		          stage('Build Application') {
		              steps {
		                  echo "Building application..."
		                  sh 'mvn clean package'
		              }
		          }
		  
		          stage('Run Tests') {
		              steps {
		                  echo "Running tests..."
		                  sh 'mvn test'
		              }
		          }
		  
		          stage('Deploy Application') {
		              steps {
		                  echo "Deploying application version ${BUILD_VERSION} to ${APP_ENV} environment..."
		                  sh 'scp target/app.jar user@server:/path/to/deploy'
		              }
		          }
		      }
		  
		      post {
		          always {
		              echo "Pipeline completed!"
		          }
		          success {
		              echo "Build and deployment succeeded!"
		          }
		          failure {
		              echo "Build or deployment failed."
		          }
		      }
		  }
		  ```
	- **Explanation**:
		- **agent any**: Uses any available Jenkins node to run the pipeline.
		- **environment**: Defines global environment variables for the pipeline.
		- **stages**:
			- **Checkout Code**: Fetches code from a Git repository.
			- **Build Application**: Runs the build command using Maven.
			- **Run Tests**: Executes tests to validate the application.
			- **Deploy Application**: Copies the built file to the deployment server.
		- **post**:
			- Actions to execute after the pipeline finishes:
				- **always**: Runs no matter the result.
				- **success**: Runs if the pipeline succeeds.
				- **failure**: Runs if the pipeline fails.
	- **Best Practices for Writing Declarative Pipelines**
	- **Keep Pipelines Simple**:
		- Start with basic stages like `Build`, `Test`, and `Deploy`.
	- **Use Environment Variables**:
		- Use `environment` to manage values like build version, API keys, etc.
	- **Version Control the Jenkinsfile**:
		- Always store the Jenkinsfile in the repository root.
	- **Fail Fast**:
		- Run tests early to catch errors quickly.
	- **Add Notifications**:
		- Use plugins like Slack or email to notify on pipeline success/failure.
	- **Debugging Pipelines**:
	- Use `echo` statements to log pipeline progress:
	  ```groovy
	  steps {
	      echo "Step is running..."
	  }
	  ```
	- Use `sh` commands to debug:
	  ```groovy
	  sh 'ls -la'
	  ```
	- Run pipeline stages incrementally:
		- Use the **"Replay"** feature in Jenkins to test minor pipeline changes.
- **Pipeline Variables and Scope**
	- **Definition**:
		- Variables in Jenkins pipelines can have different scopes: global, local, and environment-specific.
	- **Types of Variables**:
		- **Global Variables**:
			- Accessible throughout the pipeline.
			- Example:
			  ```groovy
			  def globalVar = 'I am global'
			  pipeline {
			      agent any
			      stages {
			          stage('Show Global Var') {
			              steps {
			                  echo "${globalVar}"
			              }
			          }
			      }
			  }
			  ```
		- **Local Variables**:
			- Defined inside stages or steps and not accessible outside.
			- Example:
			  ```groovy
			  pipeline {
			      agent any
			      stages {
			          stage('Local Var Example') {
			              steps {
			                  def localVar = 'I am local'
			                  echo "${localVar}"
			              }
			          }
			      }
			  }
			  ```
		- **Environment Variables**:
			- Predefined or custom variables available globally.
			- Example:
			  ```groovy
			  pipeline {
			      agent any
			      environment {
			          APP_ENV = 'production'
			      }
			      stages {
			          stage('Env Var Example') {
			              steps {
			                  echo "Environment is ${env.APP_ENV}"
			              }
			          }
			      }
			  }
			  ```
- **Error Handling with try/catch and catchError**
	- **try/catch**:
		- Used to handle errors and continue the pipeline execution.
		- Example:
		  ```groovy
		  pipeline {
		      agent any
		      stages {
		          stage('Error Handling') {
		              steps {
		                  script {
		                      try {
		                          sh 'exit 1' // Simulate failure
		                      } catch (Exception e) {
		                          echo "Error occurred: ${e}"
		                      }
		                  }
		              }
		          }
		      }
		  }
		  ```
	- **catchError**:
		- A Jenkins pipeline step to mark a stage as failed but continue execution.
		- Example:
		  ```groovy
		  pipeline {
		      agent any
		      stages {
		          stage('catchError Example') {
		              steps {
		                  catchError(buildResult: 'UNSTABLE') {
		                      sh 'exit 1' // Simulate failure
		                  }
		                  echo "This step continues even after failure."
		              }
		          }
		      }
		  }
		  ```
- **Lockable Resources (Concurrency Control)**
	- **Definition**:
		- Prevent concurrent execution of critical sections using the `lock` step.
		- Requires the **Lockable Resources Plugin**.
	- **Use Case**:
		- Useful when accessing shared resources (e.g., databases, test environments).
	- **Example**:
	  ```groovy
	  pipeline {
	      agent any
	      stages {
	          stage('Lock Resource') {
	              steps {
	                  lock(resource: 'shared-db') {
	                      echo "Accessing shared resource..."
	                      sh 'sleep 10'
	                  }
	              }
	          }
	      }
	  }
	  ```
	- **Best Practice**:
		- Define unique names for resources to avoid conflicts.
		- Keep locked steps minimal to prevent long wait times.
- **Using Groovy Code in Pipelines**
	- **Why Use Groovy?**:
		- Jenkins pipelines use Groovy for scripting.
		- Groovy allows conditional execution, loops, and reusable functions.
	- **Examples**:
		- **Loops**:
		  ```groovy
		  pipeline {
		      agent any
		      stages {
		          stage('For Loop') {
		              steps {
		                  script {
		                      for (int i = 1; i <= 3; i++) {
		                          echo "Iteration: ${i}"
		                      }
		                  }
		              }
		          }
		      }
		  }
		  ```
		- **Conditions**:
		  ```groovy
		  pipeline {
		      agent any
		      stages {
		          stage('Conditional Stage') {
		              steps {
		                  script {
		                      if (env.BRANCH_NAME == 'main') {
		                          echo "Deploying to production..."
		                      } else {
		                          echo "Skipping deployment."
		                      }
		                  }
		              }
		          }
		      }
		  }
		  ```
		- **Functions**:
			- Define reusable functions inside the script block:
			  ```groovy
			  pipeline {
			      agent any
			      stages {
			          stage('Reusable Function') {
			              steps {
			                  script {
			                      def sayHello(name) {
			                          echo "Hello, ${name}!"
			                      }
			                      sayHello('DevOps')
			                  }
			              }
			          }
			      }
			  }
			  ```
- **Best Practices for Writing Pipelines**
	- **Organize Code**:
		- Use clear stage names and logical steps.
	- **Version Control the Jenkinsfile**:
		- Store the Jenkinsfile in your repository for traceability.
	- **Parameterize Pipelines**:
		- Add input parameters for flexibility:
		  ```groovy
		  parameters {
		      string(name: 'DEPLOY_ENV', defaultValue: 'staging', description: 'Target environment')
		  }
		  ```
	- **Error Handling**:
		- Use `try/catch` or `catchError` for graceful failures.
	- **Keep Pipelines Clean**:
		- Avoid inline scripts for complex logic; move them to shared libraries.
	- **Parallel Execution**:
		- Run independent tasks in parallel to reduce build time.
		  ```groovy
		  parallel {
		      stage('Unit Tests') {
		          steps { echo 'Running unit tests' }
		      }
		      stage('Integration Tests') {
		          steps { echo 'Running integration tests' }
		      }
		  }
		  ```
	- **Lock Critical Resources**:
		- Use `lock` for shared resources to avoid conflicts.
	- **Add Notifications**:
		- Integrate email, Slack, or other tools for build notifications.