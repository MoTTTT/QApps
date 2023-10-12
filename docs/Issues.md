# Issues

## Zope

- Currently (and since before the 2016 install) zope comes bundled with plone
- Plone current installs Zope 4
- `Zope 4 is a successor to Zope 2.13, making many changes that are not backwards compatible with Zope 2.`
- Zope 2 still available, last updated 2010: <https://old.zope.dev/Products/Zope/folder_contents>
- Docker images for Zope 2 are available, e.g. <https://hub.docker.com/r/eeacms/zope-2-10-5>
- Check also <https://github.com/plone/cookiecutter-zope-instance>

## Opensearch

- Persistent data store issues: resolved by reproducing and fixing nfs issues externally
- `OpenSearch Dashboards server is not ready yet`
- `[opensearch-cluster-master-0] Not yet initialized (you may need to run securityadmin)`
- Cluster not starting up, all pods up
- High RAM utilisation, removing until workload is deployed.
- Tested with fluentd (mikrok8s enable community first)

### Levant architecture

```text
Fatal glibc error: This version of Amazon Linux requires a newer ARM64 processor compliant with at least ARM architecture 8.2-a with cryptographic extensions. 
On EC2 this is Graviton 2 or later.
```

- Prevent pod scheduling onto Levant `kubectl taint nodes levant key1=value1:NoSchedule`

### Persistent Storage permissions

```text
[2023-10-07T08:47:23,800][INFO ][o.o.s.OpenSearchSecurityPlugin] [opensearch-cluster-master-0] Disabled https compression by default to mitigate BREACH attacks. You can enable it by setting 'http.compression: true' in opensearch.yml
[2023-10-07T08:47:23,803][INFO ][o.o.e.ExtensionsManager  ] [opensearch-cluster-master-0] ExtensionsManager initialized 
[2023-10-07T08:47:23,825][ERROR][o.o.b.OpenSearchUncaughtExceptionHandler] [opensearch-cluster-master-0] uncaught exception in thread [main]
       org.opensearch.bootstrap.StartupException: OpenSearchException[failed to bind service]; nested: AccessDeniedException[/usr/share/opensearch/data/nodes];       
```

- PersistentVolumeCLaim error: `no persistent volumes available for this claim and no storage class is set`
- chown failure: `enableInitChown: false`

- Reproduce on direct nfs mount on domlen, fine tuned /etc/exports with `all_squash`

### vm.max_map_count

- `[1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]`

- Temporary: `sysctl -w vm.max_map_count=262144`
- Permanent: Add `vm.max_map_count = 262144` to /etc/sysctl.conf

## Networking

- [X] Letsencrypt acme-challenge failing `"msg"="Certificate must be re-issued" "key"="default/qsolution-cert" "message"="Issuing certificate as Secret does not exist"`: Use helm ingress controller installation, nit `microk8s enable ingress`
- [X] Node IP Address not populating in ExternalIP for NodePort: Need to use MetalLB
- [X] ingress-nginx-controller calls out to `google.com`, `fingerprints.bablosoft.com`, `ip.bablosoft.com` and `api.ipify.org`
- [X] ingress-controller giving 404 error - not routing requests to back-end
- [X] Ingress controllers:
- [X] Helm install: `helm upgrade --install ingress-nginx ingress-nginx   --repo https://kubernetes.github.io/ingress-nginx   --namespace ingress-nginx - [X] create-namespace`
- [X] microk8s enable ingress: does not provision an ingress controller
- [X] <https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.2/deploy/static/provider/aws/nlb-with-tls-termination/deploy.yaml>

## Raspberry Pi

- [X] Levant: microk8s on RPi (Ubuntu Server) not/intermittently supported:

```text
error: snap "microk8s" is not available on stable for
       this architecture (armhf) but exists on other
       architectures (amd64, arm64, ppc64el).
```

- [X] Levant: microk8s on RiPi (Ubuntu Core) intermittently supported:

```text
martinjcolley@levant:~$ snap install microk8s --channel=latest/edge/strict
error: snap "microk8s" requires classic confinement which is only available on classic systems
```

- [ ] Opensearch deploy fails to RPi arch: `kubectl taint nodes levant key1=value1:NoSchedule`

## Dolmen

- [ ] Networking issues, probably caused by Cisco Anyconnect, see refs
