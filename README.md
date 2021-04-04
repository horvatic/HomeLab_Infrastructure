# Base Ubuntu Pi 4 Setup

# Overview
A few basic commands for setting up a pi 4 with ubuntu 20.04

# Setup

## Add user
 
  Run commands:

  ```
  sudo adduser username
  ```

  ```
  sudo usermod -aG sudo username
  ```

## Setup a network

### 99-disable-network-config.cfg
  ```
  sudo vim /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
  ```
  ```
  network: {config: disabled}
  ```
  
 ### 01-netcfg.yaml
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
  
  ### 50-cloud-init.yaml
  ```
  sudo rm /etc/netplan/50-cloud-init.yaml
  ```
  ### Reboot
  ```
  sudo reboot
  ```
  ### hostname
  ```
  sudo vim /etc/hostname
  ```
  ```
  hostname
  ```
  ```
  sudo reboot
  ```
  ## Update
  ```
  sudo apt-get update
  ```
  ```
  sudo reboot
  ```
  ```
  sudo apt-get upgrade
  ```
  ```
  sudo reboot
  ```
  
  ## Setup Golang ( For local dev )
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

## Setting Up Micro K8's (For Kubernetes)
https://microk8s.io/docs
https://microk8s.io/docs/clustering

NOTE the following must be added:

```
sudo vim /boot/firmware/cmdline.txt
```
at the end of the file add
```
cgroup_enable=memory cgroup_memory=1
```
