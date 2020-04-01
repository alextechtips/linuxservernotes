<h2>Preface</h2>
I like to build my own virtual machine for local developing my WordPress sites, using this way I can run the virtual machine under any operating system.
You can also try <a href="https://www.apachefriends.org/index.html" rel="noopener noreferrer" target="_blank">XAMPP</a> or <a href="https://localwp.com/" rel="noopener noreferrer" target="_blank">local by flywheel</a>
but building my own VMs is more stable, you can follow the steps below to build your own web server to run WordPress locally.
I like to use Ubuntu Desktop for my base environment, you can use any linux distro of your choice (Just change the package manager command)

<h2>First thing first</h2>
Update your system

<pre><code>sudo apt-get update
sudo apt-get upgrade -y</code></pre>

<h2>Install tasksel</h2>

<pre><code>sudo apt install tasksel
sudo tasksel install lamp-server
sudo apt install php-curl php-gd php-mbstring php-xml php-xmlrpc</code></pre>


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

<h2>Finally</h2>
Insatll Theme
+ DIVI Theme - https://www.elegantthemes.com/affiliates/idevaffiliate.php?id=58138_1_1_5
Install Plugins
+ FakerPress - install fake contents
+ UpdraftPlus - backup and restore
+ WP Data Access
