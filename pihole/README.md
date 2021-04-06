# Pi-Hole Setup.

1. Pi hole doesn't have any setup as such. Pi hole will be ready to go right from the start.
2. In case you didn't set a WEBPASSWORD in the [docker-compose.yml](https://github.com/hgaaditya/rpi_homeserver/blob/main/pihole/docker-compose.yml) then you'll ned to reset your passowrd as follows:
    - From the docker host run the following command:
       `docker exec -it pihole_container_name pihole -a -p`
3. Once logged in you should be able to see your dashboard :
      ![image](https://user-images.githubusercontent.com/26784551/113739986-ef536d80-971d-11eb-9598-f3427ca6ce67.png)
4. Do not forget to set your Router's DNS server to the Pihole(Docker Host) IP.
        ![image](https://user-images.githubusercontent.com/26784551/113740419-4ce7ba00-971e-11eb-8cdd-ccb733c4f7f2.png)

