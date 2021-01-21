---
title: Logs
description: Octopus and Octopus Tentacle contains many logs.
hideInThisSection: false
position: 50
---

Your Octopus Server and deployment targets contain various log files. Additionally the various integrations also contain their own log files. 


## Various Logs used in Octopus 

- **Octopus Server Logs** or **Octopus Tentacle Logs:** A default installation installs log files in a subdirectory called `Logs` in Octopus Home. (`C:\Octopus\Logs`) This can be changed during installation or moved after installation. These logs will be named <b>OctopusServer<em>X</em>.txt</b> or <b>OctopusTentacle<em>X</em>.txt</b> respectivily where <em>X<em> denotes the age of the logfile, the higher the number, the older the logfile with the most recent log file being denoted by having no appended digit.

These logs contain details of the tasks carried out by the server, that are unrelated to project tasks.

The [Verbosity of these logs](/docs/logs/log-files/index.md) can be changed to provide more information for debugging. 

- **Audit Logs:** For team members to collaborate in the deployment of software, there needs to be trust and accountability. Octopus Deploy captures audit information whenever significant events happen in the system. The Audit logs are available from within the Octopus interface. [Learn More](docs/security/users-and-teams/auditing.md)

- **OctopusMigrator Logs:** If you use the [Octopus Migrator Command Line tool](docs/octopus-rest-api/octopus.migrator.exe-command-line/index.md) for exporting or importing, a set of logs, denoted by <b>OctopusMigrator<em>X<em>.txt</b> will be created.

## Common Tasks related to capturing Logs and Metrics

- [Where to find Server and Tentacle Log Files](/docs/logs/log-files.md)

- [Export Raw logs from a Project Deployment or Task](/docs/logs/get-the-raw-output-from-a-task.md)
- [Export Log files with variable logging](/docs/logs/variable-logging-and-task-log-export.md)
- [Export a Projects Deployment Process as a JSON file](/docs/logs/deployment-process-as-JSON.md)

- [Record a Memory Trace](/docs/logs/record-a-memory-trace.md)
- [Record a Performance Trace](/docs/logs/record-a-performance-trace.md)
- [Record DB Performance Metrics](/docs/logs/providing-database-performance-metrics.md)

- [Record a Process Dump](/docs/logs/process-dumps.md)
- [Record a Crash Dump](/docs/logs/capture-a-crash-dump.md)



  
