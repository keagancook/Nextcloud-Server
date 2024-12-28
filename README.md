# Nextcloud Server Setup and Configuration

## Overview
This repository provides a detailed guide for setting up, configuring, and maintaining a self-hosted Nextcloud server. Nextcloud is an open-source platform for file sharing, collaboration, and productivity, ensuring your data remains private and under your control.

## Features
- **Self-hosted file storage**
- **Calendar and Contacts integration**
- **End-to-end encryption**
- **Collaborative document editing**
- **Customizable with apps**

## Why Nextcloud?
Nextcloud provides a powerful alternative to cloud services like Google Drive, Dropbox, and iCloud, ensuring your data is private and secure. Hosting your Nextcloud server grants full control over your data and allows for robust integrations with other services.

---

## Requirements
### Hardware
- Server or Raspberry Pi with sufficient storage
- Minimum 2GB RAM (4GB recommended for optimal performance)

### Software
- Linux-based operating system (e.g., Ubuntu, Debian)
- Apache/Nginx web server
- PHP 8.0 or higher
- MariaDB/MySQL or PostgreSQL
- SSL certificate (Let's Encrypt or custom)

---

## Installation
### 1. Update System
```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Install Dependencies
```bash
sudo apt install apache2 mariadb-server libapache2-mod-php \
  php php-mysql php-common php-xml php-mbstring \
  php-curl php-zip php-intl php-gd wget unzip -y
```

### 3. Download Nextcloud
```bash
wget https://download.nextcloud.com/server/releases/latest.zip
unzip latest.zip
sudo mv nextcloud /var/www/html/
```

### 4. Set Permissions
```bash
sudo chown -R www-data:www-data /var/www/html/nextcloud
sudo chmod -R 750 /var/www/html/nextcloud
```

### 5. Configure Database
```bash
sudo mysql -u root -p
CREATE DATABASE nextcloud;
CREATE USER 'nextclouduser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextclouduser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### 6. Configure Apache
Create a virtual host configuration file:
```bash
sudo nano /etc/apache2/sites-available/nextcloud.conf
```
Add the following content:
```apache
<VirtualHost *:80>
    ServerAdmin admin@example.com
    DocumentRoot /var/www/html/nextcloud
    ServerName yourdomain.com

    <Directory /var/www/html/nextcloud/>
        Require all granted
        AllowOverride All
        Options FollowSymLinks MultiViews
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/nextcloud_error.log
    CustomLog ${APACHE_LOG_DIR}/nextcloud_access.log combined
</VirtualHost>
```
Enable the configuration and modules:
```bash
sudo a2ensite nextcloud.conf
sudo a2enmod rewrite headers env dir mime setenvif
sudo systemctl restart apache2
```

---

## Configuration
### Access Nextcloud
1. Open a browser and navigate to `http://yourdomain.com`
2. Follow the on-screen instructions to complete the setup.

### Enable SSL (Optional but Recommended)
Use Let's Encrypt for SSL encryption:
```bash
sudo apt install certbot python3-certbot-apache -y
sudo certbot --apache -d yourdomain.com
```
Renew SSL certificates automatically:
```bash
sudo crontab -e
# Add the following line:
0 0 1 * * certbot renew --quiet
```

---

## Maintenance
### Update Nextcloud
Run the updater from the web interface or use the command line:
```bash
sudo -u www-data php /var/www/html/nextcloud/updater/updater.phar
```

### Backup
- **Files**: Use `rsync` to backup the `/var/www/html/nextcloud/data` directory.
- **Database**: Backup with `mysqldump`:
  ```bash
  mysqldump -u root -p nextcloud > nextcloud_backup.sql
  ```

---

## Troubleshooting
### Common Issues
- **500 Internal Server Error**: Check file permissions and PHP version.
- **Database Connection Error**: Verify credentials in `config.php`.

### Logs
- Apache logs: `/var/log/apache2/error.log`
- Nextcloud logs: `/var/www/html/nextcloud/data/nextcloud.log`

---

## Contributing
Contributions are welcome! Feel free to submit issues or pull requests to enhance this guide.

---

## License
This project is licensed under the [MIT License](LICENSE).

---

## Contact
For questions or support, feel free to reach out:
- Email: [youremail@example.com](mailto:youremail@example.com)
- Website: [yourwebsite.com](https://yourwebsite.com)

