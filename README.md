# Rpi_Homeserver

An absolute beginners guide from Picking up a Pi from the shelves to get it up and running, hosting multiple servcies for an essential home server. 

I will focus on things that needs to be modifed/personalised to get them working. As a lot of these solution are already avialable for Rpi and Docker seperately. But doing them together sometimes gets tricky.

## Let's get started. 

Let's start with what OS you want to go ahead with. I have chosen Ubuntu 20.04 for my installtion just because of it's widespread community and ease of use. (But this turned   out to be not the greatest chioce since Rpi OS is still the only OS which support some nifty functions like HWA,but more on that later)

  ### Installing OS

    1. Download the latest image from https://ubuntu.com/download/raspberry-pi
    2. Download the Raspberry Pi Imager from https://www.raspberrypi.org/software/
    3. Use a good Class 10 or UHS Class 1 card for your OS as this would have a big impact on the overall snapiness of your system.
    4. The installer is pretty straightforward and you just select the image and disk and jsut hit install .

## Installing Docker. 

> Before you begin. As of writing this, there is something wrong with arm64 repo of docker. Which doesn't let you install using the repo. So you have to manually download the packages and install them using the guide https://docs.docker.com/engine/install/ubuntu/#install-from-a-package. But seems like something pretty trivial and shoudl be fixed pretty soon.So do check it out.

>  One more important thing is usign root user for Docker. because docker(And the servies we will be using) will eventually require you to have user with all the right of a root user, so if you don't want the hassle of service users then I'd suggest using root. 

1. Go to https://download.docker.com/linux/ubuntu/dists/focal/pool/stable/arm64/ and download the follwoing files: #Make sure you use the correct version based on your release like focal,bionic,xenial etc.
  - containerd.io_<lates_version>_arm64.deb
  - docker-ce-cli_<lates_version>_arm64.deb
  - docker-ce_<lates_version>_arm64.deb
     ```
          mkdir docker
          cd docker
          wget https://download.docker.com/linux/ubuntu/dists/focal/pool/stable/arm64/containerd.io_1.4.4-1_arm64.deb
     ```
 2. Once downloaded , you can run the ```sudo dpkg -i *``` to install all 3 packages. 
 3. And you're done!. Verify the install by runnning ```sudo docker run hello-world```

## Installing Owncloud Storage server

Now that we have docker up and running, we can go ahead and start installing our services, which are basically creating a `docker-compose.yml` file and letting docker do all the heavy lifting :).

1. Head over to the official Owncloud installtion page and give it a quick read.
2. setup up a project dir for Owncloud ```cd mkdir owncloud && cd owncloud```
3. Create a [docker-compose.yml](Cloudstorage/docker-compose.yml) file as attached/or as in the Official docs.
4. Create a [.env](Cloudstorage/.env) file as attached/ or as in the official docs.
5. Please go thorugh the comments I have added as it might save you some headache later on. Again only if you are an abolute noob like I am.
6. Once you have your files ready. Go ahead and run ```sudo docker-compose up -d``` from the same directory.
7. That's it. Well mostly. You should now have the following containers runnign which you can check by running ```sudo docker ps -a```
      - owncloud_owncloud_1 # the service and the webserver.
      - owncloud_redis_1 # The Redis container.
      - owncloud_bd_1 # The MySQL container. 
8. Now you are almost done. But there is one thing you need to do if you want to host some local storage. Owncloud does not enable this by default and needs to be manually added as mentioned [here](https://doc.owncloud.com/server/admin_manual/configuration/files/external_storage/local.html)
9. Once you have your containers running log into the conatiner with the follwoing command :
	- ```sudo docker exec -it owncloud_owncloud_1 bash```
10. Once inside you need to navigate to the `/var/www/owncloud/config/config.php` . But your bash should have already landed you in the home dir i.e. `/var/www/owncloud`
11. So you can just `vi config/config.php` and append this line `'files_external_allow_create_new_local' => 'true',` as you see [here](Cloudstorage/config.php)
12. Once done, you can exit from the container . And back in your project dir, just run the `docker-compose.yml` to recreate the container with the updated values.
13. Since we have mentioned 8090 in the .env file above, you can now login to http://<dockerhost>:8090 and shoudl be able to see the login screen.


## Installing Jellying Media Server

1. This process is very similar to Owncloud . Start by heading to the Official [Jellyfin Docs](https://jellyfin.org/docs/general/administration/installing.html) and glance it over.
2. Create a Project directoy for Jellyfin just like you did for Owncloud.```cd mkdir jellyfin && cd jellyfin```
3. Create a [docker-compose.yml](mediaserver/docker-compose.yml) file as attached/or as in the Official docs.
>  I'd suggest you go through the comments added in the above file for this one as it has some changes that are necessary for proper fucntioning.
4. Now we can create volumes (Not mandatory, docker-compose can create them during startup. But I had created them already so passing them externally refer the volumes section at the bottom of the [docker-compose.yml](mediaserver/docker-compose.yml) file. )
	- docker volume create jellyfin-config
	- docker volume create jellyfin-cache	
6. Once the file is ready run ```sudo docker-compose up -d```.
7. Bingo!. Verify that everything is working as epxpected ```sudo docker ps -a```.
8. Your jellyfin home server is up and ready check it out on http://<localhost>:8096


## Installing Pi-Hole

1. This is probably the simples of them all,But still give the [Official Doc](https://github.com/pi-hole/docker-pi-hole/#running-pi-hole-docker) a quick read.
2. Create a Project directoy for Pihole like you did earlier. ```cd mkdir pihole && cd pihole```.
3. Create a [docker-compose.yml](pihole/docker-compose.yml) file as attached or as in the Official Git.
4. Just run ```sudo docker-compose up -d```. 
5. It's that easy!. Verify by running ```sudo docker ps -a```.
6. Now you have a running Pihole DNS server. 


### There you have it . A Rpi hosting your basic services over Docker.


PS: Again this is not a how to do it best,or the most this, most that. This guide doesn't claim to be anything but a guide just to help you get things up and running if you haven't played with similar stuff before. 

Once you cross this initial barrier. You should be pretty acquainted with the Rpi-Docker system. Now you can go ahead and tinker further into things like:
	- Securing the whole setup, and even individually.
	- Implementing performace enhancements like a RAMdisk for the media server for faster trasncoding.

This are just my `to-do` things . But he possibilities are endless. You can integrate home automation extremely easily if you have smart devices already setup as well.


    




