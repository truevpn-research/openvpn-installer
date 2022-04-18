# OpenVPN Server Installer
An OpenVPN server installer shell script based on Nyr's source-code.

## openvpn-install
OpenVPN [road warrior](http://en.wikipedia.org/wiki/Road_warrior_%28computing%29) installer for Ubuntu, Debian, AlmaLinux, Rocky Linux, CentOS and Fedora.

This script will let you set up your own VPN server in no more than a minute, even if you haven't used OpenVPN before. It has been designed to be as unobtrusive and universal as possible.

### Installation
Run the script and follow the assistant:

`wget https://raw.githubusercontent.com/truevpn-research/openvpn-installer/main/openvpn-install.sh -O openvpn-install.sh && bash openvpn-install.sh`

Once it ends, you can run it again to add more users, remove some of them or even completely uninstall OpenVPN.


### Configure User-Pass Authentication

### Server Config
``
nano /etc/openvpn/server/server.conf
``

Comment ctr-verify crl.pem since we use use-pass

```
# crl-verify crl.pem
duplicate-cn
reneg-sec 0
plugin /usr/lib/x86_64-linux-gnu/openvpn/plugins/openvpn-plugin-auth-pam.so openvpn
verify-client-cert optional
username-as-common-name
```

### Quick
Add a new profile in /etc/pam.d
``
touch /etc/pam.d/openvpn
``
Add User Group
``
groupadd openvpn
``

Finally, Add user to the usergroup.

``
useradd -g "openvpn" -s /bin/false truevpn && passwd USERNAME
``

### In Details

1. Add a new profile in /etc/pam.d

In the first step the openvpn profile should be created with a:
sh ``touch /etc/pam.d/openvpn``

This profile was filled with the following content:
``
auth    required        pam_unix.so    shadow    nodelay
account required        pam_unix.so
``
There is lots information on the internet for this theme but for a short overview PAM Essentials can deliver information about session management and PAM.
2. Create a group called "openvpn" and create one new user with a password.

The group can be created over the console/terminal with a:
groupadd openvpn
The user, named "testuser" in this example, can be created with the following commands:
``
useradd -g "openvpn" -s /bin/false testuser     # creates the user to the group openvpn without shell access
passwd testuser                                 # set a password for this user
``
3. Prepared arrangements need to be integrated into the OpenVPN configuration files

The following directives should be added to the file /var/ipfire/ovpn/scripts/server.conf.local:

# Additional config directives
plugin `/usr/lib/openvpn/plugins/openvpn-plugin-auth-pam.so openvpn`
In this case, the plugin will be called over the absolute path which queries then also the before created PAM profile "openvpn".
and the following directives to the file /var/ipfire/ovpn/scripts/client.conf.local:

# Additional config directives
auth-user-pass
This entry initiates the call to the client-side account query.
