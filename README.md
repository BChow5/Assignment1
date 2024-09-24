# Guide to Create Virtual Servers on Digital Ocean using Cloud-Init for Windows Users

## Introduction

 *DigitalOcean* is a cloud provider that offers the ability to deploy virtual servers known as Droplets. When creating a Droplet, we can automate the setup process to save time and create servers with consistent repeatable configurations using Cloud-Init. 
 
 *Cloud-Init* is an open source initialization tool that was designed to make it easier to get your systems set up and already configured according to your needs. By using Cloud-init, we can customize a Droplet’s configuration, install software, manage users, and execute scripts as soon as the server boots up for the first time.

In this guide, we'll walk through the process of creating a Droplet on DigitalOcean using Cloud-Init, ensuring a smooth and automated setup for your virtual servers!

--- 

## Getting Started

### How to create an SSH Key pair

For our initial set up, we will begin by creating a Secure Shell Protocol (SSH) key pair for our droplet to enhance security and simplify access management. SSH (Secure Shell) key pairs are a more secure alternative to traditional password-based authentication by using *Public Key Cryptography.*

Public Key Cryptography uses a key pair that consists of a public key (which you place on the server) and a private key (which you keep securely on your local machine). With SSH key authentication, there's no need to worry about using weak or compromised passwords. Once set up, you can connect to the Droplet without needing to type a password each time. Instead, your local machine automatically uses the private key to authenticate you.

By default, you should already have the `ssh-keygen` util already installed on your MacOS or Windows machine.

##### This section will teach you how to:
* Create a .ssh directory
* Create a SSH key pair
<br>

#### Create a .ssh directory (If you don't have one)

To create the SSH keys on Windows, you may have to create a `.ssh` directory in your home directory first. The `.ssh` directory is a hidden folder located in a user's home directory. It is used to store configuration files and credentials related to SSH access. 

1. Open your **Terminal** 
2. Copy and run the following command in the terminal:

```bash
mkdir $HOME\.ssh
```

This will create the `.ssh` folder in your user's home directory (usually `C:\Users\YourUsername\.ssh`).
<br>
#### Creating the SSH Key Pair

In this section, we will be using the terminal to create two plain text files in the `.ssh` directory that will be our keys. We will create a "do-key" as our private key and "do-key.pub" as our public key.

1. Copy and run the following code into your Terminal window and change the appropriate information:
###### NOTE: You will need to change *"your-user-name"* and *"youremail@email.com"* to your actual information.

```bash
`ssh-keygen -t ed25519 -f C:\Users\your-user-name\.ssh\do-key -C "youremail@email.com"`
```
<br>

###### Successful key creation will look similar to this:

```bash
Your identification has been saved in /Users/example-user/.ssh/do-key.
Your public key has been saved in /Users/example-user/.ssh/do-key.pub.
The key fingerprint is:
SHA256:msISS7fgSE8oo9W02r14HgMzfixLWxVlQIcRGINFOG0 dbrian@DO-Loaner-ZHV2L
The key's randomart image is:
+---[RSA 3072]----+
|     *=+==+      |
|    + E..+       |
|    .o  .        |
|  .o .   .       |
|oo=.B   S        |
|+*oX B +         |
|o =.O X          |
|   o O.+         |
|    +oo          |
+----[SHA256]-----+
```

---

### How to add your public key to your DigitalOcean account

Once you've created your SSH keys, you will need to add it to our DigitalOcean account to use for our droplet. You will be using terminal commands to copy the contents of your new public key text file.

1. Open your **Terminal** window
2. Copy and run the following code:
###### NOTE: You will need to change *"your-user-name"* to the username on your local machine

```bash 
`Get-Content C:\Users\your-user-name\.ssh\do-key.pub | Set-Clipboard`
```


