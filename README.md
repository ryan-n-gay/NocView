<!-- TITLE: Rpi -->
<!-- SUBTITLE: A quick summary of Rpi -->

# Raspberry Pi Setup

## Operating System

[**Raspbian Lite**](https://www.raspberrypi.org/downloads/raspbian/)

This approach uses a non-GUI based OS to free up the system resources to run a web application only. 

## Login

The default login for the pi is as follows

username: pi
password: raspberry

It is **strongly** recommended that you change the default password for the pi user.

## Device Configuration

### Set Hostname

System Configuration can be done by issuing the `sudo raspi-config` command from the terminal. This will bring up a blue screen in which you can edit the device settings like the **Hostname**, **SSH connectivity**, and **VNC access** if you desire. In this case having both VNC and SSH are necessary for our use case. 

#### List to edit in raspi-config

* **Hostname:** nocview**X**
  * Replace **X** with the current display number
* Enable **SSH**
* Enable **VNC**
  * When prompted hit `Y + Enter` to install the necessary components

#### Set Pi display to use full display
[**Guide**](https://www.opentechguides.com/how-to/article/raspberry-pi/28/raspi-display-setting.html)

Back up the config file and then update it using these commands.


```
sudo cp /boot/config.txt /boot/config.txt.backup

sudo nano /boot/config.txt
```

After doing this `#disable_overscan=1 (Remove the #)` and then comment all overscan parameters.


```
#overscan_left=10
#overscan_right=10
#overscan_top=15
#overscan_bottom=15
```

Exit `Ctrl + X` press `Y` and `Enter`

#### Setup GUI and Web Browser
[**Guide**](https://die-antwort.eu/techblog/2017-12-setup-raspberry-pi-for-kiosk-mode/)

Setup Minimum Environment for GUI Applications

`sudo apt-get install --no-install-recommends xserver-xorg x11-xserver-utils xinit openbox`

Install Web Browser

`sudo apt-get install --no-install-recommends chromium-browser`

Configure Openbox

```text
# Disable any form of screen saver / screen blanking / power management
xset s off
xset s noblank
xset -dpms

# Allow quitting the X server with `CTRL`-`ALT`-`Backspace`
setxkbmap -option terminate:ctrl_alt_bksp

# Start Chromium in kiosk mode
sed -i 's/"exited_cleanly":false/"exited_cleanly":true/' ~/.config/chromium/'Local State'
sed -i 's/"exited_cleanly":false/"exited_cleanly":true/; s/"exit_type":"[^"]\+"/"exit_type":"Normal"/' ~/.config/chromium/Default/Preferences
chromium-browser --disable-infobars --kiosk 'http://your-url-here'
```

Test server - this will only work if you are on the local device only

`startx -- -nocursor`

Start X automatically on boot

`[[ -z $DISPLAY && $XDG_VTNR -eq 1 ]] && startx -- -nocursor`

Usage tips

* If Chromium (or the X server) crashes, press `Ctrl`-`Alt`-`Backspace` to kill the X server and restart it with `startx -- -nocursor`.
* If you need a terminal session, you can switch to one of the other consoles by pressing `Ctrl`-`Alt`-`F2` (or any other function key). Pressing `Ctrl`-`Alt`-`F1` brings you back to the first console where Chromium is running.

#### Setup Cron Jobs

Cron can be run as the standard user and therefore does not require `sudo` to run.

The format for cron is `m h dom mon dow command`

```text
m = minute
h = hour
dom = day of month
mon = month
dow = day of week
  Sunday = 0 & 7
  Monday = 1
  Tuesday = 2
  Wednesday = 3
  Thursday = 4
  Friday = 5
  Saturday = 6
```

`crontab -e`

Insert the following code block and adjust time and days to match that of your desired schedule.

```text
# This command will turn the display on without affecting dpms
45 6 * * 1-5 /usr/bin/vcgencmd display_power 1

# This command will turn the display off without affecting dpms
15 17 * * 1-5 /usr/bin/vcgencmd display_power 0
```

`sudo service cron restart`

### Extras

These are not necessary, but can be useful when managing multiple devices over ssh.

#### Neofetch

[**Guide**](https://github.com/dylanaraps/neofetch/wiki/Installation)

Neofetch is used to show current usages of the Pi itself. 

`sudo apt update`

`sudo apt install neofetch`

To execute run the command `neofetch`. 

Change the Neofetch config which is located in the `~/.config/neofetch/config.conf` to match this configuration. If you can't find these files, press `Ctrl + h`.

```
info title
info underline

info "OS" distro
info "Host" model
info "Local IP" local_ip
info "Kernel" kernel
info "Uptime" uptime
info "Packages" packages
info "Shell" shell
info "Resolution" resolution
info "CPU" cpu
info "CPU Usage" cpu_usage
info "GPU" gpu
info "Disk" disk
info "Memory" memory

# info "GPU Driver" gpu_driver  # Linux only
# info "DE" de
# info "WM" wm
# info "WM Theme" wm_theme
# info "Theme" theme
# info "Icons" icons
# info "Terminal" term
# info "Terminal Font" term_font
# info "Battery" battery
# info "Font" font
# info "Song" song
# info "Public IP" public_ip
# info "Users" users
# info "Install Date" install_date
# info "Locale" locale  # This only works on glibc systems.
```

To run as soon as an ssh session is initiated insert this at the bottom of the **/etc/bash.bashrc** file.

```
if [[ -n $SSH_CONNECTION ]] ; then
    neofetch
fi
```

## Conclusion

When you are finished configuring your device all that will be left to do is give it a simple reboot. 

`sudo reboot`
