<h1>Beginner Tutorial</h1>


Write a tutorial that would guide the reader through the following steps.

Starting from a Fresh Debian 12 server on digitalocean
Create a new regular user:

User can perform administrative tasks
User has bash as login shell
User can access the server via SSH


Prevent the root user from connecting to the server via SSH
Install nginx
Configure nginx to serve a sample website


<h2>How to create a new user</h2>


<h3>Make a new user</h3>

```Useradd -ms /bin/bash <user-name>```

<h3>Create a password for your user</h3>

```Passwd <user-name>```


You will be prompted with “New password:”

Type your password

You will be prompted with “Retype new password:”

Re-type your password 

<h2>How to give admin power to an user</h2>

Give admin power to your new user

```Usermod -aG sudo <user-name>```


<h2>Make it possible for your new user to connect to the server via SSH</h2>

<h3>Copy the /root/.ssh folder to your user home directory</h3>

```Sudo cp -r /root/.ssh /home/<user-name>```

<h3>Change the owner of /home/<user-name>/.ssh to <user-name>:<user-group></h3>

Sudo chown -R <user-name>:<user-group> /home/<user-name>/.ssh


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
