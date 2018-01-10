# POCKET BEAGLE INSTALLATION

## LOADING IMAGE:
Using Etcher or Win32DiskImager, make a SSD (16 GB) using this image file:
 https://debian.beagleboard.org/images/bone-debian-9.2-iot-armhf-2017-10-10-4gb.img.xz

## CONNECT & SHARE
Insert ssd and connect the micro USB to a windows based computer.
The "USB-tether-gadget", allows you to bridge and share the Ethernet Network Devices to acces Internet:
Read the Instruction on how to connect here:
https://www.digikey.com/en/maker/blogs/how-to-connect-a-beaglebone-black-to-the-internet-using-usb/cdf66181b3a5436e9ad730e4ed4cf9ee

As described in the above,  you need to create a route:
`sudo /sbin/route add default gw 192.168.7.1`

NOTE: On the new image we cannot add  name server to the resolv file,
we need to create a temporary config file:
`sudo nano /etc/resolv.conf`
`nameserver 8.8.8.8`
Remember that you have to add route and recreate `/etc/resolv.conf` at each reboot.

You can test that the route is working by issuing `ping google.com`
And get a response like that
```
PING google.com (172.217.6.206) 56(84) bytes of data.
64 bytes from lga25s54-in-f14.1e100.net (172.217.6.206): icmp_seq=1 ttl=51 time= 20.2 ms
```
A special note about FIREWALLS:  make sure your firewall is configured to  allow  a share connection !!

## UPDATING AND LOADING:
`sudo apt update`

`sudo apt upgrade bb-cape-overlays`

Get the configuration file:
`sudo wget https://github.com/PocketPilot/PocketPilot/tree/master/config/pocketpilot_pin.cfg`

Extend partition: 
`sudo /opt/scripts/tools/grow_partition.sh`

Upgrade Kernel to RT 4.14:
`sudo /opt/scripts/tools/update_kernel.sh --bone-rt-kernel --lts-4_14`

Removing some unused packages:
`sudo apt remove haveged bonescript c9-core-installer roboticscape bb-node-red-installer --purge`

Shutdown apache2 (we dont use it):
`sudo systemctl disable apache2`

REBOOT AND TEST
`sudo reboot`
 
Confirm the New kernel:
`uname -a`
`Linux beaglebone 4.14.11-bone-rt-r12 #1 PREEMPT RT Wed Jan 3 23:08:51 UTC 2018 armv7l GNU/Linux`

Confirm file system has been extended:
```
df
Filesystem 		1K-blocks	 Used 		Available 	Use% 
/dev/mmcblk0p1 	15247576 	1739372 	12837360 	12% /
```

Check SPI correctly mapped (without adding a DTB):
```
ls /dev/spi*
/dev/spidev0.0 /dev/spidev1.0 /dev/spidev1.1
```

Check the serial ports as well (dont forget its the letter "O"):
```ls /dev/ttyO*
/dev/ttyO0 /dev/ttyO1 /dev/ttyO2 /dev/ttyO4
```

Check that boot environment include the universal cape (used to map IO):
```cat /boot/uEnv.txt
uname_r=4.14.11-bone-rt-r12
enable_uboot_overlays=1
uboot_overlay_pru=/lib/firmware/AM335X-PRU-RPROC-4-4-TI-00A0.dtbo
enable_uboot_cape_universal=1
cmdline=coherent_pool=1M net.ifnames=0 quiet
```
Note: only the ''active" commands are showned.

You can check on the loaded services using
`sudo systemctl status`
or
`sudo systemctl list-dependencies`


## WIFI CONNECTION

OPTION A) CONNMAN 
Use it as a client connected to an Access Point.
In that case connman is already loaded and ready to use.
Configuring connman
```
sudo connmanctl
connmanctl> tether wifi disable
connmanctl> enable wifi
connmanctl> scan wifi
connmanctl> services
connmanctl> agent on
connmanctl> connect wifi_*_managed_psk ==> this is your access point
//Your are asked to enter your key//
connmanctl> quit
```
Once configure the service will be automatically started


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


Check the wifi dongle number with 
`ifconfig -a`
and confirm wlan0 or wlan1 or else

Manually start the wireless card
`sudo ifconfig wlan0 up`

Confirm its up 
`ifconfig`
You should see the Wlan interface with the others


Now edit network files:
`sudo nano /etc/network/interfaces and add the wifi config (with wlan-number)`
```
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

And this will be launched automatically as part of the arducopter service


## ARDUCOPTER SERVICE
We will edit a file containing instructions to load the pins and ArduCopter

`sudo nano /lib/systemd/system/arducopter.service`
```
[Unit]
Description=ArduCopter Service
After=networking.service
Conflicts=arduplane.service ardupilot.service ardurover.service

[Service]
ExecStartPre=/bin/bash -c "/usr/bin/config-pin -f /home/debian/pocketpilot_pin.cfg"
ExecStart=/home/debian/arducopter -C udp:192.168.8.34:14550 -E /dev/ttyO1

Restart=on-failure

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
