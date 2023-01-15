---
layout: post
title:  "Kubeflow example"
date:   2022-05-31 02:28:15 +0700
categories: [MLOps, Kubeflow]
---

# Kubeflow example

## Introduction
This is a tutorial to demo an example how to use a Kubeflow on Kubernetes for object detection.

In this tutorial, we will go through how to build a complete pipeline from create a simple web demo to semi automate annotate data with label tools, use Kubeflow to tracking detection, deploy to Kubernetes to auto scale.

## Install
![Image on screen](./imgs/test.jpg "Text to show on mouseover")

### Config docker

After install nvidia-docker from [instruction_link](https://cnvrg.io/how-to-setup-docker-and-nvidia-docker-2-0-on-ubuntu-18-04/)

After install, if we run 
```
cat /etc/docker/daemon.json
```
content should be:

```
{
    "runtimes": {
        "nvidia": {
            "path": "nvidia-container-runtime",
            "runtimeArgs": []
        }
    }
}

```

Add following content to deamon.json
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

After that, content should be:
```
{
    "runtimes": {
        "nvidia": {
            "path": "/usr/bin/nvidia-container-runtime",
            "runtimeArgs": []
        }
    },
    "exec-opts": ["native.cgroupdriver=systemd"],
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "100m"
    },
    "default-runtime": "nvidia",
    "storage-driver": "overlay2"
}
```

### Install kubeadm, kubectl, kubelet

```
# install requirement packages
sudo apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common -y
 
# add repository
sudo apt-get update
sudo apt-get install -y ebtables ethtool apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update
 
# install kubeadm, kubectl, kubelet (Version recommended is 1.21.1, which is tested)
sudo apt-get install kubelet=1.21.1-00 kubeadm=1.21.1-00 kubectl=1.21.1-00
```

Turn off swap
```
# swap off
swapoff -a
# add to default config
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

Config iptables 
```
cat >>/etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```

Create cluster
```
sudo kubeadm init --upload-certs --apiserver-advertise-address=107.120.70.138 --pod-network-cidr=10.100.0.0/16
```

You need modify arguments to feed with your environment:

First apiserver-advertise-address is your node master IP address, you can check your IP with: ifconfig and replace this value
Second pod-network-cidr is IP range , which uses for setup IP address for pods and nodes in your cluster
Note: pod-network-cidr isn't duplicate with any IP range in your machine 
*Note: after create a cluster, you need provide permission to your user in order to access cluster

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Setup your node master and node worker

