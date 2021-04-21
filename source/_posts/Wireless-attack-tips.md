---
title: 无线攻击的常用命令
date: 2021-01-25 09:22:25
tags: [Wireless]
categories: [Tools]
---





```
iwconfig

lsusb

demesg | grep 8187

lsmod | grep -i rtl8187

airmon-ng

iwlist wlan0 frequency

airmon-ng start wlan0
```







### 常见错误

#### ignore-negative-one

**方法一：**

在一些kali版本中，会出现这个bug

可以使用下面的命令替代：

```
aireplay-ng mon0 -0 3 -a xx:xx:xx:xx:xx:xx --ig
```

或者，指定攻击一台设备：

```
aireplay-ng mon0 -0 3 -a xx:xx:xx:xx:xx:xx -c yy:yy:yy:yy:yy:yy --ig
```

`--ig` 代表忽略channel值为-1的错误



**方法二：**

在插入网卡之前使用:

```
airmon-ng check kill
```

This will stop networking mode so there should no longer be a channel -1 error. 

在使用`airodump-ng`命令时，加上`--ignore-negative-one`标志



**方法三:**

```
service network-manager stop
airmon-ng check kill && airmon-ng start wlan0
ifconfig wlan0 down
airodump-ng -f 1000 mon0

可以尝试：
killall wpa_supplicant
```



**方法四：**

```
cd /usr/src/

sudo wget https://www.kernel.org/pub/linux/kernel/projects/backports/stable/v3.12/backports-3.12-1.tar.bz2

tar xvf backports-3.12-1.tar.bz2

cd backports-3.12-1

sudo apt-get install patch

sudo wget http://patches.aircrack-ng.org/mac80211.compat08082009.wl_frag+ack_v1.patch

sudo patch -p1 < mac80211.compat08082009.wl_frag+ack_v1.patch

sudo apt-get install libncurses5-dev
```

Now check your wireless driver first, mine is ath9k. The simplest way to check yours is by typing airmon-ng in terminal.

USE ONE THE FOLLOWING COMMANDS TO SELECT YOUR REVELENT WIRELESS CARD DRIVER, NOT ALL OF THEM !!!

(IN MY CASE IT IS make defconfig-ath9k)

```
make defconfig-alx
make defconfig-ar5523
make defconfig-ath10k
make defconfig-ath5k
make defconfig-ath6kl
make defconfig-ath9k
make defconfig-b43
make defconfig-b43legacy
make defconfig-brcmfmac
make defconfig-brcmsmac
make defconfig-carl9170
make defconfig-drm
make defconfig-ieee802154
make defconfig-iwlwifi
make defconfig-media
make defconfig-nfc
make defconfig-regulator
make defconfig-rtlwifi
make defconfig-wifi
make defconfig-wil6210
```

Now,

```
sudo make menuconfig
```



```
sudo make
```

```
sudo make install
```

```
reboot
```

参考：http://black-hat-sec.org/index.php?topic=175.0





---



### 字典生成

[How to speed up the generation of dictionaries with passwords](https://miloserdov.org/?p=5721)

#### mpfs与maskprocessor

**mpfs** is an in-memory virtual file system.

The tmpfs file system has the following features:

- The file system can use swap space when the physical load on memory requires it.
- The file system consumes as much physical memory and swap space as is required to store the current contents of the file system.
- During a remount operation (mount -o remount), the file system can be resized (without losing the existing contents of the file system).

If the tmpfs filesystem is unmounted, its contents are lost (deleted). This is very important – copy all the data from it before turning off the computer, otherwise everything will be lost.

While this is obvious, just in case: since all data resides in RAM, your computer must have enough RAM to store the data you want to put in tmpfs.

Let's create a mount point:

```
mkdir  /tmp/mytmpfs
```

Let's create a virtual file system of 20 Gigabytes in RAM:

```
sudo mount -t tmpfs -o size=20g tmpfs  /tmp/mytmpfs
```

We pass into it:

```
cd /tmp/mytmpfs
```

To create a dictionary, we will use the **maskprocessor** program. With the following command, we create a dictionary of 5 characters (uppercase and lowercase letters, numbers):

```
time maskprocessor -1 ?l?u?d ?1?1?1?1?1 > dic.txt
```



Now, for example, let's create the same dictionary on the hard disk:

```
time maskprocessor -1 ?l?u?d ?1?1?1?1?1 > dic.txt
```

As an experiment, let's take the command for converting the dictionary according to the Rule from [this article](https://miloserdov.org/?p=5715):

```
john --rules=lud5 --wordlist=dic.txt --stdout > lud5.txt
```

On the hard drive, it completed in 43 seconds, and in RAM in 41 seconds. The reason for these results is that in this case the bottleneck is the performance of one processor core (the process of creating and converting dictionaries is performed in one thread).

















