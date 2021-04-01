# RPi Homeserver 
<img src="https://www.raspberrypi.org/wp-content/uploads/2011/10/Raspi-PGB001-300x267.png" width="100" height="89">

What is this 	: An absolute beginners guide to get a Rpi up and running, hosting multiple servcies for an essential home server. 
Who is this for?: Anyone who is starting up with Docker, Anyone who is interested in home automation and a DIY server solution but doesn't want to spend a fortune.
What this isn't	: The most advanced guide, The best guide available.

I will focus on things that needs to be modifed/personalised to get them working. As a lot of these solution are already avialable for Rpi and Docker seperately. But doing them together sometimes introduces wierd issues.
This is probable onyl Part-1 of a multi part series, we will just be focusing on installation and getting these services running. The 2nd part would probably be setting up the frontend and general usage. And a 3rd would be advanced stuff which I don't have a clear plan for yet.


## Let's get started. 

Let's start with what OS you want to go ahead with. I have chosen Ubuntu 20.04 for my installtion just because of it's widespread community and ease of use. (But this turned out to be not the greatest chioce since Rpi OS is still the only OS which support some nifty functions like HWA,but more on that later). But Ubuntu should work for 99% of users.

  ### Installing OS
  <img src="https://www.shareicon.net/data/256x256/2015/07/27/75996_ubuntu_256x256.png" width="75" height="75">

1. Download the latest image from https://ubuntu.com/download/raspberry-pi
2. Download the Raspberry Pi Imager from https://www.raspberrypi.org/software/
3. Use a good Class 10 or UHS Class 1 card for your OS as this would have a big impact on the overall snapiness of your system.
4. The installer is pretty straightforward and you just select the image and disk and jsut hit install .

## Installing Docker. 
<img src="https://cdn.iconscout.com/icon/free/png-256/social-275-116309.png" width="75" height="75">

> Before you begin.
 As of writing this, there is something wrong with arm64 branch the docker repo. Which doesn't let you install using the repo. So you have to manually download the packages and install them using the guide https://docs.docker.com/engine/install/ubuntu/#install-from-a-package. But it seems like a pretty trivial issue and should be fixed pretty soon.So do check it out before you start.

>  One more important thing is using root user for Docker. Because docker(And the servies we will be using) will eventually require you to have user with all the privileges of a root user, so if you don't want the hassle of service users then I'd suggest using root. 

1. Go to https://download.docker.com/linux/ubuntu/dists/focal/pool/stable/arm64/ and download the follwoing files: #Make sure you use the correct version based on your release like focal,bionic,xenial etc.
  - containerd.io_<lates_version>_arm64.deb
  - docker-ce-cli_<lates_version>_arm64.deb
  - docker-ce_<lates_version>_arm64.deb
     ```shell
          mkdir docker
          cd docker
          wget https://download.docker.com/linux/ubuntu/dists/focal/pool/stable/arm64/containerd.io_1.4.4-1_arm64.deb
     ```
