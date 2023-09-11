# QSolutions Applications server

- Services include QApps Accounting, and line of business functions for property syndication, and property rental closed corporates.
- These are currently running in an Ubuntu VirtualBox client instance hosted on an Ubuntu host called "bukit".
- Also running in the VirtualBox client is a mail server, serving a static email archive.
- An Apache server is running at OS level on the Ubuntu host, serving static web pages.
- The Ubuntu host runs on-premise, connected to the internet via a fibre router.
- The fibre router provides bukit with a static IP and port forwarding of https traffic on the dynamic WAN address to port 443 for the Apache server, and port 8080 for the Zope listener.
- The static email archive is intentionally not accessable from the Internet.
- The hostname "qsolutions.endoftheinternet.org" is resolved to the WAN address using DynDns.
- Let's Encrypt is used as CA for the SSL certificate.

## Workload success criteria

- [ ] QApps application server: Generate and View report, Add transaction.
- [ ] Mail: Access Medico-Legal mail archive from 3rd party mac client
- [ ] Web: Access static pages, download ebook.

## Roadmap

Services to be containerised:

- Zope
- Postgres
- Mail
- Apache

Redundant architecture to be implemented:

- Implement a Kubernetes cluster
- Add second server (james)
- Add third server (TBD, not MVP)

Operationalisation

- Combine Zope and Apache traffic onto port 443
- Offload qsolutions.endoftheinternet SSL at Apache
- Reverse-proxy Zope
- Send DB backups to iCloud
- Remote admin: SSL secure and enable router external access
- Expose a ssh jump server to the internet

## Architecture

### bukit

- Late 2014 Mac Mini
- 1.4 GHz Dual Core i5
- 4 GB RAM
- Ubuntu 22.04
- 500GB SSD
- IP: 192.168.0.52

### james

- Motherboard: ASRock H61M-VS3
- 3 GHz Quad Core i5
- 8 GB RAM
- Ubuntu 22.04
- 500 GB SSD
- IP: 192.168.0.27

## Tasklist

- [ ] Create etcd cluster ??
- [ ] kubectl on bukit
- [ ] kubectl on james
- [ ] Install K8s on bukit
- [ ] Install K8s on james
- [ ] Build Kubernetes cluster on bukit and james
- [ ] Add third cluster node
- [ ] Extract Zope zexp files and check in
- [ ] Containerise Zope, including application code and config

### Issues

- [X] lates kubectl installed with apt-get on james, has different version to snap installation on bukit: Remove apt package and install from snap.
- [X] kubectl on james requires sudo, but not on bukit: Fixed with new session



### Notes

- kubeclt installed with snap
- K8s installation type: kubeadm
- containerd - cconfirm/configure foe systemd

### Command line snippets

- kubectl version --client

```text
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF
sudo modprobe overlay
sudo modprobe br_netfilter
``````

```text
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sudo sysctl --system
```


## References

- <https://kubernetes.io/docs/tasks/tools/>
- <https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/>






