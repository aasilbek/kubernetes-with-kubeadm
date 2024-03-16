# Creating Highly Available Clusters with kubeadm

With stacked control plane nodes. This approach requires less infrastructure. The etcd members and control plane nodes are co-located.

## Node configuration:
 * Three or more machines that meet kubeadm's minimum requirements for the control-plane nodes. Having an odd number of control plane nodes can help with leader selection in the case of machine or zone failure.
 * Three or more machines that meet kubeadm's minimum requirements for the workers.
 * Full network connectivity between all machines in the cluster (public or private network)
 * Superuser privileges on all machines using sudo


## Content

* [HaProxy] (docs/haproxy.md)
* [Master Node](docs/02_master_node_configurations.md)</br>
* [Worker Node](docs/03_master_node_configurations.md)</br>

