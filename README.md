## Setting-Up-SSL-for-Nginx-on-Ubuntu-AlmaLinux-with-Certbot

### 1. Install Certbot and Dependencies
Certbot is used to obtain and manage SSL certificates.

#### For AlmaLinux:
```
sudo dnf install epel-release -y
sudo dnf install certbot python3-certbot-nginx -y

```
#### For Ubuntu:
```
sudo apt update
sudo apt install certbot python3-certbot-nginx -y
```
### 2. Configure Nginx

Ensure your Nginx configuration is properly set up to serve your site.
- Locate your Nginx configuration file. For example
```
sudo nano /etc/nginx/conf.d/your-site.conf

```
Or, for a specific site:
```
sudo nano /etc/nginx/sites-available/yourdomain.com
```
- Update the configuration to include your domain:
```
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    root /path/to/your/site;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
- Save and test the configuration:
```
sudo nginx -t
sudo systemctl reload nginx
```

### 3. Obtain SSL Certificate
Run the following command to obtain an SSL certificate for your domain:
```
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```
- Certbot will automatically update your Nginx configuration to use SSL.
- If prompted, select the option to redirect HTTP to HTTPS.

### 4. Verify SSL Configuration
Check the SSL configuration file that Certbot added:
```
sudo nano /etc/nginx/conf.d/your-site.conf
```
It should include:
```
server {
    listen 443 ssl;
    server_name yourdomain.com www.yourdomain.com;

    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

    root /path/to/your/site;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}

server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    return 301 https://$host$request_uri;
}
```
### 5. Restart Nginx
Reload Nginx to apply changes:
```
sudo systemctl reload nginx

```
### 6. Automate Certificate Renewal
Certbot sets up a cron job or systemd timer to automatically renew certificates. Test renewal with:
```
sudo certbot renew --dry-run
```
### 7. Test SSL
Visit your site using https://yourdomain.com to confirm the SSL certificate is working.
You can also test your SSL configuration using an online tool like ([SSL Labs](https://www.ssllabs.com/ssltest/)).