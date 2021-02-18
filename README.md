# Run Zoom Application in a Sandbox

**After some consideration and further research I recommend using the [Flatpak version of Zoom](https://flathub.org/apps/details/us.zoom.Zoom) rather than trying to sandbox it manually. The flatpak system was built with application isolation in mind in contrast to trying to achieve a sandbox with firejail afterwards. Moreover, less experienced users don't need to configure anything or meddle with symlinks.**

**I will leave the tutorial up for now as it may still be useful for some special cases or other programs than Zoom.**

## Ubuntu
### Install Zoom
Download the client application from the [official website](https://zoom.us/download). Save the `.deb` package and install it:
```bash
sudo apt install zoom_amd64.deb
```
### Install firejail
Adopted from: [https://wiki.ubuntuusers.de/firejail/](https://wiki.ubuntuusers.de/firejail/)

Now you want to install the package `firejail` either fro the official repositories or by adding a ppa first.

*Optional:*
```bash
sudo add-apt-repository ppa:deki/firejail
sudo apt update
```
Install `firejail`:
```bash
sudo apt install firejail firejail-profiles
```

### Configure firejail sandbox for Zoom

You will need a "home" directory for `zoom`:
```
sudo mkdir -p /opt/zoom/home
sudo chown -R $USER /opt/zoom
```
Zoom can then be launched in a sandbox like this:
```bash
firejail --profile=/etc/firejail/zoom.profile --private=/opt/zoom/home /opt/zoom/ZoomLauncher
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
firejail --profile=/etc/firejail/zoom.profile --private=/opt/zoom/home /opt/zoom/ZoomLauncher
```
Afterwards make the script executable:
```bash
sudo chmod +x /usr/bin/zoom
```
### Congratulations
You can now run Zoom more securely inside a sandbox environment!

## Windows
Coming soon...
