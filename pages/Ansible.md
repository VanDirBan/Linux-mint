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
- **Loops in Ansible**
	- **Definition**:
		- Loops in Ansible allow tasks to iterate over a list of items, files, or conditions.
	- **Purpose**:
		- Automates repetitive tasks.
		- Simplifies playbooks by avoiding hardcoding repetitive actions.
- **Using Loop**
	- **Definition**:
		- The `loop` keyword is the modern way to iterate over lists.
	- **Basic Example**:
	  ```yaml
	  - name: Install multiple packages
	    apt:
	      name: "{{ item }}"
	      state: present
	    loop:
	      - nginx
	      - mysql-server
	      - php
	  ```
	- **With Variables**:
	  ```yaml
	  vars:
	    packages:
	      - nginx
	      - mysql-server
	      - php
	  tasks:
	    - name: Install packages
	      apt:
	        name: "{{ item }}"
	        state: present
	      loop: "{{ packages }}"
	  ```
	- **Enumerating with `loop`**:
		- Use `loop.index` or `loop.index0` for indexes.
		  ```yaml
		  - name: Create numbered files
		  copy:
		    dest: "/tmp/file{{ loop.index }}.txt"
		    content: "This is file number {{ loop.index }}"
		  loop:
		    - 1
		    - 2
		    - 3
		  ```
- **Using with_items**
	- **Definition**:
		- Legacy method for iterating over a list (replaced by `loop` but still supported).
	- **Basic Example**:
	  ```yaml
	  - name: Add users
	    user:
	      name: "{{ item }}"
	      state: present
	    with_items:
	      - alice
	      - bob
	      - charlie
	  ```
- **Using Until**
	- **Definition**:
		- Repeats a task until a condition is met or a timeout occurs.
	- **Syntax**:
	  ```yaml
	  - name: Wait for a service to start
	    shell: systemctl is-active nginx
	    register: result
	    until: result.stdout == "active"
	    retries: 5
	    delay: 10
	  ```
	- **Parameters**:
		- `until`: The condition to check.
		- `retries`: Maximum number of attempts.
		- `delay`: Time (in seconds) between attempts.
- **Using With_fileglob**
	- **Definition**:
		- Iterates over files matching a specific pattern.
	- **Syntax**:
	  ```yaml
	  - name: Copy all config files
	    copy:
	      src: "{{ item }}"
	      dest: /etc/myconfigs/
	    with_fileglob:
	      - "/home/user/configs/*.conf"
	  ```
	- **Best Practices**:
		- Use for batch processing of files in directories.
- **Advanced Loops**
	- **With Nested Loops**:
		- Combine two lists in a loop:
		  ```yaml
		  - name: Deploy applications to servers
		    command: "{{ item.0 }} {{ item.1 }}"
		    with_nested:
		      - [ "app1", "app2", "app3" ]
		      - [ "server1", "server2" ]
		  ```
	- **With Dictionaries**:
		- Iterate over key-value pairs:
		  ```yaml
		  vars:
		    users:
		      alice: admin
		      bob: user
		  tasks:
		    - name: Create users with roles
		      user:
		        name: "{{ item.key }}"
		        groups: "{{ item.value }}"
		      loop: "{{ users | dict2items }}"
		  ```
