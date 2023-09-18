# QSolutions Applications server

## Back story

- Services include QApps Accounting, and line of business functions for property syndication, and property rental closed corporates.
- These are currently running in an Ubuntu VirtualBox client instance hosted on an Ubuntu host called "bukit".
- Also running in the VirtualBox client is a mail server, serving a static email archive.
- An Apache server is running at OS level on the Ubuntu host, serving static web pages.
- The Ubuntu host runs on-premise, connected to the internet via a fibre router.
- The fibre router provides bukit with a static IP and port forwarding of https traffic on the dynamic WAN address to port 443 for the Apache server, and port 8080 for the Zope listener.
- The static email archive is intentionally not accessable from the Internet.
- The hostname "qsolutions.endoftheinternet.org" is resolved to the WAN address using ```DynDns```.
- ```Let's Encrypt``` is used as CA for the SSL certificate.
- Backups are stored on premise, distributed across hosts, with ad-hoc manual copy onto off-premmise media
- Assets (various ownership and type, including images, videos, and documents) are stored on premise, distributed across hosts, with ad-hoc manual copy onto off-premmise media

## Minimum Viable Product  (MVP) and End Game (EG) Roadmap

- MVP: Containerise all services (docker)
- MVP: Manage services using Kubernetes
- MVP: Cluster (2 node) on premise
- MVP: Meta-data configuration management (gitops)
- MVP: Automation
- MVP: Send DB backups to iCloud
- EG: Log aggregation
- EG: TLS
- EG: Multi-zone Cluster 3 premises, one node per premise
- EG: iCloud Drive backup
- EG: iCloud Drive sync
- EG: iCloud Drive asset upload
- EG: Asset cleanup

## Workload success criteria

- [ ] https access from on site browser
- [ ] https access from off site browser
- [ ] QApps application server: Generate and View accounting report, Add and view transaction.
- [ ] Mail: Access Medico-Legal mail archive from 3rd party mac client
- [ ] Web: Browse static pages, download ebook.

## MVP deliverables

Containerised Services:

- Zope
- Postgres
- Mail
- Apache
- jenkins

Operationalisation:

- Combine Zope and Apache traffic onto one listener
- Offload qsolutions.endoftheinternet SSL at Apache
- Reverse-proxy Zope
- Send DB backups to iCloud
- Remote admin: SSL secure and enable router external access
- Expose a ssh jump server to the internet
- Access cluster from kubectl on laptop client from internet

## Architecture

### Architecture decisions

- MVP: Use Ubuntu, not download.docker.com for containerd apt repo, no installation of build tools on servers (need a dev client with build tools)
- MVP: Single node stacked k8s control plane on bukit.
- MVP: Single worker node on james
- MVP: Kubectl on dolmen. Including from Internet.
- MVP: CNI Plugin: Calico (Network Policy support)
- EG: Jenkins container kubectl
- EG: High availability. (Note: Set ```--control-plane-endpoint bukit``` on kubeadm init unsuccessful with --config option)

### k8s node: levant

- Raspberry Pi 4 B
- 1.8GHz Broadcom BCM2711, Quad Core Cortex-A72
- 4GB RAM
- Ubuntu Server 22.04
- IP: 192.168.0.28
- MAC: E4-5F-01-82-43-90

### k8s node: bukit

- Late 2014 Mac Mini
- 1.4 GHz Dual Core i5
- 4 GB RAM
- Ubuntu 22.04
- 500GB SSD
- IP: 192.168.0.52

### k8s node: james

- Motherboard: ASRock H61M-VS3
- 3 GHz Quad Core i5
- 8 GB RAM
- Ubuntu 22.04
- 500 GB SSD
- IP: 192.168.0.27

### clients

- dolmen: MacBook Pro, macOS
- Martins-Mac: Mac Mini, macOS (EG -> Node3; Linux with MacOS in VirtualBox)

## Tasklists

### Tasks: Cleanup initial

