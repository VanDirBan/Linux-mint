- **CI/CD**
	- **Definition**:
		- **CI (Continuous Integration)**:
			- A development practice where developers integrate their code frequently into a shared repository.
			- Automated builds and tests validate the integration.
			- Goal: Detect integration issues early and reduce integration risks.
		- **CD (Continuous Delivery)**:
			- Extends CI by automating the release process to ensure code is always in a deployable state.
			- Deployment is still manual but well-prepared.
		- **CD (Continuous Deployment)**:
			- Fully automates the deployment process, releasing every successful code change to production.
			- Goal: Deliver features to users quickly and reliably.
	- **Benefits**:
		- Faster feedback on code changes.
		- Reduced deployment risks.
		- Improved collaboration between development and operations.
	- **Key Components**:
		- **Source Control System** (e.g., Git).
		- **Build Automation** (e.g., Maven, Gradle).
		- **Testing Frameworks** (e.g., JUnit, Selenium).
		- **Deployment Tools** (e.g., Ansible, Docker).
		- **CI/CD Servers** (e.g., [Jenkins](#Jenkins)).
	- **CI/CD Pipeline**:
		- **Stages**:
		  1. **Source**: Detect code changes in version control.
		  2. **Build**: Compile the application.
		  3. **Test**: Execute automated tests.
		  4. **Release**: Prepare for deployment.
		  5. **Deploy**: Deploy to staging or production.
		  6. **Monitor**: Monitor application performance and logs.
	- **Tools Overview**:
		- **CI Tools**: Jenkins, GitHub Actions, CircleCI.
		- **CD Tools**: Spinnaker, ArgoCD.
		- **Integration with Jenkins**:
			- Jenkins is a popular open-source tool for building CI/CD pipelines.
			- [See Jenkins Section](#Jenkins).