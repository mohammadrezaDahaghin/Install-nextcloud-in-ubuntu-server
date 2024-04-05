# Install nextcloud in ubuntu server

## **Install Required Packages**

```bash
apt update && apt upgrade
```

- install Apache and MySQL Server

```bash
apt install apache2 mariadb-server 
```

- Install PHP and other Dependencies and Restart Apache

```bash
apt install libapache2-mod-php php-bz2 php-gd php-mysql php-curl php-mbstring php-imagick php-zip php-ctype php-curl php-dom php-json php-posix php-bcmath php-xml php-intl php-gmp zip unzip wget
```

- Enable required Apache modules and restart Apache:

```bash
 a2enmod rewrite dir mime env headers
 systemctl restart apache2
```

## **Configure MySQL Server**

- Login to MySQL Prompt, Just type

```bash
mysql
```

- Create MySQL Database and User for Nextcloud and Provide Permissions.

```bash
CREATE USER 'nextcloud'@'localhost' IDENTIFIED BY 'passw@rd';
CREATE DATABASE IF NOT EXISTS nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextcloud'@'localhost';
FLUSH PRIVILEGES;
quit;
```

## **Install NextCloud From the Command Line**

- Run the CLI Command

```bash
cd /var/www/nextcloud
sudo -u www-data php occ  maintenance:install --database \
"mysql" --database-name "nextcloud"  --database-user "nextcloud" --database-pass \
"passw@rd" --admin-user "admin" --admin-pass "admin123"

```

- nextcloud allows access only from localhost, it could through error ***“Access through untrusted domain”***. we need to allow accessing nextcloud by using ip or domain name.

```bash
vim /var/www/nextcloud/config/config.php

  'trusted_domains' =>
  array (
    0 => 'localhost',
    1 => 'nc.mailserverguru.com',     // we Included the Sub Domain
  ),

  .....
```

- Configure Apache to load Nextcloud from the **/var/www/nextcloud** folder.

```bash
vim /etc/apache2/sites-enabled/000-default.conf

<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/nextcloud    // Chan
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

# Now, Restart Apache Server

systemctl restart apache2
```