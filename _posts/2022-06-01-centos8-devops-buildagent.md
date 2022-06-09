---
title: Using Centos 8 Stream as a self hosted build agent
author: ianpowell
date: 2022-06-01 18:12:00 +0100
categories: [.net]
tags: [misc]
---

## Install dependencies

``` shell
sudo dnf update -y
sudo dnf install git -y

rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm
sudo dnf install dotnet-sdk-2.1 -y

# Update package reference, otherwise you'll get the 6.0 RC version
sudo rpm -Uvh https://packages.microsoft.com/config/centos/8/packages-microsoft-prod.rpm
echo 'priority=50' | sudo tee -a /etc/yum.repos.d/microsoft-prod.repo

sudo dnf install dotnet-sdk-6.0 -y

# Add Mono Repo
dnf config-manager --add-repo https://download.mono-project.com/repo/centos8-stable.repo
sudo dnf install mono-complete -y
```

## Build Agent

### Add build user

``` shell
sudo useradd buildagent
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
./run.cmd
```

### Run agent as a service

Logout of buildagent account
`exit`

``` shell
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
