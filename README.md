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

- MVP: Use Ubuntu, not download.docker.com for containerd apt repo, no installation of docker tools on servers (need a dev client with tools)
- MVP: Single node stacked k8s control plane on bukit. (Note: Set ```--control-plane-endpoint bukit``` on kubeadm init)
- MVP: Single worker node on james
- MVP: Kubectl on dolmen. Including from Internet.
- EG: Jenkins container kubectl

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

### Cleanup and prep

- [ ] james: snap cleanup
- [ ] bukit: ```snap remove kube-apiserver kubectl```


- [X] /etc/hosts file configs for bukit and james
- [X] Remove docker, kubectl, and k8s components from james
- [X] Remove docker, kubectl, and k8s components from james
- [X] Clean up k8s files (/var/lib/kubelet/; /etc/kubernetes/)

- [X] james: Install containerd
- [X] bukit: Install containerd
- [X] james: Configure containerd (/etc/containerd/config.toml)
- [X] bukit: Configure containerd (/etc/containerd/config.toml)

- [X] bukit: Install kubeadm and kubelet: ```sudo apt-get install -y kubelet kubeadm```
- [ ] dolmen: Install kubectl
- [ ] bukit: Swap settings for kubeadm (sudo swapoff -a; comment out /swapfile in /etc/fstab)
- [ ] bukit: Configure kubeadm for  
- [ ] Install kubeadm, and init on servers


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
- Add `/etc/hosts` file entries on server
- Add `/private/etc/hosts` for clients

```text
192.168.0.52 bukit
192.168.0.27 james
```

## Cleanup scripts

- Clean docker installation bukit: ```sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras```
- Clean docker installation bukit: ```rm -rf /var/lib/containerd``` and  ```rm -rf /var/lib/docker```
- Clean docker installation james: ```apt remove containerd```

## Containerd installation and configuration

Full docker stack, packaged by docker: ```sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin```

Just runc and containerd, packaged by ubuntu: ```apt-get -y install containerd```

- Enable cri plugin (remove from disabled plugins) in /etc/containerd/config.toml

- To set the correct cgroup driver, and disable default cri plugin disabling, overwrite default file:

```text
cat > /etc/containerd/config.toml <<EOF
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
    SystemdCgroup = true
EOF
```

Restart containerd: ```systemctl restart containerd```


## kubeadm installation and configuration

- To set systemd as the cgroup driver, edit the KubeletConfiguration option of cgroupDriver and set it to systemd. For example:


apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
...
cgroupDriver: systemd