- [X] james: snap cleanup
- [X] bukit: ```snap remove kube-apiserver kubectl```
- [X] james: Remove docker, kubectl, and k8s components
- [X] bukit: Remove docker, kubectl, and k8s components
- [X] bukit and james: Clean up k8s files (/var/lib/kubelet/; /etc/kubernetes/)
- [X] bukit and james: Clean up docker installation files ```rm -rf /var/lib/containerd``` and  ```rm -rf /var/lib/docker```
- [X] bukit and james: Clean up etcd files ```rm -rf /var/lib/etcd```

### Tasks: Cleanup to retry kubeadm init

### Tasks: Prep for k8s installation

- [X] bukit, dolmen and james: /etc/hosts file configs for james and bukit /private/etc/hosts for dolmen
- [X] james: Install containerd
- [X] bukit: Install containerd
- [X] james: Configure containerd (/etc/containerd/config.toml)
- [X] bukit: Configure containerd (/etc/containerd/config.toml)

### k8s installation

- [X] bukit and james: Install kubeadm and kubelet: ```sudo apt-get install -y kubelet kubeadm```
- [X] bukit: Swap settings for kubeadm (sudo swapoff -a; comment out /swapfile in /etc/fstab)
- [X] bukit: Configure kubeadm for containerd  (create kubeadm-config.yaml)
- [ ] bukit: kubeadm init  --config kubeadm-config.yaml
- [ ] dolmen: Install kubectl
- [ ] Add third cluster node (virtual box on )
- [ ] Extract Zope zexp files and check in
- [ ] Containerise Zope, including application code and config

### Deprecated tasks

- [X] kubectl on bukit
- [X] kubectl on james
- [X] Confirm docker on bukit and james: run httpd
- [X] Create external etcd cluster: Removed from MVP
  
## Networking

- For MVP, set bukit up as control plane entry point, as well as ingress entry point.
- Add james entry for convenience
- Add `/etc/hosts` file entries on servers
- Add `/private/etc/hosts` for Mac clients

```text
192.168.0.52 bukit
192.168.0.27 james
192.168.0.28 levant
192.168.0.18 dolmen
```

- CNI Pulgin: Calico, using 192.168.1.0/16: ```kubeadm init --pod-network-cidr=192.168.1.0/16```


## Containerd installation and configuration

Full docker stack, packaged by docker: ```sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin```

Just runc and containerd, packaged by ubuntu: ```apt-get -y install containerd```

Containerd configuration requirements:

- Enable cri plugin in /etc/containerd/config.toml (achieved by overwriting the default file config.toml)
- Set cgroup driver to Systemd
- Configure k8s sandbox image

The following achieves this:

```text
cat > /etc/containerd/config.toml <<EOF
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
    SystemdCgroup = true
[plugins."io.containerd.grpc.v1.cri"]
  sandbox_image = "registry.k8s.io/pause:3.9"
EOF
```

Restart containerd: ```systemctl restart containerd```

## kubeadm installation and configuration

- Set Pod subnet (--pod-network-cidr): 192.168.1.0/24
- Set Service subnet 192.168.0.0/24
- Set --control-plane-endpoint bukit
- Set systemd as the cgroup driver
- Since the config file option to init is required for cgroup driver, create a config file for all options:

```text
kind: ClusterConfiguration
apiVersion: kubeadm.k8s.io/v1beta3
kubernetesVersion: v1.28.2
networking:
  serviceSubnet: "192.168.0.0/24"
  podSubnet: "192.168.1.0/24"
  dnsDomain: "cluster.local"
controlPlaneEndpoint: "bukit"
clusterName: "qapps-cluster"
---
kind: KubeletConfiguration
apiVersion: kubelet.config.k8s.io/v1beta1
cgroupDriver: systemd
```

- Init command: kubeadm init --config kubeadm-config.yaml

### Tasks: Cleanup to retdo kubeadm init

- kubectl drain <node name> --delete-emptydir-data --force --ignore-daemonsets
- kubeadm reset 
- kubectl delete node <node name>

- clean /etc/cni/net.d

