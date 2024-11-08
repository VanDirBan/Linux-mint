- **Apache2**
	- **Definition**:
		- A widely-used open-source web server that supports serving websites and applications.
	- **Key Features**:
		- Modular structure for adding/removing features.
		- Supports multiple protocols (HTTP, HTTP/2, HTTPS).
		- Virtual Hosts for hosting multiple websites on one server.
		- Secure access with SSL/TLS and `.htaccess` files.
	- **Installation (Debian/Ubuntu)**:
	  ```bash
	  sudo apt update
	  sudo apt install apache2
	  ```
	- **Basic Commands**:
		- Start server: `sudo systemctl start apache2`
		- Stop server: `sudo systemctl stop apache2`
		- Restart server: `sudo systemctl restart apache2`
		- Reload configuration: `sudo systemctl reload apache2`
		- Check status: `sudo systemctl status apache2`
	- **Configuration Files**:
		- **`/etc/apache2/apache2.conf`**: Main configuration file.
		- **`/etc/apache2/sites-available/`**: Virtual hosts configuration files.
		- **`/etc/apache2/sites-enabled/`**: Enabled virtual hosts.
	- **Virtual Host Example**:
	  ```plaintext
	  <VirtualHost *:80>
	      ServerName example.com
	      DocumentRoot /var/www/example.com
	      <Directory /var/www/example.com>
	          Options Indexes FollowSymLinks
	          AllowOverride All
	          Require all granted
	      </Directory>
	      ErrorLog ${APACHE_LOG_DIR}/example.com-error.log
	      CustomLog ${APACHE_LOG_DIR}/example.com-access.log combined
	  </VirtualHost>
	  ```
	- **Common Modules**:
		- **mod_ssl**: Enables HTTPS with SSL/TLS.
		- **mod_rewrite**: Enables URL rewriting.
		- **mod_headers**: Controls HTTP headers for security and caching.
	- **Security Tips**:
		- Enable HTTPS with SSL/TLS.
		- Use `.htaccess` for access control.
		- Disable unused modules for improved security.
	- **Performance and Optimization**:
		- **Multi-Processing Modules (MPM)**: Choose `prefork`, `worker`, or `event` based on application requirements.
		- **Caching**: Use `mod_cache` and `mod_cache_disk` to cache static content.
		- **Compression**: Enable `mod_deflate` for text content compression.
	- **Monitoring**:
		- **Log Management**: Customize log format with `LogFormat` and `CustomLog`.
		- **mod_status**: Enable real-time server status monitoring at `/server-status`.
		- **Prometheus/Grafana Integration**: Use for advanced monitoring.
	- **Security**:
		- **SSL/TLS Encryption**: Protect data with `mod_ssl`.
		- **Access Control**: Restrict access by IP or user with `.htaccess`.
		- **DDoS Protection**: Use `mod_evasive` to mitigate DoS/DDoS attacks.
	- **Automation**:
		- **Ansible**: Automate Apache installation and configuration.
		- **CI/CD Integration**: Use tools like Jenkins or GitLab CI for automated deployments.
	- **Load Balancing**:
		- **mod_proxy_balancer**: Set up load balancing across multiple servers.
		- **Failover**: Configure backup servers for high availability.