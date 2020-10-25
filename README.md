# SnapDrop installation guide - with Apache2 on linux

After installing Apache2, move the **snapdrop folder** to the /var/www folder on your server.

## Create the snapdrop server service :

Create a **snapdrop.service** file in /etc/systemd/system with the following content :

    [Unit]
    Description=Snapdrop Startup Service
    After=network.target
    
    [Service]
    ExecStart=/var/www/snapdrop/snapdrop-start.sh
    Restart=always
    RestartSec=5
    
    [Install]
    WantedBy=multi-user.target

Then execute the following lines :

    chmod 700 /var/www/snapdrop/snapdrop-start.sh
    apt-get install dos2unix
    dos2unix /var/www/snapdrop/snapdrop-start.sh
    systemctl enable snapdrop.service
    service snapdrop start

## Create the virtual host in apache2 :
Create a **snapdrop.conf** file in /etc/apache2/sites-available with the following content :

    <VirtualHost *:80>
    	ServerName snapdrop.my-domain.com
    	DocumentRoot /var/www/snapdrop/client
    
    	RewriteEngine on
    	RewriteCond %{HTTP:Upgrade} websocket [NC]
    	RewriteCond %{HTTP:Connection} upgrade [NC]
    	RewriteRule ^/?(.*) "ws://localhost:3000/$1" [P,L]
    </VirtualHost>
    
    <VirtualHost *:443>
    	ServerName snapdrop.my-domain.com
    	DocumentRoot /var/www/snapdrop/client
    
    	RewriteEngine on
    	RewriteCond %{HTTP:Upgrade} websocket [NC]
    	RewriteCond %{HTTP:Connection} upgrade [NC]
    	RewriteRule ^/?(.*) "ws://localhost:3000/$1" [P,L]
    </VirtualHost>

Then execute the following lines:

    a2ensite snapdrop.conf
    service apache2 restart
