### Advanced Nginx Topics

1. **SSL/TLS Configuration**
   - **Objective:** Secure your website with HTTPS.
   - **Steps:**
     1. Obtain an SSL certificate (e.g., from Let's Encrypt).
     2. Install Certbot:
        ```bash
        sudo apt install certbot python3-certbot-nginx
        ```
     3. Obtain and install the certificate:
        ```bash
        sudo certbot --nginx -d your_domain
        ```
     4. Test SSL:
        ```bash
        sudo systemctl restart nginx
        ```
     5. Verify by accessing `https://your_domain`.

2. **Load Balancing**
   - **Objective:** Distribute traffic across multiple servers.
   - **Steps:**
     1. Create a configuration file for load balancing:
        ```nginx
        upstream backend {
            server backend1.example.com;
            server backend2.example.com;
        }

        server {
            listen 80;
            server_name your_domain;

            location / {
                proxy_pass http://backend;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
            }
        }
        ```
     2. Test and restart Nginx:
        ```bash
        sudo nginx -t
        sudo systemctl restart nginx
        ```

3. **Caching**
   - **Objective:** Improve performance by caching content.
   - **Steps:**
     1. Configure caching in the Nginx configuration:
        ```nginx
        http {
            proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=my_cache:10m max_size=1g inactive=60m use_temp_path=off;

            server {
                listen 80;
                server_name your_domain;

                location / {
                    proxy_cache my_cache;
                    proxy_pass http://backend;
                    proxy_set_header Host $host;
                    proxy_set_header X-Real-IP $remote_addr;
                    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                    proxy_set_header X-Forwarded-Proto $scheme;
                    add_header X-Proxy-Cache $upstream_cache_status;
                }
            }
        }
        ```
     2. Test and restart Nginx:
        ```bash
        sudo nginx -t
        sudo systemctl restart nginx
        ```

4. **Rate Limiting**
   - **Objective:** Protect your site from DDoS attacks by limiting the number of requests.
   - **Steps:**
     1. Configure rate limiting:
        ```nginx
        http {
            limit_req_zone $binary_remote_addr zone=mylimit:10m rate=10r/s;

            server {
                listen 80;
                server_name your_domain;

                location / {
                    limit_req zone=mylimit burst=20 nodelay;
                    proxy_pass http://backend;
                }
            }
        }
        ```
     2. Test and restart Nginx:
        ```bash
        sudo nginx -t
        sudo systemctl restart nginx
        ```

5. **Setting Up Nginx as a Reverse Proxy**
   - **Objective:** Use Nginx to forward client requests to another server.
   - **Steps:**
     1. Basic reverse proxy configuration:
        ```nginx
        server {
            listen 80;
            server_name your_domain;

            location / {
                proxy_pass http://127.0.0.1:3000;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
            }
        }
        ```
     2. Test and restart Nginx:
        ```bash
        sudo nginx -t
        sudo systemctl restart nginx
        ```

### Practical Tips

1. **Logging and Monitoring**
   - **Objective:** Keep track of access and errors for troubleshooting and analytics.
   - **Steps:**
     1. Configure access and error logs in the server block:
        ```nginx
        server {
            listen 80;
            server_name your_domain;

            access_log /var/log/nginx/access.log;
            error_log /var/log/nginx/error.log;

            location / {
                try_files $uri $uri/ =404;
            }
        }
        ```
     2. View logs:
        ```bash
        sudo tail -f /var/log/nginx/access.log /var/log/nginx/error.log
        ```

2. **Security Hardening**
   - **Objective:** Protect your Nginx server from common security threats.
   - **Tips:**
     1. Disable server tokens to hide Nginx version:
        ```nginx
        http {
            server_tokens off;
        }
        ```
     2. Restrict access to certain locations:
        ```nginx
        location /admin {
            allow 192.168.1.0/24;
            deny all;
        }
        ```
     3. Enable HTTP security headers:
        ```nginx
        add_header X-Content-Type-Options nosniff;
        add_header X-Frame-Options "SAMEORIGIN";
        add_header X-XSS-Protection "1; mode=block";
        ```

3. **Automating Nginx with Ansible**
   - **Objective:** Use Ansible to automate Nginx configuration and management.
   - **Resources:** Provide a simple Ansible playbook example and explain its components.

### Useful Resources

1. **Nginx Documentation**
   - Official Nginx documentation: [nginx.org](https://nginx.org/en/docs/)