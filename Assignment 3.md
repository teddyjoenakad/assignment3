# Step 1:

You want to create a new user as bash login by running the following command:
```bash
useradd -ms /bin/bash <username>
```
Type whatever name you want to name your user in the username section.

# Step 2:

To add a password to the user you just created you must run the following command:
```bash
passwd <username>
```
This will prompt you to enter a password twice. For security reasons nothing will be displayed on the screen.

# Step 3:

To grant the user administrative privileges by adding it to the sudo group, you must run the following command:
```bash
usermod -aG sudo <username>
```
The `-a` option stands for "append" and is used to add the user to the specified group without removing them from other groups.
The `-G` option specifies the groups to which the user should be added. In this case, it adds the user to the `sudo` group.

