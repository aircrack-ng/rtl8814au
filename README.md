# rtl8814au
Linux drivers for rtl8814au chipset wireless adapters


## TODO
[] Fix injection capabilities again\
[] Fix USBModeSwitch function (and switch between USB 2.0 and 3.0/SuperSpeed at runtime)


## Dependencies
```
$ sudo apt update && sudo apt upgrade
$ sudo apt install build-essential dkms git libelf-dev linux-headers-`uname -r`
```


## Build and Install
```
$ git clone https://github.com/aircrack-ng/rtl8814au.git
$ cd rtl8814au
$ make
$ sudo make install
```


## DKMS installation (standard installation)
Install driver
```
$ sudo make dkms_install
```
Remove driver
```
$ sudo make dkms_remove
```


## Debian DKMS package (non-standard installation)
```
$ sudo apt install bc debhelper dpkg-dev
$ sudo dpkg-buildpackage -b --no-sign
$ cd ..
$ sudo dpkg -i rtl8814au-dkms_5.8.5.1-24835.20190115_all.deb
```


## UEFI Secure Boot compatibility
**Note:** If you have secure boot *enabled* (i.e., you're using a signed kernel), you will need to sign this module in order for it to work with your system.

To do so:

1. Create signing key
```
$ openssl req -new -x509 -newkey rsa:2048 -keyout MOK.priv -outform DER -out MOK.der -nodes -days 36500 -subj "/CN=Descriptive name/"
```

2. Sign the module
```
$ sudo /usr/src/linux-headers-$(uname -r)/scripts/sign-file sha256 ./MOK.priv ./MOK.der $(sudo modinfo -n 8814au)
```

3. Register the key to Secure Boot
```
$ sudo mokutil --import MOK.der
```
**Note 2:** you will need to supply a password here for use later on.

4. Reboot, enter MOK Management when prompted, select "Enroll MOK," follow the instructions to enroll the previously registered MOK (Machine Owner Key) with your password, and reboot one last time when prompted.

5. Confirm that the key is enrolled
```
$ sudo mokutil --test-key MOK.der
```


## Switching between USB 2.0/3.0 modes
By default, this driver is configured for USB 2.0 (i.e., a theoretical throughput of 480 MB/s).

To change the configuration:

USB 2.0 -> 3.0
```
$ sudo sh -c "echo '1' > /sys/module/8814au/parameters/rtw_switch_usb_mode"
```

USB 3.0 -> 2.0
```
$ sudo sh -c "echo '2' > /sys/module/8814au/parameters/rtw_switch_usb_mode"
```
