# Run Zoom Application in a Sandbox
## Ubuntu
### Install Zoom
Download the client application from the [official website](https://zoom.us/download). Save the `.deb` package and install it:
```bash
sudo apt install zoom_amd64.deb
```
### Install firefail
Adopted from: [https://wiki.ubuntuusers.de/firejail/](https://wiki.ubuntuusers.de/firejail/)

Now you want to install the package `firefail` either fro the official repositories or by adding a ppa first.

*Optional:*
```bash
sudo add-apt-repository ppa:deki/firejail
sudo apt update
```
Install `firefail`:
```bash
sudo apt install firejail firejail-profiles
```

### Configure firejail sandbox for Zoom
Adopted from: [https://about.gitlab.com/handbook/tools-and-tips/#zoom-on-linux-using-foss-firejail](https://about.gitlab.com/handbook/tools-and-tips/#zoom-on-linux-using-foss-firejail)

```bash
mkdir -p ~/.config/firejail/
vim ~/.config/firejail/zoom.profile
```
Then paste the following config into this file:
```
noblacklist ~/.config/zoomus.conf

include /etc/firejail/zoom.local
include /etc/firejail/disable-common.inc
include /etc/firejail/disable-programs.inc
include /etc/firejail/disable-devel.inc

whitelist ~/.zoom

caps.drop all
netfilter
nonewprivs
noroot
protocol unix,inet,inet6,netlink
seccomp

private-tmp
```
You will also need a "home" directory for `zoom`:
```
sudo mkdir -p /opt/zoom/home
sudo chown -R $USER /opt/zoom
```
Zoom can then be launched in a sandbox like this:
```bash
firejail --profile=~/.config/firejail/zoom.profile --private=/opt/zoom/home /opt/zoom/ZoomLauncher
```

### Optional but recommended configuration
If you want to be able to just start Zoom from the launcher or the terminal by typing the command `zoom` you also have to remove the symlink `/usr/bin/zoom` and create a bash script with that exact same name. **This also prevents you from accidentally run Zoom outside the sandbox!**
```bash
sudo mv /usr/bin/zoom /usr/bin/zoom.backup
sudo vim /usr/bin/zoom
```
Then insert the following:
```bash
#!/bin/bash
firejail --profile=~/.config/firejail/zoom.profile --private=/opt/zoom/home /opt/zoom/ZoomLauncher
```
Afterwards make the script executable:
```bash
sudo chmod +x /usr/bin/zoom
```
### Congratulations
You can now run Zoom more securely inside a sandbox environment!

## Windows
Coming soon...
