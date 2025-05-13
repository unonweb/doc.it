# CONFIG

* https://docs.ansible.com/ansible/latest/reference_appendices/config.html#ansible-configuration-settings

```ini
[defaults]
# (boolean) By default, Ansible will issue a warning when received from a task action (module or action plugin).
# These warnings can be silenced by adjusting this setting to False.
;action_warnings=True
```

## order

- `ANSIBLE_CONFIG` (environment variable if set)
- `ansible.cfg` (in the current directory)
- `~/.ansible.cfg` (in the home directory)
- `/etc/ansible/ansible.cfg`

```sh
nano ~/.ansible.cfg
```

## init

```sh
ansible-config init --disabled > ansible.cfg
```

## security

avoide security risks with `ansible.cfg` in the current directory:

If Ansible were to load `ansible.cfg` from a world-writable current working directory, it would create a serious security risk. Another user could place their own config file there, designed to make Ansible run malicious code both locally and remotely, possibly with elevated privileges. For this reason, Ansible will not automatically load a config file from the current working directory if the directory is world-writable.
