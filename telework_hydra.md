# Hydra on telework.si.edu

## Logging in

### Go to telework.si.edu
Log in with your si.edu address, if you haven't yet.

### Scroll down to "IT Tools"
There should be an icon for "Hydra" under the "IT Tools" section. This will take you to the "Hydra Tools" page.

### On Hydra Tools, click on "SSH terminal to hydra-login02"
This should open a new tab with a terminal window.

### Follow prompt to login
*SSH terminal to hydra-login02:* [This is your si.edu username]
*[user]@hydra-login02.cm.cluster's password*: [This is your Hydra password]

If you entered your password correctly, you should be logged in.

## Gotchas 

### Pasting multiple lines

Need to right-click and select "Paste from Browser".

This is especially important for copying from QSub Generator.

Even then, some clean-up is needed.

### Password Self-service not currently working

The link is functional under the "Hydra Tools" site, but is not working correctly. We will let everyone know when this is fixed.

### Transferring files

SCP and FileZilla will not be available through this interface.

Backup options: 
* Wget from Dropbox (and other site) links
* RClone (https://rclone.org/)
* Dropbox_uploader tool (https://confluence.si.edu/display/HPC/Disk+Space+and+Disk+Usage#DiskSpaceandDiskUsage-HowToCopy)

### Slow line output

There is a slight lag in outputting to the screen. For the most part this is just a slight annoyance, but if you run a command (like `tar` with the `-v` flag) that outputs a lot of lines, you can accidentally tie up your session. No worries -- just close the window and log back in.
