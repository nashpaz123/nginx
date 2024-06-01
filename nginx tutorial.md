# Nginx Web Server Tutorial

## Part 1: Installation

### Objective
Install Nginx on your system.

### Steps

1. **Update System Packages**
    ```bash
    sudo apt update
    ```

2. **Install Nginx**
    ```bash
    sudo apt install nginx
    ```

3. **Start Nginx**
    ```bash
    sudo systemctl start nginx
    ```

4. **Enable Nginx to Start on Boot**
    ```bash
    sudo systemctl enable nginx
    ```

5. **Verify Installation**
    - Open a web browser and navigate to `http://your_server_ip`
    - You should see the Nginx welcome page.

---

## Part 2: Basic Configuration

### Objective
Configure Nginx to serve a static website.

### Steps

1. **Navigate to Configuration Directory**
    ```bash
    cd /etc/nginx/sites-available
    ```

2. **Create a New Configuration File**
    ```bash
    sudo nano /etc/nginx/sites-available/mywebsite
    ```

3. **Basic Configuration Setup**
    ```nginx
    server {
        listen 80;
        server_name your_domain_or_IP;

        root /var/www/mywebsite;
        index index.html;

        location / {
            try_files $uri $uri/ =404;
        }
    }
    ```

    **Explanation:**
    - `listen 80;`: Nginx listens on port 80.
    - `server_name your_domain_or_IP;`: Replace `your_domain_or_IP` with your domain name or IP address.
    - `root /var/www/mywebsite;`: The root directory where the website files are located.
    - `index index.html;`: The default file to serve.
    - `location / { try_files $uri $uri/ =404; }`: Try to serve the requested URI, if not found, return a 404 error.

4. **Create a Symbolic Link to Enable the Site**
    ```bash
    sudo ln -s /etc/nginx/sites-available/mywebsite /etc/nginx/sites-enabled/
    ```

5. **Create the Website Directory and a Sample File**
    ```bash
    sudo mkdir -p /var/www/mywebsite
    echo "<html><body><h1>Hello, Nginx!</h1></body></html>" | sudo tee /var/www/mywebsite/index.html
    ```

6. **Check Configuration for Syntax Errors**
    ```bash
    sudo nginx -t
    ```

7. **Restart Nginx**
    ```bash
    sudo systemctl restart nginx
    ```

8. **Verify the Configuration**
    - Open a web browser and navigate to `http://your_domain_or_IP`
    - You should see "Hello, Nginx!" displayed.

---

## Part 3: Deploying a Simple Web Application

### Objective
Deploy a simple PHP application using Nginx and PHP-FPM.

### Steps

1. **Install PHP and PHP-FPM**
    ```bash
    sudo apt install php-fpm
    ```

2. **Create a PHP Info File**
    ```bash
    echo "<?php phpinfo(); ?>" | sudo tee /var/www/mywebsite/info.php
    ```

3. **Update Nginx Configuration for PHP**
    ```nginx
    server {
        listen 80;
        server_name your_domain_or_IP;

        root /var/www/mywebsite;
        index index.php index.html;

        location / {
            try_files $uri $uri/ =404;
        }

        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        }

        location ~ /\.ht {
            deny all;
        }
    }
    ```

4. **Restart Nginx**
    ```bash
    sudo systemctl restart nginx
    ```

5. **Verify PHP Processing**
    - Open a web browser and navigate to `http://your_domain_or_IP/info.php`
    - You should see the PHP information page.

---

## Part 4: Adding a Second Web Application

### Objective
Configure Nginx to serve a second web application.

### Steps

1. **Create a Second Configuration File**
    ```bash
    sudo nano /etc/nginx/sites-available/mysecondwebsite
    ```

2. **Basic Configuration for the Second Website**
    ```nginx
    server {
        listen 8080;
        server_name your_second_domain_or_IP;

        root /var/www/mysecondwebsite;
        index index.html;

        location / {
            try_files $uri $uri/ =404;
        }
    }
    ```

    **Explanation:**
    - `listen 8080;`: Nginx listens on port 8080 for this site.
    - `server_name your_second_domain_or_IP;`: Replace `your_second_domain_or_IP` with your second domain name or IP address.
    - `root /var/www/mysecondwebsite;`: The root directory for the second website.
    - `index index.html;`: The default file to serve.
    - `location / { try_files $uri $uri/ =404; }`: Try to serve the requested URI, if not found, return a 404 error.

