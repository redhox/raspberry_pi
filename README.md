<h1>base d'un home server sur raspberry_pi</h1>

    sudo apt update
    sudo apt upgrade
.    
    
    sudo apt install apache2 php libapache2-mod-php mariadb-server php-mysql
.  

    sudo apt install  php-common php-curl php-gd php-intl php-json php-mbstring php-xml php-zip php-imagick php-intl php-apcu php-redis php-http-request

mariadb install
       
    sudo mysql_secure_installation
.

    sudo systemctl restart mariadb.service
    
<h2>openmedia vault</h2>
    
    wget -O - https://github.com/OpenMediaVault-Plugin-Developers/installScript/raw/master/install | sudo bash
 
 <h2>jellyfin</h2>
    
    curl -fsSL https://repo.jellyfin.org/ubuntu/jellyfin_team.gpg.key | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/jellyfin.gpg
.

    echo "deb [arch=$( dpkg --print-architecture )] https://repo.jellyfin.org/$( awk -F'=' '/^ID=/{ print $NF }' /etc/os-release ) $( awk -F'=' '/^VERSION_CODENAME=/{ print $NF }' /etc/os-release ) main" | sudo tee /etc/apt/sources.list.d/jellyfin.list
  
  <h2>vpn wireguard <a href="https://github.com/Nyr/wireguard-install">lien</a> et <a href="https://youtu.be/rtUl7BfCNMY">video</a></h2>
    
    wget https://git.io/wireguard -O wireguard-install.sh && bash wireguard-install.sh
  .
  
    sudo bash wireguard-install.sh
  ecrire votre nom de domain ou ip
  choisir 1.1.1.1 en dns
  relancer "sudo bash wireguard-install.sh" pour cree d'autre client
  
  <h2>nextcloud <a href="https://medium.com/@loneauios/how-to-install-nextcloud-on-your-raspberry-pi-4-c20dfcbc45a7">lien</a> </h2>
  
    sudo mysql
.

    CREATE DATABASE nextcloud;
.    
    
    CREATE USER ‘nextclouduser’@’localhost’ IDENTIFIED BY ‘Password’;
.

    GRANT ALL ON nextcloud.* TO ‘nextclouduser’@’localhost’ IDENTIFIED BY ‘Password’ WITH GRANT OPTION;
.
    
    FLUSH PRIVILEGES;
    EXIT;
  
.
 
    sudo nano /etc/php/7.3/apache2/php.ini
.

    post_max_size = 2048M
.
    
    upload_max_filesize = 2048M
  
  Restart apache
    
    sudo a2enmod rewrite
    sudo service apache2 restart
.

    cd /var/www/html

nexcloud derniere version <a href="https://download.nextcloud.com/server/releases/">lien</a> ici la 24.0.1
  
    curl https://download.nextcloud.com/server/releases/nextcloud-24.0.1.tar.bz2 | sudo tar -jxv
.

    sudo chown -R www-data:www-data /var/www/html/nextcloud/
  
  http://ip-de-la-machine/nextcloud
  nom et password du compte admin nexcloud
  nextclouduser
  Password
  nexcloud
  localhost:3306
  
 edit /var/www/html/nextcloud/config/config.php avec le nom de domaine et/ou ip pour avoir l'acsede depuis le web
 
 <h2>ssl et https</h2>
    
    install snap
.
 
    sudo apt install snapd
.
    
     sudo snap install core; sudo snap refresh core
 .
 
    sudo snap install --classic certbot
  .
  
    sudo certbot --apache
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
