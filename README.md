<h1>les pti copier collé facile </h1>

Connect ssh sans password

    ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
.

    ssh-copy-id pi@"IP address or hostname"


    
<h2>openmedia vault</h2>
    
    wget -O - https://github.com/OpenMediaVault-Plugin-Developers/installScript/raw/master/install | sudo bash
 
  
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
    
    CREATE USER 'nextclouduser'@'localhost' IDENTIFIED BY 'Password';
.

    GRANT ALL ON nextcloud.* TO 'nextclouduser'@'localhost' IDENTIFIED BY 'Password' WITH GRANT OPTION;
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
  
  
  
   <h2>emulateur x86<a href="https://github.com/ptitSeb/box86/blob/master/docs/COMPILE.md">lien</a></h2>
  
    git clone https://github.com/ptitSeb/box86
    cd box86
    mkdir build; cd build; cmake .. -DRPI4ARM64=1 -DCMAKE_BUILD_TYPE=RelWithDebInfo
    make -j2
    sudo make install
    sudo systemctl restart systemd-binfmt
  
  
   <h2>teamspeak<a href="https://pimylifeup.com/raspberry-pi-teamspeak/">lien</a></h2>
   
   
   
   
   <h2> docker et portainer<a href="https://www.the-digital-life.com/portainer-ubuntu-tutorial/">lien</a></h2>
   
        
        docker volume create portainer_data
  
  
  .
  
        docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce
  
  
  
  
  
  
  <h2> monage d'un bucket S3/minio en local</h2> 

          sudo s3fs bucket_name /point/de/montage -o umask=0002 -o passwd_file=/bucket/passwd_file,use_path_request_style,url=https://bucket.minio_url.com -o allow_other -o uid=1000 -o gid=1000
  
