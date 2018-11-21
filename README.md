
## RTL8812AU/21AU and RTL8814AU drivers
## with monitor mode and frame injection

### 
newest driver 5.3.4 from realtek + patches to add 8814 support.
Adapted for android, for the Zenfone 3.

### Make
Building the driver for the Zenfone 3:
```
export ARCH=arm64
export CROSS_COMPILE="/path/to/toolchain"
export KSRC="/path/to/kernel/source"
export KBUILD_OUTPUT="/path/to/kernel/build/output"
make -j$(nproc --all)
```

### Loading the driver on the phone (root required!)
Execute the following commands on the phone:
```
su
insmod 88XXau.ko
``` 
The network adapter can now be connected using USB OTG.

### Notes
Root is required for every command.

For setting monitor mode
  1. Set interface down
  ```
  ip link set wlan0 down
  ``` 
  2. Set monitor mode
  ```
  iw dev wlan0 set type monitor
  ```
  3. Set interface up
  ```
  ip link set wlan0 up
  ```
For setting TX power
```
iw dev wlan0 set txpower fixed 3000
```

### LED control

#### You can now control LED behaviour statically by Makefile, for example:

```sh
CONFIG_LED_ENABLE = n
```
value can be y or n

#### Dynamically by writing to /proc/net/rtl8812au/$(your interface name)/led_enable, for example:

```sh
$ echo "0" > /proc/net/rtl8812au/$(your interface name)/led_enable
```
value can be 0 or 1

#### check current value:

```sh
$ cat /proc/net/rtl8812au/$(your interface name)/led_enable
```
