# linux-80211n-csitool-test

## 기반
https://github.com/Management001/IEEE-802.11n-CSI-Camera-Synchronization-Toolkit

Ubnuntu version : 18.04.0 lts 
Kernel version : 4.15.0-20-generic
우분투 설치시 인터넷 연결 모두 제거. 이후, 자동 업데이트 해체 상태에서 아래와 같이 진행.
(인터넷 연결시 Kernel 4.15.0.-213-generic 버전으로 강제 업데이트)

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

###

sudo modprobe -r iwlwifi mac80211

sudo modprobe iwlwifi connector_log=0x1

##

sudo ls /sys/kernel/debug/ieee80211/phy0/netdev:wlp1s0/stations/

sudo cat /sys/kernel/debug/ieee80211/phy0/netdev:wlp1s0/stations/XX:XX:XX:XX:XX:XX/rate_scale_table



echo 0x4007 | sudo tee /sys/kernel/debug/ieee80211/phy0/netdev:wlp1s0/stations/XX:XX:XX:XX:XX:XX/rate_scale_table

echo 0x4007 | sudo tee /sys/kernel/debug/ieee80211/phy0/iwlwifi/iwldvm/debug/bcast_tx_rate

echo 0x4007 | sudo tee /sys/kernel/debug/ieee80211/phy0/iwlwifi/iwldvm/debug/monitor_tx_rate



sudo dhclient wlp1s0


##

sudo linux-80211n-csitool-supplementary/netlink/log_to_file csi.dat

# On another terminal, type:
ping 192.168.0.101 -i 0.5

```
