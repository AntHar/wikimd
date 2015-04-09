# Pogoplug Pro (POGO-P02 Wifi)
* Specs: Pogoplug V3 - Dual 700Mhz ARM, 128MB RAM, SATA (internal), Gigabit Ethernet, 4 USB 2.0 Ports, Wireless*
* Specs: Pogoplug Pro - comes with built in Wi-Fi (AzureWave AW-NE762H 802.11 b/g/n PCI Express RT3090)
* Model numbers located on bottom of foot. Please ignore the label on the box. You can potential receive a Pogoplug V2 (POGO-E02 Kirkwood) when buying a P21/P22.

  - POGO-P01 - Pro (Wi-Fi)
  - POGO-P02 - Pro (Wi-Fi)
  - POGO-P21 - V3
  - POGO-P22 - V3
  - POGO-P24 - V3
  - POGO-P25 - V3
  - POGO-B01 - Classic
  - POGO-B02 - Classic
  - POGO-B03 - Classic
  - POGO-B04 - Classic

* tutorial video: [Link](https://www.youtube.com/user/gotbletu)
* tutorial website: [Link](http://blog.qnology.com/2015/04/hacking-pogoplug-v3oxnas-proclassic.html)

### activate device
- Connect power and ethernet wire
- Log into http://my.pogoplug.com
- Activate Device https://pogoplug.com/activate/
- Enable SSH in Settings https://my.pogoplug.com/settings
- Assign a SSH Password
- Note: try power cycling the Pogoplug if you dont see "Enable SSH Access" option


### SSH Connect
    # scan for live host on the network
    nmap -sP 192.168.1.1/24
    
    Starting Nmap 6.47 ( http://nmap.org ) at 2015-04-08 12:29 PDT
    Nmap scan report for router.asus.com (192.168.1.1)
    Host is up (0.00066s latency).
    Nmap scan report for 192.168.1.18
    Host is up (0.018s latency).
    Nmap scan report for OBi200 (192.168.1.99)
    Host is up (0.0012s latency).
    Nmap scan report for 192.168.1.100
    Host is up (0.00037s latency).
    Nmap scan report for PogoplugPro (192.168.1.97)
    Host is up (0.00033s latency).
    Nmap done: 256 IP addresses (5 hosts up) scanned in 2.56 seconds

* Results --> PogoplugPro (**192.168.1.97**)
    
    
    # default user is "root"
    ssh root@192.168.1.97

### install firmware
From here, everything is done via the SSH console.
    
    #Verify Pogoplug is expected version (Oxnas)
    #Stop here if not expected output.
    #Expected output
    #Hardware        : Oxsemi NAS
    cat /proc/cpuinfo | grep Hardware
    
    #stop my.pogoplug.com service
    killall hbwd
    
    #download firmware utilities
    cd /tmp
    wget http://download.qnology.com/pogoplug/v4/nanddump
    wget http://download.qnology.com/pogoplug/v4/nandwrite
    wget http://download.qnology.com/pogoplug/v4/flash_erase
    wget http://download.qnology.com/pogoplug/v4/fw_printenv
    wget http://download.qnology.com/pogoplug/v4/fw_setenv
    
    #make executable
    chmod +x flash_erase fw_printenv fw_setenv nanddump nandwrite
    
    #remount '/' as read/write
    #by default the Pogoplug OS (internal flash) is read only
    mount -o remount,rw /
    
    #setup fw_env.config for oxnas
    echo "/dev/mtd0 0x00100000 0x20000 0x20000">/etc/fw_env.config
    
    #save original envs
    /usr/local/cloudengines/bin/blparam > /blparam.txt
    
    #Download and flash new uBoot
    wget http://download.qnology.com/pogoplug/oxnas/uboot.2013.10-tld-4.ox820.bodhi.tar
    wget http://download.qnology.com/pogoplug/oxnas/uboot.2013.10-tld-4.ox820.bodhi.tar.md5
    
    #check md5sum
    md5sum -c uboot.2013.10-tld-4.ox820.bodhi.tar.md5
    
    #extract uBoot files
    tar -xf uboot.2013.10-tld-4.ox820.bodhi.tar
    
    #BE EXTRA CAREFUL WITH THE THESE COMMANDS.
    #NO TYPOS! CUT AND PASTE.
    #Erase and flash uboot on mtd0
    #Flash encoded spl stage1 to 0x0
    /tmp/flash_erase /dev/mtd0 0x0 6
    /tmp/nandwrite /dev/mtd0 uboot.spl.2013.10.ox820.850mhz.mtd0.img
    
    #Flash uboot to 0x40000
    /tmp/nandwrite -s 262144 /dev/mtd0 uboot.2013.10-tld-4.ox820.mtd0.img
    
    #Flash uboot environment
    #Erase 1 block starting 0x00100000
    /tmp/flash_erase /dev/mtd0 0x00100000 1
    /tmp/nandwrite -s 1048576 /dev/mtd0 pogopro_uboot_env.img
    
    #Set MAC Address
    /tmp/fw_setenv ethaddr "$(cat /sys/class/net/eth0/address)"
    
    #default to pogoplug classic dtb
    /tmp/fw_setenv fdt_file '/boot/dts/ox820-pogoplug-classic.dtb'
    /tmp/fw_setenv dt_load_dtb 'ext2load usb 0:1 $dtb_addr $fdt_file'
    
    #double check the MAC Address matches with
    #what is on the bottom of your Pogoplug
    /tmp/fw_printenv ethaddr
    
    #print out all uboot environment parameters
    #make sure there are no errors
    /tmp/fw_printenv > /fw_printenv.txt
    /tmp/fw_printenv

### setup netconsole
* More info here:  http://forum.doozan.com/read.php?3,14,14
* TLDR: allows troubleshooting uBoot without a serial cable.


    #Set up netconsole
    #Update IP Addresses as appropriate
    /tmp/fw_setenv preboot 'run preboot_nc'
    
    # ip address of the pogoplug
    /tmp/fw_setenv ipaddr '192.168.1.97'
    
    # ip address of your desktop
    /tmp/fw_setenv serverip '192.168.1.100'


### Debian Installation on USB Hard/Flash Drive
Plug in your USB flash drive

    #Partition your USB flash/hard drive
    /sbin/fdisk /dev/sda
    
    # Type in the following commands to erase
    # and re-partition USB flash/hard drive 
    #(WARNING - FLASH/HARD DRIVE WILL BE COMPLETELY WIPED): 
    # 
    # p # list current partitions
    # o # to delete all partitions
    # n # new partition
    # p # primary partition
    # 1 (one) # first partition
    # <enter> # default start block
    # <enter> # default end block (to use the whole drive)
    # If you're using a hard drive, create a small
    # 4GB partition instead of using the whole drive,
    # leaving the rest for a data partition
    # +4G # to create a 4GB partition
    # w # write new partition to disk
    
    #Format USB Flash Drive
    cd /tmp
    wget http://archlinuxarm.org/os/pogoplug/mke2fs
    chmod 755 mke2fs
    
    #format and label partition
    ./mke2fs -L rootfs -j /dev/sda1
    
    #mount
    mkdir /tmp/usb
    mount /dev/sda1 /tmp/usb
    cd /tmp/usb
    
    #Download Debian rootfs
    wget http://download.qnology.com/pogoplug/oxnas/Debian-3.17.0-oxnas-tld-1-rootfs-bodhi.tar.bz2 
    wget http://download.qnology.com/pogoplug/oxnas/Debian-3.17.0-oxnas-tld-1-rootfs-bodhi.tar.bz2.md5
    
    #check md5sum
    md5sum -c Debian-3.17.0-oxnas-tld-1-rootfs-bodhi.tar.bz2.md5 
    
    #extract
    tar -xvjf Debian-3.17.0-oxnas-tld-1-rootfs-bodhi.tar.bz2
    
    #cleanup
    rm Debian-3.17.0-oxnas-tld-1-rootfs-bodhi.tar.bz2*
    
    #Sync and reboot, cross your fingers
    sync
    cd ..
    umount /tmp/usb
    /sbin/reboot

### Initial Debian Setup

    #Change password
    passwd
    
    #Generate New OpenSSH Keys
    rm /etc/ssh/ssh_host*
    ssh-keygen -A
    
    #Initial update
    apt-get update
    apt-get upgrade
    
    #Set hostname to DebianPlugPro or whatever you like
    echo DebianPlugPro>/etc/hostname
    
    #Set Time Zone
    tzselect 

## = Below for Pogoplug Pro Version Only =
Basically setting up wifi connection and setup auto reconnect if it drops


### Pogoplug Pro Wireless Configuration

    #update fdt file to pogoplug pro and reboot
    #Warning: if you don't truly have a pro, your pogoplug 
    #will not boot properly.
    fw_setenv fdt_file '/boot/dts/ox820-pogoplug-pro.dtb'
    reboot

### Wi-Fi Configuration for Debian on Pogoplug

    #Add "non-free" repo
    echo "deb http://http.debian.net/debian/ wheezy main contrib non-free">>/etc/apt/sources.list
    
    #Update repo
    apt-get update
    
    #Install required Wi-Fi packages and common non-free Wi-Fi adapter firmware
    apt-get install wireless-tools wpasupplicant usbutils firmware-ralink firmware-realtek firmware-atheros
    
    #Bring up Wi-Fi adapter. If you get an error, try rebooting.
    ifconfig wlan0 up
    
    #Scan available Wi-Fi networks
    iwlist wlan0 scanning
    
    #Update interfaces file with Wi-Fi configuration
    #Add the following to the end of the /etc/network/interfaces file
    vi /etc/network/interfaces
    
    auto wlan0
    iface wlan0 inet dhcp
    wpa-ssid "YourWiFiNetworkName"
    wpa-psk "YourWiFiPassword"
    



    #Restart Networking Service
    /etc/init.d/networking restart
    
    #Check if Wi-Fi configuration successful
    #Note the IP Address assigned to wlan0
    ifconfig

### setup auto reconnect wlan0 (Wi-Fi)
Download the script and edit if you need https://github.com/dweeber/WiFi_Check/blob/master/WiFi_Check

    cd /usr/local/bin/
    wget --no-check-certificate https://raw.githubusercontent.com/dweeber/WiFi_Check/master/WiFi_Check
    chmod +x WiFi_Check
    
    apt-get install cron
    
    crontab -e
    */3 * * * * /usr/local/bin/WiFi_Check

### references
http://blog.qnology.com/2015/04/hacking-pogoplug-v3oxnas-proclassic.html
http://blog.qnology.com/2014/11/debian-on-pogoplug-tutorial-wireless.html
http://forum.doozan.com/read.php?3,14,14
https://github.com/dweeber/WiFi_Check/blob/master/WiFi_Check

### contact

                 _   _     _      _         
      __ _  ___ | |_| |__ | | ___| |_ _   _ 
     / _` |/ _ \| __| '_ \| |/ _ \ __| | | |
    | (_| | (_) | |_| |_) | |  __/ |_| |_| |
     \__, |\___/ \__|_.__/|_|\___|\__|\__,_|
     |___/                                  

- http://www.youtube.com/user/gotbletu
- https://twitter.com/gotbletu
- https://www.facebook.com/gotbletu
- https://plus.google.com/+gotbletu
- https://github.com/gotbletu
- gotbletu@gmail.com


