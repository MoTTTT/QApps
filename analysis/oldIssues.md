
## Multipass, on bukit

```text
martin@bukit:~$ sudo multipass list
[sudo] password for martin: 
list failed: The client is not authenticated with the Multipass service.
Please use 'multipass authenticate' before proceeding.
```

```text
authenticate failed: Passphrase is not set. Please `multipass set local.passphrase` with a trusted client.
```


### Node IP Address not populating in ExternalIP for NodePort

- `kubectl get service my-nginx -o wide`

```text
NAME       TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)                        AGE   SELECTOR
my-nginx   NodePort   10.152.183.20   <none>        8080:30587/TCP,443:30452/TCP   8h    run=my-nginx
```

- `kubectl get ingress --all-namespaces -o wide`

```text
NAMESPACE   NAME             CLASS   HOSTS    ADDRESS     PORTS   AGE
default     demo-localhost   nginx   levant   127.0.0.1   80      12h
```

- `calicoctl --allow-version-mismatch  get profile -o yaml | grep -C 10 projectcalico-default`


```yaml
apiVersion: projectcalico.org/v3
items:
- apiVersion: projectcalico.org/v3
  kind: Profile
  metadata:
    creationTimestamp: null
    name: projectcalico-default-allow
    resourceVersion: "0"
  spec:
    egress:
    - action: Allow
      destination: {}
      source: {}
    ingress:
    - action: Allow
      destination: {}
      source: {}
```


- `calicoctl --allow-version-mismatch  ipam check -o report.json`

- `grep -B 6 -A 10 IP_AUTODETECTION_METHOD /var/snap/microk8s/current/args/cni-network/cni.yaml`

```yaml
            # Cluster type to identify the deployment type
            - name: CLUSTER_TYPE
              value: "k8s,bgp"
            # Auto-detect the BGP IP address.
            - name: IP
              value: "autodetect"
            - name: IP_AUTODETECTION_METHOD
              value: "can-reach=192.168.0.52"
            # Enable IPIP
            # - name: CALICO_IPV4POOL_IPIP
            #   value: "Always"
            # Enable or Disable VXLAN on the default IP pool.
            - name: CALICO_IPV4POOL_VXLAN
              value: "Always"
            # Enable or Disable VXLAN on the default IPv6 IP pool.
            - name: CALICO_IPV6POOL_VXLAN
              value: "Never"
```

- `calicoctl --allow-version-mismatch  get node -o yaml`

