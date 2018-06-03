# POCKET BEAGLE INSTALLATION

## DEVELOPMENT STATUS:
As of June 3 2018
We have reverted the Real-Time Linux 4_9 becauses of ktimersoft was using too much cycles on 4_14.

Boot time is still very long with rt linux (90 secs), Work In Progress.

You need to build the new ArduCopter release (that reads on SPI2 -- it was SPI1 on 4_14).

Leds and Buzzer are not yet implemented in code (we strongly suggest that you work with a GCS connexion for tests).

Analog Voltage and Current works on the bench but not yet tested in flight (Please update).


## LOADING IMAGE:
Reference: https://elinux.org/Beagleboard:BeagleBoneBlack_Debian#Debian_Releases

Using Etcher or Win32DiskImager, make a SD card (16 GB) using a on of these images.
Select one of the latest bone debian console.
https://rcn-ee.net/rootfs/bb.org/testing/
https://rcn-ee.net/rootfs/bb.org/testing/2018-05-27/stretch-console/bone-debian-9.4-console-armhf-2018-05-27-1gb.img.xz

## CONNECT & SHARE
Connect to the PocketBeagle 
`ssh debian@beaglebone` (if connected via USB `ssh debian@192.168.7.2`)
Password temppwd

Insert ssd and connect the micro USB to a windows based computer.
The "USB-tether-gadget", allows you to bridge and share the Ethernet Network Devices to acces Internet:
Read the Instruction on how to connect here:
https://www.digikey.com/en/maker/blogs/how-to-connect-a-beaglebone-black-to-the-internet-using-usb/cdf66181b3a5436e9ad730e4ed4cf9ee

As described in the above,  you need to create a route:
`sudo /sbin/route add default gw 192.168.7.1`

NOTE: On some images (depends on releases) there is no resolv file:
`sudo nano /etc/resolv.conf`
`nameserver 8.8.8.8`
Remember that you have to add route and recreate `/etc/resolv.conf` at each reboot.

Other method (easier= dont need to edit file):
```
sudo -s
/sbin/route add default gw 192.168.7.1
echo "nameserver 8.8.8.8" >> /etc/resolv.conf
exit
```



You can test that the route is working by issuing `ping google.com`
And get a response like that
```
PING google.com (172.217.6.206) 56(84) bytes of data.
64 bytes from lga25s54-in-f14.1e100.net (172.217.6.206): icmp_seq=1 ttl=51 time= 20.2 ms
```
A special note about FIREWALLS:  make sure your firewall is configured to  allow  a share connection !!

## UPDATING AND LOADING:
`sudo apt update && sudo apt upgrade -y`

Extend partition: 
`sudo /opt/scripts/tools/grow_partition.sh`

REBOOT AND TEST
`sudo reboot`
 
Confirm file system has been extended (number may vary):
`df -h`
```
Filesystem      Size  Used Avail Use% Mounted on
udev            216M     0  216M   0% /dev
tmpfs            49M  4.4M   44M  10% /run
/dev/mmcblk0p1   15G  334M   14G   3% /
tmpfs           242M     0  242M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           242M     0  242M   0% /sys/fs/cgroup
tmpfs            49M     0   49M   0% /run/user/1000
```

Install software:
`sudo apt install -y cpufrequtils i2c-tools minicom g++ pkg-config gawk git make screen python python-dev python-lxml python-pip`

Install Python library:
`sudo pip install future`

Install Kernel 4.9 ti rt:
`sudo /opt/scripts/tools/update_kernel.sh --ti-rt-channel --lts-4_9`

Set clock to fixed 1GHz:
`sudo sed -i 's/GOVERNOR="ondemand"/GOVERNOR="performance"/g' /etc/init.d/cpufrequtils`

REBOOT AND TEST
`sudo reboot`
 
Confirm the New kernel:
`uname -a`
```
Linux beaglebone 4.14.11-bone-rt-r12 #1 PREEMPT RT Wed Jan 3 23:08:51 UTC 2018 armv7l GNU/Linux
```

Check SPI correctly mapped (without adding a DTB):
`ls /dev/spi*`

```
/dev/spidev1.0  /dev/spidev2.0  /dev/spidev2.1
```

Check the serial ports as well (dont forget its the letter "O"):
`ls /dev/ttyO*`
```
/dev/ttyO0 /dev/ttyO1 /dev/ttyO2 /dev/ttyO4
```

Check that boot environment include the universal cape (used to map IO):
`cat /boot/uEnv.txt`
```
uname_r=4.9.88-ti-rt-r111
enable_uboot_overlays=1
uboot_overlay_pru=/lib/firmware/AM335X-PRU-UIO-00A0.dtbo
enable_uboot_cape_universal=1
cmdline=coherent_pool=1M net.ifnames=0 quiet
```
Note: only the ''active" commands are showned.

You can check on the loaded services using
`sudo systemctl status`
or
`sudo systemctl list-dependencies`


## WIFI CONNECTION
General note on networking: 
PocketPilot does not not have an Ethernet port, so you can disable it from
the /etc/network/interfaces (add # at beginning of lines)

OPTION A) CONNMAN 
Use it as a client connected to an Access Point.
In that case connman is already loaded and ready to use.

Install connman:
`sudo apt install connman`

Start the interactive console:
`sudo connmanctl`

```
connmanctl> tether wifi disable
connmanctl> enable wifi
connmanctl> scan wifi
connmanctl> services
connmanctl> agent on
connmanctl> connect wifi_*_managed_psk ==> this is your access point
//Your are asked to enter your key//
connmanctl> quit
```
Once configured, the service will be automatically started.


