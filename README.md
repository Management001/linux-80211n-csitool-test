# linux-80211n-csitool-test

## 기반
https://github.com/Management001/IEEE-802.11n-CSI-Camera-Synchronization-Toolkit

Ubnuntu version : 18.04.0 lts 

Kernel version : 4.15.0-20-generic

NIC 5300 를 장착한 상태.

우분투 설치시 인터넷 연결 모두 제거. 이후, 자동 업데이트 해체 상태에서 아래와 같이 진행.

(인터넷 연결시 Kernel 4.15.0.-213-generic 버전으로 강제 업데이트)

### Up square board 설치에 경우... 참고사항

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
