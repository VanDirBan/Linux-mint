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
		- A list of managed nodes, specified in a file (e.g., `/etc/ansible/hosts` or `/home/user/.ansible/hosts`).
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
- **LEMP Stack Deployment with Ansible**
	- **Definition**:
		- LEMP stands for **[[Linux]]**, **[[Nginx]]**, **[[MySQL]]/[[MariaDB]]**, and **[[PHP]]**.
		- Ansible automates the deployment of the LEMP stack across multiple servers.
	- **Steps to Deploy a LEMP Stack**
		- **1. Prepare the Inventory File**:
			- Define the servers in the inventory file (e.g., `/etc/ansible/hosts`):
			  ```
			  [webservers]
			  web1.example.com
			  web2.example.com
			  
			  [dbservers]
			  db1.example.com
			  ```
		- **2. Create the Ansible Playbook**:
			- Example playbook to deploy the LEMP stack:
			  ```yaml
			  - name: Deploy LEMP Stack
			    hosts: all
			    become: yes
			  
			    vars:
			      php_packages:
			        - php
			        - php-mysql
			        - php-fpm
			      mysql_root_password: "securepassword"
			  
			    tasks:
			      - name: Update package repository
			        apt:
			          update_cache: yes
			  
			      - name: Install Nginx
			        apt:
			          name: nginx
			          state: present
			  
			      - name: Install MySQL server
			        apt:
			          name: mariadb-server
			          state: present
			  
			      - name: Start and enable MySQL
			        service:
			          name: mysql
			          state: started
			          enabled: yes
			  
			      - name: Set MySQL root password
			        mysql_user:
			          login_user: root
			          login_password: ""
			          user: root
			          password: "{{ mysql_root_password }}"
			          host_all: yes
			  
			      - name: Install PHP and extensions
			        apt:
			          name: "{{ php_packages }}"
			          state: present
			  
			      - name: Configure Nginx for PHP
			        template:
			          src: templates/nginx-lemp.conf.j2
			          dest: /etc/nginx/sites-available/default
			        notify:
			          - restart nginx
			  
			    handlers:
			      - name: restart nginx
			        service:
			          name: nginx
			          state: restarted
			  ```
		- **3. Create a Template for Nginx Configuration**:
			- Template file `templates/nginx-lemp.conf.j2`:
			  ```nginx
			  server {
			      listen 80;
			      server_name localhost;
			  
			      root /var/www/html;
			      index index.php index.html index.htm;
			  
			      location / {
			          try_files $uri $uri/ =404;
			      }
			  
			      location ~ \.php$ {
			          include snippets/fastcgi-php.conf;
			          fastcgi_pass unix:/run/php/php8.3-fpm.sock;
			          fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
			          include fastcgi_params;
			      }
			  }
			  ```
		- **4. Deploy the Playbook**:
			- Run the playbook to set up the LEMP stack:
			  ```bash
			  ansible-playbook lemp-playbook.yml
			  ```
	- **Explanation of Tasks**:
		- **Update Package Repository**:
			- Ensures all package lists are up-to-date.
		- **Install Nginx**:
			- Deploys the Nginx web server.
		- **Install MySQL**:
			- Installs the MySQL (or MariaDB) database server.
			- Sets a secure root password.
		- **Install PHP**:
			- Installs PHP and required extensions for Nginx.
		- **Configure Nginx for PHP**:
			- Updates the Nginx configuration to serve PHP files.
			- Uses a Jinja2 template for dynamic configuration.
		- **Handlers**:
			- Ensures Nginx is restarted whenever the configuration is updated.
	- **Best Practices**:
		- Use variables for sensitive data like passwords (`mysql_root_password`).
		- Use templates (`.j2` files) for dynamic and reusable configurations.
		- Split tasks into separate roles for modularity (e.g., `nginx`, `php`, `mysql` roles).
		- Test the playbook in a staging environment before deploying to production.
	- **Testing the LEMP Stack**:
		- **Verify Nginx**:
			- Visit the server's IP address in a browser.
		- **Test PHP**:
			- Create a PHP info file:
			  ```bash
			  echo "<?php phpinfo(); ?>" > /var/www/html/info.php
			  ```
			- Access it in a browser: `http://<server-ip>/info.php`.
		- **Verify MySQL**:
			- Log in to the database:
			  ```bash
			  mysql -u root -p
			  ```
	- **Common Issues**:
		- **Nginx Fails to Restart**:
			- Check the configuration file syntax:
			  ```bash
			  nginx -t
			  ```
		- **PHP-FPM Socket Not Found**:
			- Ensure the correct PHP-FPM version is specified in the Nginx configuration.
- **Variables in Ansible**
	- **Definition**:
		- Variables allow dynamic data management in playbooks, making them reusable and flexible.
	- **Defining Variables**:
		- In playbooks:
		  ```yaml
		  vars:
		    package_name: nginx
		  ```
		- In inventory files:
		  ```ini
		  [webservers]
		  web1 ansible_host=192.168.1.10 ansible_user=ubuntu package_name=nginx
		  ```
		- In group/host variable files:
			- Example: `group_vars/webservers.yml`:
			  ```yaml
			  package_name: nginx
			  ```
	- **Using Variables**:
		- Access with `{{ variable_name }}`:
		  ```yaml
		  - name: Install a package
		    apt:
		      name: "{{ package_name }}"
		      state: present
		  ```
	- **Types of Variables**:
		- **Host Variables**: Defined per host in inventory files.
		- **Group Variables**: Shared across hosts in a group.
		- **Facts**: System properties gathered by the `setup` module.
