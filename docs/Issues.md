# Issues

## Networking

- [X] Node IP Address not populating in ExternalIP for NodePort: Need to use MetalLB
- [X] ingress-nginx-controller calls out to `google.com`, `fingerprints.bablosoft.com`, `ip.bablosoft.com` and `api.ipify.org`
- [X] ingress-controller giving 404 error - not routing requests to back-end
- [X] 
- [X] Ingress controllers:
- [X] Helm install: `helm upgrade --install ingress-nginx ingress-nginx   --repo https://kubernetes.github.io/ingress-nginx   --namespace ingress-nginx - [X] create-namespace`
- [X] microk8s enable ingress: does not provision an ingress controller
- [X] <https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.2/deploy/static/provider/aws/nlb-with-tls-termination/deploy.yaml>

## Raspberry Pi

- [ ] Levant: microk8s on RPi (Ubuntu Server) not/intermittently supported:

```text
error: snap "microk8s" is not available on stable for
       this architecture (armhf) but exists on other
       architectures (amd64, arm64, ppc64el).
```

- [ ] Levant: microk8s on RiPi (Ubuntu Core) intermittently supported:

```text
martinjcolley@levant:~$ snap install microk8s --channel=latest/edge/strict
error: snap "microk8s" requires classic confinement which is only available on classic systems
```

## Dolmen

- [ ] Networking issues, probably caused by Cisco Anyconnect, see refs