- **Jinja Templates in Ansible**
	- **Definition**:
		- Jinja2 is a templating engine used in Ansible for creating dynamic content.
		- Templates are written in `.j2` files and rendered with variables, loops, and conditionals.
	- **Purpose**:
		- Generate configuration files, scripts, or any text-based files dynamically.
		- Simplify repetitive content with logic and variables.
	- **Basic Syntax**
		- **Variables**:
			- Insert variables into templates using double curly braces (`{{ }}`):
			  ```jinja
			  server_name: {{ inventory_hostname }}
			  user: {{ ansible_user }}
			  ```
		- **Conditionals**:
			- Add logic with `if` statements:
			  ```jinja
			  {% if ansible_os_family == "Debian" %}
			  package_manager: apt
			  {% else %}
			  package_manager: yum
			  {% endif %}
			  ```
		- **Loops**:
			- Iterate over lists or dictionaries:
			  ```jinja
			  users:
			  {% for user in users %}
			  - name: {{ user.name }}
			    role: {{ user.role }}
			  {% endfor %}
			  ```
		- **Filters**:
			- Modify or format variables with Jinja filters:
				- Uppercase a string:
				  ```jinja
				  {{ "hello" | upper }}
				  ```
				- Join a list:
				  ```jinja
				  {{ mylist | join(", ") }}
				  ```
	- **Using Jinja Templates in Ansible**
		- **Directory Structure**:
			- Store templates in the `templates/` directory of your playbook or role.
		- **Template Module**:
			- Use the `template` module to apply a Jinja2 template:
			  ```yaml
			  - name: Generate Nginx configuration
			    template:
			      src: nginx.conf.j2
			      dest: /etc/nginx/nginx.conf
			  ```
		- **Passing Variables**:
			- Variables used in templates come from:
				- Playbook variables.
				- Inventory variables.
				- Facts gathered by Ansible.
		- **Practical Examples**
			- **Dynamic Nginx Configuration**:
				- Template file `nginx.conf.j2`:
				  ```jinja
				  server {
				      listen 80;
				      server_name {{ inventory_hostname }};
				      location / {
				          proxy_pass http://{{ backend_server }};
				      }
				  }
				  ```
				- Playbook task:
				  ```yaml
				  - name: Configure Nginx
				    template:
				      src: nginx.conf.j2
				      dest: /etc/nginx/nginx.conf
				    vars:
				      backend_server: "127.0.0.1:8080"
				  ```
			- **Generate a Hosts File**:
				- Template file `hosts.j2`:
				  ```jinja
				  {% for host in groups['all'] %}
				  {{ host }} ansible_host={{ hostvars[host]['ansible_host'] }}
				  {% endfor %}
				  ```
				- Playbook task:
				  ```yaml
				  - name: Generate /etc/hosts
				    template:
				      src: hosts.j2
				      dest: /etc/hosts
				  ```
	- **Common Jinja2 Filters**
		- **Default**:
			- Provide a fallback value:
			  ```jinja
			  {{ variable | default("fallback_value") }}
			  ```
		- **Replace**:
			- Replace part of a string:
			  ```jinja
			  {{ "example.com" | replace("com", "org") }}
			  ```
		- **Trim**:
			- Remove leading and trailing whitespace:
			  ```jinja
			  {{ "  hello  " | trim }}
			  ```
		- **Length**:
			- Get the length of a list or string:
			  ```jinja
			  {{ mylist | length }}
			  ```
	- **Best Practices**
		- Use descriptive variable names for clarity.
		- Store templates in the `templates/` directory for better organization.
		- Test templates independently before deploying them with Ansible.
		- Leverage conditionals and loops to reduce redundancy in templates.
		- Use `debug` to verify variable values before applying templates.
