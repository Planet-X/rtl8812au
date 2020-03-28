## RTL8812AU/21AU and RTL8814AU drivers
Specifically adapted for the sm8150 Android kernel.

### What's new?
```
* 8814au and 8821au has got a update, about time
  it's a closer fit to the 8812au and it's code now.

* AP mode had 30 sec. disconnect issue, that's fixed.
* Fix some AUTOSUSPEND and EARLY_SUSPEND issues
* Some cleanup of unused code.

* Some other minors all over ..
* The 8814au and 8821au is handling/performing netter now
 
```

### IPERF3 benchmark
<b>[Device]</b> Alfa Networks AWUS036ACH<br>
<b>[Chipset]</b> 88XXau (rtl8812au)<br>
<b>[Branch]</b> v5.6.4.1<br>
<b>[Distance]</b> 10m free sight
```
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec  11.6 MBytes  97.4 Mbits/sec    0   96.2 KBytes
[  5]   1.00-2.00   sec  11.2 MBytes  93.8 Mbits/sec    0    100 KBytes
[  5]   2.00-3.00   sec  11.2 MBytes  93.8 Mbits/sec    0    100 KBytes
[  5]   3.00-4.00   sec  11.2 MBytes  93.8 Mbits/sec    0    100 KBytes
[  5]   4.00-5.00   sec  11.2 MBytes  93.8 Mbits/sec    0    100 KBytes
[  5]   5.00-6.00   sec  11.4 MBytes  95.9 Mbits/sec    0    105 KBytes
[  5]   6.00-7.00   sec  11.2 MBytes  93.8 Mbits/sec    0    105 KBytes
[  5]   7.00-8.00   sec  11.3 MBytes  94.9 Mbits/sec    0    157 KBytes
[  5]   8.00-9.00   sec  11.2 MBytes  93.8 Mbits/sec    0    157 KBytes
[  5]   9.00-10.00  sec  11.2 MBytes  94.3 Mbits/sec    0    157 KBytes
[  5]  10.00-11.00  sec  11.2 MBytes  93.8 Mbits/sec    0    157 KBytes
[  5]  11.00-12.00  sec  11.2 MBytes  93.8 Mbits/sec    0    157 KBytes
[  5]  12.00-13.00  sec  11.2 MBytes  94.4 Mbits/sec    0    157 KBytes
[  5]  13.00-14.00  sec  11.2 MBytes  93.8 Mbits/sec    0    157 KBytes
[  5]  14.00-15.00  sec  11.2 MBytes  94.4 Mbits/sec    0    157 KBytes
[  5]  15.00-16.00  sec  10.9 MBytes  91.7 Mbits/sec    0    157 KBytes
[  5]  16.00-17.00  sec  11.2 MBytes  94.4 Mbits/sec    0    157 KBytes
[  5]  17.00-18.00  sec  11.2 MBytes  94.4 Mbits/sec    0    157 KBytes
[  5]  18.00-19.00  sec  11.2 MBytes  94.4 Mbits/sec    0    157 KBytes
[  5]  19.00-20.00  sec  11.2 MBytes  93.8 Mbits/sec    0    157 KBytes
[  5]  20.00-21.00  sec  11.2 MBytes  93.8 Mbits/sec    0    157 KBytes
[  5]  21.00-22.00  sec  11.2 MBytes  93.8 Mbits/sec    0    157 KBytes
[  5]  22.00-23.00  sec  11.2 MBytes  93.8 Mbits/sec    0    157 KBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-23.15  sec   260 MBytes  94.2 Mbits/sec    0             sender
[  5]   0.00-23.15  sec  0.00 Bytes  0.00 bits/sec                  receiver
```

### Make
Building the driver for the OnePlus 7 Pro, using clang:
```
export ARCH=arm64
export SUBARCH=arm64
export KSRC="/path/to/kernel/source"
export KBUILD_OUTPUT="/path/to/kernel/build/output"
export CROSS_COMPILE="/path/to/gcc_toolchain"
export CC_DIR="/path/to/clang/bin"
make -j$(nproc --all)
```

### Loading the driver on the phone (root required!)
Execute the following commands on the phone:
```
su
insmod 8814au.ko
``` 

### Using the driver as main wifi interface
Make sure the module has been loaded, but your usb wifi adapter is NOT connected.
Then follow this closely:

  1. Disable wifi in android
  2. Remove internal wifi interfaces:
  ```
  su
  iw dev wlan0 del
  iw dev wlan1 del
  ``` 
  3. Connect your USB wifi adapter
  3. Enable wifi in android. The usb wifi adapter will now be used.

### Notes
Root is required for every command!

For setting monitor mode:
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
For setting TX power:
```
$ sudo iw wlan0 set txpower fixed 3000
```

### LED control

#### statically by module parameter in /etc/modprobe.d/8812au.conf or wherever, for example:

```sh
options 88XXau rtw_led_ctrl=0
```
value can be 0 or 1

#### or dynamically by writing to /proc/net/rtl8812au/$(your interface name)/led_ctrl, for example:

```sh
$ echo "0" > /proc/net/rtl8812au/$(your interface name)/led_ctrl
```
value can be 0 or 1

#### check current value:

```sh
$ cat /proc/net/rtl8812au/$(your interface name)/led_ctrl
```

### USB Mode Switch

0: doesn't switch, 1: switch from usb2.0 to usb 3.0 2: switch from usb3.0 to usb 2.0
```sh
$ rmmod 88XXau
$ modprobe 88XXau rtw_switch_usb_mode:int (0: no switch 1: switch from usb2 to usb3 2: switch from usb3 to usb2)
```

### Credits / Contributors

```
Alfa Networks - https://www.alfa.com.tw/
Realtek.      - https://www.realtek.com
aircrack-ng   - https://www.aircrack-ng.org

astsam        - https://github.com/astsam
evilphish     - https://github.com/evilphish
fariouche     - https://github.com/fariouche
CGarces       - https://github.com/CGarces
ZerBea        - https://github.com/ZerBea
lwfinger      - https://github.com/lwfinger
Ulli-Kroll.   - https://github.com/Ulli-Kroll

```
