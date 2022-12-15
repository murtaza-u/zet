# Kube VIP

Kube VIP is software based load balancer for Kubernetes services and
control-plane (for building HA clusters). For a HA cluster, Kube VIP has
the capability to provide a high availability virtual IP for the
Kubernetes control-plane.

Kube VIP needs to be installed as a [static pod](/20221015114633) since
we need a virtual IP in order to bootstrap Kubernetes (the classical
chicken and egg problem).

The following must be ran on **one** of the control plane nodes:

## Set virtual IP

An **unused** IP address in your network must be reserved for Kube VIP.
In production environment you likely also want to configure a DNS `A
record` entry pointing to it.

```sh
# unused IP address or FQDN pointing to that IP address
export VIP=192.168.122.30
```

## Set interface

The name of the interface on the control plane node (`ip -c a`).

```sh
export INTERFACE=enp1s0
```

## Set Kube VIP version

```sh
# v0.5.7 at the time of writing
export KVVERSION=$(curl -sL https://api.github.com/repos/kube-vip/kube-vip/releases | jq -r ".[0].name")
```

## Pull down Kube VIP image

`ctr` is a client for interacting with `containerd` and is installed
along with it. If you happen to use another container runtime (example:
CRI-O) look for similar alternatives.

```sh
sudo ctr image pull ghcr.io/kube-vip/kube-vip:$KVVERSION
alias kube-vip="ctr run --rm --net-host ghcr.io/kube-vip/kube-vip:$KVVERSION vip /kube-vip"
```

## Create manifest

Manifests for static pods live under /etc/kubernetes/manifests/

```sh
sudo kube-vip manifest pod \
    --interface $INTERFACE \
    --address $VIP \
    --controlplane \
    --services \
    --arp \
    --leaderElection | sudo tee /etc/kubernetes/manifests/kube-vip.yaml
```

Omit `--services` if you do **not** want to use Kube VIP for Kubernetes
services. Read more about the flags
[here](https://kube-vip.io/docs/installation/flags/)

## Bootstrap Kubernetes cluster

With Kube VIP setup, the cluster can now be bootstrapped.

```sh
sudo kubeadm init \
  --control-plane-endpoint $VIP \
  [additional arguments ...]
```

## Related

* <https://kube-vip.io/>
* <https://github.com/kubernetes/kubeadm/blob/main/docs/ha-considerations.md>

  #k8s