- **Roles in Ansible**
	- **Definition**:
		- Roles in Ansible provide a structured way to organize and reuse tasks, variables, handlers, templates, and files.
		- They simplify complex playbooks by breaking them into reusable and modular components.
	- **Purpose**:
		- Improve playbook readability and maintainability.
		- Enable sharing and reusability across projects.
	- **Role Structure**
		- **Default Role Directory Layout**:
		  ```plaintext
		  roles/
		    └── role_name/
		        ├── tasks/           # Main tasks of the role (main.yml is required)
		        ├── handlers/        # Handlers for service restarts or similar actions
		        ├── files/           # Static files to copy to managed nodes
		        ├── templates/       # Jinja2 templates for dynamic content
		        ├── vars/            # Variables with high priority (not recommended for sensitive data)
		        ├── defaults/        # Default variables for the role
		        ├── meta/            # Role metadata, including dependencies
		        └── README.md        # Documentation for the role
		  ```
	- **Creating a Role**
		- **Command to Create a Role**:
			- Use the `ansible-galaxy init` command:
			  ```bash
			  ansible-galaxy init role_name
			  ```
			- This generates the default directory structure for the role.
		- **Example**:
			- Create a role named `nginx`:
			  ```bash
			  ansible-galaxy init nginx
			  ```
	- **Components of a Role**
		- **Tasks**:
			- Main tasks to execute:
				- File: `roles/nginx/tasks/main.yml`
				- Example:
				  ```yaml
				  - name: Install Nginx
				    apt:
				      name: nginx
				      state: present
				  
				  - name: Start Nginx
				    service:
				      name: nginx
				      state: started
				  ```
		- **Handlers**:
			- Define actions triggered by tasks:
				- File: `roles/nginx/handlers/main.yml`
				- Example:
				  ```yaml
				  - name: Restart Nginx
				    service:
				      name: nginx
				      state: restarted
				  ```
		- **Templates**:
			- Store Jinja2 templates for dynamic configuration:
				- Directory: `roles/nginx/templates/`
				- Example:
					- Template file: `nginx.conf.j2`
					- Task:
					  ```yaml
					  - name: Configure Nginx
					    template:
					      src: nginx.conf.j2
					      dest: /etc/nginx/nginx.conf
					    notify:
					      - Restart Nginx
					  ```
		- **Files**:
			- Store static files to copy to managed nodes:
				- Directory: `roles/nginx/files/`
				- Example:
				  ```yaml
				  - name: Copy static file
				    copy:
				      src: myfile.txt
				      dest: /etc/myapp/myfile.txt
				  ```
		- **Variables**:
			- Define default and optional variables:
				- `defaults/main.yml` for low-priority variables:
				  ```yaml
				  nginx_port: 80
				  ```
				- `vars/main.yml` for high-priority variables:
				  ```yaml
				  app_env: production
				  ```
	- **Using a Role in a Playbook**
		- **Basic Usage**:
		  ```yaml
		  - name: Deploy Nginx
		    hosts: webservers
		    roles:
		      - nginx
		  ```
		- **Passing Variables to Roles**:
		  ```yaml
		  - name: Deploy Nginx with custom port
		    hosts: webservers
		    roles:
		      - role: nginx
		        vars:
		          nginx_port: 8080
		  ```
	- **Role Dependencies**
		- **Definition**:
			- Roles can declare dependencies on other roles.
		- **Configuration**:
			- File: `roles/nginx/meta/main.yml`
			- Example:
			  ```yaml
			  dependencies:
			    - role: common
			    - role: firewall
			  ```
	- **Best Practices**
		- Use meaningful role names that reflect their purpose (e.g., `nginx`, `mysql`).
		- Store reusable roles in a `roles/` directory at the root of the playbook.
		- Include a `README.md` file in each role to document its purpose and usage.
		- Use `defaults/main.yml` for variables to allow overriding.
		- Avoid hardcoding values in tasks; rely on variables for flexibility.
		- Test roles independently before integrating them into playbooks.
