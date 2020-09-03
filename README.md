# Ansible Collection - jeichler.argocd_ocp

This collection will allow you to install and configure [ArgoCD](https://argoproj.github.io/argo-cd/) on your OpenShift 4 cluster.
It was tested with OCP 4.5. It is assumed that OpenShift Oauth is used for the login.

It is very early work and therefore has limited functionality, PR's and feature requests are welcome :)

An example is available under the l[playbooks directory](playbooks/).

## Required python modules

You require the python-openshift or python-k8 module.

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

```yaml
argocd_operator_channel: "alpha"
```

Channel for the ArgoCD operator.

```yaml
argocd_operator_approval_strategy: "Automatic"
```

Approval strategy for updates of the ArgoCD operator.

```yaml
argocd_operator_source: "community-operators"
```

Operator source for the ArgoCD operator.

```yaml
argocd_namespace: "argocd"
```

Namespace where the ArgoCD Operator will be installed and ArgoCD will be configured.

```yaml
argocd_groups:
  - name: argoadmins
    permission: admin
    users: []
  - name: argousers
    permission: readonly
    users: []
```

Used to configure RBAC. The groups are configured as policy in ArgoCD, but will also be created in Openshift if not present. The list of users will be added to the groups respectively in OCP.


```yaml
argocd_name: "argo"
```

Name of the CustomResource "ArgoCD" which will be created to spin up ArgoCD.


```yaml
argocd_dex_version: "v2.22.0-openshift"
```

Defines which image version to use for the dex server.