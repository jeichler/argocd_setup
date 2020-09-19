Role Name
=========

see <https://github.com/jeichler/ansible_argocd_setup/blob/master/README.md>

Requirements
------------

see <https://github.com/jeichler/ansible_argocd_setup/blob/master/README.md>

Role Variables
--------------

see <https://github.com/jeichler/ansible_argocd_setup/blob/master/README.md>

Dependencies
------------

N/A

Example Playbook
----------------

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

License
-------

MIT
