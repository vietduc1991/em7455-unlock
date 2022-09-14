# em7455-unlock
unlock7455


Update Firmware On EM7455 Chip And Set Device ID To Sierra Wireless
Update Firmware On EM7455 Chip And Set Device ID To Sierra Wireless

This documentation applies to Linux Mint 20.2 & 21 and confirmed to work when running from a live environment:

Step 1: Install the software needed to update modem firmware

sudo add-apt-repository universe
sudo apt update
sudo apt-get install libqmi-glib5 libqmi-proxy libqmi-utils curl libuuid-tiny-perl libipc-shareable-perl minicom -y

Step 2: Download and unpack modem firmware from the "EM/MC74xx Approved FW Packages" page: Download the 7455 Generic Linux Binaries as pictured here



Open a terminal and unzip (replace with the version that you downloaded):

unzip SWI9X30C_02.33.03.00_Generic_002.072_001.zip

Step 3: Stop the modem manager so it doesn't interfere with the update process

systemctl stop ModemManager

Step 4: Download swi_setusbcomp.pl utility for switching the modes the modem is capable of operating in

wget https://www.thinkpenguin.com/files/em7455-modem-software/swi_setusbcomp.pl

Step 5: Put the modem into QMI mode (this may differ depending on the modem you have or revision thereof)

sudo perl swi_setusbcomp.pl --usbcomp=6

Step 6: Unplug and reconnect the modem

Step 7: Grab the device ID and flash the latest firmware

deviceid=`lsusb | grep -i -E '1199:9071|1199:9079|413C:81B6' | awk '{print $6}'`

sudo qmi-firmware-update --update -d "$deviceid" SWI9X30C_02.33.03.00.cwe SWI9X30C_02.33.03.00_GENERIC_002.072_001.nvu

Step 8: We need access to serial so put the modem in a mode which supports NEMA (8 for our modems)

sudo perl swi_setusbcomp.pl --usbcomp=8

Step 9: Unplug the modem and then plug it back in

Step 10: Using minicom we will run a few commands to set the vendor ID to stock upstream chip firm Sierra Wireless

sudo minicom -D /dev/ttyUSB2

#verify the firmware version that you flashed took

AT!IMPREF?

# set vendor ID to stock

AT!ENTERCND="A710"
AT!USBPID=9071,9070
AT!USBVID=1199
AT!USBPRODUCT="EM7455"
AT!PRIID="9904802","001.001","Generic-Laptop"
AT!RESET

Step 11: Quit minicom

'ctrl-A' then 'X' and then - ENTER

Step 12: Unplug the modem and then plug it back in

Step 13: For normal desktop distributions you will want to set the modems operating mode to MBIM

sudo perl swi_setusbcomp.pl --usbcomp=9

Step 14: Unplug the modem and then plug it back in

Step 15: To test the modem start ModemManager back up

systemctl start ModemManager

Step 16: Check to see if the modem shows up as a Sierra Wireless device using lsusb

Bus 001 Device 011: ID 1199:9071 Sierra Wireless, Inc. EM7455

Step 17: Run dmesg and see if the modem is using the cdc_mbim module (it should be) and that there are no /dev/ttyUSB[X] devices are being reported
