- **What is Ansible?**
	- **Definition**:
		- Ansible is an open-source automation tool for IT infrastructure.
		- Used for configuration management, application deployment, and orchestration.
	- **Key Features**:
		- Agentless: No software needs to be installed on managed nodes.
		- Simple: Uses YAML for writing playbooks (human-readable syntax).
		- Idempotent: Ensures tasks are executed only when necessary, avoiding repeated changes.
		- Extensible: Supports plugins, modules, and custom scripts.
- **How Ansible Works**
	- **Control Node**:
		- The machine where Ansible is installed and commands are executed.
	- **Managed Nodes**:
		- Servers or devices managed by Ansible.
		- Communicates via SSH (or WinRM for Windows).
	- **Inventory**:
		- A list of managed nodes, specified in a file (e.g., `/etc/ansible/hosts`).
		- Example:
		  ```
		  [webservers]
		  web1.example.com
		  web2.example.com
		  
		  [databases]
		  db1.example.com
		  db2.example.com
		  ```
	- **Modules**:
		- Pre-built scripts used to perform tasks (e.g., `yum`, `apt`, `copy`, `file`).
		- Example:
		  ```bash
		  ansible all -m ping
		  ```
			- `-m`: Specifies the module (`ping` module tests connectivity).
- **Core Components**
	- **Playbooks**:
		- Files written in YAML to define tasks.
		- Example:
		  ```yaml
		  - name: Install Apache
		    hosts: webservers
		    tasks:
		      - name: Install Apache package
		        yum:
		          name: httpd
		          state: present
		  ```
	- **Tasks**:
		- Actions executed on managed nodes (e.g., install a package, copy a file).
	- **Roles**:
		- Reusable sets of tasks, variables, templates, and files organized for modularity.
- **Installing Ansible**
	- Install on the control node:
	  ```bash
	  sudo apt update
	  sudo apt install ansible -y
	  ```
	- Verify installation:
	  ```bash
	  ansible --version
	  ```
- **Basic Commands**
	- Check connectivity:
	  ```bash
	  ansible all -m ping
	  ```
	- Run a single task:
	  ```bash
	  ansible webservers -m yum -a "name=httpd state=present"
	  ```
		- `-a`: Arguments for the module.
	- Execute a playbook:
	  ```bash
	  ansible-playbook playbook.yml
	  ```
- **Best Practices**
	- Use meaningful group names in the inventory file.
	- Test connectivity with `ansible all -m ping` before running tasks.
	- Keep playbooks modular and reusable with roles.
	- Use version control (e.g., Git) to manage playbook changes.
	- Follow YAML best practices: consistent indentation, no tabs, and proper structure.