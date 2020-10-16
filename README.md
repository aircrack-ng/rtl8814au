# rtl8814au
Drivers for the rtl8814au chipset for wireless adapters

# iperf3 results
```sh
Connecting to host **.***.**.**, port 5201
[  5] local 10.0.0.101 port 55110 connected to **.***.**.** port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec  1.82 MBytes  15.2 Mbits/sec   23   89.1 KBytes       
[  5]   1.00-2.00   sec  2.49 MBytes  20.9 Mbits/sec    0    102 KBytes       
[  5]   2.00-3.00   sec  2.49 MBytes  20.9 Mbits/sec    0    120 KBytes       
[  5]   3.00-4.00   sec  3.11 MBytes  26.1 Mbits/sec    0    139 KBytes       
[  5]   4.00-5.00   sec  3.73 MBytes  31.3 Mbits/sec    0    157 KBytes       
[  5]   5.00-6.00   sec  4.04 MBytes  33.9 Mbits/sec    0    175 KBytes       
[  5]   6.00-7.00   sec  4.16 MBytes  34.9 Mbits/sec    0    194 KBytes       
[  5]   7.00-8.00   sec  4.78 MBytes  40.1 Mbits/sec    0    211 KBytes       
[  5]   8.00-9.00   sec  4.85 MBytes  40.7 Mbits/sec    0    228 KBytes       
[  5]   9.00-10.00  sec  5.97 MBytes  50.0 Mbits/sec    0    246 KBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.00  sec  37.4 MBytes  31.4 Mbits/sec   23             sender
[  5]   0.00-10.04  sec  36.5 MBytes  30.5 Mbits/sec                  receiver
```

# build & install
```sh
$ git clone -b v5.8.5.1 https://github.com/aircrack-ng/rtl8814au.git
$ cd rtl8814au
$ make
$ sudo make install
```

# debian dkms package (require dpkg-dev, dkms, dh-modaliases)
```sh
sudo apt install  debhelper dpkg-dev dkms dh-modaliases
cd driver
dpkg-buildpackage -b --no-sign
cd ..
dpkg -i *.deb
```



## UEFI Secure Boot - (boot the kernel with signed)
 if insmod the module it shows error of "Required key not available", you are using a kernel which is signed
 Only signed module can be use in this condition.

 ![sign needed error](pics/need-sign.png)

1. Create signing keys

```
    openssl req -new -x509 -newkey rsa:2048 -keyout MOK.priv -outform DER -out MOK.der -nodes -days 36500 -subj "/CN=Descriptive name/"
```
2. Sign the module

```
    sudo /usr/src/linux-headers-$(uname -r)/scripts/sign-file sha256 ./MOK.priv ./MOK.der /path/to/module
```
3. Register the keys to Secure Boot

```
    sudo mokutil --import MOK.der
```
		Supply a password for later use after reboot

4. Reboot and follow instructions to Enroll MOK (Machine Owner Key).
   Here's a sample with pictures. The system will reboot one more time.
5. Confirm the key is enrolled

```
mokutil --test-key MOK.der
```



# USB2.0/3.0 mode switch

<pre>
 initial it will use USB2.0 mode which will limite 5G 11ac throughput (USB2.0 bandwidth only 480Mbps => throughput around 240Mbps)
when modprobe add following options will let it switch to USB3.0 mode at initial driver
options 8814au rtw_switch_usb_mode=1
</<pre>


## TODO: run time change usb2.0/3.0 mode
### usb2.0 => usb3.0
```
sudo sh -c "echo '1' > /sys/module/8814au/parameters/rtw_switch_usb_mode"
```
### usb3.0 => usb2.0
```
sudo sh -c "echo '2' > /sys/module/8814au/parameters/rtw_switch_usb_mode"
```