- **Variables in Ansible**
	- **Definition**:
		- Variables in Ansible store dynamic data and enable customization of playbooks.
		- Defined in various places, such as playbooks, inventory files, role defaults, or passed externally as `extra-vars`.
	- **Defining Variables**
		- **In Playbooks**:
			- Defined under `vars`:
			  ```yaml
			  vars:
			    app_name: "my_app"
			    app_port: 8080
			  ```
		- **In Inventory Files**:
			- Host-specific variables:
			  ```ini
			  [webservers]
			  web1 ansible_host=192.168.1.10 app_name=my_app
			  web2 ansible_host=192.168.1.11 app_name=my_other_app
			  ```
			- Group variables:
				- File: `group_vars/webservers.yml`
				- Content:
				  ```yaml
				  app_port: 8080
				  ```
		- **In Role Defaults/Vars**:
			- Default variables (low priority):
				- File: `roles/my_role/defaults/main.yml`
			- High-priority variables:
				- File: `roles/my_role/vars/main.yml`
	- **Overriding Variables**
		- **Order of Precedence** (Highest to Lowest Priority):
		  1. Extra variables (`--extra-vars`).
		  2. Role variables (`vars/main.yml`).
		  3. Playbook variables (`vars` in playbook).
		  4. Inventory group variables (`group_vars`).
		  5. Inventory host variables.
		  6. Role defaults (`defaults/main.yml`).
	- **External Variables: Extra Vars**
		- **Definition**:
			- Extra variables (`extra-vars`) are passed at runtime using the `--extra-vars` option.
			- They override all other variable definitions.
		- **Syntax**:
			- Pass a single variable:
			  ```bash
			  ansible-playbook playbook.yml --extra-vars "app_name=my_custom_app"
			  ```
			- Pass multiple variables:
			  ```bash
			  ansible-playbook playbook.yml --extra-vars "app_name=my_app app_port=9090"
			  ```
			- Pass variables as JSON:
			  ```bash
			  ansible-playbook playbook.yml --extra-vars '{"app_name": "my_app", "app_port": 9090}'
			  ```
	- **Using Variables in Playbooks**
		- **Basic Usage**:
		  ```yaml
		  - name: Configure application
		    hosts: webservers
		    vars:
		      app_name: "default_app"
		    tasks:
		      - name: Create configuration file
		        template:
		          src: config.j2
		          dest: "/etc/{{ app_name }}/config.yaml"
		  ```
		- **Override with `extra-vars`**:
			- Command:
			  ```bash
			  ansible-playbook playbook.yml --extra-vars "app_name=custom_app"
			  ```
			- Result:
				- The `app_name` will be set to `custom_app`, overriding the default value.
	- **Practical Example**
		- **Dynamic Application Deployment**:
			- Playbook:
			  ```yaml
			  - name: Deploy application
			    hosts: webservers
			    vars:
			      app_name: "default_app"
			      app_port: 8080
			    tasks:
			      - name: Install dependencies
			        apt:
			          name: "{{ item }}"
			          state: present
			        loop:
			          - nginx
			          - python3
			          - python3-pip
			      - name: Start application on port {{ app_port }}
			        shell: "python3 /var/www/{{ app_name }}/app.py --port {{ app_port }} &"
			  ```
			- Run with custom variables:
			  ```bash
			  ansible-playbook deploy.yml --extra-vars "app_name=my_web_app app_port=9090"
			  ```
	- **Best Practices for Variables and Extra Vars**
		- **Use Default Variables**:
			- Define default values in `defaults/main.yml` or in the playbook.
		- **Pass Sensitive Data Securely**:
			- Use Ansible Vault for encrypting sensitive variables.
		- **Leverage Extra Vars for Dynamic Data**:
			- Use `--extra-vars` to customize playbook execution without modifying the playbook itself.
		- **Avoid Hardcoding**:
			- Keep variable values configurable and environment-specific.
	- **Testing Variables**
		- Use the `debug` module to verify variable values:
		  ```yaml
		  - name: Debug variables
		    debug:
		      msg: "Application: {{ app_name }} is running on port {{ app_port }}"
		  ```
