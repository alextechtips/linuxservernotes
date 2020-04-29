<h1>How To Create a Self-Signed SSL Certificate for Apache in Ubuntu</h1>

<h3>Create the SSL Certificate</h3>

```
$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt
```

Fill out the prompts appropriately

```
Output
Country Name (2 letter code) [AU]:HK
State or Province Name (full name) [Some-State]:Kowloon
Locality Name (eg, city) []:WTS
Organization Name (eg, company) [Internet Widgits Pty Ltd]:ATT Studio
Organizational Unit Name (eg, section) []:Server Team
Common Name (e.g. server FQDN or YOUR name) []:server_IP_address or testing.com
Email Address []:info@testing.com

```

<h3>Create a strong Diffie-Hellman group</h3>

```
$ sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
```

<h3>Configure Apache to Use SSL</h3>

```
$ sudo touch /etc/apache2/conf-aviliable/ssl-params.conf
```

and edit it like below

```
$ sudo nano /etc/apache2/conf-available/ssl-params.conf
```

```
# from https://cipherli.st/
# and https://raymii.org/s/tutorials/Strong_SSL_Security_On_Apache2.html

SSLCipherSuite EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH
SSLProtocol All -SSLv2 -SSLv3
SSLHonorCipherOrder On
# Disable preloading HSTS for now.  You can use the commented out header line that includes
# the "preload" directive if you understand the implications.
#Header always set Strict-Transport-Security "max-age=63072000; includeSubdomains; preload"
Header always set Strict-Transport-Security "max-age=63072000; includeSubdomains"
Header always set X-Frame-Options DENY
Header always set X-Content-Type-Options nosniff
# Requires Apache >= 2.4
SSLCompression off 
SSLSessionTickets Off
SSLUseStapling on 
SSLStaplingCache "shmcb:logs/stapling-cache(150000)"

SSLOpenSSLConfCmd DHParameters "/etc/ssl/certs/dhparam.pem"
```

<h3>Modify the Default Apache SSL Virtual Host File</h3>

```
$ sudo cp /etc/apache2/sites-available/default-ssl.conf /etc/apache2/sites-available/testing.com-ssl.conf
$ sudo nano /etc/apache2/sites-available/testing.com-ssl.conf
```
edit the file like below
```
<IfModule mod_ssl.c>
        <VirtualHost _default_:443>
                #ServerAdmin webmaster@localhost
                ServerAdmin info@testing.com    <---

                DocumentRoot /var/www/testing.com   <---

                ErrorLog ${APACHE_LOG_DIR}/error.log
                CustomLog ${APACHE_LOG_DIR}/access.log combined

                SSLEngine on

                #SSLCertificateFile      /etc/ssl/certs/ssl-cert-snakeoil.pem
                #SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
                SSLCertificateFile      /etc/ssl/certs/apache-selfsigned.crt    <---
                SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key    <---

                <FilesMatch "\.(cgi|shtml|phtml|php)$">
                                SSLOptions +StdEnvVars
                </FilesMatch>
                <Directory /usr/lib/cgi-bin>
                                SSLOptions +StdEnvVars
                </Directory>

                BrowserMatch "MSIE [2-6]" \                         <---
                               nokeepalive ssl-unclean-shutdown \   <---
                               downgrade-1.0 force-response-1.0     <---

        </VirtualHost>
</IfModule>
```

<h3>Enable the Changes in Apache</h3>
```
$ sudo a2enmod ssl
$ sudo a2enmod headers
```

<h3>Enable our SSL Virtual Host with the a2ensite command</h3>
```
$ sudo a2ensite default-ssl
```

<h3>Enable ssl-params.conf</h3>
```
$ sudo a2enconf ssl-params
```

<h3>Check to make sure that there are no syntax errors in our files</h3>
```
$ sudo apache2ctl configtest
```

<h3>Restart apache</h3>
```
$ sudo systemctl restart apache2
```

<h3>Add redirect to .htaccess file</h3>
```
# BEGIN rlrssslReallySimpleSSL rsssl_version[3.3.1]
<IfModule mod_rewrite.c>
RewriteEngine on
RewriteCond %{HTTPS} !=on [NC]
RewriteRule ^(.*)$ https://%{HTTP_HOST}/$1 [R=301,L]
</IfModule>
# END rlrssslReallySimpleSSL
```
