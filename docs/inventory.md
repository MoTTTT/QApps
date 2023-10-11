# Inventory

## Bare metal

### k8s node: sigiriya

- Late 2014 Mac Mini
- 2.80GHz i5-4308U (2 core, 4 thread)
- 8GB RAM (soldered)
- Ubuntu Server 22.04 (upgrade from mcOS)
- 2TB SSD (upgrade from 500GB)
- eth0 IP: 192.168.0.6
- eth1 IP:
- dolmen key exchange: ssh colleymj@sigiriya

### k8s node: bukit

- Late 2014 Mac Mini
- 1.4 GHz Dual Core i5
- 4 GB RAM (soldered)
- Ubuntu 22.04
- 500GB SSD
- eth0 IP: 192.168.0.52
- eth1 IP:
- dolmen key exchange: ssh martin@bukit

### k8s node: james

- Motherboard: ASRock H61M-VS3
- 3 GHz Quad Core i5
- 16GB (upgrade from 8 GB) RAM
- Ubuntu 22.04
- 500 GB SSD
- eth0 IP: 192.168.0.27
- eth1 IP:
- dolmen key exchange: ssh colleymj@james

### k8s node: levant

- Raspberry Pi 4 B
- 1.8GHz Broadcom BCM2711, Quad Core Cortex-A72
- 4GB RAM
- Ubuntu Core 22
- IP: 192.168.0.28

## Other devices

### k8s lbr: oovo

- MetalLBR L2 (ARP) Load balancer
- IP: 192.168.0.131

### IOT device anasazi

- Raspberry Pi 3
- Quad Core 1.2GHz Broadcom BCM2837 64bit CPU
- 1GB RAM
- IP: 192.168.0.11
- MAC: B8-27-EB-BE-0D-EB

### Admin Client: dolmen

- MacBook Pro
- Apple M1
- 16GB RAM
- macOs Ventura 13.5.2

### Virtual server: ularu

- Virtualbox on bukit
- Ubuntu 16.04.7
- Zope Version 2.13.24 (python 2.7.11, linux2)
- QApps release 3.0
- 1GB RAM
- IP: 192.168.0.3
- MAC: 08-00-27-C3-E9-F6
- ssh colleymj@ularu -p 2222
- NOTE: To be turned down

## Host lists

### /etc/hosts

- Add `/etc/hosts` file entries on servers
- Add `/private/etc/hosts` for Mac clients

```text
192.168.0.6   sigiriya
192.168.0.11  anasazi
192.168.0.18  dolmen
192.168.0.27  james
192.168.0.28  levant
192.168.0.52  bukit
192.168.0.131 ovoo
192.168.0.132 inuksuk
192.168.0.3   ularu
```

### Ingress hosts

- Apache: `musings.thruhere.net`
- Tests: `qapps.does-it.net`
- Zope - QApps: `qsolutions.endoftheinternet.org`
- K8s API: `control.southern.podzone.net`
- Dashboard: `dashboard.southern.podzone.net`
- Opensearch GUI: `opensearch.southern.podzone.net`
- Fluentd Kibana GUI: `kibana.southern.podzone.net`

### wildcard hostnames on DynDns

- central.podzone.net
- colley.endoftheinternet.org
- control.podzone.net
- east.podzone.net
- eastern.podzone.net
- musings.thruhere.net
- north.podzone.net
- northern.podzone.net
- poc.endoftheinternet.org
- qapps.does-it.net
- qsolutions.endoftheinternet.org
- southern.podzone.net
- west.podzone.net
- western.podzone.net
