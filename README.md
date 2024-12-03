# linux-80211n-csitool-test

test1

## 기반
https://github.com/Management001/IEEE-802.11n-CSI-Camera-Synchronization-Toolkit

Ubnuntu version : 18.04.0 lts 

Kernel version : 4.15.0-20-generic

NIC 5300 를 장착한 상태.

우분투 설치시 인터넷 연결 모두 제거. 이후, 자동 업데이트 해체 상태에서 아래와 같이 진행.

(인터넷 연결시 Kernel 4.15.0.-213-generic 버전으로 강제 업데이트)

### Up square board 설치에 경우... 참고사항

NIC CARD 장착시 우분투 설치가 안되거나 강제 제부팅 현상이 발생.

Ubnuntu version : 18.04.0 lts 가 아닌 18.04.5 lts 으로 설치 이후 커널을 다운그레이드 진행.

사용한 커널 버전

Linux 4.15.0-137-generic

커널 변경 방법
https://andrewpage.tistory.com/411

## 참고 사이트

### 18.04.0 lts Download site
https://old-releases.ubuntu.com/releases/18.04/

### CSI part
https://hackmd.io/@linjiayou/HJUOPkwjF

  https://github.com/spanev/linux-80211n-csitool
  
  https://github.com/dhalperi/linux-80211n-csitool-supplementary
  
https://www-sop.inria.fr/teams/diana/orion/

https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/refs/

### Camera part
https://kwonkai.tistory.com/128

카메라 연동 오류 존재. 현재는 파악중.

```bash

# CSI 데이터를 추출하기 위한 설치단계

sudo apt-get install build-essential linux-headers-$(uname -r) git-core

sudo add-apt-repository ppa:ubuntu-toolchain-r/test

sudo apt-get update

sudo apt-get install gcc-8 g++-8

sudo apt-get install libgtk2.0-dev

sudo apt-get install pkg-config 

sudo apt-get install cmake

sudo apt update

ls -l /usr/bin/gcc /usr/bin/g++

sudo rm /usr/bin/gcc

sudo rm /usr/bin/g++

sudo ln -s /usr/bin/gcc-8 /usr/bin/gcc

sudo ln -s /usr/bin/g++-8 /usr/bin/g++

ls -l /usr/bin/gcc /usr/bin/g++

sudo apt update

sudo apt-get install gcc make linux-headers-$(uname -r) git-core

git clone https://github.com/spanev/linux-80211n-csitool.git

cd linux-80211n-csitool

CSITOOL_KERNEL_TAG=csitool-$(uname -r | cut -d . -f 1-2)

git checkout ${CSITOOL_KERNEL_TAG}

make -j `nproc` -C /lib/modules/$(uname -r)/build M=$(pwd)/drivers/net/wireless/intel/iwlwifi modules

sudo make -C /lib/modules/$(uname -r)/build M=$(pwd)/drivers/net/wireless/intel/iwlwifi INSTALL_MOD_DIR=updates modules_install

sudo depmod

cd ..

git clone https://github.com/dhalperi/linux-80211n-csitool-supplementary.git

for file in /lib/firmware/iwlwifi-5000-*.ucode; do sudo mv $file $file.orig; done

sudo cp linux-80211n-csitool-supplementary/firmware/iwlwifi-5000-2.ucode.sigcomm2010 /lib/firmware/

sudo ln -s iwlwifi-5000-2.ucode.sigcomm2010 /lib/firmware/iwlwifi-5000-2.ucode

make -C linux-80211n-csitool-supplementary/netlink

---------------------------------------------------------------------------------------------------

# CSI 추출 파일 및 카메라 동기화를 위한 OpenCV 설치단계

cd IEEE-802.11n-CSI-Camera-Synchronization-Toolkit/camera_tool

unzip opencv-2.4.13.6.zip

cd opencv-2.4.13.6/install

cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local ..

cmake ..

make

sudo make install

sudo gedit /etc/ld.so.conf.d/opencv.conf

GEDIT에서 아래 한 줄 작성 및 저장

/usr/local/lib

sudo ldconfig

sudo gedit /etc/bash.bashrc

GEDIT에서 아래 두 줄 작성 및 저장

PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig
export PKG_CONFIG_PATH

sudo reboot

---------------------------------------------------------------------------------------------------
# Compile user-application:

cd IEEE-802.11n-CSI-Camera-Synchronization-Toolkit/supplementary/netlink

GEDIT에서 아래 한 줄 작성 및 저장

/usr/local/lib

make


해당 과정을 거치고 wifi로부터 CSI데이터를 받을 수 있는지만 체크


카메라 확인

ls -ltr /dev/video*

설치

sudo apt-get install v4l-utils -y

v4l2-ctl --list-devices

이후, 카메라 설치를 위한 Opencv단계를 거침

sudo apt install vlc

설치된 vlc media player를 열고 'Capture Device'에서 카메라 장치 ex)'/dev/vidio0' 선택

재생버튼을 눌러 카메라가 정상적으로 작동하는지 확인



다시 한 번, <#CSI 데이터를 추출하기 위한 설치단계>를 수행


---------------------------------------------------------------------------------------------------
# 추출 단계

= 와이파이 연결확인
iw dev wlp1s0 link


sudo modprobe -r iwlwifi mac80211

sudo modprobe iwlwifi connector_log=0x1


sudo ls /sys/kernel/debug/ieee80211/phy0/netdev:wlp1s0/stations/

sudo cat /sys/kernel/debug/ieee80211/phy0/netdev:wlp1s0/stations/XX:XX:XX:XX:XX:XX/rate_scale_table


echo 0x4007 | sudo tee /sys/kernel/debug/ieee80211/phy0/netdev:wlp1s0/stations/XX:XX:XX:XX:XX:XX/rate_scale_table

echo 0x4007 | sudo tee /sys/kernel/debug/ieee80211/phy0/iwlwifi/iwldvm/debug/bcast_tx_rate

echo 0x4007 | sudo tee /sys/kernel/debug/ieee80211/phy0/iwlwifi/iwldvm/debug/monitor_tx_rate


sudo dhclient wlp1s0


##

[1]
# On another terminal, type:
ping 192.168.0.101 -i 0.5

[2]
# On another terminal, type:
cd IEEE-802.11n-CSI-Camera-Synchronization-Toolkit/supplementary/netlink/log_to_file test.dat

[3]
# On another terminal, type:
sudo linux-80211n-csitool-supplementary/netlink/log_to_file csi.dat

```

