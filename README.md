# Ansikube

[![License](https://img.shields.io/badge/License-GPLv3-informational.svg)](https://opensource.org/licenses/gpl-3.0)

The purpose of this project is to provide an operational Kubernetes cluster within minutes. This includes components to handle traffic, TLS certificates, datastores, monitoring and a few other features. Everything is handled through Ansible playbooks and roles, using simple YAML definitions and configurations.

Please keep in mind that this is still a work in progress and thus the project could go through a major overhaul at some point.

## Requirements

You will need the following:

* Ansible 2.9+
* The [community.kubernetes](https://github.com/ansible-collections/community.kubernetes/) ansible collection
* An available Kubernetes (1.16+) cluster

This project has been tested with the following kubernetes distributions and/or providers:
* k3d for local environments
* [GKE](https://cloud.google.com/kubernetes-engine) managed kubernetes service
* [OVH](https://www.ovhcloud.com/fr/public-cloud/kubernetes/) managed kubernetes service

And with the following OAuth providers:
* GitHub
* Google

There is no reason for this not to work with other providers as long as you have the appropriate context defined in your kube config file.

<details>
  <summary>Installing Ansible</summary>

```sh
pip3 install --upgrade pip
pip3 install openshift
pip3 install ansible-base
pip3 install ansible
ansible-galaxy collection install community.kubernetes
```
</details>

<details>
  <summary>Installing k3d</summary>

```sh
export K3D_INSTALL_DIR=~/.local/bin # optional
curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash

k3d cluster create local \
--agents 2 --no-lb \
--port 80:80@server[0] --port 443:443@server[0] \
--k3s-server-arg "--no-deploy=traefik"
```
</details>

## Usage

Create your configuration file and adapt it to your needs:
```sh
cp configurations/example.yaml configurations/context.local.yaml
```

Then, run the configuration playbook to create cluster resources (such as namespaces and service accounts):
```sh
ansible-playbook --diff playbooks/config.yaml --limit local [--check]
```

Finally, run the installation playbook to deploy secrets, releases and additional CRDs:
```sh
ansible-playbook --diff playbooks/install.yaml --limit local [--check]
```

That's it, your cluster is now ready.

### Want to go further ?

#### Replacing the default hosts

You can override the default inventory by creating a file in the inventories folder (using the following convention: `inventories/hosts.__SOMETHING__.yaml`). That file will then automatically be included and it will override any similar entry found in the default hosts file.

### Want to start over ?

Delete the releases and associated resources:
```sh
ansible-playbook --diff playbooks/install.yaml --limit local --tags "uninstall" [--check]
```

Remove the cluster configuration:
```sh
ansible-playbook --diff playbooks/config.yaml --limit local --tags "purge" [--check]
```
