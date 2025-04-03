# Prerequisites

In this lab you will review the machine requirements necessary to follow this tutorial.

## Virtual or Physical Machines

This tutorial requires four (4) virtual or physical AMD64 machines running Ubuntu 22.04.5 LTS (Jammy Jellyfish). The following table list the four machines and thier CPU, memory, and storage requirements.

| Name    | Description            | CPU | RAM   | Storage |
|---------|------------------------|-----|-------|---------|
| student-node | Administration host    | 2   | 2GB | 10GB    |
| controlplane  | Kubernetes server      | 2   | 4GB   | 20GB    |
| node01  | Kubernetes worker node | 2   | 4GB   | 20GB    |
| node02  | Kubernetes worker node | 2   | 4GB   | 20GB    |

How you provision the machines is up to you, the only requirement is that each machine meet the above system requirements including the machine specs and OS version. Once you have all four machine provisioned, verify the system requirements by running the `uname` command on each machine:

```bash 
uname -mov
```

After running the `uname` command you should see the following output:

```text
#26~22.04.1-Ubuntu SMP Wed Feb 19 06:54:57 UTC 2025 x86_64 GNU/Linux
```

Next: [setting-up-the-jumpbox](02-jumpbox.md)