- **Debugging with Debug Module**
	- **Purpose**:
		- Prints variable values or custom messages during playbook execution.
	- **Examples**:
		- Print a variable:
		  ```yaml
		  - name: Print the package name
		    debug:
		      var: package_name
		  ```
		- Print a custom message:
		  ```yaml
		  - name: Print a custom message
		    debug:
		      msg: "The selected package is {{ package_name }}"
		  ```
	- **Use Cases**:
		- Debug variable values.
		- Check dynamic data during execution.
- **Using Set_fact**
	- **Definition**:
		- The `set_fact` module dynamically creates variables during playbook execution.
	- **Examples**:
		- Set a variable based on a condition:
		  ```yaml
		  - name: Set custom fact
		    set_fact:
		      custom_fact: "{{ 'nginx' if ansible_distribution == 'Ubuntu' else 'httpd' }}"
		  ```
		- Use the fact in a task:
		  ```yaml
		  - name: Install package based on custom fact
		    apt:
		      name: "{{ custom_fact }}"
		      state: present
		  ```
	- **Best Practices**:
		- Use `set_fact` for temporary, dynamic variables.
		- Combine with conditionals for advanced logic.
- **Using Register**
	- **Definition**:
		- Captures the output of a task into a variable for later use.
	- **Examples**:
		- Register the output of a command:
		  ```yaml
		  - name: Get disk usage
		    command: df -h
		    register: disk_usage
		  ```
		- Use the registered variable:
		  ```yaml
		  - name: Print disk usage
		    debug:
		      var: disk_usage.stdout
		  ```
		- Conditional task with `register`:
		  ```yaml
		  - name: Check if a package is installed
		    shell: dpkg -l | grep nginx
		    register: nginx_check
		    ignore_errors: yes
		  
		  - name: Install nginx if not present
		    apt:
		      name: nginx
		      state: present
		    when: nginx_check.rc != 0
		  ```
	- **Best Practices**:
		- Use `register` for capturing dynamic data like command outputs.
		- Combine with `debug` to verify task results during execution.
- **Blocks in Ansible**
	- **Definition**:
		- `block` groups multiple tasks into a logical unit for better organization, error handling, and conditional execution.
	- **Purpose**:
		- Simplifies complex playbooks.
		- Handles multiple tasks together under the same condition or exception handling.
	- **Basic Syntax**:
	  ```yaml
	  - name: Block example
	    block:
	      - name: Install Nginx
	        apt:
	          name: nginx
	          state: present
	      - name: Start Nginx
	        service:
	          name: nginx
	          state: started
	    rescue:
	      - name: Print an error message
	        debug:
	          msg: "Something went wrong during the block execution!"
	    always:
	      - name: Ensure the system is updated
	        apt:
	          update_cache: yes
	  ```
		- **block**: Main tasks to execute.
		- **rescue**: Tasks to run if any task in the block fails.
		- **always**: Tasks that run regardless of success or failure of the block.
- **Using Conditions with When**
	- **Definition**:
		- `when` is used to execute tasks or blocks conditionally based on variables, facts, or command results.
	- **Basic Syntax**:
	  ```yaml
	  - name: Conditional task
	    apt:
	      name: nginx
	      state: present
	    when: ansible_distribution == 'Ubuntu'
	  ```
	- **Common Use Cases**:
		- OS-specific tasks:
		  ```yaml
		  - name: Install a package on Ubuntu
		    apt:
		      name: nginx
		      state: present
		    when: ansible_os_family == "Debian"
		  ```
		- Using variables:
		  ```yaml
		  - name: Install custom package
		    apt:
		      name: "{{ package_name }}"
		      state: present
		    when: package_name is defined
		  ```
- **Block-When for Conditional Blocks**
	- **Definition**:
		- Applies a condition to an entire block of tasks using `when`.
	- **Example**:
	  ```yaml
	  - name: Install and start Nginx if Ubuntu
	    block:
	      - name: Install Nginx
	        apt:
	          name: nginx
	          state: present
	      - name: Start Nginx
	        service:
	          name: nginx
	          state: started
	    when: ansible_os_family == "Debian"
	  ```
		- **Explanation**:
			- The condition `when: ansible_os_family == "Debian"` applies to all tasks inside the block.
- **Error Handling in Blocks**
	- **Using Rescue and Always**:
		- **Rescue**:
			- Executes specific tasks when a block fails.
		- **Always**:
			- Ensures critical tasks run regardless of block success or failure.
		- **Example**:
		  ```yaml
		  - name: Update and install Nginx
		    block:
		      - name: Update package cache
		        apt:
		          update_cache: yes
		      - name: Install Nginx
		        apt:
		          name: nginx
		          state: present
		    rescue:
		      - name: Print an error message
		        debug:
		          msg: "Block failed. Manual intervention may be required."
		    always:
		      - name: Clean temporary files
		        file:
		          path: /tmp/install.log
		          state: absent
		  ```