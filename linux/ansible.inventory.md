# INVENTORY

```sh
/etc/ansible/hosts # default location
```

```sh
ansible-inventory -i inventory --list # check inventory
```

## ini

```ini
mail.example.com

[webservers]
foo.example.com
bar.example.com

[dbservers]
one.example.com
two.example.com
three.example.com
```

## yaml

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/yaml_inventory.html
* YAML-based inventory, should start with the `all` group and contain `hosts`/`vars`/`children` entries.
* Host entries can have sub-entries defined, which will be treated as variables.
* Vars entries are normal group vars.
* Children are ‘child groups’, which can also have their own vars/hosts/children and so on.

### flat

```yaml
ungrouped:
  hosts:
    mail.example.com:
webservers:
  hosts:
    foo.example.com:
    bar.example.com:
dbservers:
  hosts:
    one.example.com:
    two.example.com:
    three.example.com:
```

### hierarchical

```yml
all: # keys must be unique, i.e. only one 'hosts' per group
    hosts:
        test1:
        test2:
            host_var: value
    vars:
        group_all_var: value
    children:   # key order does not matter, indentation does
        other_group:
            children:
                group_x:
                    hosts:
                        test5   # Note that one machine will work without a colon
                #group_x:
                #    hosts:
                #        test5  # But this won't
                #        test7  #
                group_y:
                    hosts:
                        test6:  # So always use a colon
            vars:
                g2_var2: value3
            hosts:
                test4:
                    ansible_host: 127.0.0.1
        last_group:
            hosts:
                test1 # same host as above, additional group membership
            vars:
                group_last_var: value
```

## behavioral inventory parameters

* https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html#connecting-to-hosts-behavioral-inventory-parameters

```yml
---
all:
  hosts:
    localhost:
      ansible_connection: local
      ansible_connection: ssh
```

### become connection variables

You can define different `become` options for each managed node or group. You can define these variables in inventory or use them as normal variables.

- `ansible_become`

  overrides the `become` directive and decides if privilege escalation is used or not.

- `ansible_become_method`

  which privilege escalation method should be used

- `ansible_become_user`

  set the user you become through privilege escalation; does not imply `ansible_become: true`

- `ansible_become_password`

  set the privilege escalation password. See [Using encrypted variables and files](https://docs.ansible.com/ansible/latest/vault_guide/vault_using_encrypted_content.html#playbooks-vault) for details on how to avoid having secrets in plain text

Run all tasks as `root` on a server named `webserver`, but you can only connect as the `manager` user, you could use an inventory entry like this:

```ini
webserver ansible_user=manager ansible_become=true
```

## groups

Ansible creates two default groups: `all` and `ungrouped`

It is suggested that you define groups based on 

* purpose of the host (roles)
* geography or datacenter location

### parent/child group relationships

- Any host that is a member of a child group is automatically a member of the parent group.
- Groups can have multiple parents and children, but not circular relationships.
- Hosts can also be in multiple groups, but there will only be **one** instance of a host at runtime. Ansible merges the data from multiple groups.

```yml
east:
  hosts:
    foo.example.com:
    one.example.com:
    two.example.com:
west:
  hosts:
    bar.example.com:
    three.example.com:
prod:
  children:
    east:
test:
  children:
    west:
```
