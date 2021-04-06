# Owncloud Setup

## Setting up the frond end.

1. After our setup from the main guide. You should be able to go to the homepage and see the following screen greeting you 
![image](https://user-images.githubusercontent.com/26784551/113721713-62a0b380-970d-11eb-9906-a4d86500bb95.png)

2. Login with the default `admin:admin` username and password combination.
3. Once logged in click on the dropdown on the top right of the screen and go to the users section and change the default password.
    ![image](https://user-images.githubusercontent.com/26784551/113724197-bd3b0f00-970f-11eb-9caf-d8b071c3a7c5.png)

4. After setting up a password, we need to get your local storage to show up in the UI, which is again not done by default and manually needs to be mounted.
  - Again from the dropdown from the top right, select settings.
  - This will be your Admin Screen, On the left panel the top part will be general user settings and the bottom section is only for admin users.
  - From the bottom(admin) section, Open the storage page.
    - Here you will see an empty section of storage you can add, click on the ![image](https://user-images.githubusercontent.com/26784551/113723837-5ddcff00-970f-11eb-88aa-209a283242a6.png) dropdown and select `Local`.
    - Then you will see some fields as you see in the image below.
           ![image](https://user-images.githubusercontent.com/26784551/113723229-c677ac00-970e-11eb-9a54-a617cbf878a9.png)
    - Here, you will have to populate the fields according to your container configuration. Also provide access to the users/groups that you want.
        > Note that the path here is what you have passed in to the container, and not the mount path of the Host OS itself.
    - Also click on the small gear icon at the far left of the storage entry and set properties as per your preferences.
           ![image](https://user-images.githubusercontent.com/26784551/113725920-6cc4b100-9711-11eb-9ca7-9d5e8ef01737.png)

5. Once you have set this, go back to your home screen by clicking on the hamburger menu and click on `Files`. And there you should see your newly mounted directory.
    > Verify that you are able to access all the directories inside. 
    > If you followed the original giude, you should already have set automount options for your disk in the Host OS `/etc/fstab`. If not, Set this right away. Owncloud does not like it when it doesn't find a disk that it is expecting and you will have to end up scrapping the container or dig into the DB to fix some stuff.

6. This covers most of the basic setup. Go to the users section and add as many users as you want and define groups and permissions if you are going to share this with friends and family.
7. I would suggest following the Official documentation for any other functionality as it would not be under the scope of this general guide.

## Some cool features worth checking out.

1. Drop feature enabling you to create a drop link which you can share with others for them to upload anything without having to log in.
2. Sharing features, which allow you to share a file with a link just like google allows you. But with a lot more granular control on the duration, Permissions and Expiry of the share.
3. Apps section where you can Add-on other simple extension-like apps to extend functionality.
      