- **Import vs Include in Ansible**
	- **Definition**:
		- Both `import` and `include` are used to organize playbooks and tasks by including external files or roles.
		- **Import**:
			- Static: The content is loaded and processed during playbook parsing.
			- Tasks and roles are imported at the start of execution.
		- **Include**:
			- Dynamic: The content is loaded and processed during playbook execution.
			- Tasks and roles are included only when the `include` statement is encountered.
	- **Including and Importing Tasks**
		- **Purpose**:
			- Break large playbooks into smaller, reusable task files.
		- **Syntax**:
			- **Import Tasks**:
				- Static inclusion of tasks.
				- Example:
				  ```yaml
				  - name: Import common tasks
				    import_tasks: common.yml
				  ```
			- **Include Tasks**:
				- Dynamic inclusion of tasks, allowing conditionals.
				- Example:
				  ```yaml
				  - name: Include tasks conditionally
				    include_tasks: setup.yml
				    when: ansible_os_family == "Debian"
				  ```
		- **Use Cases**:
			- **import_tasks**: Use for static and predictable tasks.
			- **include_tasks**: Use for dynamic scenarios with `when` or `loop`.
	- **Including and Importing Roles**
		- **Purpose**:
			- Use roles for modular and reusable configurations.
		- **Syntax**:
			- **Import Roles**:
				- Static inclusion of roles.
				- Example:
				  ```yaml
				  - name: Import roles
				    import_role:
				      name: nginx
				  ```
			- **Include Roles**:
				- Dynamic inclusion of roles with conditions.
				- Example:
				  ```yaml
				  - name: Include role conditionally
				    include_role:
				      name: nginx
				    when: ansible_distribution == "Ubuntu"
				  ```
		- **Use Cases**:
			- **import_role**: Use for mandatory roles that must always run.
			- **include_role**: Use for roles that depend on conditions or variables.
	- **Examples**
		- **Organizing Tasks**:
			- Directory structure:
			  ```plaintext
			  playbooks/
			    ├── main.yml
			    ├── tasks/
			    │   ├── common.yml
			    │   ├── setup.yml
			  ```
			- `main.yml`:
			  ```yaml
			  - name: Main playbook
			    hosts: all
			    tasks:
			      - import_tasks: tasks/common.yml
			      - include_tasks: tasks/setup.yml
			        when: ansible_os_family == "RedHat"
			  ```
		- **Including Roles**:
			- Directory structure:
			  ```plaintext
			  roles/
			    ├── nginx/
			    │   ├── tasks/
			    │   │   ├── main.yml
			  playbooks/
			    ├── site.yml
			  ```
			- `site.yml`:
			  ```yaml
			  - name: Deploy Nginx
			    hosts: webservers
			    roles:
			      - { role: nginx, when: ansible_distribution == "Ubuntu" }
			  ```
			- Using `include_role` for conditional execution:
			  ```yaml
			  - name: Conditional Nginx role
			    include_role:
			      name: nginx
			    when: ansible_distribution == "Debian"
			  ```
	- **Differences Between Import and Include**
		- **Static (import)**:
			- Tasks and roles are evaluated before execution starts.
			- Cannot use dynamic conditions like `when` or `loop`.
		- **Dynamic (include)**:
			- Tasks and roles are evaluated during execution.
			- Supports conditionals and loops.
	- **Best Practices**
		- Use `import_tasks` for static and essential tasks that always execute.
		- Use `include_tasks` for conditional or dynamic scenarios.
		- Use `import_role` for roles required for all scenarios.
		- Use `include_role` for conditional role execution.
		- Keep task and role files modular and reusable.
- **Delegate_to in Ansible**
	- **Definition**:
		- `delegate_to` is an Ansible directive used to execute a specific task on a server (host) other than the one defined in the playbook.
		- Commonly used for managing centralized services like load balancers, database servers, or CI/CD pipelines.
	- **Purpose**:
		- Redirect tasks to specific servers when certain actions must be performed on a different machine.
		- Useful for centralizing configurations or orchestrating processes.
	- **Basic Syntax**
		- **Structure**:
		  ```yaml
		  - name: Task description
		    module_name:
		      option1: value1
		      option2: value2
		    delegate_to: target_server
		  ```
		- **Example**:
			- Running a task on a load balancer to update configuration:
			  ```yaml
			  - name: Update load balancer configuration
			    command: /usr/local/bin/update-lb
			    delegate_to: loadbalancer.example.com
			  ```
	- **Use Cases**
		- **Load Balancer Updates**:
			- Apply changes to a load balancer after deploying application servers:
			  ```yaml
			  - name: Reload Nginx on the load balancer
			    service:
			      name: nginx
			      state: reloaded
			    delegate_to: lb.example.com
			  ```
		- **Database Management**:
			- Run a database task on a central database server:
			  ```yaml
			  - name: Create a new database
			    mysql_db:
			      name: my_database
			      state: present
			    delegate_to: db.example.com
			  ```
		- **Centralized Orchestration**:
			- Configure centralized services, like CI/CD tools, from a specific host.
	- **Practical Example**
		- **Deploy Application and Update Load Balancer**:
			- Playbook:
			  ```yaml
			  - name: Deploy application and update load balancer
			    hosts: app_servers
			    tasks:
			      - name: Deploy the application
			        copy:
			          src: /local/path/to/app
			          dest: /var/www/html/
			        
			      - name: Notify the load balancer
			        command: /usr/local/bin/reload-lb
			        delegate_to: lb.example.com
			  ```
		- **Managing Multiple Hosts with Delegation**:
		  ```yaml
		  - name: Manage tasks on different servers
		    hosts: webservers
		    tasks:
		      - name: Perform task on the primary server
		        shell: echo "This runs on the primary server"
		        delegate_to: primary-server.example.com
		  ```
	- **Important Points**
		- The `delegate_to` directive only affects the task it is defined in, not the entire playbook.
		- The inventory variables of the delegated host (e.g., `ansible_host`, `ansible_user`) are used during the task execution.
	- **Best Practices**
		- Use `delegate_to` sparingly and only when necessary.
		- Clearly document tasks that use `delegate_to` to avoid confusion.
		- Combine `delegate_to` with `register` to capture results and use them later in the playbook.
		- Test the delegated tasks to ensure they execute properly on the target server.
	- **Common Issues and Debugging**
		- **Issue**:
			- Delegated tasks fail due to missing inventory details for the target host.
			- **Solution**:
				- Ensure the target server is defined in the inventory file with proper variables.
		- **Issue**:
			- SSH issues while delegating tasks.
			- **Solution**:
				- Verify the SSH access to the target server using the same credentials as in the playbook.
