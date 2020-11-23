# Ansikube

The purpose of this project is to provide an operational Kubernetes cluster within minutes. This includes components to handle traffic, TLS certificates, datastores, monitoring and a few other features. Everything is handled through Ansible playbooks and roles, using simple YAML definitions and configurations.

Please keep in mind that this is still a work in progress and thus the project could go through a major overhaul at some point.

## Requirements
### Ansible

Install PIP and the main Ansible packages:
```sh
[sudo] apt-get install python3-pip
pip3 install --upgrade pip
pip3 install ansible-base
pip3 install ansible
```

Then, install the [community.kubernetes](https://github.com/ansible-collections/community.kubernetes/) collection:
```sh
pip3 install openshift
ansible-galaxy collection install community.kubernetes
```

### k3d

```sh
# Exporting K3D_INSTALL_DIR is useful if you want to change the installation folder (default is /usr/local/bin)
export K3D_INSTALL_DIR=~/.local/bin
curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash
k3d cluster create local --k3s-server-arg '--no-deploy=traefik'
```
> **_NOTE:_**
> Kind and Minikube can be used as well, but you need to change the ingress_service_type to `NodePort`.

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