OPTION B) HOSTAPD
Use it as an Access Point, on which you can connect yoy PC or Tablet.
Please note that not all chipsets can act as an Acces Point, 
take a look at the column AP =Yes: 
https://wireless.wiki.kernel.org/en/users/Drivers

This is the short list of the AP chipset:

Driver	    Manufacturer	   PHY modes
* ath9k_htc	 Atheros	        B/G/N
* carl9170	  ZyDAS/Atheros	  A(1)/B/G/N
* libertas_tfMarvell	        B/G
* p54usb	    Intersil/Conexant	A(1)/B/G
* rt73usb	   Ralink	        A(1)/B/G
* rt2500usb	 Ralink	        A(1)/B/G
* rt2800usb	Ralink	         A(1)/B/G/N
* rtl8192cu	Realtek	        B/G/N
* zd1211rw	 ZyDAS/Atheros	  A(2)/B/G

First disable connman:
`sudo systemctl disable connman`

Then update tools:
`sudo apt-get install hostapd dnsmasq iptables iw wireless-tools`

You may need to get the driver for the wifi stick, 
this is the driver for TP-LINK TL-WN722N:
`sudo apt-get install firmware-atheros`

Check the wifi dongle id with 
`sudo ifconfig -a`
and confirm wlan0 or wlan1 or else

Manually start the wireless card
`sudo ifconfig wlan0 up`

Confirm its up 
`sudo ifconfig`
You should see the Wlan interface with the others network devices


Now edit network files:
`sudo nano /etc/network/interfaces and add the wifi config (with wlan-number)`
```
allow-hotplug wlan0
auto wlan0
iface wlan0 inet static
hostapd /etc/hostapd/hostapd.conf
address 192.168.8.1
netmask 255.255.255.0
```

Then edit the hostapd.conf file: 
`sudo nano /etc/hostapd/hostapd.conf`
```
interface=wlan0
driver=nl80211
ssid=Pocket-Pilot
channel=1
ignore_broadcast_ssid=0
country_code=US
ieee80211d=1
hw_mode=g
macaddr_acl=0
max_num_sta=10
auth_algs=1
ignore_broadcast_ssid=0
rsn_preauth=1
rsn_preauth_interfaces=wlan0
wpa=3
wpa_passphrase=MINIMUM_8_CHARACTERS
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
```

Finaly make a new dnsmasq.conf file
make a copy of existing 
`sudo mv /etc/dnsmasq.conf /etc/dnsmasq.bkup`

create new file
`sudo nano /etc/dnsmasq.conf`
```
interface=lo,wlan0
no-dhcp-interface=lo
dhcp-range=192.168.8.20,192.168.8.40,255.255.255.0,12h

interface=usb0
dhcp-range=192.168.7.1,192.168.7.1,12h
```

ENABLE THE SERVICES
`sudo systemctl enable networking`
`sudo systemctl enable hostapd`

You can now reboot
`sudo reboot`

While monitoring the WIFI from the Windows PC, the ssid=Pocket-Pilot should appear within in a minute
Connect to Pocket-Pilot using the wpa_passphrase 
open a ssh session (Putty)
log on to 192.168.8.1
and voilà ;-)


## ARDUPILOT
Make Firmware as per BBBMINI instructions:
https://github.com/mirkix/BBBMINI/blob/master/doc/software/software.md
and build using pocket profile:
`/waf configure --board=pocket`

copy file in /home/debian

### PIN CONFIGURE
The new kernel is now using uboot overlays 
https://elinux.org/Beagleboard:BeagleBoneBlack_Debian#U-Boot_Overlays

All the I/O are configured from user space using config-pin utility
This utility can use a parameter file to automatically configure the Beagle Pocket
(We have downloaded this configuration file on previous step)
This is how its launched manually:
`sudo config-pin -f ./pocketpilot_pin.cfg`

Get the file:
`wget https://raw.githubusercontent.com/PocketPilot/PocketPilot/master/config/pocketpilot_pin.cfg`



## ARDUCOPTER SERVICE
We will edit a file containing instructions to load the pins and ArduCopter

`sudo nano /lib/systemd/system/arducopter.service`
```
[Unit]
Description=ArduCopter Service
After=networking.service
StartLimitIntervalSec=0
Conflicts=arduplane.service ardupilot.service ardurover.service

[Service]
ExecStartPre=/bin/sleep 30
ExecStartPre=/bin/bash -c "/usr/bin/config-pin -f /home/debian/pocketpilot_pin.cfg"
ExecStart=/home/debian/arducopter -B /dev/ttyO2 -C udp:192.168.7.1:14550

Restart=on-failure
RestartSec=1

[Install]
WantedBy=multi-user.target
```

ENABLE THE NEW SERVICE
`sudo systemctl enable arducopter.service`
the service will start automatically at bootup.


SERVICE RELATED UTILITIES
If you want to start/stop this service (or any other services):

Start service: `systemctl start {SERVICENAME}`

`sudo systemctl start arducopter.service`

Stop service: `systemctl stop {SERVICENAME}`

`sudo systemctl stop arducopter.service`

`sudo systemctl status`

`sudo systemctl list-dependencies`


## TEST THE POCKET-BEAGLE
* Start Pocket-Beagle
* Connect to Acces Point
* Start Mission Planner
* Connect UDP
* Configure as per ArduPilot instructions
