---
title:  How to share network folder
author: ianpowell
date: 2021-02-23 10:33:00 +0100
categories: [linux]
tags: []
---

# How to share network folder

Excerpt from : https://www.makeuseof.com/set-up-network-shared-folder-ubuntu-with-samba/

Want to share files with multiple devices on a single network? Create a shared folder on your Ubuntu machine using Samba.
If you have ever wanted to easily share files on your home network across multiple operating systems, then look no further than Samba.

This guide will show you how to set up a network shared folder on Ubuntu Linux using Samba. With the Samba server, you can easily share files on your network, regardless of whether you are using Windows, macOS, or Linux.

## What Is Samba?

Samba is a file-sharing service that implements open source versions of the SMB suite of protocols, which was originally developed by Microsoft and IBM. Samba contains programs that allow it to interoperate with Microsoft Windows file sharing protocols.

Samba also allows you to easily communicate with other clients using standard TCP/IP networking.

### Step 1: Installing Samba

This guide will use Ubuntu Linux 20.04 LTS, but the steps should work even if you are using Ubuntu 16.04 or later. Begin by updating your package source information.

``` bash
sudo apt update
```

Then, install Samba using the command below:

``` bash
sudo apt install samba
```

To check if Samba has been successfully installed, run the following command:

``` bash
smbd --version
```

The output should be similar to the one below.

### Step 2: Configuring Samba

To be able to share files securely with other network devices, you have to configure the Samba server. The main configuration file for Samba is located at `/etc/samba/smb.conf` on your PC. This guide uses the Vim text editor for editing the Samba configuration file, but feel free to use any other text editor of your choice.

**Note** : You need to have administrative privileges to edit the configuration file.

``` bash
sudo vim /etc/samba/smb.conf
```

Add the following lines to the bottom of the config file.

```
[sambashare]
comment= Network Shared Folder by Samba Server on Ubuntu
path = /home/your_username/sambashare
force user = smbuser
force group = smbgroup
create mask = 0664
force create mode = 0664
directory mask = 0775
force directory mode = 0775
public = yes
read only = no
```

Remember to update the path parameter with your username. You can get your username by running the following command:

``` bash
echo $USER
```

To [exit the Vim editor](https://www.makeuseof.com/vim-save-file/) after making your changes, simply type `:wq` and press the Enter key.

Understanding the Configurations

Here is a brief description of the configuration lines that you just added.

- Section: A new section in the configuration file is represented by square brackets ([ ]). In this case, the section is `[sambashare]`.
- Comment: This line of code provides a brief outline of what this section is about. Especially, it is useful if you have several shared directory sections in the config file.
- Path: This is the path to the directory of your designated network shared folder.
- Force user: The system user that the Samba server will use for sharing files.
- Force group: The name of the group to which the Samba system user will belong.
- Create mask: This parameter will set permissions for newly created files in the shared folder. In this case, the value is 0664 which means that the owner of the file and the group will have read and write permissions while other users will only have read permissions.
- Force create mode: Works in conjunction with the create mask parameter in order to set the correct file permissions.
- Directory mask: This parameter determines the permissions for folders in the shared folder. Permissions of 0775, means that the owner and the group have read, write, and execute permissions, while others have read and execute permissions only.
- Force directory mode: This parameter works in collaboration with the directory mask to make sure that the correct directory permission is set.
- Public: This parameter specifies that this is a public folder on your network and that other devices can access it.
- Read only: Specifies the permissions for modifying the files within the shared folder.

### Step 3: Creating Samba Resources

Having configured the Samba server, now you have to create the necessary resources such as the Samba user and the directory to share. These resources will facilitate the process of sharing a folder on the network.

#### 1. Shared Folder

You need to create the shared folder in the path specified in the Samba config file above. This guide uses a shared folder named sambashare located in your home directory.
Navigate to your home directory using the [cd command](https://www.makeuseof.com/cd-command-in-linux/).
``` bash 
cd ~
```

Then create the shared directory using the command below:

``` bash
mkdir -p sambashare
```

#### 2. Samba User and Group

The next step is to create the Samba system user and group specified in the configuration file.
You can create the Samba system group using the following command:

``` bash
sudo groupadd --system smbgroup
```

Next, create the Samba system user using **useradd**.

``` bash
sudo useradd --system --no-create-home --group smbgroup -s /bin/false smbuser
```

The command above creates a system user and adds the user to the Samba group created above. Also since this is a system user, no home directory will be created.

#### Step 3. Changing the Shared Folder Owner

Once the Samba user and group are in place, you can now change the shared folder owner to the new user smbuser and the group to smbgroup. You can achieve this using the command below:

``` bash
sudo chown -R smbuser:smbgroup ~/sambashare
```

Finally, issue the command below to give the group write access to the shared folder and the content inside it.

``` bash
sudo chmod -R g+w ~/sambashare
```

#### Step 4: Restarting the Samba Service

You should restart the Samba service for the changes in the Samba configuration file to take effect.

``` bash
sudo systemctl restart smbd
```

After the service restarts, you can check its status with the command below:

``` bash
sudo systemctl status smbd
```

Note: If you have your firewall enabled, you should also add Samba to your enabled rules using the ufw command.

``` bash
sudo ufw enable samba
```

#### Step 5: Accessing the Shared Folder

Your shared folder is now accessible by the devices on your network.

##### On Windows

In Windows, you can access the shared folder using Windows Explorer. You can start file explorer using the Windows + E keyboard shortcut.

In the address bar, type `\\ip_address_of_pc_with_shared_folder\sambashare`.

Remember to replace with the correct IP address and shared folder name.

The system will also ask you to enter the username and password of the user on the Linux PC.

##### On Ubuntu

On Ubuntu Linux, open the default file manager and click on the Other Locations button. Then, in the Connect to Server input, enter an IP address in the following format:

``` bash
smb://ip_adresss_of_pc_with_shared_folder/sambashare
```

You can either connect as a registered user or anonymous. Keep in mind that if you select Registered User from the dropdown, you'll have to specify the credentials of the user.

##### On macOS

Mac users can access the shared folder easily as well. In the Finder menu, click on the Network tab, and the computer with the public shared folder will be listed. Select it and you should be able to access the files.

##### Sharing Files Between Multiple Devices Efficiently

This guide has looked at how to share files on a network using Samba. With Samba, you can share files on a network regardless of the operating system that you are running on the devices.

Not only Linux, but you can also configure a shared network folder on your Windows machine

