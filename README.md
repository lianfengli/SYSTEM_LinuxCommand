
更新源(x86 框架)

```
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bk
sudo gedit /etc/apt/sources.list

# Erase and add 
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse

sudo apt-get update
Block nouveau for cuda
sudo gedit /etc/modprobe.d/blacklist.conf

# add
blacklist nouveau
options nouveau modeset=0  

sudo update-initramfs -u
Block software updater
sudo gedit /etc/apt/apt.conf.d/99update-notifier
# comment all with #

sudo update-initramfs -u