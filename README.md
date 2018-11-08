# raspberry-pi-mesh
Guide to setting up an 802.11s on Raspbian Stretch Lite

1. Download an update raspian stretch lite image and flash it to an SD card.
2. Insert the SD card into your pi and plugin in an hdmi cable and a usb keyboard. Nothing
    else! Then plug in the power cable.
3. After the system boots login as user pi password raspberry.
4. Execute the following commands to remove software that will interfere with mesh network
    setup.
    sudo apt-get purge dhcpd5 isc-dhcp-client isc-dhcp-common
5. Reboot the system.
    sudo rebooot.
6. Create the necessary systemd-networkd config files in /etc/systemd/network.

```
/etc/systemd/network/10-eth0.link
========================
[Match]
# Insert the mac address of the pi ethernet adapter. Use ifconfig -a to find it.
MACAddress=aa:bb:cc:dd:ee:ff
[Link]
Name=wired
```
```
/etc/systemd/network/10-wired.network
============================
[Match]
Name=wired
[Network]
DHCP=yes
```
7. Enable systemd-networkd.
    sudo systemctl enable systemd-networkd
    sudo reboot
8. Log back in. ifconfig should now show an active interface called wired. Next sort out name
    resolution.
    sudo systemctl enable systemd-resolved
    sudo systemctl start systemd-resolved
9. Check for the existence of the dynamic resolv.conf (or could be called something with stub
    in it).
    ls /run/systemd/resolve
10. Make /etc/resolv.conf a symbolic link to it.
    sudo rm /etc/resolv
    sudo ln -s /run/systemd/resolve/resolv.conf /etc
11. Now would be a goof time to run raspi-config to chamge the hostname, the pi user login password, and enable the ssh server. Also for completeness.
    sudo apt-get update
    sudo apt-get upgrade
12. Reboot. This is a must because there may have been a kernel update, and the current kernel is probably pointing at a non-existent /lib/modules directory. So when you plug in your WiFi adapter in the next step nothing will happen.
13. The next step is to setup the config for your wireless adapter and see if it has been recognised.
```
iw dev info
```
    If nothing is shown apart or there is a warning about 80211. You need to check that your device has a driver installed. If it is good then take a note of the mac address (addr field in output) and then check if it supports mesh point mode
```
iw phy0 info|grep "mesh point"
```
    If you see a line saying mesh point then you are good to go. If not you need to try a different wireless adapter.
14. Create the following files in /etc/systemd/network.

```
/etc/systemd/network/10-meshpoint.Link
======================================
[Match]
MACAddress=xx:xx:xx:xx:xx:xx

[Link]
Name=meshpoint
```

```
/etc/systemed/network/10-meshpoint.network
==========================================
[Match]
Name=meshpoint
```
15. Create a directory named /etc/systemd/network/10-meshpoint.network.d and put the following file in it. You can use whatever part of the locally administered address space you like.
```
/etc/systemd/network/10-meshpoint.network.d/meshpoint.conf
==========================================================
[Address]
Address=10.12.34.1/24
```
16. Reboot your system.
