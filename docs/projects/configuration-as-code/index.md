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

## Requirements

- You need to be running Octopus Server **2020.6** or higher. 
- You need [Git](https://git-scm.com/docs/git) installed and running on the Octopus Server. By default, Octopus assumes `git` is available in the PATH environment variable.

## Known limitations


## How to use Configuration as Code
