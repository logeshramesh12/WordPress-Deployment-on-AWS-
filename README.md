# WordPress Deployment on AWS EC2

This project demonstrates how to deploy a WordPress website on AWS EC2 using the AWS Free Tier with Ubuntu Server, Apache, MySQL, PHP, Route 53, and HTTPS using Let's Encrypt.

---

# Project Overview

This documentation covers:

- Launching an AWS EC2 instance
- Installing Apache, MySQL, and PHP (LAMP Stack)
- Configuring WordPress
- Connecting a custom domain using Route 53
- Securing the website with SSL (HTTPS)

---

# Tech Stack

- AWS EC2 (Ubuntu 22.04 LTS)
- Apache2
- MySQL
- PHP
- WordPress
- Route 53
- Certbot (Let's Encrypt SSL)

---

# Architecture

```text
User Browser
      ↓
Route 53 Domain DNS
      ↓
AWS EC2 Instance
      ↓
Apache Web Server
      ↓
WordPress + MySQL
```

---

# Step 1: Launch EC2 Instance

AWS Console → EC2 → Launch Instance

## Configuration

- AMI: Ubuntu Server 22.04 LTS
- Instance Type: t2.micro (Free Tier)

## Security Group Rules

| Type | Port | Source |
|------|------|--------|
| SSH | 22 | My IP |
| HTTP | 80 | Anywhere |
| HTTPS | 443 | Anywhere |

---

# Step 2: Install Apache, MySQL & PHP

## Update System

```bash
sudo apt update && sudo apt upgrade -y
```

## Install Apache

```bash
sudo apt install apache2 -y
```

## Install MySQL

```bash
sudo apt install mysql-server -y
```

## Install PHP & Extensions

```bash
sudo apt install php libapache2-mod-php php-mysql php-curl php-gd php-xml php-mbstring php-xmlrpc php-soap php-intl php-zip unzip curl -y
```

## Restart Apache

```bash
sudo systemctl restart apache2
```

---

# Step 3: Configure MySQL Database

## Login to MySQL

```bash
sudo mysql
```

## Create Database & User

```sql
CREATE DATABASE wordpress;

CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'StrongPassword123!';

GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost';

FLUSH PRIVILEGES;

EXIT;
```

---

# Step 4: Download & Install WordPress

```bash
cd /tmp

curl -O https://wordpress.org/latest.tar.gz

tar -xvf latest.tar.gz

sudo mv wordpress /var/www/html/
```

---

# Step 5: Configure Permissions

```bash
sudo chown -R www-data:www-data /var/www/html/wordpress

sudo chmod -R 755 /var/www/html/wordpress
```

---

# Step 6: Configure Apache Virtual Host

## Create Configuration File

```bash
sudo nano /etc/apache2/sites-available/wordpress.conf
```

## Add Configuration

```apache
<VirtualHost *:80>
    ServerAdmin admin@example.com

    DocumentRoot /var/www/html/wordpress

    <Directory /var/www/html/wordpress>
        AllowOverride All
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

## Enable Site & Rewrite Module

```bash
sudo a2ensite wordpress.conf

sudo a2enmod rewrite

sudo systemctl reload apache2
```

---

# Step 7: Configure WordPress

```bash
cd /var/www/html/wordpress

cp wp-config-sample.php wp-config.php

sudo nano wp-config.php
```

## Update Database Details

```php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wpuser');
define('DB_PASSWORD', 'StrongPassword123!');
define('DB_HOST', 'localhost');
```

## Generate WordPress Secret Keys

```bash
curl -s https://api.wordpress.org/secret-key/1.1/salt/
```

Copy and replace the authentication keys in `wp-config.php`.

---

# Step 8: Verify Installation

Open browser:

```text
http://YOUR_ELASTIC_IP
```

Complete the WordPress setup wizard:

- Site Title
- Username
- Password
- Email

---

# Step 9: Connect Custom Domain Using Route 53

## Create Hosted Zone

Go to:

```text
AWS Route 53 → Hosted Zone
```

## Create A Record

| Record Type | Name | Value |
|-------------|------|-------|
| A | logesh.online | YOUR_ELASTIC_IP |

Update your domain nameservers with Route 53 nameservers.

---

# Step 10: Enable HTTPS with Let's Encrypt

## Install Certbot

```bash
sudo apt install certbot python3-certbot-apache -y
```

## Configure SSL

```bash
sudo certbot --apache
```

Follow the prompts:

- Enter Email
- Accept Terms
- Enter Domain Name
- Choose Redirect HTTP → HTTPS

---

# Step 11: Verify HTTPS

Open:

```text
https://logesh.online
```

You should see a secure HTTPS connection enabled.

---

# Project Outcome

Successfully deployed a secure WordPress website on AWS EC2 using:

- AWS Free Tier
- Custom Domain
- Route 53 DNS
- HTTPS SSL Certificate

---

# Folder Structure

```text
WordPress-Deployment-on-AWS/
│
├── README.md
```

---

# Future Improvements

- Configure automated backups
- Setup CloudFront CDN
- Add monitoring with CloudWatch
- Use RDS instead of local MySQL
- Configure Auto Scaling

---

# Author

## Logesh

- Domain: logesh.online
- AWS Cloud & DevOps Enthusiast

---

# License

This project is for learning and educational purposes.
