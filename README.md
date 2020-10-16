# Ansible Collection - jeichler.argocd_ocp

This collection will allow you to install and configure [ArgoCD](https://argoproj.github.io/argo-cd/) on your OpenShift 4 cluster.
It was tested with OCP 4.5. It is assumed that OpenShift Oauth is used for the login to ArgoCD.

It is very early work and therefore has limited functionality, PR's and feature requests are welcome :)

## For the impatient ones

### Setup ArgoCD

Playbook:

```yaml
# ansible-playbook -i yourplaybook.yaml -e k8s_kubeconfig=<pathtokubeconfig>
- name: install argocd
  hosts: localhost
  gather_facts: False

  roles:
    - role: jeichler.argocd_ocp.argocd_setup
```

In the inventory:

```yaml
argocd_groups:
- name: argoadmins
  permission: admin
  users:
    - jeichler
- name: argousers
  permission: readonly
  users: []
```

Check the [defaults](https://github.com/jeichler/argocd_setup/blob/master/roles/argocd_setup/defaults/main.yaml) to see what can be configured.

### Setup Bitnami Sealed Secrets

```yaml
# Example usage:
# ansible-playbook -i yourplaybook.yaml -e k8s_kubeconfig=<pathtokubeconfig> --ask-become-pass

- hosts: localhost
  gather_facts: false
  roles:
  - role: jeichler.argocd_ocp.sealed_secrets_setup
    vars:
      sealed_secrets_private_key: "{{ lookup('file', '/tmp/certs/mytls.key') }}"
      sealed_secrets_public_key: "{{ lookup('file', '/tmp/certs/mytls.crt') }}"
```

Check the [defaults](https://github.com/jeichler/argocd_setup/blob/master/roles/sealed_secrets_setup/defaults/main.yaml) to see what can be configured.

## Required python modules

### argocd_setup

The requirements listed here: <https://docs.ansible.com/ansible/latest/modules/k8s_module.html>

### sealed_secrets_setup

* The requirements listed here: <https://docs.ansible.com/ansible/latest/modules/k8s_module.html>
* oc client

## K8s authentication

All options which are possible via the [k8s module](https://docs.ansible.com/ansible/latest/modules/k8s_module.html) and [k8s_info module](https://docs.ansible.com/ansible/latest/modules/k8s_info_module.html) can be used to authenticate using the below mentioned variables:

```yaml
api_key: "{{ k8s_api_key | default(omit) }}"
ca_cert: "{{ k8s_ca_cert | default(omit) }}"
client_cert: "{{ k8s_client_cert | default(omit) }}"
client_key: "{{ k8s_client_key | default(omit) }}"
context: "{{ k8s_context | default(omit) }}"
host: "{{ k8s_host | default(omit) }}"
kubeconfig: "{{ k8s_kubeconfig | default(omit) }}"
password: "{{ k8s_password | default(omit) }}"
proxy: "{{ k8s_proxy | default(omit) }}"
username: "{{ k8s_username | default(omit) }}"
validate_certs: "{{ k8s_validate_certs | default(omit) }}"
```

## Configuration Options

### argocd_setup

| Variable Name            | Default Value       | Description |
|:-------------------------|:-------------------:|:------------|
| argocd_operator_channel | alpha | subscription channel for ArgoCD operator |
| argocd_operator_approval_strategy | Automatic | manual or automatic operator update |
| argocd_operator_source | community-operators | catalog source of operator |
| argocd_namespace | argocd | Namespace where the ArgoCD Operator will be installed and ArgoCD will be configured. This namespace will be created if it does not exist. |
| argocd_groups | see [values in defaults](roles/argocd_setup/defaults/main.yaml) | by default the OpenShift groups argoadmins and argousers will be created. If you define this var, you may want to change the group names and specify concrete users for the groups.|
| argocd_name | argo | Name of the ArgoCD CR |
| argocd_insecure | False | value for `spec.server.insecure` in the ArgoCD CR. We set it to false by default to simply use OCP's router edge termination |
| argocd_route | `argo.<basedomain of default ingresscontroller>` | hostname of the route for ArgoCD |

Optionally you can configure intitial repos and repository credentials along the way.
Check the respectice [comments in defaults](roles/argocd_setup/defaults/main.yaml) to see how this can be configured.

### sealed_secrets_setup

Most of the work has been [provided already](https://github.com/rahmed-rh/oc4-learn/tree/master/secret-managment/sealed-secrets), I just put it into ansible.

WARNING: it is required to be logging in to the respective cluster before executing this role.

This role aims at setting up [SealedSecrets](https://github.com/bitnami-labs/sealed-secrets). The following configuration options exist:

```yaml
sealedsecrets_version: v0.12.5
```

```yaml
sealed_secrets_controller_downloadurl: "https://github.com/bitnami-labs/sealed-secrets/releases/download/{{ sealedsecrets_version }}/controller.yaml"
```

```yaml
sealed_secrets_binary_path: "/usr/local/bin/kubeseal"
```

```yaml
sealed_secrets_kubeseal_downloadurl: "https://github.com/bitnami-labs/sealed-secrets/releases/download/{{ sealedsecrets_version }}/kubeseal-linux-amd64"
```

```yaml
sealed_secrets_namespace: sealed-secrets
```

```yaml
# when both are specified, we run the BYO certificates scenario as described here:
# https://github.com/bitnami-labs/sealed-secrets/blob/v0.12.5/docs/bring-your-own-certificates.md
# sealed_secrets_private_key: "/path/to/privatekey"
# sealed_secrets_public_key: "/path/to/publickey"
sealed_secret_custom_cert_secretname: sealed-secret-custom-cert
```
