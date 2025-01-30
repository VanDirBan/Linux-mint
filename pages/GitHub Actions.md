- **GitHub Actions Overview**
	- **Definition**:
		- GitHub Actions is a CI/CD (Continuous Integration / Continuous Deployment) tool built into GitHub.
		- Automates workflows for building, testing, and deploying applications directly from a GitHub repository.
	- **Comparison with [[Jenkins]]**:
		- **Built-in vs Self-Hosted**: GitHub Actions is integrated into GitHub, whereas Jenkins requires a separate server.
		- **YAML-based Pipelines**: GitHub Actions uses `.github/workflows/*.yml`, while Jenkins uses `Jenkinsfile`.
		- **Managed Runners vs Self-Hosted Agents**: GitHub provides managed runners; Jenkins requires setting up nodes manually.
- **Core Concepts**
	- **Workflow**
		- A process that executes jobs based on triggers.
		- Example:
		  ```yaml
		  name: CI Pipeline
		  on: [push, pull_request]
		  jobs:
		    build:
		      runs-on: ubuntu-latest
		      steps:
		        - name: Checkout Code
		          uses: actions/checkout@v3
		        - name: Run Tests
		          run: npm test
		  ```
	- **Triggers (`on`)**
		- Defines when a workflow runs.
		- Examples:
		  ```yaml
		  on:
		    push:
		      branches:
		        - main
		    pull_request:
		      types: [opened, synchronize]
		    schedule:
		      - cron: "0 0 * * *"
		  ```
		- **Comparison with Jenkins**:
			- Similar to Jenkins `triggers { pollSCM('* * * * *') }` in declarative pipelines.
	- **Jobs**
		- Defines a set of tasks that run in sequence or in parallel.
		- Example:
		  ```yaml
		  jobs:
		    build:
		      runs-on: ubuntu-latest
		      steps:
		        - run: echo "Building project"
		  ```
		- **Comparison with Jenkins**:
			- Similar to Jenkins "Stages" in pipelines.
	- **Steps**
		- Individual commands executed within a job.
		- Each step runs in a new shell.
		- Example:
		  ```yaml
		  steps:
		    - name: Checkout Repository
		      uses: actions/checkout@v3
		    - name: Run Tests
		      run: npm test
		  ```
		- **Comparison with Jenkins**:
			- Equivalent to `steps { sh 'npm test' }` in Jenkins.
	- **Runners**
		- Virtual machines that execute jobs.
		- Available options:
			- **GitHub-Hosted Runners** (`ubuntu-latest`, `windows-latest`).
			- **Self-Hosted Runners** (like Jenkins agents).
- **Reusable Components**
	- **Actions**
		- Predefined or custom reusable tasks.
		- Example:
		  ```yaml
		  - name: Use Node.js
		    uses: actions/setup-node@v3
		    with:
		      node-version: 16
		  ```
		- **Comparison with Jenkins**:
			- Equivalent to plugins in Jenkins.
	- **Secrets & Environment Variables**
		- Securely store sensitive data.
		- Example:
		  ```yaml
		  env:
		    API_KEY: ${{ secrets.API_KEY }}
		  ```
		- **Comparison with Jenkins**:
			- Similar to Jenkins credentials store (`withCredentials` block).
	- **Artifacts & Caching**
		- Used for storing and sharing files between jobs.
		- Example:
		  ```yaml
		  - name: Upload Build Artifacts
		    uses: actions/upload-artifact@v3
		    with:
		      name: my-artifact
		      path: dist/
		  ```
		- **Comparison with Jenkins**:
			- Similar to `archiveArtifacts 'dist/**'`.
- **Example: Basic CI/CD Pipeline**
  ```yaml
  name: CI/CD Pipeline
  on: [push]
  jobs:
    build:
      runs-on: ubuntu-latest
      steps:
      - name: Checkout Code
        uses: actions/checkout@v3  
      - name: Install Dependencies
        run: npm install  
      - name: Run Tests
        run: npm test  
    deploy:  
      runs-on: ubuntu-latest  
      needs: build  
      steps:  
      - name: Deploy to Production
        run: echo "Deploying..."
  ```