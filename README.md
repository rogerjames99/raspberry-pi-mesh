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

11. Now would be a goof time to run raspi-config to chamge the hostname, the pi user login
password, and enable the ssh server. Also for completeness.
sudo apt-get update
sudo apt-get upgrade

12. Reboot


13. The next step is to setup the config for your wireless adapter. Plug it in and determine its mac address.
