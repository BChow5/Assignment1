# Guide to Create Virtual Servers on Digital Ocean using Cloud-Init for Arch Linux Users

## Introduction

 DigitalOcean is a cloud provider that offers the ability to deploy virtual servers known as Droplets. When creating a Droplet, we can automate the setup process to save time and create servers with consistent repeatable configurations using Cloud-Init. 
 
Cloud-init is an industry standard tool that allows you to automate the initialization of your Linux instances. This means that you can use cloud-init to inject a file into your Droplets at deployment that automatically sets up things like new users, firewall rules, app installations, and SSH keys. This tutorial uses doctl to add an SSH key to your account and deploy the Droplets with the cloud-init file.

In this guide, we'll walk through the process of creating a Droplet on DigitalOcean using Cloud-Init on Windows, ensuring a smooth and automated setup for your virtual servers!

### Prerequisites 
- A computer running Arch Linux 
- The Arch Linux image provided in the Assets folder
- All code provided should be run through the Terminal

***

## How to create an SSH Key pair

For the initial set up, you will begin by creating a Secure Shell Protocol (SSH) key pair using our local machine. SSH (Secure Shell) key pairs are a more secure alternative to traditional password-based authentication by using Public Key Cryptography.

Public Key Cryptography uses a key pair that consists of a public key (which you place on the server) and a private key (which you keep securely on your local machine). This file tells the server which public keys are allowed to access the account without a password. With SSH key authentication, there's no need to worry about using weak or compromised passwords. Once set up, you can connect to the Droplet without needing to type a password each time. Instead, your local machine automatically uses the private key to authenticate you.

By default, you should already have the `ssh-keygen` util already installed on your MacOS or Windows machine.

In this section, we will be using the terminal to create two plain text files in the `.ssh` directory that will be our keys.
- We will create a "hw-key" as our private key
- And we will create "hw-key.pub" as our public key

1. Copy then run the following code and after changing the appropriate information:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/hw-key -C "youremail@email.com"
```

###### NOTE: You will need to change *"youremail@email.com"* to your actual information.

###### Successful key creation will look something like this:

![Image of the SSH key making confirmation](/Assets/Images/SSH_key_make.png)

***
## How to Install Doctl

`doctl` is the official DigitalOcean command line interface (CLI) and it allows you to interact with the DigitalOcean API via the command line.

This section will teach you how to:
* Install doctl
* Create an API token
* Activate the API token
* Validate doctl

### Install Doctl

1. Copy and run the following code to download doctl

```bash
sudo pacman -S doctl
```

### Create an API Token

This step will be done on [DigitalOcean](https://www.digitalocean.com/) to create a new API token for your account. An API token is a unique identifier used to authenticate and authorize requests to an Application Programming Interface (API). It acts as a digital key that allows applications, scripts, or users to interact with an API securely without needing to provide credentials like usernames and passwords directly.

It's important that the API token has both read and write access. It needs write access to be able to create your droplet, otherwise it would only be able to gather information. 

#### NOTE: The API token string is only displayed once, so be sure to save it in a safe place for late use

1. Click **API** on the left side menu
2. Click the **Generate New Token**

A New Personal Access Token page will appear and you will need to fill out the following fields:

![Image of the API settings](/Assets/Images/api_settings.png)

1. Type a name for the token
2. Choose when the token expires
3. Select **Full Access** (this will give the API token both read and write access)
4. Click **Generate Token**
5. Save your API token somewhere safe for later use

### Use the API Token to Grant Account Access to Doctl

1. Copy and run the following code to connect your API token

#### NOTE: Be sure to give this authentication a name by changing "NAME"

```bash 
doctl auth init --context NAME
```

1. Enter in the API token string (the one you made earlier) when prompted by `doctl auth init`
2. Copy and run the following code to switch to the correct authenticated account

#### NOTE: Change "NAME" to the name of the account you want to switch to that appears after `doctl auth list`

```bash 
doctl auth list
doctl auth switch --context NAME
```

### Validate that doctl is working

1. Copy and run the following code to confirm you have successfully authorized doctl

```bash 
doctl account get
```

#### Successful output looks like this:

![Image of doctl validation confirmation](/Assets/Images/doctl_validate.png)

***
## How to add your public key to your DigitalOcean account

You will now add your SSH keys to your DigitalOcean account using doctl. You will be using terminal commands to add your new public key text file.

1. Copy and run the following code to upload your public key to your DigitalOcean account 

#### Note: You will need to change "git-user" to your desired key name and "hw-key.pub" to your public key file name

```bash
doctl compute ssh-key import git-user --public-key-file ~/.ssh/hw-key.pub
```

#### NOTE: A successful import will look like this:

![Image of the public key import to DigitalOcean](/Assets/Images/public_key_upload.png)

2. Copy and run the following code to verify a successful import 

```bash 
cat ~/.ssh/hw-key.pub
```
## How to Create a Droplet on DigitalOcean

This step will be creating our droplet on the [DigitalOcean](https://www.digitalocean.com/) website. Droplets are Linux-based virtual machines (VMs) that run on top of virtualized hardware. Each Droplet you create is a new server you can use, either standalone or as part of a larger, cloud-based infrastructure.

#### This section will teach you how to:

* Upload an Arch Linux Image to DigitalOcean
* Create a new Arch Linux Droplet

### Upload an image to DigitalOcean

You will be uploading the provided Arch Linux image to Digital Ocean for your droplet. The disk image provided is a file that contains an exact copy of the data and structure of a physical disk drive. It's essentially a snapshot of the disk that contains information on everything from the files and folders to the operating system and boot information. This disk image will be the basis for the droplet to be built with.

You can find the Arch Linux image in the Assets folder.

1. Click the **Manage** dropdown menu in the left side menu 
2. Select **Backups & Snapshots**
3. Select **Custom Images**
4. Click the blue **Upload Image** button.
5. Upload the Arch Linux image in the provided Assets folder

#### NOTE: After clicking upload, a new settings box will open and you will need to select the following settings

1. Select **Arch Linux** in the Distribution dropdown menu
2. Select **San Francisco 3** in the Choose a Datacenter Region Section 
	-  We choose San Francisco 3 as our data center in the example because it is the closest to our location
3. Click **Upload Image** to finish

### Setting up cloud-init  

Cloud-init will allow us to set up a server with some initial configuration. 

1. Copy and run the following code to create your cloud-config.yaml file

```bash
nvim cloud-config.yaml
```

2. Copy and paste the following code

#### NOTE: Use "ctrl + shift + v" to paste with the correct formatting 

```bash 
#cloud-config
users:
	name: user-name #change me
    primary_group: user-group # change me
    groups: wheel
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-ed25519 your-ssh-public-key # change this
packages:
  - ripgrep
  - rsync
  - neovim
  - fd
  - less
  - man-db
  - bash-completion
  - tmux
