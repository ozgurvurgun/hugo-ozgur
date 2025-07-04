---
title: Simple zero downtime deployment with github actions
description:
date: 2023-09-14 
draft: false
tags:  
---


Github actions is a powerful tool. Today I built a simple deployment configuration for a client. 

It is uploading repository content to server via rsync. 
 
### Preperations:

We need to create ssh keys. 
`ssh-keygen -t rsa -b 4096 -C "your_email@example.com"` generate your keys. You will have private and public keys. 

Add generated public key to your servers `.ssh/authorized_keys` file. 
Add generated private key to github repositories secrets as `PKEY`

### How is working? 
We will use shimataro/ssh-key-action@v2 action to configure ssh key in runner. Save SSH_HOST,SSH_USER and APP_ROOT folders in github repository secrets. 

Then we will use `ssh-keyscan` command to update runners `know_hosts` file. 

I use commit sha as folder name. And running folder create command on server. 

Then i am sending files to that folder. After that i am updating symlink to current folder and removing all folders. I am keeping 5 most updated folders for rollbacks. Then restarting the server. 

I'm adding full actions file in here as reference. Hope i find time next time to give more details about that. 

have fun.



```yaml
name: Deployment
run-name: Deployment
on: 
  push:
    branches:
      - deploy-debug
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        with:
          ref: deploy-debug
          
      - name: Install SSH Key
        uses: shimataro/ssh-key-action@v2
        with:
          key: ${{ secrets.PKEY }}
          known_hosts: 'just-a-placeholder-so-we-dont-get-errors'
      - name: Adding Known Hosts
        run: ssh-keyscan -H ${{ secrets.SSH_HOST }} >> ~/.ssh/known_hosts
      - name: Create a folder
        run: ssh ${{ secrets.SSH_USER }}@${{ secrets.SSH_HOST }} 'mkdir ${{github.sha}}'
      - name: Deploy with rsync 
        run: rsync --exclude '.git' --exclude '.github' --exclude '.gitignore' -avz ./ ${{ secrets.SSH_USER }}@${{ secrets.SSH_HOST }}:${{ secrets.APP_ROOT }}
      - name: Update link
        run: ssh ${{ secrets.SSH_USER }}@${{ secrets.SSH_HOST }} 'ln -s  ${{ secrets.APP_ROOT }}/${{github.sha}} /var/www/current'
      - name: Remove older versions
        run: ssh ${{ secrets.SSH_USER }}@${{ secrets.SSH_HOST }} 'cd ${{ secrets.APP_ROOT }} && folders=($(find . -maxdepth 1 -type d -exec stat -f "%m %N" {} \; | sort -n | cut -d' ' -f2-)) && num_folders="${#folders[@]}" && keep_count=5 && [ "$num_folders" -gt "$keep_count" ] && delete_count=$((num_folders - keep_count)) && for ((i = 0; i < delete_count; i++)); do rm -rf "${folders[i]}"; done && echo "Removed old versions" || echo "Nothing to remove."'
      - name: Reload the Apache
        run: ssh ${{ secrets.SSH_USER }}@${{ secrets.SSH_HOST }} 'service apache2 reload'