- **Error Handling in Ansible**
	- **Definition**:
		- Ansible provides mechanisms to handle errors gracefully during playbook execution.
		- Includes tools like `ignore_errors`, `failed_when`, `rescue`, and `always` to manage task outcomes and ensure reliability.
	- **Ignoring Errors**
		- **ignore_errors**:
			- Allows playbook execution to continue even if a task fails.
			- **Syntax**:
			  ```yaml
			  - name: Install a package
			    apt:
			      name: nginx
			      state: present
			    ignore_errors: yes
			  ```
			- **Best Practices**:
				- Use when the task failure is not critical.
				- Document why ignoring the error is necessary.
	- **Custom Failure Conditions**
		- **failed_when**:
			- Specifies custom conditions to determine task failure.
			- **Syntax**:
			  ```yaml
			  - name: Check a service status
			    shell: systemctl is-active nginx
			    register: result
			    failed_when: result.stdout != "active"
			  ```
			- **Use Cases**:
				- Handle non-standard exit codes.
				- Define custom failure logic based on command output.
	- **Error Handling with Blocks**
		- **Using rescue and always**:
			- `rescue`: Executes tasks when a block fails.
			- `always`: Executes tasks regardless of block success or failure.
			- **Example**:
			  ```yaml
			  - name: Error handling example
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
			      - name: Print error message
			        debug:
			          msg: "Failed to install or start Nginx."
			    always:
			      - name: Ensure package list is updated
			        apt:
			          update_cache: yes
			  ```
	- **Registering and Analyzing Task Results**
		- **register**:
			- Captures the result of a task for analysis in subsequent tasks.
			- **Example**:
			  ```yaml
			  - name: Check disk usage
			    shell: df -h
			    register: disk_result
			  
			  - name: Print disk usage
			    debug:
			      var: disk_result.stdout
			  ```
		- **Combine with failed_when**:
			- Use registered results to define failure conditions.
	- **Retrying Tasks**
		- **until**:
			- Repeats a task until a condition is met or a maximum number of retries is reached.
			- **Syntax**:
			  ```yaml
			  - name: Wait for a service to start
			    shell: systemctl is-active nginx
			    register: result
			    until: result.stdout == "active"
			    retries: 5
			    delay: 10
			  ```
			- **Use Cases**:
				- Waiting for asynchronous events (e.g., service startup, resource availability).
	- **Handling Critical Failures**
		- **force_handlers**:
			- Ensures handlers are triggered even if a task fails.
			- Enable in the playbook:
			  ```yaml
			  - name: Example playbook
			    hosts: all
			    force_handlers: yes
			  ```
	- **Best Practices**
		- Use `ignore_errors` sparingly to avoid masking critical issues.
		- Combine `failed_when` with meaningful conditions for better error detection.
		- Use `rescue` and `always` in `block` to handle critical workflows.
		- Capture and analyze task results with `register` for advanced logic.
		- Test playbooks thoroughly to anticipate and handle potential errors.
