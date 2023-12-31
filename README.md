# QApps README.md

[![Hosted By: Cloudsmith](https://img.shields.io/badge/OSS%20hosting%20by-cloudsmith-blue?logo=cloudsmith&style=for-the-badge)](https://cloudsmith.com)

This repo contains working artefacts for the dev, test and build of a kubernetes cluster and its workload.

As the solution progressed, the QApps repo has been refactored into a number of other repos:

- Requirements, specification, design, and build notes have been moved to the podzone repo, and will be served as part of the deliverable on [PodZone](https://docs.podzone.net).
- The deployment artefacts are packaged as helm charts. Where these are polished enough, and there is some possibility the results may be useful to others, published on [Cloudsmith](https://cloudsmith.io/~q-solutions/repos/static-site/packages/)
- These charts are moved to their own repos, e.g. [static-site](https://github.com/MoTTTT/static-site)
- Site deployment values have been moved to local storage, for security reasons.

An attempt has been made to clean up leaving a reference of the first working iteration of the solution.

Package repository hosting is graciously provided by  [Cloudsmith](https://cloudsmith.com).

```text
Cloudsmith is the only fully hosted, cloud-native, universal package management solution, that
enables your organization to create, store and share packages in any format, to any place, with total
confidence.
```