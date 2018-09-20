---
title: Create Kubernetes Target Command
description: Cmdlet for creating a Kubernetes Cluster target
position: 50
---


## Kubernetes Cluster
Command: **_New-OctopusKubernetesTarget_**

| Parameter                 | Value                                         |
| ------------------------- | --------------------------------------------- |
| `-name`                   | name for the Octopus deployment target        |
| `-clusterUrl`             | The management endpoint for the cluster       |
| `-namespace`              | The namespace of the cluster                  |
| `-octopusAccountIdOrName` | Name or Id of the Account Resource in Octopus |
| `-octopusProxyIdOrName`   | Name or Id of the Proxy Resource in Octopus   |
| `-octopusRoles`           | Comma separated list of Roles to assign       |
| `-updateIfExisting`       | Will update an existing target with the same name, create if it doesn't exist |

Example:
```powershell
New-OctopusKubernetesTarget -name "My Awesome Kube" `
                            -clusterUrl "https://10.3.13.37" `
                            -namespace "default"  `
                            -octopusAccountIdOrName "Dev Kube Account" `
                            -octopusRoles "WebKubeCluster" `
                            -updateIfExisting
                            -updateIfExisting
```