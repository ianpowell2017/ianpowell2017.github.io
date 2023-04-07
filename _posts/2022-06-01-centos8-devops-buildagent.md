---
title: Using Centos 8 Stream as a self hosted build agent
author: ianpowell
date: 2022-06-01 18:12:00 +0100
categories: [linux]
tags: [centos,centos8]
---

## Install dependencies

**Important:** Boot using the `root` account

``` shell
dnf update -y
dnf install git -y

rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm
dnf install dotnet-sdk-2.1 -y

# Update package reference, otherwise you'll get the 6.0 RC version
rpm -Uvh https://packages.microsoft.com/config/centos/8/packages-microsoft-prod.rpm
echo 'priority=50' | sudo tee -a /etc/yum.repos.d/microsoft-prod.repo

dnf install dotnet-sdk-6.0 -y

dnf install dotnet-sdk-7.0 -y

dnf install aspnetcore-runtime-7.0 -y

dnf install dotnet-runtime-7.0 -y

dnf module enable nodejs:18

dnf install nodejs -y

# Add Mono Repo
dnf config-manager --add-repo https://download.mono-project.com/repo/centos8-stable.repo
dnf install mono-complete -y

yum install -y powershell
```

## Build Agent

### Add build user

``` shell
useradd buildagent
passwd buildagent
usermod -aG wheel buildagent
```

### Download agent

[Microsoft article](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/v2-linux?view=azure-devops)

``` shell
wget https://vstsagentpackage.azureedge.net/agent/2.204.0/vsts-agent-linux-x64-2.204.0.tar.gz
mkdir /build
cd /build
tar zxvf ~/vsts-agent-linux-x64-2.204.0.tar.gz
cd ..
chown -R buildagent:buildagent ./build
```

### Configure agent

``` shell
su -l buildagent
cd /build
./config.sh
```

### Run agent to test

``` shell
./run.sh
```

### Run agent as a service

``` shell
su -l buildagent

cd /build
./svc.sh install
./svc.sh start
```

## Install Chrome browser for Web UI testing

[Stack overflow link](https://stackoverflow.com/questions/46109812/steps-to-install-and-run-headless-chrome-browser-on-centos-6-5-using-chrome-driv)

``` shell
wget https://chromedriver.storage.googleapis.com/100.0.4896.20/chromedriver_linux64.zip

mkdir /chrome
cd /chrome
unzip ~/chromedriver_linux64.zip

./chromedriver --version
cd ~

wget https://dl.google.com/linux/direct/google-chrome-stable_current_x86_64.rpm

sudo yum install google-chrome-stable_current_x86_64.rpm

cd /nas
cd build
./svc.sh stop
./svc.sh start
```

## Enable the build agent to deploy SQL scripts

``` shell
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/8/prod.repo```
sudo yum remove unixODBC-utf16 unixODBC-utf16-devel
sudo yum install mssql-tools unixODBC-devel
Do you accept the license terms? (Enter YES or NO)
```
- yes
- yes

``` shell
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
source ~/.bashrc
```
To execute as part of build

`/opt/mssql-tools/bin/sqlcmd -S servername,1433 -U yourusername -P 'your password' -d databasename -i script.sql`

### Potential errors

The user's home directory could not be determined. Set the 'DOTNET_CLI_HOME' environment variable to specify the directory to use.

#### Solution

Add `Environment=DOTNET_CLI_HOME=/tmp` to the service file automatically created.

``` shell
systemctl daemon-reload

./svc.sh stop
./svc.sh start
```
