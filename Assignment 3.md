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

Now log out and try signing in with root. You should get an error that looks like this:
```bash
root@146.190.162.85: Permission denied (publickey).
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

Now you will have nginx enabled if you type:
```bash
sudo systemctl status nginx
```

And a new root document directory will be defined in the `/var/www/html` directory. To see the ip address, type in 
```bash
ip addr
```
and find the address 146.190.162.85. You can curl this to access the document:
```bash
curl 146.190.162.85
```
## Step 2:
Create a new `my-site` directory in `/var/www` and then create an `index.html` with this script in it:
```bash
sudo mkdir my-site
cd my-site
sudo vim index.html
```
```html
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
```
Then `:wq` to save and quit
## Step 3:
Now you want to create a new file `my-site.conf` in `/etc/nginx/sites-available` and paste the following nginx config into it:
```bash
sudo vim my-site.conf
```
```
server {
	listen 80;
	listen [::]:80;
	
	root /var/www/my-site;
	
	index index.html;
	
	server_name _;
	
	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ =404;
	}
}
```
Then `:wq`
## Step 4:
Create a symbolic link for the `/etc/nginx/sites-available/my-site.conf` to `/etc/nginx/sites-enabled`:
```bash
sudo ln -s /etc/nginx/sites-available/my-site.conf /etc/nginx/sites-enabled/
```
The `-s` option stands for "symbolic" or "soft" link. This option is used to create a symbolic link rather than a hard link.

NOTICE: Remove the default file before proceeding:
```bash
sudo rm default
```

## Step 5:
When you have a symbolic link in sites-enabled run the following command:
```bash
sudo nginx -t
```
this tests your nginx configurations.

## Step 6:
If you don't see any errors restart the nginx service:
```bash
sudo systemctl restart nginx
```

## Step 7:
Check the IP address by typing:
```bash
ip addr
```
and finding the address starting with `146.` in this case it is `146.190.162.85` 

now curl that IP address and you will see the content of the `my-site` file:
```bash
curl 146.190.162.85
```

You can also copy that IP address on the browser to see your webpage.