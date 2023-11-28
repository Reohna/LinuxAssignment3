# Beginner Tutorial


Write a tutorial that would guide the reader through the following steps.

Starting from a Fresh Debian 12 server on digitalocean
Create a new regular user:

User can perform administrative tasks
User has bash as login shell
User can access the server via SSH


Prevent the root user from connecting to the server via SSH
Install nginx
Configure nginx to serve a sample website


## How to create a new user


### Make a new user

```Useradd -ms /bin/bash <user-name>```

### Create a password for your user

```Passwd <user-name>```


You will be prompted with “New password:”

Type your password

You will be prompted with “Retype new password:”

Re-type your password 

## How to give admin power to an user

Give admin power to your new user

```Usermod -aG sudo <user-name>```


## Make it possible for your new user to connect to the server via SSH

### Copy the /root/.ssh folder to your user home directory

```Sudo cp -r /root/.ssh /home/<user-name>```

### Change the owner of your /home/user-name

```Sudo chown -R <user-name>:<user-group> /home/<user-name>/.ssh```


How to connect to your ssh as your new user

ssh -i .ssh/<key-name> <user-name>@Your_Droplet_IP


How to remove root user from logging into the ssh

Sudo vim sshd_config

Find “PermitRootLogin” and type “no” after it, separated by a space
Example: “PermitRootLogin no”

Sudo systemctl restart ssh.service

Test that your root user cannot login:

ssh -i .ssh/<key-name> root@Your_Droplet_IP

You will receive a message:
root@your-droplet-ip: Permission denied (publickey)
