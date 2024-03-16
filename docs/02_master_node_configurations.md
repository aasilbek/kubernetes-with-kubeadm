# Setup cluster
```bash
LOAD_BALANCER=
MASTER_1=
```
```bash
sudo kubeadm init --control-plane-endpoint $LOAD_BALANCER:6443 --upload-certs --apiserver-advertise-address=$MASTER_1 --pod-network-cidr=10.244.0.0/16
```
## This command gives commands to join master and worker  nodes . Copy them to file . They will be like 
```bash
kubeadm join 45.156.24.239:6443 --token vmt6kr.o9l0sofs6k4wd5g7 \
	--discovery-token-ca-cert-hash sha256:69af88483ad21c800b99dbb2b1c00092fe09d916d7ff7e70fabcc5e67c2bc1aa \
	--control-plane --certificate-key be05b216188319e9e71fbb932948709d0c655e8d3b47463ad35162e5be93f74e

kubeadm join 45.156.24.239:6443 --token vmt6kr.o9l0sofs6k4wd5g7 \
	--discovery-token-ca-cert-hash sha256:69af88483ad21c800b99dbb2b1c00092fe09d916d7ff7e70fabcc5e67c2bc1aa 
```

## Configure kubeconfig
```bash
{
    mkdir -p $HOME/.kube
    cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    chown $d(id -u):$(id -g) $HOME/.kube/config
}
```

# Addons. Install flannel
```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

# If you lost join command , get them with
```bash
kubeadm token create --print-join-command
```
