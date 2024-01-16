# How to set a persistent Battery Charge Limit 
Kernel 5.4 brought the ability to set the battery charge threshold for some Asus laptops, by modifying the `charge_control_end_threshold` variable exposed under `/sys/class/power_supply/BAT0/`[[1]](https://github.com/torvalds/linux/commit/7973353e92ee1e7ca3b2eb361a4b7cb66c92abee)[[2]](https://patchwork.kernel.org/project/platform-driver-x86/patch/20190813003023.6748-1-kristian@klausen.dk/).

By default this value is set to `100` and reset on every power cycle[[3]](https://github.com/torvalds/linux/commit/7973353e92ee1e7ca3b2eb361a4b7cb66c92abee#diff-38095ba4871836b9eeaa6e1904739d398da72cda890e5ef932daec2995579db3R429-R434).

The effect of its change can be demonstrated as follows:

$ cat /sys/class/power_supply/BAT0/status
Charging
$ cat /sys/class/power_supply/BAT0/capacity
74
echo 60 > /sys/class/power_supply/BAT0/charge_control_end_threshold
$ cat /sys/class/power_supply/BAT0/status
Not charging

### systemd service

In order to automatically change the value at boot, [create]( https://wiki.archlinux.org/title/Create "Create") the following [systemd service]( https://wiki.archlinux.org/title/Systemd#Writing_unit_files "Systemd"):

```
$ sudo vim /etc/systemd/system/battery-charge-threshold.service
```

	[Unit]
	Description=Set the battery charge threshold
	After=multi-user.target
	StartLimitBurst=0
	
	[Service]
	Type=oneshot
	Restart=on-failure
	ExecStart=/bin/bash -c 'echo 80 > /sys/class/power_supply/BAT 1/charge_control_end_threshold'
	
	[Install]
	WantedBy=multi-user.target

```
$ sudo systemctl daemon-reload
$ sudo systemctl start battery-charge-threshold.service
$ sudo systemctl enable battery-charge-threshold.service
```

and then [enable]( https://wiki.archlinux.org/title/Enable "Enable") it.

`Restart=on-failure` and `StartLimitBurst=0` are used to work around the service failing with a `/bin/bash: /sys/class/power_supply/BAT0/charge_control_end_threshold: Permission denied` error.

This error is due to the service being started before [asus-wmi](https://github.com/torvalds/linux/blob/master/drivers/platform/x86/asus-wmi.c) could be loaded by the kernel (noted as `kernel: battery: new extension: ASUS Battery Extension` in the [journal](https://wiki.archlinux.org/title/Journal "Journal")), making it impossible to write there.

The combination of those two options allows unlimited restart of the service for `DefaultStartLimitIntervalSec` (set to 10 seconds by default in `/etc/systemd/system.conf`), leaving enough time for the sysfs path to become available and writable and thus ensuring the fastest execution of the service.

#### ***OK so systemd service didnt work***

### udev rule

The battery's `charge_control_end_threshold` power supply class attribute does not initially exist. It is added to the [sysfs(5)](https://man.archlinux.org/man/sysfs.5) directory by the `asus-nb-wmi` [kernel module](https://wiki.archlinux.org/title/Kernel_module "Kernel module"). Create a [udev rule](https://wiki.archlinux.org/title/Udev_rule "Udev rule") for `asus-nb-wmi` to set the battery's charge threshold:

```
$ sudo vim /etc/udev/rules.d/asus-battery-charge-threshold.rules
```

```
ACTION=="add", KERNEL=="asus-nb-wmi", RUN+="/bin/bash -c 'echo _value_ > /sys/class/power_supply/BAT?/charge_control_end_threshold'"
```

This rule will load automatically

The final script is....

### Persist after hibernation

While this setting will persist after [suspending to RAM](https://wiki.archlinux.org/title/Power_management/Suspend_and_hibernate "Power management/Suspend and hibernate"), it will be reset when resuming from hibernation. In order to re-execute the service after hibernation, use one of the methods described in [Power management#Sleep hooks](https://wiki.archlinux.org/title/Power_management#Sleep_hooks "Power management").

If using [Power management#Generic service template](https://wiki.archlinux.org/title/Power_management#Generic_service_template "Power management"), [enable](https://wiki.archlinux.org/title/Enable "Enable") an instance of the created template: `sleep@battery-charge-threshold.service`

If creating a script as described in [Power management#Hooks in /usr/lib/systemd/system-sleep](https://wiki.archlinux.org/title/Power_management#Hooks_in_/usr/lib/systemd/system-sleep "Power management"), use something similar to:

```
$ sudo vim /usr/lib/systemd/system-sleep/battery-threshold.sh
```

```
#!/bin/sh
case $1/$2 in
  pre/*)
    echo "Going to $2..."
    ;;
  post/*)
    echo "Waking up from $2..."
    echo 80 > /sys/class/power_supply/BAT1/charge_control_end_threshold
    ;;
esac
```

***Note: Make sure the `echo <value>` and `BAT1` directories are correct in all 3 files***