---


# 1. Installation instructions of integrated CSI toolkit
## (1). Kernel version:
Before proceeding further, you need to check the version of your kernel. It should be **4.15**, otherwise, the commands below won't work. The following command will print that information:
```ruby
uname -r
```

## (2). First download the essential packages:
```ruby
sudo apt-get install build-essential linux-headers-$(uname -r) git-core
```
```ruby
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
```
```ruby
sudo apt-get update
```
If you don't need to install this gcc version, skip a below line :)
```ruby
sudo apt-get install gcc-8 g++-8
```
```ruby
sudo apt-get install libgtk2.0-dev
```
```ruby
sudo apt-get install pkg-config 
```
```ruby
sudo apt-get install cmake
```
```ruby
sudo apt update
```

## (3). Check the GCC version:
```ruby
ls -l /usr/bin/gcc /usr/bin/g++
```
```ruby
sudo rm /usr/bin/gcc
```
```ruby
sudo rm /usr/bin/g++
```
```ruby
sudo ln -s /usr/bin/gcc-8 /usr/bin/gcc
```
```ruby
sudo ln -s /usr/bin/g++-8 /usr/bin/g++
```
```ruby
ls -l /usr/bin/gcc /usr/bin/g++
```
```ruby
sudo apt update
```

## (4). Unzip OpenCV for utlizing the USB camera:
```ruby
cd IEEE-802.11n-CSI-Camera-Synchronization-Toolkit/camera_tool
```
```ruby
unzip opencv-2.4.13.6.zip
```
```ruby
cd opencv-2.4.13.6/install
```

## (5). Compile and install OpenCV:
```ruby
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local ..
```
```ruby
cmake ..
```
```ruby
make
```
```ruby
sudo make install
```

## (6). Configuration OpenCV:
```ruby
sudo gedit /etc/ld.so.conf.d/opencv.conf
```
Then add the /usr/local/lib command to the file.
```
  /usr/local/lib
```
```ruby
sudo ldconfig
```
Then add the following command to the end of the file:
```ruby
sudo gedit /etc/bash.bashrc
```
```
  PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig
  export PKG_CONFIG_PATH
```
```ruby
sudo reboot
```

## (7). If a kernel change is needed (optional)
Installing a specific kernel version
```ruby
sudo apt-get install linux-image-4.15.0-20-generic
sudo apt-get install linux-headers-4.15.0-20-generic
sudo apt-get install linux-modules-4.15.0-20-generic
sudo apt-get install linux-modules-extra-4.15.0-20-generic
```

GRUB set
```ruby
sudo gedit /etc/default/grub
```
```
  ... Omitted parts ...

  ## Setting the name of the installed Linux kernel version
  GRUB_DEFAULT='Advanced options for Ubuntu>Ubuntu, with Linux 4.15.0-20-generic'

  ## With the settings below, a countdown screen will appear for 3 seconds.
  ## During the countdown, pressing the 'ESC', 'F4', or 'SHIFT' key will bring up the GRUB boot menu.
  ## At this point, you can choose a kernel version other than the default kernel version.
  GRUB_TIMEOUT_STYLE=countdown
  GRUB_TIMEOUT=3

  ... Omitted parts ...
```

GRUB update
```ruby
sudo update-grub
```
```
  Sourcing file `/etc/default/grub'
  Generating grub configuration file ...
  Found linux image: /boot/vmlinuz-4.15.0-20-generic

  ... Omitted parts ...
