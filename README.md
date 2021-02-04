# Ansible Learnings

## Pre-Requisite

###      1) OpenSSH-Server on all hosts installed and configured as a systemctl service. Incase if you dont have it install it using "sudo apt install openssh-server"
###      2) Firewall Corporate & OS level firewall allowing shh connections. For Example "ufw allow ssh"
###      3) GIT installed. Verify using command "which git" incase its not available get it using "sudo apt install git"
 

## Generate SSH KeyPair for Ansible engine to connect to the desired hosts.  This doesnot have password associated with private key, thus its paramount to safekeep and protect the keypair. 
### ssh-keygen -t ed25519 -C ansible_master_key  # Give Proper Path e.g. ~/.ssh/id_ansible_master_key There will be two files created by this command.


## Generate default KeyPair for logging into the Ansible Host Server, Commit changes to Github.
### ssh-keygen -t ed25519 -C Debasish_Key # Give Proper Path e.g. ~/.ssh/id_Debasish_key, There will be two files created by this command.

## Create an alias by editing your specific .bashrc file
### vi .bashrc
###     alias sshd='eval $(ssh-agent -s) && ssh-add ~/.ssh/id_25519_Debasish'
###     :wq
### . .bashrc

## Activate the SSH-AGENT and supply your specific password for default keypair.

### sshd

## Create your Repo on GitHub and Add your default keypair (~/.ssh/id_25519_Debasish.pub) to GitHub UI > Setting > SSH & PGP .  
## Add the SSH public key to github. As its a public key its perfectly safe to add it on public repo.

## Go back to your Instance and login. 
### Run "git clone" command to clone your repo from gitHub. Make sure you choose the link for SSH Under CODE.
 
### git clone git@github.com:debasish-mohanty/ansible.git

## Now you will see your GitHub repo is cloned on your machine.

## We need to configure basic metadata for our github setup.
### cd ansible
### git config --global user.name "Debasish Mohanty"
### git config --global user.email "debasishmohanty@hotmail.com"

## Verify the same has been properly configured 
### cat ~/.gitconfig

## Lets Modify the ReadMe file with this guidance information.
### vi README.md

## Verify Current Status of git - svn 
### git status # Note : The outpur shows all those file where some modification has happened in "Red" Colour.

## Add file(s) to be marked as ready for next process.
### git add README.md

## Verify the "git status" again and note the file(s) have turned Green indicating file(s) are ready for next step. 
### git status

## Commit the Changes for the file(s) with a message using -m
### git commit -m "Updated readme file"

## Push changes to Github for safekeeping in a version control environment.
### git push origin main