```yaml
apiVersion: projectcalico.org/v3
items:
- apiVersion: projectcalico.org/v3
  kind: Node
  metadata:
    annotations:
      projectcalico.org/kube-labels: '{"beta.kubernetes.io/arch":"amd64","beta.kubernetes.io/os":"linux","kubernetes.io/arch":"amd64","kubernetes.io/hostname":"sigiriya","kubernetes.io/os":"linux","microk8s.io/cluster":"true","node.kubernetes.io/microk8s-controlplane":"microk8s-controlplane"}'
    creationTimestamp: "2023-09-25T21:53:09Z"
    labels:
      beta.kubernetes.io/arch: amd64
      beta.kubernetes.io/os: linux
      kubernetes.io/arch: amd64
      kubernetes.io/hostname: sigiriya
      kubernetes.io/os: linux
      microk8s.io/cluster: "true"
      node.kubernetes.io/microk8s-controlplane: microk8s-controlplane
    name: sigiriya
    resourceVersion: "178628"
    uid: f54bf63e-828b-48bd-8f6f-a9b2eae4b0b1
  spec:
    addresses:
    - address: 192.168.0.6/24
      type: CalicoNodeIP
    - address: 192.168.0.6
      type: InternalIP
    bgp:
      ipv4Address: 192.168.0.6/24
    ipv4VXLANTunnelAddr: 10.1.73.64
    orchRefs:
    - nodeName: sigiriya
      orchestrator: k8s
  status: {}
- apiVersion: projectcalico.org/v3
  kind: Node
  metadata:
    annotations:
      projectcalico.org/kube-labels: '{"beta.kubernetes.io/arch":"amd64","beta.kubernetes.io/os":"linux","kubernetes.io/arch":"amd64","kubernetes.io/hostname":"james","kubernetes.io/os":"linux","microk8s.io/cluster":"true","node.kubernetes.io/microk8s-controlplane":"microk8s-controlplane"}'
    creationTimestamp: "2023-09-25T21:55:12Z"
    labels:
      beta.kubernetes.io/arch: amd64
      beta.kubernetes.io/os: linux
      kubernetes.io/arch: amd64
      kubernetes.io/hostname: james
      kubernetes.io/os: linux
      microk8s.io/cluster: "true"
      node.kubernetes.io/microk8s-controlplane: microk8s-controlplane
    name: james
    resourceVersion: "178718"
    uid: 32263168-6297-4024-98a1-57bc2526e0b0
  spec:
    addresses:
    - address: 192.168.0.27/24
      type: CalicoNodeIP
    - address: 192.168.0.27
      type: InternalIP
    bgp:
      ipv4Address: 192.168.0.27/24
    ipv4VXLANTunnelAddr: 10.1.159.192
    orchRefs:
    - nodeName: james
      orchestrator: k8s
  status: {}
- apiVersion: projectcalico.org/v3
  kind: Node
  metadata:
    annotations:
      projectcalico.org/kube-labels: '{"beta.kubernetes.io/arch":"amd64","beta.kubernetes.io/os":"linux","kubernetes.io/arch":"amd64","kubernetes.io/hostname":"bukit","kubernetes.io/os":"linux","microk8s.io/cluster":"true","node.kubernetes.io/microk8s-controlplane":"microk8s-controlplane"}'
    creationTimestamp: "2023-09-25T21:54:06Z"
    labels:
      beta.kubernetes.io/arch: amd64
      beta.kubernetes.io/os: linux
      kubernetes.io/arch: amd64
      kubernetes.io/hostname: bukit
      kubernetes.io/os: linux
      microk8s.io/cluster: "true"
      node.kubernetes.io/microk8s-controlplane: microk8s-controlplane
    name: bukit
    resourceVersion: "178818"
    uid: 72b305bd-26cc-4c49-9146-8dda0036ded1
  spec:
    addresses:
    - address: 192.168.0.52/24
      type: CalicoNodeIP
    - address: 192.168.0.52
      type: InternalIP
    bgp:
      ipv4Address: 192.168.0.52/24
    ipv4VXLANTunnelAddr: 10.1.88.64
    orchRefs:
    - nodeName: bukit
      orchestrator: k8s
  status: {}
kind: NodeList
metadata:
  resourceVersion: "179089"
```



- bukit: Frequent *WARNING* level entries in log from kubectl cluster-info dump, starting at 2023-09-24 21:09:33.030, reporting about every 5 mins
- no *WARNING* level entries in sigiriya  
- james: `2023-09-24 21:07:07.680 [ERROR][65] felix/route_table.go 1040: Failed to get link attributes error=interface not present ifaceRegex="^vxlan.calico$" ipVersion=0x4 tableIndex=0`
- james: also has the `Failed to delete neighbor FDB entry ` *WARNING* log entries


```text
2023-09-24 21:07:58.101 [INFO][49] felix/health.go 306: Overall health status changed: live=true ready=true
+---------------------------+---------+----------------+-----------------+--------+
|         COMPONENT         | TIMEOUT |    LIVENESS    |    READINESS    | DETAIL |
+---------------------------+---------+----------------+-----------------+--------+
| CalculationGraph          | 30s     | reporting live | reporting ready |        |
| FelixStartup              | 0s      | reporting live | reporting ready |        |
| InternalDataplaneMainLoop | 1m30s   | reporting live | reporting ready |        |
+---------------------------+---------+----------------+-----------------+--------+
```

```text
W0924 21:07:57.530090      50 client_config.go:617] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
```

```text
2023-09-24 21:07:58.182 [INFO][49] felix/route_table.go 932: Syncing routes: found unexpected route; ignoring due to grace period. dest=10.1.88.65/32 ifaceName="cali050ccefdf88" ifaceRegex="^cali.*" ipVersion=0x4 tableIndex=0
2023-09-24 21:07:58.182 [INFO][49] felix/route_table.go 932: Syncing routes: found unexpected route; ignoring due to grace period. dest=10.1.88.66/32 ifaceName="cali642ae36495f" ifaceRegex="^cali.*" ipVersion=0x4 tableIndex=0
```