3. **Create a Symbolic Link to Enable the Second Site**
    ```bash
    sudo ln -s /etc/nginx/sites-available/mysecondwebsite /etc/nginx/sites-enabled/
    ```

4. **Create the Second Website Directory and a Sample File**
    ```bash
    sudo mkdir -p /var/www/mysecondwebsite
    echo "<html><body><h1>Welcome to the Second Site!</h1></body></html>" | sudo tee /var/www/mysecondwebsite/index.html
    ```

5. **Check Configuration for Syntax Errors**
    ```bash
    sudo nginx -t
    ```

6. **Restart Nginx**
    ```bash
    sudo systemctl restart nginx
    ```

7. **Verify the Configuration**
    - Open a web browser and navigate to `http://your_second_domain_or_IP:8080`
    - You should see "Welcome to the Second Site!" displayed.

---

## Part 5: Troubleshooting

### Objective
Identify and resolve common issues.

### Common Issues and Solutions

1. **Issue 1: Nginx Fails to Start**
    - **Problem:** Configuration error.
    - **Solution:** Check the configuration for syntax errors.
        ```bash
        sudo nginx -t
        ```

2. **Issue 2: 404 Not Found**
    - **Problem:** Incorrect root directory or missing files.
    - **Solution:** Ensure the root directory and files exist.
        ```bash
        sudo ls /var/www/mywebsite
        sudo ls /var/www/mysecondwebsite
        ```

3. **Issue 3: PHP Files Download Instead of Execute**
    - **Problem:** PHP-FPM not properly configured.
    - **Solution:** Ensure `location ~ \.php$` block is correctly set up in Nginx configuration and PHP-FPM is running.
        ```bash
        sudo systemctl status php7.4-fpm
        ```

### Troubleshooting Exercise

**Objective:** Induce and fix issues.

#### Exercise Steps

1. **Create a Syntax Error in the Nginx Configuration**
    - Edit the configuration file:
        ```bash
        sudo nano /etc/nginx/sites-available/mywebsite
        ```
    - Introduce an error (missing semicolon):
        ```nginx
        server {
            listen 80
            server_name your_domain_or_IP;

            root /var/www/mywebsite;
            index index.html;
        }
        ```

2. **Try to Restart Nginx**
    ```bash
    sudo systemctl restart nginx
    ```

3. **Fix the Error by Adding the Missing Semicolon**
    - Edit the configuration file:
        ```bash
        sudo nano /etc/nginx/sites-available/mywebsite
        ```
    - Correct the error:
        ```nginx
        server {
            listen 80;
            server_name your_domain_or_IP;

            root /var/www/mywebsite;
            index index.html;
        }
        ```

4. **Verify and Restart Nginx**
    ```bash
    sudo nginx -t
    sudo systemctl restart nginx
    ```

5. **Change the Root Directory to a Non-existent Path**
    - Edit the configuration file:
        ```bash
        sudo nano /etc/nginx/sites-available/mywebsite
        ```
    - Change the root directory:
        ```nginx
        root /var/www/nonexistent;
        ```

6. **Navigate to the Site and Observe the 404 Error**

7. **Fix the Root Directory Back to `/var/www/mywebsite` and Restart Nginx**
    - Edit the configuration file:
        ```bash
        sudo nano /etc/nginx/sites-available/mywebsite
        ```
    - Correct the root directory:
        ```nginx
        root /var/www/mywebsite;
        ```
    - Restart Nginx:
        ```bash
        sudo systemctl restart nginx
        ```

---

### Summary

By the end of this tutorial, you should be able to:
- Install Nginx and PHP-FPM.
- Configure Nginx to serve multiple static sites and a PHP application.
- Troubleshoot common configuration issues.
