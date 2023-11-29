# Creating a new user that replaces the root user and can access the server via SSH
## Step 1:
You want to create a new user as bash login by running the following command:
```bash
useradd -ms /bin/bash <username>
```
Type whatever name you want to name your user in the username section.
The `-m` option stands for "create home directory." When used, this option instructs `useradd` to create the user's home directory if it doesn't already exist.
The `-s /bin/bash` option sets the user's login shell to Bash. The `bash` shell is commonly used as the default shell for users.

## Step 2:
To add a password to the user you just created you must run the following command:
```bash
passwd <username>
```
This will prompt you to enter a password twice. For security reasons nothing will be displayed on the screen.

## Step 3:
To grant the user administrative privileges by adding it to the sudo group, you must run the following command:
```bash
usermod -aG sudo <username>
```
The `-a` option stands for "append" and is used to add the user to the specified group without removing them from other groups.
The `-G` option specifies the groups to which the user should be added. In this case, it adds the user to the `sudo` group.

## Step 4:
To make it possible for your new user to connect to the server via ssh you must first copy the .ssh directory from the root users home directory to the new users home directory:
```bash
sudo cp -r /root/.ssh /home/<username>
```
The `-r` option in the `cp` command stands for "recursive." When used with `cp`, it allows the command to copy directories and their contents.

## Step 5:
Then change ownership of the directory and make the new copied user directory be owned by the new user and the new users primary group:
```bash
sudo chown -R <username>:<username> /home/<username>/.ssh
```
The `-R` option in the `chown` command stands for "recursive." When used with `chown`, it recursively changes ownership for the specified directory and its contents.
## Step 6:
To test that you can connect to your server with your new regular user, replace 'root' with your new user-name in the ssh command you use to connect to your server:
```bash
ssh -i do-key <username>@**IP address**
```

## Step 7:
Now edit ssh configuration so that the root user can no longer connect to the server via ssh. Search for the `sshd_config` file in the etc directory:
```bash
sudo vim /etc/ssh/sshd_config
```
Then look for the line `PermitRootLogin yes` and change yes, to no. `then press :wq` to save the file. Save the file and restart the ssh service: 
```bash
sudo systemctl restart ssh.service
```


# Making a sample website using nginx

## Step 1:
First update the package list:
```bash
sudo apt update
```
Then install nginx:
```bash
sudo apt install nginx
```

