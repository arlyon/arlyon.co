---
title: "Rpi Security"
date: 2018-01-13T15:23:19Z
draft: true
tags: ["raspberry", "pi", "linux"]
categories: ["linux"]
---

edit2: PasswordAuthentication typo corrected.

edit: and you should all configure unattended updates to keep the system secure. I haven't wrote about this because I never tried unattended updates before.

Well, changing a local user password doesn't strengthen the security of the system. If you have physical access there's 0 security.

Every time I make a new Linux machine, and this is valid for the PI of course, I do the following steps:

Update the system and reboot once..reboot can be avoided but I don't care at this point. I don't feel like checking if a particular security update required me to reboot.
apt-get update && apt-get upgrade -y
Install and configure sudo.
Sometimes already installed with the system. There's two strategy that I use. Either we grant sudo access to everyone part of the sudo group (named wheel in certain system, I think) or we only permit individual accounts access. The latter is tighter control.

apt-get install sudo
Create a user account: adduser <username>
I believe any users belonging to the sudo group is automatically granted sudo access without having to do anything else so let's add the group to the user account: usermod -a -G sudo <username>
To permit individual accounts (optional):

Run the sudo editor tool, do not edit the file manually: visudo
For individual account access add this line to the file: <UserName> ALL=(ALL) ALL change "<UserName> to the linux account name. Probably comment the line granting access to the sudo group.
Tight up ssh access and root
Well, passwords aren't safe. Anyone could forcebrute your password and get access. It's a bad idea to have the root account around because then you can't really track who actually ran a command. You will only see that someone using root did some shit.

So we will do a few things, generate a ssh keypair, disable passwords, disable root login, and disable root account. The keys will be 4096 bytes long, unthinkable to factor it with today's modern computers. Feel free to supply a password to encrypt it. That way if anyone gets hold of your private key, they won't be able to do anything with it until they crack your, hopefully, difficult to crack password. The process generates a pair of keys. If you haven't supplied a different names they will be: id_rsa and id_rsa.pub. The public key (.pub) can be shared publicly. The other one (the private key) is a secret and must be secured at all cost.

From your client machine: ssh-keygen -t rsa -b 4096 and give it a password to encrypt it (otherwise just press enter). If you change the name of the keys, you will have to supply additional options to specify the key when you are trying to ssh into your account.

Authorize your key to your newly created user account (NOT THE ROOT ACCOUNT!) ssh-copy-id [-i [identity_file]] user@host. Supply the -i (identity) flag with the path to your public key (.pub) if you changed the name.

Log back in to your user account.

Open sshd_config: sudo nano /etc/ssh/sshd_config

Find the line containing PermitRootLogin and change it to "no" or just add the line if it doesn't exists: PermitRootLogin no. You can't ssh into the root account anymore.

Limit ssh access to specific users: AllowUsers <user1> <user2> [...]

Disable password login: PasswordAuthentication no

reload the service: sudo service sshd restart or sudo systemctl sshd restart

Delete the root account password so no one can ever log into it: passwd -d root

Install fail2ban: sudo apt-get install fail2ban. By default, it will monitor sshd logs and temporary ban IP addresses trying to bruteforce their way in.

Copy the jail.conf file of fail2ban for good measure. If you want to override the default config you must do it in the jail.local file: sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local

Configure the firewall
I'm dumb, we're all dumb. Who knows how to use iptables? I don't. Let's use ufw - Uncomplicated Firewall. It handles ipv4 and ipv6 rules by default:

sudo apt-get install ufw

sudo ufw default deny incoming - we're denying all incoming traffics by default.

sudo ufw default allow outgoing - we're allowing all outgoing traffic. Look, technically you could have a backdoor at one point, dialing home and sending data somewhere. Whitelisting incoming outgoing traffic is too hard to track and maintain.

sudo ufw allow ssh - let's not lock ourself out.

sudo ufw enable - The new firewall rules are enabled now.

If you are running a website you can allow http/https traffic like so:

sudo ufw allow http

sudo ufw allow https

You can check the rules with: sudo ufw status verbose.