```
```ruby
reboot
```

Press [ESC], [SHIFT], or [F4] to select the desired Linux kernel version, such as "Linux 4.15.0-20-generic."
```ruby
sudo apt-get install gcc make linux-headers-$(uname -r) git-core
```
```ruby
sudo apt update
```

## (8). Build and install the modified wireless LAN driver:
```ruby
git clone https://github.com/spanev/linux-80211n-csitool.git
```
```ruby
cd linux-80211n-csitool
```
```ruby
CSITOOL_KERNEL_TAG=csitool-$(uname -r | cut -d . -f 1-2)
```
```ruby
git checkout ${CSITOOL_KERNEL_TAG}
```
```ruby
make -j `nproc` -C /lib/modules/$(uname -r)/build M=$(pwd)/drivers/net/wireless/intel/iwlwifi modules
```
```ruby
sudo make -C /lib/modules/$(uname -r)/build M=$(pwd)/drivers/net/wireless/intel/iwlwifi INSTALL_MOD_DIR=updates modules_install
```
```ruby
sudo depmod
```
```ruby
cd ..
```

## (9). Install the Modified firmware:
```ruby
git clone https://github.com/dhalperi/linux-80211n-csitool-supplementary.git
```
```ruby
for file in /lib/firmware/iwlwifi-5000-*.ucode; do sudo mv $file $file.orig; done
```
```ruby
sudo cp linux-80211n-csitool-supplementary/firmware/iwlwifi-5000-2.ucode.sigcomm2010 /lib/firmware/
```
```ruby
sudo ln -s iwlwifi-5000-2.ucode.sigcomm2010 /lib/firmware/iwlwifi-5000-2.ucode
```

## (10). Build the userspace logging tool:
```ruby
make -C linux-80211n-csitool-supplementary/netlink
```

## (11). Compile user-application:
```ruby
cd IEEE-802.11n-CSI-Camera-Synchronization-Toolkit/supplementary/netlink
```
```ruby
sudo gedit /etc/ld.so.conf.d/opencv.conf
```
Then add the /usr/local/lib command to the file.
```
  /usr/local/lib
```
```ruby
make
```
Check if CSI data can be received from WiFi (refer to Guide 2-1).

## (12). Install v4l utils and vlc
Check your camera device
```ruby
ls -ltr /dev/video*
```
```ruby
sudo apt-get install v4l-utils -y
```
```ruby
v4l2-ctl --list-devices
```
```ruby
sudo apt install vlc
```
Open the installed vlc media player and select your camera device ex)'/dev/vidio0' in 'Capture Device'. Press the play button to check if the camera is operating normally.

# 2. Guideline Operation of integrated CSI toolkit
## (1). setting of the WiFi router and wireless LAN card:
The below comments (1) only need to be performed once after turning on the computer.
```ruby
iw dev wlp1s0 link
```
```ruby
sudo modprobe -r iwlwifi mac80211
```
```ruby
sudo modprobe iwlwifi connector_log=0x1
```
The below comment returns the MAC address, such as XX:XX:XX:XX:XX:XX.
```ruby
sudo ls /sys/kernel/debug/ieee80211/phy0/netdev:wlp1s0/stations/
```
Check the supported modulation, and transmission rate table from the connected WiFi
```ruby
sudo cat /sys/kernel/debug/ieee80211/phy0/netdev:wlp1s0/stations/XX:XX:XX:XX:XX:XX/rate_scale_table
```
Set the transmit rate from the checked table. In our case, we set it as 0x4007 (16-QAM, 1/2).
```ruby
echo 0x4007 | sudo tee /sys/kernel/debug/ieee80211/phy0/netdev:wlp1s0/stations/XX:XX:XX:XX:XX:XX/rate_scale_table
```
```ruby
echo 0x4007 | sudo tee /sys/kernel/debug/ieee80211/phy0/iwlwifi/iwldvm/debug/bcast_tx_rate
```
```ruby
echo 0x4007 | sudo tee /sys/kernel/debug/ieee80211/phy0/iwlwifi/iwldvm/debug/monitor_tx_rate
```

## (2). Starting this toolkit program (utilized three kernel terminals):
```ruby
sudo dhclient wlp1s0
```
comment under kernel terminal, 

where the camera parameters constructed [*Camera ID*] [*Save Interval*] [*Auto Exit*]

[*Camera ID*]: This parameter controls which camera to use when the computer has multiple cameras. When set to 0, the program will use the first camera. When set to 1, the program will use the second camera.

[*Save Interval*]: This parameter controls the speed at which images are saved. When set to 0, the program will save each frame of the camera. When set to 1, the program will save an image every other frame.

[*Auto Exit*]: This parameter controls whether the program automatically exits when CSI collects stops. When set to 0, This program will always run. When set to 1, This program will automatically exit when no CSI is acquired within 1 second.


Open another kernel terminal, where ping testing is a way to encourage the collection of more CSI samples:
```ruby
ping 192.xxx.xxx.xxx -i 0.3
```

Open the first Linux kernel to execute 'log_to_file':
```ruby
cd linux-80211n-csitool-supplementary/netlink
sudo ./log_to_file test.dat
```

Open the second Linux kernel and write the below command:
```ruby
cd linux-80211n-csitool-supplementary/netlink
./camera 0 1 0
```


