---
title: Configuration as Code
description: Configuration as Code for projects is a human-readable version of the Octopus application deployment or runbook processes stored in git source control.
position: 10
---

:::warning
**Configuration as Code is part of our Early Access Program (EAP) and may contain bugs or be unstable.**
:::

Configuration as Code, or _Config-as-Code_ for short is the human-readable, text representation of an Octopus project stored in a popular versioned control system, [Git](https://git-scm.com/). This includes the [deployment process](/docs/deployment-process/index.md) and [runbooks](/docs/runbooks/index.md) too. 

Until now, when you configured a project in Octopus, the configuration would be stored as records in a relational database. With Config as Code, some of that configuration will be persisted as files in a git repository instead of the database.

### Requirements

In order to configure a project to use store 

- Install git 
- Empty, but initialised repo

### How to configure

### Create a release

### What to expect



Teams will be able to turn on Git integration per project, and Octopus will store the process to the Git repository configured for the project. Users will also be able to view and edit the deployment process with the process editor in the Octopus Web Portal, and when saved, Octopus will create a commit with the changes to the Git repository.