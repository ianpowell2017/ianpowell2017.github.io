---
title: Centos 8 notes
author: ianpowell
date: 2022-06-23 11:32:00 +0100
categories: [learning]
tags: [centos,centos8,linux]
---

# Using Centos 8 notes

[Download and install 'BitVise Windows SSH Client'](https://www.bitvise.com/ssh-client)

- New SFTP window for drag and drop upload / download / Erase / Rename
- New terminal console for **cmd** access.

Note: Linux is **CASE SENSITIVE**, file.txt and File.txt are can be two separate files.

## General

- `cd` - To navigate folder structure
eg. `cd /nas/nopCommerce-test`
- `ls` - To list files / folders in directory
- `ls -la` - To list files / folders with information of who owns the files and byte size and timestamp
- `pwd` - To display current path
- `exit` - Ends terminal session

## Ownership

- `sudo chown www-data:www-data _filenamehere_` - Change ownership of files in `_filenamehere_` to belong to `www-data`

  **NOTE:** When files are uploaded using the drag and drop (scp) files are written using your account. For Kestrel to be able to run / access then, the ownership needs to be changed to the account which kestrel runs under `www-data`

### Start / Stop / Status of running daemon process

- `sudo systemctl start _servicenamehere_.service` - This command starts the daemon service called `_servicenamehere_.service`
Starts the kestrel instance daemon process
- `sudo systemctl stop _servicenamehere_.service` - This command stops the daemon service called `_servicenamehere_.service`
- `sudo journalctl -fu _servicenamehere_` - Tails the STDOUT / STDERR from the service called `_servicenamehere_`

  **CTRL-C** to end console output.

## Miscellaneous

- `du -cha --max-depth=1 / | grep -E "M|G"` - To find out where disk space is being used from root directory folder
- `du -cha --max-depth=1 /var/nginx | grep -E "M|G"` - To find out where disk space is being used from /var/nginx directory folder

## Periodic maintenance

- `sudo yum update` answer y when prompted
- `sudo yum upgrade` answer y when prompted

## Important directories

`/etc/systemd/system` - Contains the service daemon files which run the applications and restart the apps on crash
