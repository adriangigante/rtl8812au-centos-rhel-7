## Forks

Forked from cccooo/rtl8812au-centos-7.6
- Updated Makefile to compile on RHEL/CentOS 7.6.7.8
- Added further instruction on how to install the driver

## RTL8812AU/21AU and RTL8814AU drivers
## with monitor mode and frame injection

### 
newest driver 5.3.4 from realtek + patches to add 8814 support

### DKMS
This driver can be installed using [DKMS]. This is a system which will automatically recompile and install a kernel module when a new kernel gets installed or updated. To make use of DKMS, install the `dkms` package, which on Debian (based) systems is done like this:
```
sudo apt-get install dkms
```

### Installation of Driver
In order to install the driver open a terminal in the directory with the source code and execute the following command:
```
sudo ./dkms-install.sh
```

### Removal of Driver
In order to remove the driver from your system open a terminal in the directory with the source code and execute the following command:
```
sudo ./dkms-remove.sh
```

### Make
For building & installing the driver with 'make' use
```
make
make install
```

### Add module
Add module to the kernel
```
modprobe 88XXau
```
Done!
Your wireless device should be up & running. You can check you logs with
```
smesg
```
and expect something like this
```
[ 4854.663662] 88XXau: loading out-of-tree module taints kernel.
[ 4854.694921] 88XXau: module verification failed: signature and/or required key missing - tainting kernel
[ 4854.781005] usbcore: registered new interface driver rtl88xxau
[ 4854.795396] IPv6: ADDRCONF(NETDEV_UP): wlp0s20f0u13: link is not ready
[ 4855.062479] IPv6: ADDRCONF(NETDEV_UP): wlp0s20f0u13: link is not ready
[ 4855.064963] IPv6: ADDRCONF(NETDEV_UP): wlp0s20f0u13: link is not ready
[ 4855.077320] IPv6: ADDRCONF(NETDEV_UP): wlp0s20f0u13: link is not ready
[ 5199.941756] IPv6: ADDRCONF(NETDEV_UP): wlp0s20f0u13: link is not ready
```

### Notes
Download
```
git clone https://github.com/adriangigante/rtl8812au-centos-rhel-7.git
cd rtl*
```
Package / Build dependencies (Kali)
```
sudo apt-get install build-essential
sudo apt-get install bc
sudo apt-get install libelf-dev
sudo apt-get install linux-headers-`uname -r`
```
For Raspberry (RPI)
```
sudo apt-get install raspberrypi-kernel-headers
```
For setting monitor mode
  1. Fix problematic interference in monitor mode. 
  ```
  airmon-ng check kill
  ```
  You may also uncheck the box "Automatically connect to this network when it is avaiable" in nm-connection-editor. This only works if you have a saved wifi connection.
  
  2. Set interface down
  ```
  sudo ip link set wlan0 down
  ``` 
  3. Set monitor mode
  ```
  sudo iw dev wlan0 set type monitor
  ```
  4. Set interface up
  ```
  sudo ip link set wlan0 up
  ```
For setting TX power
```
sudo iw wlan0 set txpower fixed 3000
```

### LED control

#### You can now control LED behaviour statically by Makefile, for example:

```sh
CONFIG_LED_ENABLE = n
```
value can be y or n

#### statically by module parameter in /etc/modprobe.d/8812au.conf or wherever, for example:

```sh
options 88XXau rtw_led_enable=0
```
value can be 0 or 1

#### or dynamically by writing to /proc/net/rtl8812au/$(your interface name)/led_enable, for example:

```sh
$ echo "0" > /proc/net/rtl8812au/$(your interface name)/led_enable
```
value can be 0 or 1

#### check current value:

```sh
$ cat /proc/net/rtl8812au/$(your interface name)/led_enable
```

### NetworkManager

Newer versions of NetworkManager switches to random MAC address. Some users would prefer to use a fixed address. 
Simply add these lines below
```
[device]
wifi.scan-rand-mac-address=no
```
at the end of file /etc/NetworkManager/NetworkManager.conf and restart NetworkManager with the command:
```
sudo service NetworkManager restart
```
