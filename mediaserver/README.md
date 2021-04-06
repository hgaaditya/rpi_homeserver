# Setting up JellyFin

## Setting up the Frontend.

1. If you have followed the initial guide you should have sa screen like this greeting you. 
      ![image](https://user-images.githubusercontent.com/26784551/113730987-01c9a900-9716-11eb-91bd-269f02686f28.png)

2. The Initial setup Wizard is pretty straight forward, so just follow it and setup a user account. Once done you should be at your homepage.
      ![image](https://user-images.githubusercontent.com/26784551/113731278-47867180-9716-11eb-96c3-65c94439bdee.png)

3. Now we would have to add your media into the library.
     - Click on the Add Media Library button (Can also be founr in the Admin `Dashboard` menu in the top left dropdown)
     - Here select what type of media you want to add and Give the library a name.
     - Now click on the ![image](https://user-images.githubusercontent.com/26784551/113732132-f32fc180-9716-11eb-945a-471ac2c23ad7.png) button.
     - Here you should see a list of volumes, which if you remember we passed to the container.
            ![image](https://user-images.githubusercontent.com/26784551/113732301-1b1f2500-9717-11eb-8f3c-ecd257ac8cac.png) 
     - In my case, the files are under /media, so I will just select that and then select on the movies directory inside it.
     - Once selected, just hit OK and this library should now be on your Homepage. 

4. We are still not done. We need to do some tweaks to get the Rpi to play along well with our setup.
      ### NOTE :  The following settings will be applicable only to Raspberry Pi.
     - From your homepage, click on the hamburger menu on the left and and go to your dashboard.
     - From your left panel, under the `Server` section you'll find the Playback menu.
     - Here set the HWA to OMX. And disable all the decoding options. 
            ![image](https://user-images.githubusercontent.com/26784551/113734634-1fe4d880-9719-11eb-9aff-7a0f3e937e5f.png)
     - Scroll down to the bottom of the page and make sure you have these two properties set 
            ![image](https://user-images.githubusercontent.com/26784551/113734924-65090a80-9719-11eb-92b0-6b1649f1ebb4.png)
        > These properties are not any officical recommendations from the docs. These are just what I fiddled around and found a good balance that works.
  
   
