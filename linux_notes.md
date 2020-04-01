<h2>First thing first</h2>

Update your system

<pre><code>
sudo apt-get update
sudo apt-get upgrade -y
</code></pre>

<h2>Install SSH</h2>

<pre><code>
sudo apt install openssh-server
sudo systemctl enable ssh
</code></pre>

Create an authorized keys file

<pre><code>sudo touch ~/.ssh/authorized_keys</code></pre>

Copy over client pub file over and put it into the authorized keys file

<pre><code>sudo cat client.pub >> ~/.ssh/authorized_keys</code></pre>

<h2>Install webmin</h2>

Add Webmin Ubuntu repository

Open /etc/apt/sources.list file to manually add the repository link. In order to open the sources.list file, run the following command as root in terminal:

<pre><code>sudo nano /etc/apt/sources.list</code></pre>

Add the following line in order to add the Webmin repository to the list:

<pre><code>deb http://download.webmin.com/download/repository sarge contrib</code></pre>

The next step is to get the Webmin PGP key for the newly added repository. This way the system will trust this repository. Run the following command to do so:

<pre><code>wget http://www.webmin.com/jcameron-key.asc</code></pre>

Then add the key through the following command:

<pre><code>sudo apt-key add jcameron-key.asc</code></pre>

<h4>Installing Webmin</h4>

<pre><code>
sudo apt update
sudo apt install webmin -y
</code></pre>

Configure Webmin

Stop Webmin from using TLS/SSL

The next step is to tell Webmin to stop using TLS/SSL as we will later configure Apache for this purpose. Run the following command in order to access the miniserv.conf file:

<pre><code>sudo nano /etc/webmin/miniserv.conf</code></pre>

change ssl=1 to ssl=0 at line 10

Restart Webmin

<pre><code>sudo systemctl restart webmin</code></pre>

<h2>Install tasksel</h2>

<pre><code>
sudo apt install tasksel
sudo tasksel install lamp-server
sudo apt install php-curl php-gd php-mbstring php-xml php-xmlrpc
</code></pre>


<h2>Edit host file to use the domain name</h2>
<pre><code>sudo nano /etc/hosts</code></pre>
>ip.ip.ip.ip [testing.com] [www.testing.com]

<h2>Edit Apache2 Virtual host file</h2>
<pre><code>sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/[testing.com].conf
sudo nano /etc/apache2/sites-available/[testing.com].conf</code></pre>

.conf file example
<pre><code>&lt;Directory /var/www/[testing.com]&gt;
    Require all granted
&lt;/Directory&gt;
&lt;VirtualHost *:80&gt;
    ServerName [testing.com]
    ServerAlias www.[testing.com]
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/[testing.com]
&lt;/VirtualHost&gt;</code></pre>

<h2>Make the directory</h2>
<pre><code>sudo mkdir -p /var/www/[testing.com]</code></pre>

<h2>Enable virtual host config</h2>
<pre><code>sudo a2dissite 000-default.conf
sudo a2ensite [testing.com].conf
sudo systemctl reload apache2</code></pre>

<h2>Create Database</h2>
<pre><code>sudo mysql -u root
mysql> CREATE DATABASE database;
mysql> GRANT ALL PRIVILEGES ON database.* TO 'username'@'localhost' IDENTIFIED BY 'password';
mysql> quit</code></pre>

<pre><code>sudo mysql_secure_installation</code></pre>

<h2>Install mySQL client phpmyadmin</h2>

<pre><code>sudo apt-get update
sudo apt-get install -y phpmyadmin</code></pre>

http://localhost/phpmyadmin/

<h2>Enable apache rewrite for wordpress permalinks</h2>
<pre><code>sudo nano /etc/apache2/apache2.conf</code></pre>

Find
Options Indexes FollowSymLinks
AllowOverride None
Require all granted

Change
AllowOverride None
To
AllowOverride All

<pre><code>sudo a2enmod rewrite
sudo systemctl reload apache2</code></pre>

<h2>Config Apache2 PHP</h2>
<pre><code>sudo nano /etc/php/7.2/apache2/php.ini</code></pre>

max_input_time = 30
upload_max_filesize = 20M
post_max_size = 21M

<pre><code>sudo systemctl reload apache2</code></pre>

