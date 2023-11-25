# Setup and Configure K3 on Raspberry Pi 4/Ubuntu V22

## Hardware Used
1. 4 Raspberry Pi 4s (1 4GB & 3 2GB) 
1. 4 Raspberry Pi 4 Power Supply (USB-C) https://www.amazon.com/dp/B07TYQRXTK
1. 4 32 GB SD cards
1. GeeekPi Raspberry Pi Cluster Case https://www.amazon.com/gp/product/B083FDHPBH
1. 5-Port Gigabit Ethernet Unmanaged Switch https://www.amazon.com/gp/product/B07S98YLHM
1. Cat6 Ethernet Cables https://www.amazon.com/gp/product/B00NQA2H3O

## Software Used

### Laptop/Desktop
1. WIndows 11 
1. Raspberry Pi Imager https://www.raspberrypi.com/software/
1. win32 disk imager https://win32diskimager.org/
1. kubectl https://kubernetes.io/docs/tasks/tools/
1. Kubernetes Dashboard https://github.com/kubernetes/dashboard
   
### Raspberry Pis 
1. Ubuntu Server 22.04.3 LTS (64-bit) from Raspberry Pi Imager
1. K3 from https://k3s.io/ 

## Network Configuration
<img src='https://github.com/jetbotml/K3PiCluster/blob/main/NetworkConfig.png' width="60%" height="60%">

## Steps

### Create and Configure the controlnode from Laptop/Desktop
1. Flash OS using Raspberry Pi Imager - Ubuntu Server 22.04.3 LTS (64-bit)
1. Boot the Pi using the new card
1. Powershell window (xxx.xxx.xxx.xxx = the IP your pi)
   - **ssh ubuntu@xxx.xxx.xxx.xxx**   Note: default password is ubuntu and you will need to change it on first login.
   - **exit** the ssh session (type exit)
   - **ssh-keygen.exe** generate a key to use to connect to the pi. Accept the defaults, no password
   - **cat ~/.ssh/id_rsa.pub | ssh ubuntu@xxx.xxx.xxx.xxx "cat >>~/.ssh/authorized_keys"** This will load the new key to the pi authorized keys. 
   - **ssh ubuntu@xxx.xxx.xxx.xxx** should not need to login since it is useing the key now
   - **sudo apt-get update**
   - **sudo apt-get upgrade -y**
   - **sudo nano /boot/firmware/config.txt** add the following two lines under the [all] section
        - dtoverlay=disable-wifi
        - dtoverlay=disable-bt
   - **sudo nano /boot/firmware/cmdline.txt** append the following to the end of the first line
      - cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory swapaccount=1
   - **sudo hostnamectl set-hostname controlnode**
   - **sudo reboot now**
   - **ssh ubuntu@xxx.xxx.xxx.xxx**
   - **curl -sfL https://get.k3s.io | sh -**
   - run commands
      - **sudo kubectl cluster-info**
      - **sudo kubectl config view**
      - **sudo kubectl get pods -A**
      - **sudo kubectl get nodes**
      - If the above commands do not work run these, reboot and try again
        - **mkdir ~/.kube**
        - **sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config && sudo chown $USER ~/.kube/config**
        - **sudo chmod 600 ~/.kube/config && export KUBECONFIG=~/.kube/config**
   - **exit** the ssh session (type exit)
1. Download kubectl (https://kubernetes.io/docs/tasks/tools) download to your desktop's user folder
1. Powershell window - Setup Laptop/Desktop to connect and run kubectl
   - **mkdir ~/.kube**
   - **ssh ubuntu@xxx.xxx.xxx.xxx "sudo cat /etc/rancher/k3s/k3s.yaml" > .kube/config**
   - **notepad.exe .kube/config** Update the IP to the pi IP
   - run commands
      - **.\kubectl.exe get nodes**
      - **.\kubectl.exe get pods -A**
      - **.\kubectl.exe cluster-info**
      - **.\kubectl.exe config view**

