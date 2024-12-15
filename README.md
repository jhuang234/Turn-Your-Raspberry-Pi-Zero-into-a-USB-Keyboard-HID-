# Turn-Your-Raspberry-Pi-Zero-into-a-USB-Keyboard-HID-
pi@raspberrypi:~ $ echo "dtoverlay=dwc2" | sudo tee -a /boot/firmware/config.txt
pi@raspberrypi:~ $ echo "dwc2" | sudo tee -a /etc/modules
pi@raspberrypi:~ $ sudo echo "libcomposite" | sudo tee -a /etc/modules


You need to add this to the END of the line in cmdline.txt here:
$sudo nano /boot/firmware/cmdline.txt

rootwait modules-load=dwc2,ghid



pi@raspberrypi:~ $ sudo touch /usr/bin/isticktoit_usb
pi@raspberrypi:~ $ sudo chmod +x /usr/bin/isticktoit_usb

/usr/bin/isticktoit_usb:

#!/bin/bash
cd /sys/kernel/config/usb_gadget/
mkdir -p isticktoit
cd isticktoit
echo 0x1d6b > idVendor # Linux Foundation
echo 0x0104 > idProduct # Multifunction Composite Gadget
echo 0x0100 > bcdDevice # v1.0.0
echo 0x0200 > bcdUSB # USB2
mkdir -p strings/0x409
echo "fedcba9876543210" > strings/0x409/serialnumber
echo "Tobias Girstmair" > strings/0x409/manufacturer
echo "iSticktoit.net USB Device" > strings/0x409/product
mkdir -p configs/c.1/strings/0x409
echo "Config 1: ECM network" > configs/c.1/strings/0x409/configuration
echo 250 > configs/c.1/MaxPower

#Add functions here
mkdir -p functions/hid.usb0
echo 1 > functions/hid.usb0/protocol
echo 1 > functions/hid.usb0/subclass
echo 8 > functions/hid.usb0/report_length
echo -ne \\x05\\x01\\x09\\x06\\xa1\\x01\\x05\\x07\\x19\\xe0\\x29\\xe7\\x15\\x00\\x25\\x01\\x75\\x01\\x95\\x08\\x81\\x02\\x95\\x01\\x75\\x08\\x81\\x03\\x95\\x05\\x75\\x01\\x05\\x08\\x19\\x01\\x29\\x05\\x91\\x02\\x95\\x01\\x75\\x03\\x91\\x03\\x95\\x06\\x75\\x08\\x15\\x00\\x25\\x65\\x05\\x07\\x19\\x00\\x29\\x65\\x81\\x00\\xc0 > functions/hid.usb0/report_desc
ln -s functions/hid.usb0 configs/c.1/
#End functions

ls /sys/class/udc > UDC
chmod 666 /dev/hidg0


 you have to run usr/bin/isticktoit_usb manually or through systemd on boot if you want it to work.





 tk@orangepilite:~$ nmcli dev wifi list
*  SSID                     MODE   CHAN  RATE       SIGNAL  BARS  SECURITY  
   EasyBox-116D28           Infra  9     54 Mbit/s  59      ▂▄▆_  WPA1 WPA2 
   Snort-Honeynet           Infra  13    54 Mbit/s  59      ▂▄▆_  WPA2      
   --                       Infra  6     54 Mbit/s  44      ▂▄__  WPA2      
   DISTORTEDPEOPLE          Infra  9     54 Mbit/s  40      ▂▄__  WPA1 WPA2 
   WLAN-341381              Infra  1     54 Mbit/s  22      ▂___  WPA2      
   FRITZ!Box Fon WLAN 7360  Infra  1     54 Mbit/s  17      ▂___  WPA1 WPA2 

tk@orangepilite:~$ sudo nmcli --ask dev wifi connect 'Snort-Honeynet'
[sudo] password for tk: 
Password: 
Device 'wlan0' successfully activated with 'ea8c06e7-4edd-4455-a336-4e736e9bef74'.
