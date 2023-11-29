# Tutorial: How to connect to a droplet with a new user, remove root from ssh login, and setup nginx


This is a tutorial that would guide you through the following steps.

Starting from a Fresh Debian 12 server on digitalocean
Create a new regular user:

- User can perform administrative tasks
- User has bash as login shell
- User can access the server via SSH
- Install nginx
- Configure nginx to serve a sample website


Prevent the root user from connecting to the server via SSH
Install nginx
Configure nginx to serve a sample website

***Please* Replace all placeholders ```example. <user-name>``` with your own IP, username, group name, key name**


## How to create a new user with administrative permissions

In this section, we will create a new user with administrative permissions. We will also give the new user the ability to connect to the server with ssh and remove 

the root user's ability to ssh connect to the server.

***It is important to not use the root user for daily tasks and removing it is a great safety measure***


### Step 1: Make a new regular user

In this step, we will create a new regular user with bash as its shell. This user will not have administrative permissions yet.

```useradd -ms /bin/bash <user-name>```

*-useradd will create a new user*

*-m will create the user's home directory and copy default files to it*

*-s specifies the login shell for the user: /bin/bash*


### Step 2: Create a password for your user

In this step, we will add a password to the new user that you have just created.

```passwd <user-name>```

>You will be prompted with “New password:”
>
>>*Type your desired password*
>
>You will be prompted with “Retype new password:”
>
>>*Re-type your password*



### Step 3: Give the user administrative permissions

```usermod -aG sudo <user-name>```


### Step 4: Make it possible for your new user to access the server with SSH

In this step, we will allow the user the connect to the server with ssh. You will need to copy the /root/.ssh file to your home directory and

change the user and group owner of your home directory's .ssh to your user

#### Copy the /root/.ssh folder to your user home directory

```sudo cp -r /root/.ssh /home/<user-name>```

#### Change the owner of your new ssh file in your home directory

```sudo chown -R <user-name>:<user-group> /home/<user-name>/.ssh```


### Step 5: Connect to your server with your new user

After setting up your new user and adding the ssh file to your home directory. The following command will allow you to connect to your new server with your new user:

```ssh -i .ssh/<key-name> <user-name>@<Your_server_IP>```


### Step 6: Stop your root user from logging in with ssh

```sudo vim sshd_config```

Find “PermitRootLogin” and type “no” after it, separated by a space

Example: “PermitRootLogin no”



### Step 7: Restart your ssh.service before testing your root user 

Make sure to restart your ssh.service to apply the changes made to the sshd_config file, by using 

```sudo systemctl restart ssh.service```


### Step 8: Test logging in with your root user

```ssh -i .ssh/<key-name> root@Your_Droplet_IP```

If everything has been done correctly, you should see this message:
root@your-droplet-ip: Permission denied (publickey)

**If you received the message above, you have completed the first part of the tutorial**

### **Congratulations, you are now able to login with your new user and your server is secured!**

*****************************************************************************************************************************************


## How to configure nginx to server a simple website

In this section, you will install nginx and configure it to serve a simple website.

### Step 1: Install nginx

You can install nginx with the following command: ```sudo apt install nginx```

### Step 2: Create a directory in /var/www/

In this step, you will create a directory in /var/www/that will store all of your .html files

```mkdir /var/www/<directory-name>```

### Step 3: Make an index.html file and create the basic layout

Use ``` sudo vim /var/www/<directory-name>/index.html``` to create an index.html file in the directory you've made, then copy and paste the 

the basic HTML layout into index.html before you write and quit with ```:wq```
   
    
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>2420</title>
        <style>
            body {
                display: flex;
                align-items: center;
                justify-content: center;
                height: 100vh;
                margin: 0;
            }
            h1 {
                text-align: center;
            }
        </style>
    </head>
    <body>
        <h1>Hello, World</h1>
    </body>
    </html>

### Step 4: Creating your own server block

Create a conf file using the same name as the directory you made in /var/www/<directory-name> in ```/etc/nginx/sites-available``` by using ```vim /etc/nginx/sites-available/<directory-name>```

> example: if your directory is ```var/www/Example1``` do ```vim /etc/nginx/sites-available/Example1.conf```
 

Once completed, paste the the example server block into the file:

      server {
      	listen 80 default_server;
      	listen [::]:80 default_server;
      	
      	root /var/www/html;
      	
      	index index.html index.htm index.nginx-debian.html;
      	
      	server_name _;
      	
      	location / {
      		# First attempt to serve request as file, then
      		# as directory, then fall back to displaying a 404.
      		try_files $uri $uri/ =404;
      	}
      }

***Make sure to write into the file before quitting***


