---
title: "Set up a permanent VPN using openVPN and PIA Centos 7"
date: 2019-03-17T14:02:57Z
draft: false
author: "Gareth Jones"
tags: ["self-hosting", "VPN", "openvpn", "CentOS 7"]
code:
  copy: true
  maxShownLines: 50
---

### Packages to install

Firstly install the latest updates to your machine.

```Bash
yum update -y
```

Then we need to install the epel repository.
```Bash
yum install epel-release -y
```
And then the final tools we’ll need for general setup and to run PIA.

```Bash
yum install openvpn unzip curl wget -y
```

### Configuration
Now we need to retrieve the config for openvpn to use.

```Bash
cd /etc/openvpn
```

Use wget to download the zip file that contains all the vpn configurations for each PIA location.

```Bash
wget https://www.privateinternetaccess.com/openvpn/openvpn.zip
```

Use unzip to extract the .opvpn files to the directory.

```Bash
unzip openvpn.zip
```

Create a credentials file for your PIA logins with your favourite text editor (mines nano) default is vi.

```Bash
vim creds.conf
```

Place your PIA credentials in the file like this.

```
[username]
[password]
```

Save this file then update the permissions on the file.

```Bash
chown root:root creds.conf
chmod 400 creds.conf
```

Create a symlink for the config file you want to use, it's best to use the closest server to you to decrease latency and also keep issues with decreased speed to a minimum, I chose manchester but use the below replacing [PIA-server] with your server of choice.

```Bash
ln -s /etc/openvpn/[PIA-server].ovpn server.conf
```

Open the config file

```Bash
vim [PIA-server].ovpn
```

add/edit the line `auth-user-pass` to `auth-user-pass cred.conf`

On my server, I have SELinux set to permissive which means it logs what it would normally block if you would like to do the same edit the se config file /etc/selinux/config to show permissive in the line selinux.

Alternatively run the command

```Bash
restorecon -Rv /etc/openvpn/
```

Now to be able to test if this is working correctly we need to check what our current public IP is this is simple to do.

```Bash
systemctl enable --now openvpn@server
```

### Starting the service

To enable openvpn at boot and start it run the following.

```Bash
systemctl enable --now openvpn@server
```

Run the following to test if the VPN has connected properly.

```Bash
curl ipecho.net/plain ; echo
```

If its different it worked now reboot and see if starts on boot as expected.

reboot now
Run the curl command again

```Bash
curl ipecho.net/plain ; echo
```

If it's still different then your have successfully set up the service to auto connect on boot.