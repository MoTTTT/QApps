# Implementation Notes

## Decisions

- Microk8s Kubernetes distribution
- Build tools (kubectl, calicoctl, ansible etc) on dolmen workstation
- k8s IOT Edge on anasazi RPi
- Use nfs on sigiriya for persistent storage
- Secure ingress host based routing

## Network

- Fibre router: Static IPs for control plane and worker nodes
- Fibre router: (As-is) Dynamic DNS for ```qsolutions.endoftheinternet.org```
- Fibre router: Port forwarding: 443 to k8s L2 loadbalancer (As-is goes to dolmen)
- Fibre router: Restrict DHCP IP allocation range for clients to `192.168.0.2 - 192.168.0.120`
- MetalLB: IP address range: `192.168.0.131-192.168.0.140`
- DynDns: Add wildcard routes for all internet hosts in inventory, e.g. ```*.southern.podzone.net```
- DynDns: Update IP address using ddclient
- To dev, test and debug ingress, add: qapps.does-it.net

## Node installations

- For levant, to fix calico vxlan missing dependency: `sudo apt install linux-modules-extra-raspi`
- For RiPi: Add to /boot/firmware/cmdline.txt: `cgroup_enable=memory cgroup_memory=1 net.ifnames=1 `
- Ubuntu Server and Desktop: `sudo snap install microk8s --classic`
- Ubuntu Core: `sudo snap install microk8s --channel=latest/edge/strict`
- If required to prevent deployment to RPi arch (e.g. Opensearch): `kubectl taint nodes levant key1=value1:NoSchedule`

## Cluster configuration

Enable dashboard and rbac microk8s add-ons.

### Ingress configuration

MetalLB is used to implement an L2 load balancer. The metallb micrik8s add-on is required:

`sudo microk8s enable metallb ; Set 192.168.0.131-192.168.0.132`

An ingress controller is required. De-facto standard seems to be ingress-nginx.

NOTE: Enabling the micrik8s add-on failed to produce a working ingress for me, I believe because of namespace issues.

Load ingress-nginx using helm:
  
`helm upgrade --install ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --create-namespace`

Per-host ingress configuration is applied via the kubernetes API. Configuration is per internet host in the inventory. For implementation details see the following files in config/ directory:

- `podzone-control-ingress.yaml`
- `podzone-dashboard-ingress.yaml`
- `podzone-musings-ingress.yaml`
- `podzone-plone-ingress.yaml`

### Certificate configuration

LetsEncrypt https certificates are used for https ingress. The CertificateManager add on is required.

- `sudo microk8s enable cert-manager`

For implementation details see the following files in config/ directory:

- `podzone-certificateIssuer.yaml`
- `podzone-certs.yaml`

### Persistent data

A cluster-external NFS service is used to provide a Storage class for Persistent volume requirements, set up on sigiriya with access from `192.168.0.0/24`.

- `helm3 repo add csi-driver-nfs https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/charts
microk8s helm3 repo update`
- `helm3 install csi-driver-nfs csi-driver-nfs/csi-driver-nfs --namespace kube-system --set kubeletDir=/var/snap/microk8s/common/var/lib/kubelet`
- `chmod -R 777 /srv/nfs`
- `chown -R nobody:nogroup /srv/nfs`
- Add volumes into /etc/exports:

```text
/srv/nfs      192.168.0.0/24(rw,all_squash,sync,no_subtree_check)
/srv/nfs/k8s  192.168.0.0/24(rw,all_squash,sync,no_subtree_check)
/srv/nfs/mac  192.168.0.0/24(rw,all_squash,sync,no_subtree_check)
/srv/nfs/apachesites  192.168.0.0/24(rw,all_squash,sync,no_subtree_check)
```

The following file in the config/ directory creates a general purpose storage class `nfs-csi`, and one specifically for the web site `apache-storage-class`:

- `podzone-pc-nfs.yaml`

### Application installation (to be automated)

For implementation details see the following files in config/ directory:

- web server: `podzone-apache.yaml``
- Plone: `podzone-plone.yaml``
- Git Sync: podzone-git-sync.yaml

### Supporting Infrastructure

- `sudo snap install prometheus`: Available on localhost:9090

### Opensearch

- Edit `https://github.com/opensearch-project/helm-charts/blob/main/charts/opensearch/values.yaml` for opensearch-bukit.yaml, opensearch-james.yaml, opensearch-sigiriya.yaml
- `helm repo add opensearch https://opensearch-project.github.io/helm-charts/`
- `helm install opensearch-master opensearch/opensearch -f opensearch-bukit.yaml`
- `helm install opensearch-client opensearch/opensearch -f opensearch-james.yaml`
- `helm install opensearch-data opensearch/opensearch -f opensearch-sigiriya.yaml`
- `helm install dashboards opensearch/opensearch-dashboards`

### Zope

- Initial install of Plone, provides Zope 4, which is not backward compatable with the Zope 2 applications, so use a legacy zope image.
- Zope volume mounts: ./var/filestorage; ./var/blobstorage; ./products
