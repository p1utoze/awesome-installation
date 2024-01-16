# How to prevent WiFi driver crash after waking up from sleep
***I had set the wireless adapter power management as off
And created a bash script to power off continously as cron job*** ^c97de2

To undo it: execute these commands:
```
$ sudo su
$ crontab -e
*/1 * * * * /etc/pm/power.d/wireless_power_management_off
```
And 
Change the value of `wifi.powersave` field in `/etc/NetworkManager/conf.d/default-wifi-powersave-on.conf`

Possible values for the `wifi.powersave` field are:
```
NM_SETTING_WIRELESS_POWERSAVE_DEFAULT (0): use the default value
NM_SETTING_WIRELESS_POWERSAVE_IGNORE  (1): don't touch existing setting
NM_SETTING_WIRELESS_POWERSAVE_DISABLE (2): disable powersave
NM_SETTING_WIRELESS_POWERSAVE_ENABLE  (3): enable powersave
```


It is not sufficient to turn off wireless power management at boot.

There are probably hooks like if I plug off power adapter.

So one of possible solutions is as follows; step-by-step.

Create a directory, where you wish to store the file, if not already having one for all your scripts, I personally want to have it in `/etc/pm/`:

```
sudo mkdir -p /etc/pm/power.d
```

Create (anywhere you like) a script, name it to be sensible, for me it is:

```
sudo nano /etc/pm/power.d/wireless_power_management_off
```

I used `nano`, but use whatever, e.g. if you want to create the file graphically, eg. with `gedit` (LM17) or `xed` (LM18):

```
gksudo gedit /etc/pm/power.d/wireless_power_management_off
gksudo xed /etc/pm/power.d/wireless_power_management_off
```

Enter the following contents to the file:

```
#!/bin/bash

/sbin/iwconfig wlan0 power off
```

Save the file.

Owner of the file should be `root`, if you created the file as normal user somewhere, go to the folder where it is and fix it with:

```
sudo chown root:root wireless_power_management_off
```

Next, you need to set proper permissions to the file, `rwx` for owner:

```
sudo chmod 700 wireless_power_management_off
```

Finally we will be executing the script every minute using CRON; **dirty but worky**:

```
sudo crontab -e
```

If you never edited `crontab` before, it will ask what editor you wish to use, this is totally up to you.

Paste this to the end of the file:

```
*/1 * * * * /etc/pm/power.d/wireless_power_management_off
```

Wait a minute and then you may check if power management if turned off:

```
iwconfig wlan0 | grep "Power Management"
```

Example output:

```
Power Management:off
