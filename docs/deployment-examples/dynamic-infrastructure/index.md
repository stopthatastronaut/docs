---
title: Dynamic Infrastructure
description: Octopus Deploy can support a variety of scenarios for deploying applications to dynamically provisioned infrastructure.
position: 28
---

Octopus Deploy supports a variety of scenarios for deploying your applications. Sometimes, where you are deploying to is known ahead of time, however, with cloud hosting you can spin up the hosting infrastructure on-demand.


## Prerequisites

Before you can use the dynamic infrastructure features, you will need the following as a minimum:

- Environment to deploy to, with **Enable Dynamic Infrastructure** enabled
- Azure account
- Empty project
- Application package to deploy (this varies depending on the guide)
- Understanding of the hosting platform

## Guides

The following guides will help guide you through some common scenarios for creating the dynamic infrastructure and deploying the applications:

- [Basic example](/docs/deployment-examples/dynamic-infrastructure/simple-azure-web-app.md)
- [ARM template](/docs/deployment-examples/dynamic-infrastructure/azure-arm-template.md)
- [New Target Triggered](/docs/deployment-examples/dynamic-infrastructure/deployment-target-trigger.md)
- [Multi-Region Azure Web App](/docs/deployment-examples/dynamic-infrastructure/multi-region-azure-webapp.md)
- [Tenanted Applications](/docs/deployment-examples/dynamic-infrastructure/multi-tenant-scenario.md)