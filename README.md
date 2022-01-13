# Home Lab Infrastructure Commands and Setup

## Setup

### OS
Each Node is installed with Ubuntu or Debian

### Add user
 
  Run commands:

  ```
  sudo adduser username
  ```

  ```
  sudo usermod -aG sudo username
  ```

### Setup a network

#### 99-disable-network-config.cfg
  ```
  sudo vim /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
  ```
  ```
  network: {config: disabled}
  ```
  
 #### 01-netcfg.yaml
  ```
  sudo vim /etc/netplan/01-netcfg.yaml
  ```
  ```
  network:
    version: 2
    renderer: networkd
    ethernets:
      eth0:
        dhcp4: no
        addresses: [10.0.0.20/24]
        gateway4: 10.0.0.1
        nameservers:
          addresses: [75.75.75.75]
  ```
  NOTE: Ip address may vary
  
  #### 50-cloud-init.yaml
  ```
  sudo rm /etc/netplan/50-cloud-init.yaml
  ```

  #### hostname
  NOTE: when setting up for K8's hostname must be lowercase
  ```
  sudo vim /etc/hostname
  ```
  ```
  hostname
  ```
  ```
  sudo reboot
  ```
  ### Update
  ```
  sudo apt-get update
  ```

  ```
  sudo apt-get upgrade
  ```
  ```
  sudo reboot
  ```
  
  ### Setup Golang ( For local dev )
  ```
  wget https://golang.org/dl/go1.16.3.linux-arm64.tar.gz
  ```
  NOTE: The version of go will change. Most up to date ARMv8 version is listed here: https://golang.org/dl/
  
  Kind: Archive. OS: Linux, Arch:	ARMv8
  
  ```
  sudo tar -C /usr/local -xzf go1.16.3.linux-arm64.tar.gz
  ```
  ```
  rm go1.16.3.linux-arm64.tar.gz
  ```
  ```
  vim $HOME/.profile
  ```
  Add the following:
  ```
  export PATH=$PATH:/usr/local/go/bin
  ```
  ```
  exit
  ```
  ```
  go version
  ```

### Setting Up Micro K8's (For Kubernetes)

https://microk8s.io/docs

https://microk8s.io/docs/clustering

NOTE the following must be added: ( For raspberry pi's only )

```
sudo vim /boot/firmware/cmdline.txt
```
at the end of the file add 
```
cgroup_enable=memory cgroup_memory=1
```
#### Clustering Host Changes
All nodes must have the local IP of all the other nodes in the cluster. Go to each node and edit the `/etc/hosts` file

Example:
```
sudo nano /etc/hosts

10.0.0.101 nodehostnameone
10.0.0.102 nodehostnametwo
10.0.0.103 nodehostnamethree
```

### Install Docker Or Containerd.io ( No longer needed on K8's Setup as its handled by MicroK8's ):

Docker:

References: https://wiki.learnlinux.tv/index.php/Setting_up_a_Raspberry_Pi_Kubernetes_Cluster_with_Ubuntu_20.04
```
curl -sSL get.docker.com | sh
```
Set Docker daemon options:
```
sudo vim /etc/docker/daemon.json
```
```
 {
   "exec-opts": ["native.cgroupdriver=systemd"],
   "log-driver": "json-file",
   "log-opts": {
     "max-size": "100m"
   },
   "storage-driver": "overlay2"
 }
 ```
 
 containerd:
 ```
 sudo apt install -y containerd.io
 ```
 
 NOTE: You may need to add the apt package reference to be able to install this package
 
 ### Enable routing. Uncomment net.ipv4.ip_forward=1 
 ```
 sudo vim /etc/sysctl.conf
 ```
 ```
 net.ipv4.ip_forward=1
 ```
 
 #### Snap update
 Setting up snap to refresh on friday afternoon, as 4 times a day may be too hard on a pi
 When having more than one node offset the update of each by 15 minutes
 ```
 sudo snap set system refresh.timer=fri,18:15
 ```
 
 #### Ingress
 Add an external ip to access the cluster outside the local network
 ```
 nano /var/snap/microk8s/current/certs/csr.conf.template
 ```
 Add the follow Ips
 ```
IP.1 = 127.0.0.1
#MOREIPS
IP.98 = x.x.x.x # Master Ip local address
IP.99 = x.x.x.x # Ip address of WAN
```
