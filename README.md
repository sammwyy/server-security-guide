# Server Security Guide
A guide for secure your linux server!

## Index:
- [1. SSH & Authentication+](#ssh-&-authentication)
  - [What is SSH?](#what-is-ssh?)
  - [What is SSH?](#the-ssh-port?)

## SSH & Authentication
### What is SSH?
SSH is the name of a protocol and the program that implements it whose main function is remote access to a server through a secure channel in which all information is encrypted.  

This service is the one we will use to authenticate and access the terminal of our server or manage the files are SFTP.  

### The SSH port
The SSH port (default is 22) is the one that allows us from our computer or any device to connect to this service with an SSH client (example [Bitvise](https://www.bitvise.com/ssh-client-download) or [Putty](https://www.putty.org/)).

It is very important to protect it, the most basic thing would be to change the port.  
To do this we will follow the following instructions:  
```bash
# In a terminal:
apt install nano
nano -w /etc/ssh/sshd_config
```
This command will open the file `/etc/ssh/sshd_config` which contains the parameters of the SSH service. We will go to the line that contains `# Port 22` and we will eliminate the sharp (#), then we will change the number 22 for which we want to be our new SSH port.  
then we will restart the SSH service using:  
```bash
service sshd restart
```
