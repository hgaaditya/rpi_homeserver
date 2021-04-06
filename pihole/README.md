# Pi-Hole Setup.

1. Pi hole doesn't have any setup as such. Pi hole will be ready to go right from the start.
2. In case you didn't set a WEBPASSWORD in the [docker-compose.yml](https://github.com/hgaaditya/rpi_homeserver/blob/main/pihole/docker-compose.yml) then you'll ned to reset your passowrd as follows:
    - From the docker host run the following command:
       `docker exec -it pihole_container_name pihole -a -p`
3. Once logged in you should be able to see your dashboard :
      