3. Open [DigitalOcean](https://www.digitalocean.com/) in your web browser and login 
4. Click on **Settings** in the left side menu
5. Select **Security**
6. Click **Add SSH Key**
7. Paste the contents of your public key into the **Public Key** text box
8. Type a name for your SSH key in the **Key Name** text box
###### NOTE: It's important to name your keys appropriately to distinguish between multiple keys.
9. Click **Add SSH Key** to finish
<br>

You have now added your key and it will automatically be copied to your next created droplet!

---

### How to Create a Droplet on DigitalOcean

This step will be creating our droplet on the [DigitalOcean](https://www.digitalocean.com/) website. Droplets are Linux-based virtual machines (VMs) that run on top of virtualized hardware. Each Droplet you create is a new server you can use, either standalone or as part of a larger, cloud-based infrastructure. Your created droplet will be based Arch Linux using the provided file. Arch Linux is a lightweight and flexible Linux distribution that is known for its simplicity and customization options. 

##### This section will teach you how to:
* Upload an Arch Linux Image to DigitalOcean
* Create a new Arch Linux Droplet
<br>

#### Upload an image to DigitalOcean

You will be uploading the provided Arch Linux disk image to Digital Ocean as the basis to build your droplet. The disk image provided is a file that contains an exact copy of the data and structure of a physical disk drive. It's essentially a snapshot of the disk that contains information on everything from the files and folders to the operating system and boot information. 

You can find the Arch Linux image in the Assets folder. 

1. Click the **Manage** dropdown menu
2. Select **Backups & Snapshots**
3. Select **Custom Images** 
4. Click the blue **Upload Image** button. 
5. Upload the Arch Linux image from the provided Assets folder

###### NOTE: After clicking upload, a new settings box will open and you will need to select the following settings

6. Select **Arch Linux** in the Distribution dropdown menu
7. Select **San Francisco 3** in the Choose a Datacenter Region section
8. Click **Upload Image** to finish
<br>

#### Create a new Arch Linux Droplet

1. Open [DigitalOcean](https://www.digitalocean.com/) in your web browser and login 
2. Click the green **Create** button
3. Select **Droplets**

###### NOTE: After Selecting Droplets, a new settings menu will open and you will need to select the following settings

4. Select **San Francisco** for Choose Region
5. Select **San Francisco Data Center 3** for Data Center
6. Select **Custom Images**
7. Select the Arch Linux image you uploaded in the previous step

![Image of choosing the Arch Linux image](\Assets\Images\arch_linux.png)

8. Select **Basic** for Droplet Type
9. Select the **$7/mo Premium AMD CPU** for CPU Options

![Image of choosing the options for your droplet settings image](\Assets\Images\droplet_type.png)

10. Select **SSH Key** the Authentication Method
11. Select the SSH key you added to your account earlier.
12. Type your desired name in Hostname  
13. Click **Advanced Options**
14. Select **Add Initialization scripts (free)**

![Image of the advanced options in DigitalOcean](\Assets\Images\advanced_options.png)

15. Open the **cloud-init-config.yaml** file provided in the Assets folder
16. Copy and paste the contents from **cloud-init-config.yaml** into the text box
###### NOTE: You will need to change information for name. primary_group, and ssh-authorized keys (ssh-authorize-keys should be changed to your SSH public key that you made earlier)

```bash 
#cloud-config
users:
  - name: student #change me
    primary_group: student #change me
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

17. Type your desired name in the **Host Name** text box
18. Click **Create Droplet** to finish

###### NOTE: Once completed, it may take a few minutes to finish uploading the droplet. Afterwards, you will see a screen similar to this:

![Image of completed droplet](\Assets\Images\completed_droplet.png)

Congratulations! You've now successfully created your droplet.

---

### How to Connect to your Droplet Using SSH

You will create a SSH config file to make it easier to connect to your droplet. Without doing this step, you would have to input your droplet's IP address every time you wanted to connect.

#### Create a Config File

1. Open your Terminal
2. Copy and run the following code in your terminal:
```bash 
code config
```

3. Copy the following code into your new config file

```bash
Host arch
  HostName 209.38.132.29
  User arch
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/do-key
  StrictHostKeyChecking no
  UserKnownHostsFile /dev/null
```

4. Copy the IP address of the droplet from DigitalOcean 

![Image of completed droplet](\Assets\Images\completed_droplet.png)

5. Change *HostName* to the IP address of your droplet
###### NOTE: You may need to change other information to match your settings of your droplet and SSH keys
6. Save the *Config* file to finish
<br>

##### Example of connecting using SSH:
```bash
ssh arch
```

You're now ready to connect to your droplet using SSH!