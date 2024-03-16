# Forwarding IPv4 and letting iptables see bridged traffic Execute the below mentioned instructions:
```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system
```

# Verify above command by running the following command:
```bash
sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward
```


# Installing Container Runtime on the Kubernetes Nodes

 CRI is a standard interface for the management of containers. Since v1.24 the use of dockershim has been fully deprecated and removed from the code base. [containerd replaces docker](https://kodekloud.com/blog/kubernetes-removed-docker-what-happens-now/) as the container runtime for Kubernetes, and it requires support from [CNI Plugins](https://github.com/containernetworking/plugins) to configure container networks, and [runc](https://github.com/opencontainers/runc) to actually do the job of running containers.

Reference: https://github.com/containerd/containerd/blob/main/docs/getting-started.md

### Download and Install Container Networking

The commands in this lab must be run on each instance. Login to each controller instance using SSH Terminal.

Here we will install the container runtime `containerd` from the Ubuntu distribution and  CNI tools from the Kubernetes distribution. 

Set up the Kubernetes `apt` repository

```bash
{
  KUBE_LATEST=$(curl -L -s https://dl.k8s.io/release/stable.txt | awk 'BEGIN { FS="." } { printf "%s.%s", $1, $2 }')
  sudo mkdir -p /etc/apt/keyrings
  curl -fsSL https://pkgs.k8s.io/core:/stable:/${KUBE_LATEST}/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

  echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/${KUBE_LATEST}/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
}
```

Install `containerd` and CNI tools, first refreshing `apt` repos to get up to date versions.

```bash
{
  sudo apt update
  sudo apt install -y containerd kubernetes-cni ipvsadm ipset dnsutils
}
```

Set up `containerd` configuration to enable systemd Cgroups

```bash
{
  sudo mkdir -p /etc/containerd
  containerd config default | sed 's/SystemdCgroup = false/SystemdCgroup = true/' | sudo tee /etc/containerd/config.toml
}
```

Now restart `containerd` to read the new configuration

```bash
sudo systemctl restart containerd
```


# Update the apt package index, install kubelet, kubeadm and kubectl, and pin their version:
```bash
{
  sudo apt-get install -y kubelet kubeadm kubectl
  sudo apt-mark hold kubelet kubeadm kubectl
  sudo swapoff -a
}
```
# Set up cluster
LoadBalancer Configuration : [HaProxy] (haproxy.md)
Master Node Configuration  : [Master Node](02_master_node_configurations.md)</br>
Worker Node Configuration  : [Worker Node](03_master_node_configurations.md)</br>