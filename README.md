# Ansikube

This project is used to configure and manage Kubernetes clusters through Ansible, using simple YAML definitions and configurations.

The purpose of this project is to provide an operational Kubernetes cluster within minutes. This includes components to handle traffic, TLS certificates, datastores, monitoring and a few other features.

Please note that this is still a work in progress and thus the project could go through a major overhaul at some point.

## Requirements

### Ansible

First, install PIP and the main Ansible packages:
```sh
# Install pip3 if it is not installed already
[sudo] apt-get install python3-pip
# Upgrade it to latest version
pip3 install --upgrade pip
# Starting with version 2.10, Ansible has been split into
# two different packages
pip3 install ansible-base
pip3 install ansible
```

Then, install the [community.kubernetes](https://github.com/ansible-collections/community.kubernetes/) collection:
```sh
# The community.kubernetes Ansible collection requires the
# openshift python client to interact with Kubernetes APIs
pip3 install openshift
# Install the collection
ansible-galaxy collection install community.kubernetes
```

### k3d

```sh
# Exporting K3D_INSTALL_DIR is useful if you want to change
# the installation folder (default is /usr/local/bin)
export K3D_INSTALL_DIR=~/.local/bin
curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash
k3d cluster create local --k3s-server-arg '--no-deploy=traefik'
```
> **_NOTE:_**
> This also works with kind or minikube, but you need to change the ingress_service_type to `NodePort`.

## Usage

Create your configuration file and adapt it to your needs:
```sh
cp configurations/example.yaml configurations/local.context.yaml
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

### Want to start over ?

```sh
# This will delete releases, secrets and resources
ansible-playbook --diff playbooks/install.yaml --limit local --tags "uninstall" [--check]
# This will wipe roles, namespaces and configuration
ansible-playbook --diff playbooks/config.yaml --limit local --tags "purge" [--check]
```
