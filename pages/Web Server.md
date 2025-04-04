- #Apache2
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
		- [[Ansible]]: Automate Apache installation and configuration.
		- **CI/CD Integration**: Use tools like [[Jenkins]] or [[GitLab CI/CD]] for automated deployments.
	- **Load Balancing**:
		- **mod_proxy_balancer**: Set up load balancing across multiple servers.
		- **Failover**: Configure backup servers for high availability.
- #Nginx
	- **Definition**:
		- A high-performance, open-source web server and reverse proxy designed to handle a large number of simultaneous connections.
	- **Key Features**:
		- **Asynchronous Architecture**: Efficiently handles multiple connections with minimal resources.
		- **Reverse Proxy and Load Balancing**: Routes traffic to internal servers and distributes load.
		- **Caching**: Caches static resources and application responses to reduce server load.
		- **SSL/TLS Support**: Provides secure connections with HTTPS.
	- **Installation (Debian/Ubuntu)**:
	  ```bash
	  sudo apt update
	  sudo apt install nginx
	  ```
	- **Basic Commands**:
		- Start server: `sudo systemctl start nginx`
		- Stop server: `sudo systemctl stop nginx`
		- Restart server: `sudo systemctl restart nginx`
		- Reload configuration: `sudo systemctl reload nginx`
		- Check status: `sudo systemctl status nginx`
	- **Configuration Files**:
		- **`/etc/nginx/nginx.conf`**: Main configuration file.
		- **`/etc/nginx/sites-available/`**: Directory for virtual host configurations.
		- **`/etc/nginx/sites-enabled/`**: Symbolic links to active sites.
	- **Modules**:
		- **HTTP Proxy**: Forward requests to backend servers.
		- **SSL/TLS**: Enable HTTPS.
		- **Caching**: Reduce load by caching responses.
		- **Load Balancing**: Distribute requests across multiple servers.
	- **Security Tips**:
		- Use SSL/TLS with Let's Encrypt for HTTPS.
		- Limit connections and request rates to protect against DDoS.
		- Configure headers for security enhancements.
	- **Performance Optimization**:
		- **worker_processes auto**: Automatically sets workers based on CPU cores.
		- **worker_connections 1024**: Defines max connections per worker.
		- **gzip on**: Enables gzip compression.
		- **proxy_cache**: Enables caching for backend responses.
	- **Load Balancing**:
		- **Round Robin**: Default load balancing method.
		- **Least Connections**: Sends requests to server with least connections.
		- **IP Hash**: Consistent server for specific client IP.
	- **Monitoring and Logging**:
		- **Custom Logs**: Add custom fields for better insights.
		- **Prometheus/Grafana**: Integration for real-time monitoring.
	- **CI/CD Integration**:
		- **Docker**: Official image for easy deployment.
		- [[Ansible]]: Automates installation and configuration.
	- **Useful Modules**:
		- **ngx_http_realip_module**: Corrects client IP behind proxies.
		- **ngx_http_stub_status_module**: Provides basic server status.