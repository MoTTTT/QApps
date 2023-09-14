# Issues - Pre-cleanup

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
