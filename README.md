# Raspberry Pi Node Setup

## Setup SD Card
1. [Install Raspbian](https://www.raspberrypi.org/documentation/installation/installing-images/mac.md) on SD card.
1. Insert SD card into raspberrypi

## Setup Git Remote
1. Startup raspberrypi, and ssh into it.
2. Create user
```
useradd git
password git
```
3 Login as 'git'
```
sudo su - git
```
4. Create git, receive folder
```
mkdir receive.git && cd receive.git && git init --bare
```
5. Create post-receive file
```
vi ~/receive.git/hooks/post-receive

#!/bin/sh

# Create cloned folder
rm -rf ~/_source
mkdir ~/_source

# Checkout git repository to cloned folder
cd ~/source.git
GIT_WORK_TREE=~/_source git checkout -f
cd ~/_source
npm install
npm rebuild

#Stop the service
pm2 stop ~/source/server.js

#Move the cloned folder to master
rm -rf ~/source
mv ~/_source ~/source

# Run Forever
cd ~/source
NODE_ENV="production" PORT=80 pm2 start server.js
```

6. Make file executable
```
chmod 0775 ~/source.git/hooks/post-receive
```

## Install Node

1. [Download NVM](https://github.com/creationix/nvm) and run the following:
```
nvm install v4.x.x
nvm alias default v4.x.x
```
2. Install pm2
```
npm install -g pm2
```

## Setup as remote server

In your local environment add it as a remote repository
```
git remote add pi git@(ip):receive.git
```

Then, you can do the normal `git push pi master`