- **Ansible Vault**
	- **Definition**:
		- Ansible Vault is a tool for encrypting sensitive data like passwords, API keys, or certificates.
		- Encrypted files can still be used in playbooks without exposing the data in plaintext.
	- **Use Cases**
		- Store sensitive variables (e.g., database credentials).
		- Encrypt entire playbooks, inventory files, or variable files.
		- Securely share sensitive data among team members.
	- **Basic Commands**
		- **Create an encrypted file**:
		  ```bash
		  ansible-vault create secrets.yml
		  ```
			- Opens an editor to input the file content, which will be encrypted upon saving.
		- **Encrypt an existing file**:
		  ```bash
		  ansible-vault encrypt existing_file.yml
		  ```
		- **Decrypt a file**:
		  ```bash
		  ansible-vault decrypt encrypted_file.yml
		  ```
		- **Edit an encrypted file**:
		  ```bash
		  ansible-vault edit secrets.yml
		  ```
			- Opens the encrypted file for editing.
		- **View an encrypted file**:
		  ```bash
		  ansible-vault view secrets.yml
		  ```
	- **Using Encrypted Variables in Playbooks**
		- **Encrypt Variables File**:
		  ```bash
		  ansible-vault encrypt group_vars/all/secrets.yml
		  ```
		- **Reference in a Playbook**:
		  ```yaml
		  - name: Use encrypted variables
		    hosts: all
		    vars_files:
		      - group_vars/all/secrets.yml
		    tasks:
		      - name: Print the secret
		        debug:
		          msg: "Database password is {{ db_password }}"
		  ```
	- **Using Vault with Playbooks**
		- **Run a Playbook with Vault**:
			- When running a playbook that references encrypted files, specify the password:
			  ```bash
			  ansible-playbook playbook.yml --ask-vault-pass
			  ```
			- Use a password file for automation:
			  ```bash
			  ansible-playbook playbook.yml --vault-password-file /path/to/password_file
			  ```
	- **Encrypting Variables Inline**
		- Encrypt a variable directly using the `ansible-vault encrypt_string` command:
		  ```bash
		  ansible-vault encrypt_string 'mypassword' --name 'db_password'
		  ```
			- Output:
			  ```yaml
			  db_password: !vault |
			    $ANSIBLE_VAULT;1.1;AES256
			    62386263373561303831663235663839323534303432313938616561333666633639303832333338
			    ...
			  ```
			- Use the encrypted string directly in your playbook or variable files.
	- **Best Practices**
		- Use a centralized file (e.g., `vault.yml`) to store all secrets and reference it in playbooks.
		- Secure the vault password file with appropriate file permissions.
		- Rotate and update secrets regularly.
		- Use `encrypt_string` for embedding encrypted values directly in files.
		- Do not commit the vault password file or decrypted secrets to version control.
	- **Common Issues**
		- **Forgotten Vault Password**:
			- Without the password, encrypted data cannot be recovered.
		- **Inconsistent Encryption**:
			- Ensure all team members use the same encryption key/password.
		- **Automation Challenges**:
			- Use `--vault-password-file` for automation in CI/CD pipelines.
	- **Advanced Features**
		- **Rekey Vault Files**:
			- Change the encryption password for an existing vault:
			  ```bash
			  ansible-vault rekey secrets.yml
			  ```
		- **Encrypt Multiple Files**:
			- Encrypt all variable files in a directory:
			  ```bash
			  find group_vars/ -type f -name '*.yml' -exec ansible-vault encrypt {} \;
			  ```
		- **Custom Encryption Cipher**:
			- Specify a different cipher (default is AES256):
			  ```bash
			  ansible-vault create --vault-id dev@prompt secrets.yml
			  ```
	- **Changing the Default Text Editor for Ansible Vault**
		- By default, Ansible Vault uses the editor specified by the `EDITOR` environment variable.
		- **Change the Editor Temporarily**:
			- Set the editor inline while running the command:
			  ```bash
			  EDITOR=nano ansible-vault edit secrets.yml
			  ```
		- **Change the Editor Permanently**:
			- Update the `EDITOR` variable in your shell configuration file (e.g., `.bashrc` or `.zshrc`):
			  ```bash
			  echo "export EDITOR=nano" >> ~/.bashrc
			  source ~/.bashrc
			  ```
			- Replace `nano` with your preferred editor (e.g., `vim`, `code`).