2. Once downloaded, you can run the ```sudo dpkg -i *``` to install all 3 packages. 
3. And you're done!.... Not really. We still need to install `docker-compose` but for now, Verify the install by runnning ```sudo docker run hello-world```
4. Okay so the last step here is to install [docker-compose](https://docs.docker.com/compose/install/#install-using-pip). Again because the repo for arm64 is a little borked. You would have to install it with pip. For which you need Python(3).
5. Go ahead and install pyton3, pip and any other required dependecies listed here [Official Doc](https://docs.docker.com/compose/install/#install-using-pip).
6. Then just run `sudo pip install docker-compose`. 
7. That's it. For real this time. Verify by running `docker-compose --version` .


Now that we have docker up and running, we can go ahead and start installing our services, which is as simple as creating a `docker-compose.yml` file with all the details and  letting docker do all the heavy lifting :).

## Installing Owncloud Storage server
<img src="https://cloudash64.net/owncloud/core/img/favicon-touch.svg" width="75" height="75">

1. Head over to the official Owncloud installtion [page](https://doc.owncloud.com/server/admin_manual/installation/docker/) and give it a quick read.
2. Setup up a project directory for Owncloud ```cd mkdir owncloud && cd owncloud```.
3. Create a [docker-compose.yml](Cloudstorage/docker-compose.yml) file as attached/or as in the Official docs.
```yaml
version: '2.1'  # Everything is default from the owncloud git other than the storage mount, which is commented below.

volumes:
  files:
    driver: local
  mysql:
    driver: local
  backup:
    driver: local
  redis:
    driver: local

services:
  owncloud:
    image: owncloud/server:${OWNCLOUD_VERSION}
    restart: always
    ports:
      - ${HTTP_PORT}:8080  # Yoo don't need to touch this eventhough you have other services running on 8080. The .env handles the HTTP port which then automatically binds to 8080 internally.
    depends_on:
      - db
      - redis
    environment:   # Change the default passwords as per your requirement.
      - OWNCLOUD_DOMAIN=${OWNCLOUD_DOMAIN}
      - OWNCLOUD_DB_TYPE=mysql
      - OWNCLOUD_DB_NAME=owncloud
      - OWNCLOUD_DB_USERNAME=owncloud
      - OWNCLOUD_DB_PASSWORD=owncloud
      - OWNCLOUD_DB_HOST=db
      - OWNCLOUD_ADMIN_USERNAME=${ADMIN_USERNAME}
      - OWNCLOUD_ADMIN_PASSWORD=${ADMIN_PASSWORD}
      - OWNCLOUD_MYSQL_UTF8MB4=true
      - OWNCLOUD_REDIS_ENABLED=true
      - OWNCLOUD_REDIS_HOST=redis
    healthcheck:
      test: ["CMD", "/usr/bin/healthcheck"]
      interval: 30s
      timeout: 10s
      retries: 5
    volumes:
      - files:/mnt/data
      - /mnt/ext_hdd:/mnt/ext_storage   # This will be your actual storage for the Cloudserver. I have mounted a 4TB external USB 3.0 HDD [IMP: Please automount your disks using etc/fstab before doing this]
  db:
    image: webhippie/mariadb:latest
    restart: always
    environment:
      - MARIADB_ROOT_PASSWORD=owncloud
      - MARIADB_USERNAME=owncloud
      - MARIADB_PASSWORD=owncloud
      - MARIADB_DATABASE=owncloud
      - MARIADB_MAX_ALLOWED_PACKET=128M
      - MARIADB_INNODB_LOG_FILE_SIZE=64M
    healthcheck:
      test: ["CMD", "/usr/bin/healthcheck"]
      interval: 30s
      timeout: 10s
      retries: 5
    volumes:
      - mysql:/var/lib/mysql
      - backup:/var/lib/backup

  redis:
    image: webhippie/redis:latest
    restart: always
    environment:
      - REDIS_DATABASES=1
    healthcheck:
      test: ["CMD", "/usr/bin/healthcheck"]
      interval: 30s
      timeout: 10s
      retries: 5
    volumes:
      - redis:/var/lib/redis
```
4. Create an [.env](Cloudstorage/.env) file as attached/ or as in the official docs.
```shell
OWNCLOUD_VERSION=10.7
OWNCLOUD_DOMAIN=localhost:8080
ADMIN_USERNAME=admin
ADMIN_PASSWORD=admin
HTTP_PORT=8090 # This is where we are handlign the port binding. So that you don't need to modify the docker-compose . You can just use any free port and pass it here so that it                     will bind to the 8080 port of the owncloud conatiner.

```
5. Please go thorugh the comments I have added as it might save you some headache later on. Only if you are an abolute noob like I am.
6. Once you have your files ready. Go ahead and run ```sudo docker-compose up -d``` from the same directory.
7. That's it. Well mostly. You should now have the following containers running which you can check by running ```sudo docker ps -a```
      - owncloud_owncloud_1 # the service and the webserver.
      - owncloud_redis_1 # The Redis container.
      - owncloud_bd_1 # The MySQL container. 
8. Now you are almost done. But there is one thing you need to do if you want to host some local storage. Owncloud does not enable this by default and needs to be manually added as mentioned [here](https://doc.owncloud.com/server/admin_manual/configuration/files/external_storage/local.html)
9. Once you have your containers running log into the main owncloud service conatiner with the follwoing command :
	- ```sudo docker exec -it owncloud_owncloud_1 bash```
10. Once inside you need to navigate to the `/var/www/owncloud/config/config.php`. But your bash should have already landed you in the home dir i.e. `/var/www/owncloud`
11. So you can just `vi config/config.php` and append this line `'files_external_allow_create_new_local' => 'true',` as you see [here](Cloudstorage/config.php)
```php
  'logtimezone' => 'UTC',
  'installed' => true,
  'instanceid' => 'oc5p34bmso83',
  'files_external_allow_create_new_local' => 'true',
);
```
12. Once done, you can exit from the container. And back in your project directory, just run the `docker-compose.yml` to recreate the container with the updated values.
13. Since we have mentioned 8090 in the .env file above, you can now login to http://<dockerhost>:8090 and shoudl be able to see the login screen.


## Installing Jellying Media Server
<img src="https://developer.asustor.com/uploadIcons/0020_999_1568614457_Jellyfin_256.png" width="75" height="75">

1. This process is very similar to Owncloud. Start by heading to the Official [Jellyfin Docs](https://jellyfin.org/docs/general/administration/installing.html) and glance it over.
2. Create a Project directoy for Jellyfin just like you did for Owncloud ```cd mkdir jellyfin && cd jellyfin```.
3. Create a [docker-compose.yml](mediaserver/docker-compose.yml) file as attached/or as in the Official docs.
```yaml
version: "3.5"
services:
  jellyfin:
    image: jellyfin/jellyfin
    container_name: jellyfin
    user: root:root
    network_mode: "host"
    volumes:
      - jellyfin-config:/config    # These volumees can either be created before and passed (as we have done here, refer the volumes section at the bottom) OR you can create them during the docker-compose run.
      - jellyfin-cache:/cache
      - /mnt/ext_hdd/:/media       # This will be your media storage. In my case it is same as my external HDD serving the cloud server. Modify this accordingly. The syntax is soruce:destination as in <host path>:<container path>.
      - /mnt/ext_hdd/rpi_Scratchdisk:/config/transcodes  # This is required only if you are transcoding your media files, transcoding is very intense on the I/O and can kill  a flash drive overtime. So using my USB 3.0 HDD as a scratch disk to handle temp transcoding writes as it is much more resilient to random read/writes. Will post a better solution in future when I try something like a Ramdisk with mergerfs.
    devices:
      - /dev/vchiq:/dev/vchiq  # This is only specific for a Rpi. Other devices would use VAAPI and for that the respective render devices would have to be forwarded .
    restart: "unless-stopped"
    # Optional - alternative address used for autodiscovery
    environment:
      - JELLYFIN_PublishedServerUrl=http://example.com
volumes:
  jellyfin-config:
    external: true  # this tag is only needed is passing volumes created before hand. Otherwise this can be omitted.
  jellyfin-cache:
    external: true
```
>  I'd suggest you go through the comments added in the above file for this one as it has some changes that are necessary for proper fucntioning.
4. Now we can create volumes (Not mandatory, docker-compose can create them during startup. But I had created them already so I'm passing them externally, refer the volumes section at the bottom of the [docker-compose.yml](mediaserver/docker-compose.yml) file. )
```
	- docker volume create jellyfin-config
	- docker volume create jellyfin-cache	
```
6. Once the file is ready run ```sudo docker-compose up -d```.
7. Bingo!. Verify that everything is working as epxpected ```sudo docker ps -a```.
8. Your Jellyfin Media server is now up and running. check it out on http://<localhost>:8096


## Installing Pi-Hole
<img src="https://developer.asustor.com/uploadIcons/0020_110807_1589822880_icon%20256.png" width="75" height="75">

1. This is probably the simplest of them all, but still give the [Official Doc](https://github.com/pi-hole/docker-pi-hole/#running-pi-hole-docker) a quick read.
2. Create a Project directoy for Pihole like you did earlier ```cd mkdir pihole && cd pihole```.
3. Create a [docker-compose.yml](pihole/docker-compose.yml) file as attached or as in the Official Git.
```yaml
version: "3"

# https://github.com/pi-hole/docker-pi-hole/blob/master/README.md

services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    # For DHCP it is recommended to remove these ports and instead add: network_mode: "host"
    ports:     # I am not running DHCP on the pihole , just DNS so leaving them as it is. Problem with most Indian routers is that they do not allow your DNS and LAN IPs to be in the same subnet :( . Which is stupid, but that's how it is. 
      - "53:53/tcp"
      - "53:53/udp"
      - "67:67/udp"
      - "80:80/tcp"
      - "443:443/tcp"
    environment:
      TZ: 'America/Chicago'  # Change this according to your region. Otherwise debugging can become a headache.
      # WEBPASSWORD: 'set a secure password here or it will be random'
    # Volumes store your data between container upgrades
    volumes:
      - './etc-pihole/:/etc/pihole/'
      - './etc-dnsmasq.d/:/etc/dnsmasq.d/'
      # run `touch ./var-log/pihole.log` first unless you like errors
      # - './var-log/pihole.log:/var/log/pihole.log'
    # Recommended but not required (DHCP needs NET_ADMIN)
    #   https://github.com/pi-hole/docker-pi-hole#note-on-capabilities
    cap_add:
      - NET_ADMIN
    restart: unless-stopped
```
4. Just run ```sudo docker-compose up -d```. 
5. It's that easy!. Verify by running ```sudo docker ps -a```.
6. Now you have a running Pihole DNS server. 


### There you have it . A Rpi hosting your basic services over Docker.
### This is just the first part. I will follow this up with how to further set these up and tweak them for your liking. This was just the installation related part.


PS: Again this is not a how to do it best,or the most this, most that. This guide doesn't claim to be anything but a guide just to help you get things up and running if you haven't played with similar stuff before. 

Once you cross this initial barrier. You should be pretty acquainted with the Rpi-Docker system. Now you can go ahead and tinker further into things like:
	- Securing the whole setup, and even securing services individually and enabling https etc.
	- Implementing performace enhancements like a RAMdisk for the media server for faster trasncoding.

This are just my `to-do` things . But the possibilities are endless. You can integrate home automation extremely easily if you have smart devices already setup as well.


    




