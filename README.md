
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
```


Block nouveau for cuda
```
sudo gedit /etc/modprobe.d/blacklist.conf

# add
blacklist nouveau
options nouveau modeset=0  

sudo update-initramfs -u
```


Block software updater
```
sudo gedit /etc/apt/apt.conf.d/99update-notifier
# comment all with #

sudo update-initramfs -u
```


Install essential
```
sudo apt-get install -y build-essential vim net-tools screen tmux cmake 
sudo apt-get install -y gparted vlc

# Install OpenCV C++
sudo apt-cache madison libopencv-dev
sudo apt-get install -y libopencv-dev
pkg-config --modversion opencv4

# Snap install netron
snap install netron

# git unzip zip cmake-curses-gui
# wget xarclock pkg-config

# Install Chrome
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo dpkg -i google-chrome-stable_current_amd64.deb

# Install GUI software
sudo dpkg -i code_1.78.2-1683731010_amd64.deb
sudo dpkg -i Feishu-linux_x64-6.1.11.deb
sudo dpkg -i wps-office_11.1.0.11698_amd64.deb
sudo dpkg -i SunloginClient_11.0.1.44968_amd64.deb 

# Install this version for free
sudo dpkg -i Typora_Linux_0.11.18_amd64.deb

```

Color terminal
```
# in ./bashrc add
PS1='${debian_chroot:+($debian_chroot)}\[\033[01;35;01m\]\u\[\033[00;31;01m\]@\[\033[01;36;01m\]\h\[\033[00;31;01m\]:\[\033[00;00;01m\]\w \[\033[01;32;01m\]\$ \[\033[01;33;01m\]'
```

Install 显卡驱动
```
显示器链接GPU显卡插口，若是黑屏则改链接CPU显卡插口

# install
软件和更新->附加驱动->选择无"server"的版本
重启

# check graphic card
设置->关于->显卡
```

Mount Disk
```
#https://blog.csdn.net/singledevil0/article/details/126254764

# Use gparted 
# delete and new create disk
# pick ntfs or ext4

sudo mount /dev/sda3 /diskb

# check UUID
sudo blkid /dev/sda1
# or
sudo fdisk -l
# get this UUID：17F81D67-ACC4-44C4-82D9-BAD0C97A7FB1

sudo gedit /etc/fstab
# add to end
# UUID=B2B6C7B9-4CAB-4677-8AB1-59641F46F576 /diskb ext4 defaults 0 2

# softlink
mkdir /diskb/lilf
ln -s /diskb/lilf /home/zksd/
```

安装docker - 阿里云镜像
```
#https://blog.csdn.net/u013546115/article/details/139722170

#Step1：更新系统软件包
sudo apt-get update

#Step2：安装依赖包【用于通过HTTPS来获取仓库】
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common

#Step3：添加Docker官方GPG密钥

#Step3.1：切换root用户（curl命令需要使用root角色，sudo授权不行）
sudo -i

#Step3.2：添加Docker官方GPG密钥
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/trusted.gpg.d/docker-ce.gpg

#Step3.3：注销root用户
exit

#Step4：验证0EBFCD88 是公钥的指纹。执行这个命令后，系统会显示与该指纹相关的公钥信息。
sudo apt-key fingerprint 0EBFCD88

#Step5：添加Docker阿里稳定版软件源
sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"

#Step6：再次更新软件包
sudo apt-get update

#Step7：安装默认最新版
sudo apt-get install docker-ce docker-ce-cli containerd.io

# check install  
sudo docker run hello-world
```

安装docker - 官方
```
# uninstall all dockers
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

# install latest
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# check install
sudo docker run hello-world
```

avoid enter sudo everytime
```
sudo groupadd docker
sudo usermod -aG docker zksd
cat /etc/group # check if zksd exist
sudo systemctl restart docker
sudo chmod a+rw /var/run/docker.sock
move docker root directory
```

move docker root directory
```
sudo -i

systemctl stop docker

rsync -aP /var/lib/docker/ /home/zksd/lilf/lean/docker
cp -R /var/lib/docker/* /home/zksd/lilf/lean/docker
mv /var/lib/docker /var/lib/docker.old

gedit /etc/docker/daemon.json
#add the following
#{
#"data-root": "/home/zksd/lilf/lean/docker"
#}

systemctl start docker

# check the new root dir
docker info  

rm -rf /var/lib/docker.old
```

Install nvidia-container-toolkit
could not select device driver "" with capabilities: [[gpu]].
```
systemctl stop docker

# finish print OK
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -

distribution=$(. /etc/os-release;echo $ID$VERSION_ID)

curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

# slow
sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit

systemctl restart docker
```

Pull docker env
```
docker pull nvcr.io/nvidia/tensorrt:22.07-py3

# close will remove container
docker run --gpus all --ipc=host --rm -it -v /home/zksd/lilf/project:/share nvcr.io/nvidia/tensorrt:22.07-py3

# Check Cuda Driver
nvcc --version

dpkg -l | grep cudnn
cat /usr/include/cudnn_version.h | grep CUDNN_MAJOR -A 2
dpkg -l | grep libnvinfer
dpkg -l | grep TensorRT
dpkg -l | grep opencv
cat /proc/version

# Check GPU Usage
watch -n -0.5 -d nvidia-smi
```



