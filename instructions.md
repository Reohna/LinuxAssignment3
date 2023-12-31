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

***Please* Replace all placeholders ```example. <user-name>``` with your IP, username, group name, key name**


## How to create a new user with administrative permissions

In this section, we will create a new user with administrative permissions. We will also give the new user the ability to connect to the server with ssh and remove the root user's ability to ssh connect to the server.

***It is important to not use the root user for daily tasks and removing it is a great safety measure***


### Step 1: Make a new regular user

In this step, we will create a new regular user with bash as its shell. This user will not have administrative permissions yet.

The command ```useradd -ms /bin/bash <user-name>``` creates a new Linux user with the specified username, a home directory, and sets the Bash shell as their default login shell.

*-```useradd``` will create a new user*

*```-m``` will create the user's home directory and copy default files to it*

*```-s``` specifies the login shell for the user: /bin/bash*


### Step 2: Create a password for your user

In this step, we will add a password to the new user that you have just created.

The command ```passwd <user-name>``` is used to change the password for a specified user on Linux. Simply replace <user-name> with the desired username when prompted.

>You will be prompted with “New password:”
>
>>*Type your desired password*
>
>You will be prompted with “Retype new password:”
>
>>*Re-type your password*



### Step 3: Give the user administrative permissions

The command ```usermod -aG sudo <user-name>``` is used to add a user to the "sudo" group on a Linux system. This grants the specified user administrative privileges, allowing them to execute commands with superuser (root) privileges. The -aG options stand for "append to groups," ensuring that the user is added to the "sudo" group without affecting their membership in other groups. Replace <user-name> with the actual username of the user you want to grant sudo access to. After running this command, the user will be able to use the sudo command to perform administrative tasks on the system.




### Step 4: Make it possible for your new user to access the server with SSH

In this step, we will allow the user the connect to the server with ssh. You will need to copy the /root/.ssh file to your home directory and

change the user and group owner of your home directory's .ssh to your user

#### Copy the /root/.ssh folder to your user home directory

The command ```sudo cp -r /root/.ssh /home/<user-name>``` copies the SSH configuration directory from the root user's home directory to the home directory of the specified user, ensuring they have access to the necessary SSH settings.

#### Change the owner of your new ssh file in your home directory

The command ```sudo chown -R <user-name>:<user-group> /home/<user-name>/.ssh``` changes the ownership of the ".ssh" directory and its contents in the specified user's home directory, ensuring that both the user and their designated group have the appropriate permissions for SSH-related files. 

### Step 5: Connect to your server with your new user

After setting up your new user and adding the ssh file to your home directory. The following command will allow you to connect to your new server with your new user:

To connect to a server using SSH, run ```ssh -i .ssh/<key-name> <user-name>@<Your_server_IP>``` replacing <key-name>, <user-name>, and <Your_server_IP> with the appropriate values.


### Step 6: Stop your root user from logging in with ssh

To stop your root user from using ssh to connect to your server, you will have to edit the SSH daemon configuration file, by using ```sudo vim sshd_config``` then find “PermitRootLogin” and type “no” after it, separated by a space.

Example: “PermitRootLogin no”

#### Congratulations, you are now able to use your new admin account! However, it is safe practice to stop allowing your root user to connect to your server. This will protect your server because all servers have a "root" account and it is easy for people to access your server by attempting to log into a root user vs a username you make yourself. Let'sprevent the root user from accessing the server with ssh.



### Step 7: Restart your ssh.service before testing your root user 

Make sure to restart your ssh.service to apply the changes made to the sshd_config file, by using 

This command ```sudo systemctl restart ssh.service``` restarts the SSH (Secure Shell) service on a Linux system using the systemd init system, applying any configuration changes made to the SSH service without requiring a full system restart


### Step 8: Test logging in with your root user

 Connect to a server (Droplet) as the root user using SSH with the command ```ssh -i .ssh/<key-name> root@Your_Droplet_IP```

If everything has been done correctly, you should see this message:
```root@your-droplet-ip: Permission denied (publickey)```

**If you received the message above, you have completed the first part of the tutorial**

### **Congratulations, you have completed part 1 of the tutorial**

*****************************************************************************************************************************************


## How to configure nginx to server a simple website

In this section, you will install nginx and configure it to serve a simple website.

### Step 1: Install nginx

You can install nginx with the following command: ```sudo apt install nginx``` then test to see if nginx is working properly by using
```curl <your-ip-address>```

***If your curl has an error because it's not enabled or not active, make sure to ```sudo systemctl enable nginx``` then ```sudo systemctl start nginx```***

*The command ```sudo systemctl enable nginx``` is used to enable the Nginx web server to start automatically on system boot. When the command is executed with sudo for superuser privileges, it creates symbolic links or units to include Nginx in the list of services that should start during the system's boot process. This ensures that Nginx will automatically launch whenever the system is restarted or powered on.*

*The command ```sudo systemctl start nginx``` initiates the Nginx web server, immediately starting its services.*

### Step 2: Create a directory in /var/www/

In this step, you will create a directory in /var/www/that will store all of your .html files

The command ```mkdir /var/www/<directory-name>``` creates a new directory with the specified name within the /var/www/ directory path on a Linux system. This is where website or application files are often stored. 

### Step 3: Make an index.html file and create the basic layout

Use ```sudo vim /var/www/<directory-name>/index.html``` to create an index.html file in the directory you've made, then copy and paste the 

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
 

Once completed, paste the example server block into the file and edit the root path to the directory you created in /var/www/ :

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

### Step 5: Create a symbolic link

In this step, you will ```cd to /etc/nginx/sites-enabled``` then create a symbolic link to the conf file you made with the following command: 

```ln -s /etc/nginx/sites-available/<your.conf>```

**Congratulations, you have created a symbolic link to your configuration file!**

### Step 6: Make sure your symbolic link works

Use the following command to check if your symbolic link works: ``` sudo nginx -t ```

### Step 7: Restart your nginx server before curling the IP address

This is the final step, if your symbolic link works, you can use ```sudo systemctl restart nginx``` then try 

```curl <your-ip-address>```

### If your basic HTML page displays properly, you have completed the tutorial!