- **Dynamic Inventory in AWS using ec2.py**
	- **Definition**:
		- A dynamic inventory script (`ec2.py`) for AWS retrieves EC2 instance information and dynamically generates an inventory for Ansible.
		- Useful for managing dynamic cloud environments where servers are frequently added or removed.
	- **Dynamic Inventory Basics**
		- **Static Inventory**:
			- Requires manual updating of hosts in inventory files.
			- Example:
			  ```ini
			  [webservers]
			  web1 ansible_host=192.168.1.10
			  web2 ansible_host=192.168.1.11
			  ```
		- **Dynamic Inventory**:
			- Automatically discovers and lists instances based on real-time data from AWS.
			- Reduces manual effort and ensures up-to-date inventory.
	- **ec2.py Script**
		- **Purpose**:
			- A Python script provided by Ansible to query AWS and generate dynamic inventory.
		- **Source**:
			- Download the script and its configuration file (`ec2.ini`) from the official Ansible repository:
			  ```bash
			  curl -O https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/ec2.py
			  curl -O https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/ec2.ini
			  ```
		- **Make the Script Executable**:
		  ```bash
		  chmod +x ec2.py
		  ```
	- **Configuring ec2.py**
		- **Edit the `ec2.ini` Configuration File**:
			- Configure AWS access and filtering options:
			  ```ini
			  [ec2]
			  regions = us-east-1,us-west-2
			  destination_variable = private_ip_address
			  vpc_destination_variable = private_ip_address
			  instance_filters = tag:Environment=Production
			  ```
			- Key settings:
				- **regions**: Define AWS regions to query.
				- **destination_variable**: Specify private or public IP for Ansible connections.
				- **instance_filters**: Filter instances by tags or other criteria.
		- **AWS Credentials**:
			- Ensure that AWS CLI is configured with valid credentials:
			  ```bash
			  aws configure
			  ```
			- Alternatively, use environment variables:
			  ```bash
			  export AWS_ACCESS_KEY_ID=your_access_key
			  export AWS_SECRET_ACCESS_KEY=your_secret_key
			  ```
	- **Using ec2.py as Dynamic Inventory**
		- **Basic Command**:
			- Test the dynamic inventory:
			  ```bash
			  ./ec2.py --list
			  ```
			- Output: JSON structure of instances grouped by tags, regions, etc.
		- **Run Playbooks with Dynamic Inventory**:
		  ```bash
		  ansible-playbook -i ec2.py playbook.yml
		  ```
	- **Filtering Instances**
		- Use tags or attributes in `ec2.ini` to filter instances:
			- Example: Only include instances with `Environment=Production` tag:
			  ```ini
			  instance_filters = tag:Environment=Production
			  ```
	- **Practical Example**
		- **Tag-Based Inventory Grouping**:
			- AWS Tags:
				- `Name=web1`, `Environment=Production`
				- `Name=db1`, `Environment=Staging`
			- Use dynamic inventory with filtering:
			  ```yaml
			  - name: Configure web servers
			    hosts: tag_Environment_Production
			    tasks:
			      - name: Install Nginx
			        apt:
			          name: nginx
			          state: present
			  ```
	- **Best Practices**
		- Limit regions in `ec2.ini` to reduce query time.
		- Use AWS tags to organize and manage instance groups effectively.
		- Secure AWS credentials using IAM roles or environment variables.
		- Test `ec2.py` output regularly to ensure accurate inventory.
	- **Common Issues**
		- **Permission Errors**:
			- Ensure the AWS IAM user or role has the required permissions:
			  ```json
			  {
			    "Version": "2012-10-17",
			    "Statement": [
			      {
			        "Effect": "Allow",
			        "Action": [
			          "ec2:DescribeInstances",
			          "ec2:DescribeTags"
			        ],
			        "Resource": "*"
			      }
			    ]
			  }
			  ```
		- **Empty Inventory**:
			- Verify filters in `ec2.ini`.
			- Ensure instances are tagged correctly.
	- **Alternative Approaches**
		- **ansible-inventory Command**:
			- Use Ansible's built-in support for plugins like `aws_ec2` for dynamic inventory instead of `ec2.py`:
			  ```yaml
			  plugin: aws_ec2
			  regions:
			    - us-east-1
			  filters:
			    tag:Environment: Production
			  ```