<h2>Install WordPress</h2>
<pre><code>sudo wget https://wordpress.org/latest.tar.gz</code></pre>

<h2>Unpack the gz</h2>
<pre><code>tar -xvf latest.tar.gz</code></pre>

<h2>move all files to the site root</h2>
<pre><code>cd wordpress
sudo mv * ..</code></pre>

<h2>remove wordpress folder and gz file</h2>
<pre><code>sudo rm -R wordpress
sudo rm latest.tar.gz</code></pre>

<h2>Copy sample config file</h2>
<pre><code>sudo cp wp-config-sample.php wp-config.php</code></pre>

Edit the database user and password then remove the sample file
go to https://api.wordpress.org/secret-key/1.1/salt/
replace salt secret

<h2>Add Permission to folders</h2>
<pre><code>chown -R www-data:www-data /var/www/[testing.com]/*
sudo systemctl reload apache2</code></pre>

<h2>Config Let’s Encrypt cert</h2>

Add repository:

<pre><code>sudo add-apt-repository ppa:certbot/certbot</code></pre>

Install Certbot’s Apache package with apt:
<pre><code>sudo apt install python-certbot-apache</code></pre>

verify the syntax of apache configuration file:
<pre><code>sudo apache2ctl configtestsudo apache2ctl configtest</code></pre>

Restart apache services
<pre><code>sudo systemctl reload apache2</code></pre>

<h2>Config Firewall</h2>
<pre><code>sudo ufw status</code></pre>

<h5>Add rules</h5>
<pre><code>
sudo ufw allow 'Apache Full'
sudo ufw allow ssh
sudo ufw allow 10000
</code></pre>

<h5>Remove rules</h5>
<pre><code>
sudo ufw delete allow 'Apache'
sudo ufw delete allow 'ssh'
sudo ufw delete allow 10000
</code></pre>

<h5>Enable ufw</h5>
<pre><code>sudo ufw enable</code></pre>

Verifying Certbot Auto-Renewal
<pre><code>sudo certbot renew --dry-run</code></pre>

<h2>Install and config Self sign certificate</h2>
Enable Apache SSL module
<pre><code>
sudo a2enmod ssl
sudo service apache2 restart
</code></pre>
Generate self sign certificate
<pre><code>
sudo mkdir /etc/apache2/ssl
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt
</code></pre>

<h2>Edit Apache Site conf file</h2>
<pre><code>
cd /etc/apache2/sites-available
sudo cp default-ssl.conf example-ssl.conf
</code></pre>
Add SSLCertificateFile and SSLCertificateKeyFile path
<pre><code>
SSLCertificateFile /etc/apache2/ssl/apache.crt
SSLCertificateKeyFile /etc/apache2/ssl/apache.key
</code></pre>
Enable SSL site
<pre><code>
sudo a2ensite example-ssl.conf
sudo service apache2 restart
</code></pre>

<hr>

<h2>Reset mySQL root password</h2>

<pre><code>
sudo /etc/init.d/mysql stop
sudo mkdir /var/run/mysqld
sudo chown mysql /var/run/mysqld
sudo mysqld_safe --skip-grant-tables&
</code></pre>
<pre><code>
sudo mysql --user=root mysql
mysql> update user set authentication_string=PASSWORD('your_password_here') where user='root';
mysql> update user set Password=PASSWORD('your_password_here') where user='root';
mysql> update user set plugin="mysql_native_password" where User='root';
mysql> flush privileges;
mysql> exit
</code></pre>
<pre><code>
sudo killall -u mysql
sudo /etc/init.d/mysql start
</code></pre>
<pre><code>sudo mysql -p -u root</code></pre>

<h2>Install Indicator SysMonitor</h2>

<pre><code>
sudo add-apt-repository ppa:fossfreedom/indicator-sysmonitor
sudo apt update
sudo apt install indicator-sysmonitor
</code></pre>

<h2>Glances system monitor</h2>
<pre><code>
sudo curl -L https://bit.ly/glances | /bin/bash
</code></pre>
or
<pre><code>
sudo wget -O- https://bit.ly/glances | /bin/bash
</code></pre>
or PyPI the simple way
<pre><code>
sudo pip install glances
</code></pre>
