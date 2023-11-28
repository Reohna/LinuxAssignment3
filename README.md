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

***Please* Replace all placeholders "<Something>" with your own IP, username, group name, key name**


## How to create a new user with administrative permissions

In this section, we will create a new user with administrative permissions. We will also give the new user the ability to connect to the server with ssh and remove 

the root user's ability to ssh connect to the server.

***It is important to not use the root user for daily tasks and removing it is a great safety measure***


### Step 1: Make a new regular user

useradd will create a new user with the /bin/bash directory

-m will create the user's home directory and copy default files to it 

-s specifies the login shell for the user: /bin/bash

```useradd -ms /bin/bash <user-name>```


### Step 2: Create a password for your user

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

Make sure to restart your ssh.service to apply the changes made t othe sshd_config file

```sudo systemctl restart ssh.service```

Test that your root user cannot login:



### Step 8: Try to connect to your droplet with root user

```ssh -i .ssh/<key-name> root@Your_Droplet_IP```

You will receive a message:
root@your-droplet-ip: Permission denied (publickey)

**If you received the message above, you have completed the first part of the tutorial**

**Congratulations, your server is secure!**
