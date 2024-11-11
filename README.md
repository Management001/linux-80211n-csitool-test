# linux-80211n-csitool-test

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
