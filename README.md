# Guide to Create Virtual Servers on Digital Ocean using Cloud-Init

## Introduction

 DigitalOcean is a cloud provider that offers the ability to deploy virtual servers known as Droplets. When creating a Droplet, we can automate the setup process to save time and create servers with consistent repeatable configurations using Cloud-Init. Cloud-Init is an open source initialization tool that was designed to make it easier to get your systems up and running with a minimum effort, already configured according to your needs. By using Cloud-init, we can customize a Droplet’s configuration, install software, manage users, and execute scripts as soon as the server boots up for the first time.

In this guide, we'll walk through the process of creating a Droplet on DigitalOcean using Cloud-Init, ensuring a smooth and automated setup for your virtual servers.

## Getting Started

### How to create an SSH Key pair

For our initial set up, we will begin by creating an Secure Shell Protocol (SSH) key pair for our droplet to enhance security and simplify access management. SSH (Secure Shell) key pairs are a more secure alternative to traditional password-based authentication by using Public Key Cryptography. The cryptography is a key pair consists of a public key (which you place on the server) and a private key (which you keep securely on your local machine). With SSH key authentication, there's no need to worry about using weak or compromised passwords. Once set up, you can connect to the Droplet without needing to type a password each time. Instead, your local machine automatically uses the private key to authenticate you.

By default, you should already have the `ssh-keygen` util already installed on your MacOS or Windows machine.

#### Create a .ssh directory (If you don't have one)

To create the SSH keys on Windows, you may have to create a .ssh directory in your home directory first.

1. Open your **Terminal** 
2. Run the following command:

```bash
mkdir $HOME\.ssh
```

This will create the `.ssh` folder in your user's home directory (usually `C:\Users\YourUsername\.ssh`).

#### Creating the SSH Key Pair

In this section, we will be using the terminal to create two plain text files in the .ssh directory that will be our keys.
- We will create a "do-key" as our private key
- And we will create "do-key.pub" as our public key

1. Enter the following code into your Terminal window and change the appropriate information:

```bash
`ssh-keygen -t ed25519 -f C:\Users\your-user-name\.ssh\do-key -C "youremail@email.com"`
```

NOTE: You will need to change *your-user-name* and *"youremail@email.com"* to your actual information.

### How to add your public key to your DigitalOcean account

Once we've created our SSH keys, we will need to add it to our DigitalOcean account to use for our droplet. We will be using terminal commands to copy our new public key to add to our DigitalOcean droplet. 