```text
2023-09-24 21:08:07.983 [INFO][49] felix/route_table.go 936: Remove old route dest=10.1.88.65/32 ifaceName="cali050ccefdf88" ifaceRegex="^cali.*" ipVersion=0x4 routeProblems=[]string{"unexpected route"} tableIndex=0
2023-09-24 21:08:07.984 [INFO][49] felix/route_table.go 936: Remove old route dest=10.1.88.66/32 ifaceName="cali642ae36495f" ifaceRegex="^cali.*" ipVersion=0x4 routeProblems=[]string{"unexpected route"} tableIndex=0
2023-09-24 21:08:07.984 [INFO][49] felix/conntrack.go 90: Removing conntrack flows ip=10.1.88.66
2023-09-24 21:08:07.986 [INFO][49] felix/conntrack.go 90: Removing conntrack flows ip=10.1.88.65
```


2023-09-24 21:09:33.030 [WARNING][49] felix/route_table.go 1077: Failed to delete neighbor FDB entry {LinkIndex:32 Family:7 State:128 Type:0 Flags:2 FlagsExt:0 IP:185.125.190.49 HardwareAddr: LLIPAddr:<nil> Vlan:0 VNI:0 MasterIndex:0} error=invalid argument ifaceName="vxlan.calico" ifaceRegex="^vxlan.calico$" ipVersion=0x4 tableIndex=0


```text
023-09-25 06:17:11.473 [WARNING][49] felix/route_table.go 1077: Failed to delete neighbor FDB entry {LinkIndex:32 Family:7 State:128 Type:0 Flags:2 FlagsExt:0 IP:35.232.111.17 HardwareAddr: LLIPAddr:<nil> Vlan:0 VNI:0 MasterIndex:0} error=invalid argument ifaceName="vxlan.calico" ifaceRegex="^vxlan.calico$" ipVersion=0x4 tableIndex=0
```

IPs:

- connectivity-check.ubuntu.com:

- 185.125.190.18 (is-content-cache-2.ps5.canonical.com)
- 185.125.190.17
- 185.125.190.48

- Google Cloud:

- 35.232.111.17
- 34.122.121.32
- 35.224.170.84


- Ubuntu

- 91.189.91.48


```text
2023-09-25 06:17:11.483 [WARNING][49] felix/route_table.go 1227: Failed to access interface but it appears to be up error=netlink update operation failed ifaceName="vxlan.calico" ifaceRegex="^vxlan.calico$" ipVersion=0x4 link=&netlink.Vxlan{LinkAttrs:netlink.LinkAttrs{Index:32, MTU:1450, TxQLen:0, Name:"vxlan.calico", HardwareAddr:net.HardwareAddr{0x66, 0x46, 0x49, 0xc5, 0xc9, 0xf}, Flags:0x13, RawFlags:0x11043, ParentIndex:0, MasterIndex:0, Namespace:interface {}(nil), Alias:"", Statistics:(*netlink.LinkStatistics)(0xc000964780), Promisc:0, Allmulti:0, Multi:1, Xdp:(*netlink.LinkXdp)(0xc0004112a8), EncapType:"ether", Protinfo:(*netlink.Protinfo)(nil), OperState:0x0, PhysSwitchID:0, NetNsID:-1, NumTxQueues:1, NumRxQueues:1, GSOMaxSize:0x10000, GSOMaxSegs:0xffff, GROMaxSize:0x10000, Vfs:[]netlink.VfInfo(nil), Group:0x0, Slave:netlink.LinkSlave(nil)}, VxlanId:4096, VtepDevIndex:2, SrcAddr:net.IP{0xc0, 0xa8, 0x0, 0x34}, Group:net.IP(nil), TTL:0, TOS:0, Learning:false, Proxy:false, RSC:false, L2miss:false, L3miss:false, UDPCSum:true, UDP6ZeroCSumTx:false, UDP6ZeroCSumRx:false, NoAge:false, GBP:false, FlowBased:false, Age:300, Limit:0, Port:4789, PortLow:0, PortHigh:0} tableIndex=0

```
