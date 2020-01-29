# Server Security Guide
A guide for secure your linux server!

## Index:
- [1. SSH & Authentication](#ssh-&-authentication)
  - [What is SSH?](#what-is-ssh?)
  - [The SSH port](#the-ssh-port?)
  - [Bruteforce - Limit ssh connections](#Bruteforce---Limit-ssh-connections)
- [2. Ports and services](#ports-&-services)
  - [Block Port Scans](#block-port-scans)


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

### Bruteforce - Limit ssh connections
Remember that if you have a firewall service, you will not be able to access it again if you do not open the new port that you will place instead of 22.  

There is a password attack called "Brute Force", this coincides in making multiple password attempts until you find the correct one.  
To block this we can configure our server to allow only one connection every one minute from an IP address. 

Having iptables installed and ready to use, we will write to the terminal:
```bash
iptables -I INPUT -p tcp --dport 22 -i eth0 -m state --state NEW -m recent --set
iptables -I INPUT -p tcp --dport 22 -i eth0 -m state --state NEW -m recent --update --seconds 60 --hitcount 2 -j DROP
```

If you have changed the SSH port that comes by default, you must change the parameter "22" in the command.  
You can also change the time that must pass between connection from the same ip, the default parameter is 60 seconds.  

## Ports & Services
### Block Port Scans
It is important if we have services with public ports on our server to block network scans to prevent intruders from knowing where to attack.  

For this we will install a tool that will help us to falsify ports and we will install it as follows.
```bash
# If we do not have git installed, we will install it:
apt install git

# then we will clone the repository using:
git clone https://github.com/drk1wi/portspoof.git
cd portspoof

# and we will install the program as follows:
./configure && make && sudo make install
portspoof -c portspoof.conf -s portspoof_signatures -D
```

Finally, we must specify which ports we do not want to be redirected and which do.  
For this we will use the following command:
```bash
iptables -t nat -A PREROUTING -i venet0 -p tcp -m tcp -m multiport --dports INSERT:PORT,RANGE:HERE -j REDIRECT --to-ports 4444
```

Here is a brief explanation:  
Suppose I want to block all ports except 22.  
Then the range of ports would be like this:  
1: 21.23: 65535  

Now suppose I want to block all ports except 22 and 80, then the range of ports would look like this:  
1: 21.23: 79.81: 65535  

Now as a last example, suppose I want to block all ports except 22, 80 and 3306, so the range of ports would look like this:  
1: 21.23: 79.81: 3305,3307: 65535  

it is understood?  
Basically in the following operation:  
1: 21.23: 79.81: 3305,3307: 65535  

We specify that:  
from 1 to 21 will be blocked (leaving 22 free)  
from 23 to 79 will be blocked (leaving 80 free)  
81 to 3305 will be blocked (leaving 3306 free)  
and finally from 3007 to 65535 they will be blocked.  

(port 65535 is the last)  
