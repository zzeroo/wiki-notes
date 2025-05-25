## Step 1: Install Required Dependencies

The first step is to install required dependencies for Snipe IT. Run the following command to update the package manager:

```
sudo apt update
```

Then, install the required dependencies by running the following command:

```
sudo apt install git curl
```

## Step 2: Install Apache Web Server

Snipe IT requires a web server to run. In this tutorial, we will use the Apache web server to serve Snipe IT. Run the following command to install Apache web server:

```
sudo apt install apache2
```

Once the installation is completed, you need to enable Apache and start the service by running the following commands:

```
sudo systemctl enable apache2
sudo systemctl start apache2
```

https://www.server-world.info/en/note?os=Debian_12&p=httpd&f=3

## Step 3: Install PHP

```
apt install php-common libapache2-mod-php php-cli
```

```
sudo apt install php8.2 libapache2-mod-php8.2 php8.2-common php8.2-cli php8.2-curl php8.2-mbstring php8.2-mysql php8.2-xml php8.2-zip php8.2-bcmath php8.2-gd
```

### 3.1: Optional "imagick"

```
sudo apt install imagemagick
```


### Step 4: MariaDB

```
sudo apt install mariadb-server
```

```
sudo systemctl enable mariadb.service
```

#### Secure the MariaDB Server

MariaDB includes insecure defaults such as test databases and user authentication is not active by default. Follow the steps below to enable authentication, remove insecure defaults, and secure the MariaDB server.

```
sudo mariadb-secure-installation
```

- Press Enter to proceed with the default `root` database user password.

```
Enter current password for root (enter for none):
```

- Press N and press Enter to enable password authentication when prompted to switch to Unix socket authentication,

```
Switch to unix_socket authentication [Y/n]
```

- Press Y and then Enter to change the default `root` database user password.

```
Change the root password? [Y/n]
```

- Press Y + Enter to remove anonymous users.

```
Remove anonymous users? [Y/n]
```

- Disable remote access to the root database user.

```
Disallow root login remotely? [Y/n]
```

- Remove the default test database.

```
Remove test database and access to it? [Y/n]
```

- Reload privileges to save changes.

```
Reload privilege tables now? [Y/n]
```

- Restart the MariaDB service to apply your configuration changes.

```
sudo systemctl restart mariadb
```

https://docs.vultr.com/how-to-install-mariadb-on-debian-12

#### Create SnipeIT database and user

```
CREATE DATABASE snipeit;
```

```
CREATE USER snipeit@localhost IDENTIFIED BY '';
```

```
GRANT ALL PRIVILEGES ON snipeit.* to snipeit@localhost;
```

```
FLUSH PRIVILEGES;
```

The PHP connectors for MySQL generally work with MariaDB as well.
https://mariadb.com/kb/en/php/

## Step 5: Install Snipe IT

After installing the required dependencies, you can now install Snipe IT. Run the following command to clone the Snipe IT repository:

```
sudo git clone https://github.com/snipe/snipe-it.git /var/www/html/snipe-it
```

Once the cloning is completed, navigate to the snipe-it directory and copy the .env.example to .env file:

```
cd /var/www/html/snipe-it
sudo cp .env.example .env
```

Update the .env file with your database credentials:

```
DB_HOST=localhost
DB_DATABASE=snipeit
DB_USERNAME=your_database_username
DB_PASSWORD=your_database_password
```


Update the .env file with additional details:
%% `APP_URL` is important. Without this setup the composer command below trows an error: https://github.com/snipe/snipe-it/issues/11213 %%

```
APP_URL=https://snipeit
APP_TIMEZONE='Europe/Berlin'
APP_LOCALE='en-GB'
```


Next, install composer by running the following command:

```
sudo curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
```

Then, run the following command to install the required dependencies:

```
sudo composer install --no-dev --prefer-source
```

Once the installation is completed, run the following command to generate an application key:

```
sudo php artisan key:generate
```

Finally, run the following command to set the correct permissions for the Snipe IT installation:

```
sudo chown -R www-data:www-data /var/www/html/snipe-it
sudo chmod -R 755 /var/www/html/snipe-it
```


## Step 6: Configure Apache for Snipe IT

SnipeIT need Apache's `mod_rewrite` module:
https://www.xmodulo.com/how-to-enable-mod_rewrite-in-apache2-on-debian-ubuntu.html

Debian systems, it has `mod_rewrite` module installed, but not enabled by default.

```
sudo a2enmod rewrite
```

The final step is to configure Apache web server for Snipe IT. Open the default Apache configuration file using a text editor:

```
sudo vim /etc/apache2/sites-available/000-default.conf
```

Add the following lines to the configuration file:

```
<VirtualHost *:80>
        DocumentRoot /var/www/html/snipe-it/public
        Redirect "/" "https://snipeit.zzeroo.lan/"
</VirtualHost>
```

```
sudo vim /etc/apache2/sites-available/000-default.conf
```

Add the following lines to the configuration file:

```
<VirtualHost *:443>
        ServerAdmin webmaster@zzeroo.com
        <Directory /var/www/html/snipe-it/public>
                Allow From All
                AllowOverride All
                Options -Indexes
        </Directory>

        DocumentRoot /var/www/html/snipe-it/public
        ServerName snipeit.zzeroo.lan
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
        SSLEngine on
        SSLCertificateFile      /etc/ssl/certs/v-snipeit-01-zzeroo-lan.pem
        SSLCertificateKeyFile   /etc/ssl/private/v-snipeit-01-zzeroo-lan.key
        <FilesMatch "\.(?:cgi|shtml|phtml|php)$">
                SSLOptions +StdEnvVars
        </FilesMatch>
        <Directory /usr/lib/cgi-bin>
                SSLOptions +StdEnvVars
        </Directory>
</VirtualHost>
```

Finally, restart the Apache web server by running the following command:

```
sudo systemctl restart apache2
```




https://snipe-it.readme.io/docs/requirements
https://www.php.net/manual/en/install.unix.debian.php
https://snipeit.zzeroo.lan/
