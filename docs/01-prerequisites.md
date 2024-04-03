# Prerequisites

## Prepare Opstella's User

## Prepare Non-Production's kube-config 

1. Navigate to "https://app-opstella.devops.ntplc.co.th/sso".
2. Log in using your Opstella credentials.
3. In the "Deploy" section, locate the "**nt-opstella-non-production**" card and click it to download kube-config file.

## How to fork workshop repository

1. Navigate to the workshop repository.
2. On the top right corner, click the "Fork" button.
3. In the project URL, locate the namespace (the part after the slash) and update it to your username.
4. Click the "Fork Project" button to create your fork.

 
## Prepare SSH key

* Put this command to generate your SSH key

```bash
ssh-keygen -N ""
```

* Login Docker to Harbor registry

```bash
docker login -u <username> https://registry.devops.ntplc.co.th
```

> You will have your SSH private and public key in `~/.ssh/` directory

## Install Docker Desktop
```bash
Download at "https://www.docker.com/products/docker-desktop/"
```

## Install kubectl
* [Install kubectl on Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)
* [Install kubectl on macOS](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/)
* [Install kubectl on Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/)


## Install Docker Compose

* [On Linux and macOS](https://docs.docker.com/compose/install/standalone/#on-linux)
* [On Windows OS](https://docs.docker.com/compose/install/standalone/#on-windows-server)

## Navigation

* [Home](../README.md)
* Next: [docker](03-docker.md)
