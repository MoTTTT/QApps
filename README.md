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

## Architecture

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

## Tasklist

- [X] Confirm docker on bukit and james: run httpd
- [X] Create external etcd cluster: Removed from MVP
- [ ] /etc/hosts file configs
- [ ] Fix containerd on james
- [X] kubectl on bukit
- [X] kubectl on james
- [ ] Install K8s on bukit
- [ ] Install K8s on james
- [ ] Build Kubernetes cluster on bukit and james
- [ ] Add third cluster node (virtual box on )
- [ ] Extract Zope zexp files and check in
- [ ] Containerise Zope, including application code and config

### Containerd configuration

- Enable cri plugin (remove from disabled plugins)
  
## Networking

- For MVP, set bukit up as control plane entry point, as well as ingress entry point.
- Add james entry for convenience
- Add `/etc/hosts` file entries on server
- Add `/private/etc/hosts` for clients

```text
192.168.0.52 bukit
192.168.0.27 james
```

- Set ```--control-plane-endpoint bukit``` on kubeadm init

### Issues

- [X] lates kubectl installed with apt-get on james, has different version to snap installation on bukit: Remove apt package and install from snap.
- [X] kubectl on james requires sudo, but not on bukit: Fixed with new session
- [ ] james: ```Unit containerd.service could not be found.```
- [ ] bukit: ```transport: Error while dialing: dial unix /var/run/containerd/containerd.sock: connect: no such file or directory```: Snap docker socket location (```systemctl status snap.docker.dockerd.service```): ```/run/snap.docker/containerd/containerd.sock```. Use kubeadm option ```--cri-socket``` to override default.
- [ ] kubeadm init on james: ```[WARNING Service-Kubelet]: kubelet service is not enabled, please run 'systemctl enable kubelet.service'```
- [ ] kubeadm init: Warning with swap being enabled. May need to ignore this on bukit with 4BG RAM. Monitor.
- [X] kubeadm init (bukit): Issues with ports in use: 2379, 2380: Stop, remove etcd
- [X] kubeadm init (bukit): /var/lib/etcd not empty: Clean up after removing etcd
- [X] kubeadm init (bukit): ```[ERROR CRI]: container runtime is not running: output: time="2023-09-14T11:34:07+02:00" level=fatal msg="validate service connection: validate CRI v1 runtime API for endpoint \"unix:/run/snap.docker/containerd/containerd.sock\": rpc error: code = Unimplemented desc = unknown service runtime.v1.RuntimeService"```: Exclude cri from disabled plugins in /etc/containerd/config.toml
- [ ] kubeadm init (bukit): ```W0914 13:40:56.356831   53246 checks.go:835] detected that the sandbox image "registry.k8s.io/pause:3.6" of the container runtime is inconsistent with that used by kubeadm. It is recommended that using "registry.k8s.io/pause:3.9" as the CRI sandbox image.```

### Notes

- kubeclt installed with snap
- K8s installation type: kubeadm
- containerd - confirm/configure for systemd: issues on james

## References

- <https://kubernetes.io/docs/tasks/tools/>
- <https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/>
- <https://kubernetes.io/docs/setup/best-practices/multiple-zones/>
- <https://kubernetes.io/docs/setup/best-practices/node-conformance/>
- <https://kubernetes.io/docs/setup/best-practices/>
- <https://kubernetes.io/docs/setup/best-practices/certificates/>
- <https://kubernetes.io/docs/concepts/overview/components/>
- <https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/>
- <https://kubernetes.io/docs/setup/best-practices/certificates/>
- <https://kubernetes.io/docs/setup/best-practices/node-conformance/>
- <https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/>
- <https://kubernetes.io/docs/concepts/cluster-administration/logging/>
- <https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/>
- <https://github.com/container-storage-interface/spec/blob/master/spec.md>
- <https://kubernetes.io/blog/2023/08/31/legacy-package-repository-deprecation/>
- <https://kubernetes.io/docs/concepts/services-networking/>
- <https://kubernetes.io/docs/reference/setup-tools/kubeadm/>
- <https://forum.snapcraft.io/t/data-locations/24905>
- <https://forum.snapcraft.io/t/quickstart-guide/3876>
- <https://forum.snapcraft.io/t/the-snap-format/698>
- <https://hackernoon.com/managing-ubuntu-snaps-the-stuff-no-one-tells-you-625dfbe4b26c>
- <https://katacontainers.io/>