disable_root: true
```

3. Press the "i" key after pasting the text to enter insert mode to edit the file contents
1. Change the information (at least the ssh-authorized-keys)
3. Press "esc" key to exit inset mode
4. Type `:` then `wq` and then press enter key to save and finish 

### Create a new Arch Linux Droplet
Back in your terminal, you will be running the following `doctl` command to create the Droplets. you may need to change 

1. Copy and paste the following code into your terminal 

```bash 
doctl compute droplet create example-droplet --image 165084633 --size s-1vcpu-1gb-amd --region sfo3 --ssh-keys 43491384 --user-data-file ~/cloud-config.yaml 
```

Here is what each part of the command represents:

* `doctl compute droplet create`: The command doctl requires to create Droplets.
* `--image`: The OS image used to create the Droplet. For this example, the Droplet uses Arch Linux
* `--size s-1vcpu-1gb`: The number of processors and the amount of RAM each Droplet has. In this case, each Droplet has one processor and 1 GB of RAM.
* `--region sfo3`: The region to create the Droplets in. In this example, doctl deploys the Droplets into the San Francisco 3 datacenter region because it's the closest location.
* `--ssh-keys`: The SSH keys to import into the Droplet from your DigitalOcean account. You can retrieve a list of available keys by running `doctl compute ssh-key list`
* `--user-data-file <path-to-your-cloud-init-file>`: Specifies the path to your cloud-config.yaml file. For example, `~/cloud-config.yaml`. 

#### Successful droplet creation looks like:

![Image of the completed droplet creation](/Assets/Images/complete_droplet_make.png)

## How to connect to your droplet using SSH

1. Open your Terminal
2. Run the following code:

```bash
doctl compute ssh 447352201 --ssh-key-path ~/.ssh/hw-key --ssh-user user-name
```

3. Copy the following code into your new config file

```bash
Host arch
  HostName 24.144.89.149
  User arch
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/hw-key
  StrictHostKeyChecking no
  UserKnownHostsFile /dev/null

```

4. Copy the IP address of the droplet from DigitalOcean

![Image of completed droplet](/Assets/Images/completed_droplet.png)

5. Change the **HostName** to the IP address of your droplet
6. Save the Config file to finish


You're now ready to connect to your droplet using SSH! You can now connect to your droplet by using `ssh user-name`

