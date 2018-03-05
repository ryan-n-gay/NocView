<!-- TITLE: Rpi -->
<!-- SUBTITLE: A quick summary of Rpi -->

# Raspberry Pi Setup

## Device Configuration

### Set Host Name

**Host name:** nocview**X**

* Replace **X** with the current display number

### Install Neofetch

Neofetch is used to show current usages of the Pi itself.

`sudo add-apt-repository ppa:dawidd0811/neofetch`

`echo "deb http://dl.bintray.com/dawidd6/neofetch jessie main" | sudo tee -a /etc/apt/sources.list  326  curl "https://bintray.com/user/downloadSubjectPublicKey?username=bintray"| sudo apt-key add`

`sudo apt update`

`sudo apt install neofetch`

To execute run the command `neofetch`.

To run as soon as an ssh session is initiated insert this at the bottom of the **~/.bashrc** file.


```
if [[ -n $SSH_CONNECTION ]] ; then
    neofetch
fi
```


## Configure Browser

 ##### Initial file creation to load application

`sudo nano ~/.config/autostart/autoChromium.desktop`

#### Insert:


```
[Desktop Entry]
Type=Application
Exec=/usr/bin/chromium-browser --noerrdialogs --disable-session-crashed-bubble
--disable-infobars --start-fullscreen **link of your choice**
X-Gnome-Autostart-enable=true
Name[en_US]=AutoChromium
Name=AutoChromium
Comment=Start Chromium when GNOME starts
```



## Create Scripts
### Create Screen Off script

`cd /etc/cron.daily/`

`sudo nano screenOff.sh`

#### Insert:



```
xset -display :0 dpms force off
```




Exit `Ctrl`+`X` press `Y` and `Enter`

__Make Script exicutable__

`sudo chmod +x screenOff.sh`


### Create Screen On script

`cd /etc/cron.daily/`

`sudo nano screenOn.sh`


#### Insert:

```
xset -display :0 dpms force on && sudo xset s off && sudo xset -dpms && sudo xset s noblank
```

Exit `Ctrl`+`X` press `Y` and `Enter`

__Make Script exicutable__

`sudo chmod +x screenOn.sh`

## Setup Cron job

`crontab -e`

#### Insert:



```
	45 6 * * 1-5 /etc/cron.daily/screenOn.sh

	15 17 * * 1-5 /etc/cron.daily/script.sh
```



Exit `Ctrl`+`X` press `Y` and `Enter`

### Restart Cron

`sudo service cron restart`
