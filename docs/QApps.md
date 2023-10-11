# QApps Business Brief

## Business Brief: As-is

- Services include QApps Accounting, and line of business functions for property syndication, and property rental closed corporates.
- These are currently running in an Ubuntu VirtualBox client instance hosted on an Ubuntu host (bukit).
- Also running in the VirtualBox client is a mail server, serving a static email archive.
- An Apache server is running at OS level on the Ubuntu host, serving static web pages.
- The Ubuntu host runs on-premise, connected to the internet via a fibre router.
- The fibre router provides bukit with a static IP and port forwarding of https traffic on the dynamic WAN address to port 443 for the Apache server, and port 8080 for the Zope listener.
- The static email archive is not accessable from the Internet.
- The hostname "qsolutions.endoftheinternet.org" is resolved to the WAN address using ```DynDns```.
- ```Let's Encrypt``` is used as CA for the SSL certificate.
- Backups are stored on premise, distributed across hosts, with ad-hoc manual copy onto off-premmise media
- Assets (various ownership and type, including images, videos, and documents) are stored on premise, distributed across hosts, with ad-hoc manual copy onto off-premmise media

## Business Brief: To-be

- Consolidation of web apps and content
- Zope and Postgresql forklift (only config)
- Consumer Cloud case study
- Migration of primary premmise (SA to UK)
- Multipremise (SA, UK, EU)
- Distributed Storage for assets
- Backup storage to iCloud
- GitHub for config, docs and code
- Backup GitHub to Storage
- IoT Edge presence on each premmise
- Factor out failed tl-wr845n access point
- Refactor wired network

### Consumer cloud

- No public cloud runtime (d1, d2) dependencies
- Consumer computing resources
- Consumer networking
- Consumer internet access
- Off grid viability for on prem services
- On-premmise services set {full storage, all services}
- Off-premmise (internet facing) services set {storage set, web}

## Workload success criteria

- [X] https access from on site browser
- [X] https access from off site browser
- [ ] QApps application server: Generate and View accounting report, Add and view transaction.
- [ ] Mail: Access Medico-Legal mail archive from 3rd party mac client
- [ ] Web: Browse static sites
- [ ] Web: Download ebooks 

## MVP deliverables

Containerised Services:

- Zope
- Postgres
- Mail
- Apache

Operationalisation:

- Combine Zope and Apache traffic onto one listener
- Offload qsolutions.endoftheinternet SSL at Apache
- Reverse-proxy Zope
- Send DB backups to iCloud
- Remote admin: Secure external access
- Access cluster from tools on laptop client from internet

