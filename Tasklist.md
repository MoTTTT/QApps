# Tasks for building Consumer Cloud Zone 1: southern.podzone.net

Each of these are further decomposed below:

- [X] Business brief
- [X] Consumer Cloud definition
- [X] Workload Success criteria
- [X] MVP deliverables
- [X] k8s cluster build
- [X] Networking build
- [X] Ingress Build
- [X] Persistant storage
- [ ] Security
- [ ] Expose k8s API
- [ ] Applications and Services

## Tasklist: k8s cluster build

- [X] james (upgrade RAM)
- [X] sigiriya (upgrade disk)
- [X] bukit
- [X] levant

## Tasklist: Networking build

- [X] Network design
- [X] Refactor wired network
- [X] Router config (port forward, dynamic dns)
- [X] https certificate for (qsolutions.endoftheinternet.org)
- [X] Load balancer (MetalLB)
- [X] DynDns WAN IP updates (ddclient)

## Tasklist: Ingress Build

- [X] Certificate manager
- [X] Certificates  for 4 X LetsEncrypt host certs
- [X] Route to Apache static sites
- [X] Route to k8s dashboard
- [X] Route to Search GUI : Cancelled
- [ ] Route to k8s API
- [X] Route to zope

## Security

- [X] https for all external access
- [ ] enable rbac
- [ ] configure dashboard access controls
- [ ] user management

## Tasklist: Storage Applications and Services

- [ ] Consolidate assets into iCloud
- [ ] Extract source into GitHub
- [ ] Extract Zope zexp files and check in
- [ ] Containerise Zope, including application code and config
- [X] Consolidate Web sites

### Breakdown: Containerise Zope, including application code and config

- [X] Plone Service and Deployment
- [X] Plone ingress for qsolutions.endoftheinternet.org
- [X] Ingress rewrite rules for Zope Virtual Host Monster
- [ ] Plone ZPsycopgDA installation (containerInit?)
- [ ] Postgress Service and Deployment
- [ ] Postgress ingress?

## Tasklist: Extract source into GitHub

- [X] Book: Telling; BA Colley
- [X] Book: Cannon Becket; AH Colley
- [X] Book: All the Saints; AH Colley
- [ ] Code: MicroNode assets
- [ ] Code: BHC assets
- [ ] Code: Archve/Backup search
