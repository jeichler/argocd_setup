Role Name
=========

see <https://github.com/jeichler/ansible_argocd_ocp/blob/master/README.md>

Requirements
------------

see <https://github.com/jeichler/ansible_argocd_ocp/blob/master/README.md>

Role Variables
--------------

see <https://github.com/jeichler/ansible_argocd_ocp/blob/master/README.md>

Dependencies
------------

N/A

Example Playbook
----------------

```yaml

# Example usage:
# ansible-playbook -i yourplaybook.yaml -e k8s_kubeconfig=<pathtokubeconfig>
- name: install argocd
  hosts: localhost
  gather_facts: False

# ideally you define the vars in an inventory
  roles:
    - role: jeichler.argocd_ocp.argocd_setup
      vars:
        argocd_groups:
        - name: argoadmins
          permission: admin
          users:
            - jeichler
        - name: argousers
          permission: readonly
          users: []

```

License
-------

MIT
