<h2>First thing first</h2>
Update your system

sudo apt-get update
sudo apt-get upgrade -y

<h2>Install tasksel</h2>
sudo apt install tasksel
sudo tasksel install lamp-server
sudo apt install php-curl php-gd php-mbstring php-xml php-xmlrpc

<h2>Edit host file to use the domain name</h2>
sudo nano /etc/hosts
>ip.ip.ip.ip [testing.com] [www.testing.com]

<h2>Edit Apache2 Virtual host file</h2>
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/[testing.com].conf
sudo nano /etc/apache2/sites-available/[testing.com].conf

.conf file example
&lt;Directory /var/www/[testing.com]&gt;
    Require all granted
&lt;/Directory&gt;
&lt;VirtualHost *:80&gt;
    ServerName [testing.com]
    ServerAlias www.[testing.com]
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/[testing.com]
&lt;/VirtualHost&gt;

<h2>Make the directory</h2>
sudo mkdir -p /var/www/[testing.com]

<h2>Enable virtual host config</h2>
sudo a2dissite 000-default.conf
sudo a2ensite [testing.com].conf
sudo systemctl reload apache2

<h2>Create Database</h2>
sudo mysql -u root
mysql> CREATE DATABASE database;
mysql> GRANT ALL PRIVILEGES ON database.* TO 'username'@'localhost' IDENTIFIED BY 'password';
mysql> quit

sudo mysql_secure_installation

sudo apt-get update
sudo apt-get install -y phpmyadmin

http://localhost/phpmyadmin/

<h2>Enable apache rewrite for wordpress permalinks</h2>
sudo nano /etc/apache2/apache2.conf

Find
Options Indexes FollowSymLinks
AllowOverride None
Require all granted

Change
AllowOverride None
To
AllowOverride All

sudo a2enmod rewrite
sudo systemctl reload apache2

<h2>Config Apache2 PHP</h2>
sudo nano /etc/php/7.2/apache2/php.ini

max_input_time = 30
upload_max_filesize = 20M
post_max_size = 21M

sudo systemctl reload apache2

<h2>Install WordPress</h2>
sudo wget https://wordpress.org/latest.tar.gz

<h2>Unpack the gz</h2>
tar -xvf latest.tar.gz

<h2>move all files to the site root</h2>
cd wordpress
sudo mv * ..

<h2>remove wordpress folder and gz file</h2>
sudo rm -R wordpress
sudo rm latest.tar.gz

<h2>Copy sample config file</h2>
sudo cp wp-config-sample.php wp-config.php

Edit the database user and password then remove the sample file
go to https://api.wordpress.org/secret-key/1.1/salt/
replace salt secret

<h2>Add Permission to folders</h2>
chown -R www-data:www-data /var/www/[testing.com]/*

sudo systemctl reload apache2

<h2>Finally</h2>
Insatll Theme
+ DIVI Theme - https://www.elegantthemes.com/affiliates/idevaffiliate.php?id=58138_1_1_5
Install Plugins
+ FakerPress - install fake contents
+ UpdraftPlus - backup and restore
+ WP Data Access
