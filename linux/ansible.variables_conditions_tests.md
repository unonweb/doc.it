
# JINJA

## tests

- https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_tests.html
- always executed on control node

### length of list

```yml
- name: Install user flatpaks
  when: flatpaks_user | length > 0
  community.general.flatpak:
    method: user
    state: present
    name: "{{ flatpaks_user }}"
```

### is empty

```yml
tasks:

- fail: msg="The variable 'bar' is empty"
  when: bar | length == 0

- shell: echo "The variable 'foo' is not empty: '{{ foo }}'"
  when: foo | length > 0
```

### is defined and not empty

```yml
tasks:

- shell: echo "The variable 'foo' is defined and not empty"
  when: (foo is defined) and (foo|length > 0)

- fail: msg="The variable 'bar' is not defined or empty"
  when: (bar is not defined) or (bar|length == 0)
```

## filters

- https://jinja.palletsprojects.com/en/stable/templates/#list-of-builtin-filters

### list

- convert string to array

```yml
{{ ansible_hostname | list }}
```

### replace

```yml
{{ "Hello World" | replace("Hello", "Goodbye") }}
# -> Goodbye World
```

get path of all roles

```yml
# --> /media/common/code/ansible/frida/playbooks/roles/
vars:
    _roles_path: "{{ role_path | replace(role_name, '') }}"
```
# PYTHON

- https://jinja.palletsprojects.com/en/stable/templates/#python-methods

## string
### split

```yml
# /etc/systemd/system/media-personal.mount => media-personal.mount
- name: Disable unit
  become: true
  failed_when: false
  notify: Systemd daemon-reload
  loop: "{{ systemd_unit_paths }}"
  ansible.builtin.systemd_service:
    name: "{{ item.split('/')[-1] }}"
    state: stopped
    enabled: false
```

# CONDITIONS

* https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_conditionals.html
* https://jinja.palletsprojects.com/en/latest/templates/#comparisons
* you do not need to use `{{ }}` to use variables inside conditionals, as these are already implied.

When you have multiple conditions that all need to be true (that is, a logical and), you can specify them as a list:

```yml
tasks:
  - name: Shut down CentOS 6 systems
    ansible.builtin.command: /sbin/shutdown -t now
    when:
      - ansible_facts['distribution'] == "CentOS"
      - ansible_facts['distribution_major_version'] == "6"
```

## conditionally set variables

### with jinja conditions

```yml
- name: Conditional (true and false)
  set_fact: 
    my_boolean: "{{ 'true' if my_var == 'foo' else 'false' }}"  
```

### with when clause

```yml
- name: assign value to variable only if condition is met
  when: my_condition
  set_fact: 
    my_var: 'foo' 
```

```yml
- set_fact: snmp_group="wheel"
  when: ansible_os_family=='RedHat'

- set_fact: snmp_group="snmp"
  when: ansible_os_family=='Debian'
```
## operators

* https://jinja.palletsprojects.com/en/latest/templates/#logic
* Use `when: var` rather than `when: var == True` (or conversely `when: not var`)

### in

```yml
- name: Print debug message
    when: "user_name in ['Udo', 'Dirk', 'Bufdi']"
    ansible.builtin.debug:
      msg:
      - "user_name: {{ user_name }}"
      - "inventory_hostname: {{ inventory_hostname }}"
```

## conditions based on registered variables

```yaml
- hosts: servers
  tasks:
    - name: Ansible check file exists.
      stat:
        path: /etc/issue
      register: p
    - ansible.builtin.debug:
        msg: "File exists..."
      when: p.stat.exists
    - ansible.builtin.debug:
        msg: "File not found"
      when: p.stat.exists == False
```

## conditions based on variables

### bool

```yml
vars:
  epic: true
  monumental: "yes"
tasks:
- name: Run the command if "epic" or "monumental" is true
  ansible.builtin.shell: echo "This certainly is epic!"
  when: epic or monumental | bool

- name: Run the command if "epic" is false
  ansible.builtin.shell: echo "This certainly isn't epic!"
  when: not epic
```

### defined

```yml
# Jinja2’s defined test:
tasks:
- name: Run the command if "foo" is defined
  ansible.builtin.shell: echo "I've got '{{ foo }}' and am not afraid to use it!"
  when: foo is defined

- name: Fail if "bar" is undefined
  ansible.builtin.fail: msg="Bailing out. This play requires 'bar'"
  when: bar is undefined
```

# TYPES

## lists

```yaml
# The values bands and bands2 are equivalent:
vars:
    bands:
      - The Beatles
      - Led Zeppelin
      - The Police
      - Rush
    bands2: ['The Beatles', 'Led Zeppelin', 'The Police', 'Rush']
```

## list of dictionaries

Beide zuvor beschriebenen Variablen-Typen können miteinander  kombiniert werden. Folgendes Beispiel zeigt eine Liste von Dictionaries:

```yml
list_of_dicts:
  - first_name: Alice
    last_name: Cooper
    job: singer
  - first_name: Bob
    last_name: Marley
    job: singer
```

```yml
# loop over list of dicts
- name: Loop over some list_of_dicts
      loop: "{{ list_of_dicts }}"
      ansible.builtin.debug:
        msg: "Firstname: {{ item.first_name }} Lastname: {{ item.last_name }}"
      tags:
        - list_of_dicts
```

# VARIABLES

## special variables

* https://docs.ansible.com/ansible/latest/reference_appendices/special_variables.html

```yml
- name: Show all variables
  ansible.builtin.debug:
    msg: "{{ hostvars[inventory_hostname] }}"
```

### get path of all roles

```yml
# --> /media/common/code/ansible/frida/playbooks/roles/
vars:
    _roles_path: "{{ role_path | replace(role_name, '') }}"
```
## precedence

1. role defaults (defined in role/defaults/main.yml) 
2. inventory file or script group vars 
3. inventory group_vars/all 
4. playbook group_vars/all 
5. inventory group_vars/* 
6. playbook group_vars/* 
7. inventory file or script host vars 
8. inventory host_vars/* 
9. playbook host_vars/* 
10. host facts / cached set_facts 
11. play vars
12. play vars_prompt
13. play vars_files
14. role vars (defined in role/vars/main.yml)
15. block vars (only for tasks in block)
16. task vars (only for the task)
17. include_vars
18. set_facts / registered vars

## naming restricctions

**Variable names must contain only lowercase alphanumeric characters and the underscore  character**. Variable names must also start with either an alphabetic or underscore `_` character.
## use

### vars_files

```yml
---
# in the above example, this would be vars/external_vars.yml
somevar: somevalue
password: magic
```

```yml
---
- hosts: all
  remote_user: root
  vars:
    favcolor: blue
    version:
      - v1
      - v2
      - v3
  vars_files:
    - /vars/external_vars.yml
  
  tasks:
  - name: This is just a placeholder
    version: "{{ version[2] }}"
    ansible.builtin.command: /bin/echo foo
```

## access

```yml
smb:
  src: //192.168.10.100/nasenbaer
  user: "{{ host.nas_user }}"
  pass: "{{ host.nas_pass }}"
```
## register

You can create variables from the output of an Ansible task with the task keyword `register`. You can use registered variables in any later tasks in your play. For example:

```yml
- hosts: web_servers

  tasks:

     - name: Run a shell command and register its output as a variable
       ansible.builtin.shell: /usr/bin/foo
       register: foo_result
       ignore_errors: true

     - name: Run a shell command using output of the previous task
       ansible.builtin.shell: /usr/bin/bar
       when: foo_result.rc == 5
```

## inventory

### in the inventory file

* setting variables in the main inventory file is only a shorthand
* preferred: store variables in separate host and group variable files

#### host variables

You can easily assign a variable to a single host and then use it  later in playbooks. You can do this directly in your inventory file.

```ini
[atlanta]
host1 http_port=80 maxRequestsPerChild=808
host2 http_port=303 maxRequestsPerChild=909
```

```yml
atlanta:
  hosts:
    host1:
      http_port: 80
      maxRequestsPerChild: 808
    host2:
      http_port: 303
      maxRequestsPerChild: 909
```

Unique values like non-standard **SSH ports** work well as host variables. You can add them to your Ansible inventory by adding the port number after the hostname with a colon:

```ini
badwolf.example.com:5309
```

**Connection** variables also work well as host variables:

```ini
[targets]
localhost              ansible_connection=local
other1.example.com     ansible_connection=ssh        ansible_user=myuser
other2.example.com     ansible_connection=ssh        ansible_user=myotheruser
```

#### group variables

Group variables are a convenient way to apply variables to multiple hosts at once.

```yml
atlanta:
  hosts:
    host1:
    host2:
  vars:
    ntp_server: ntp.atlanta.example.com
    proxy: proxy.atlanta.example.com
```

### in dedicated files

* storing variables in separate host and group variable files is a more robust approach to describing your system policy
* You can also use lists and hash data in host and group variables files, which you cannot do in your main inventory file.
* Host and group variable files must use YAML syntax
* Ansible loads host and group variable files by searching **paths relative to the inventory file or the playbook file**

```yml
---
# /etc/ansible/group_vars/raleigh
# stores the variables for the `raleigh` group:
ntp_server: acme.example.org
database_server: storage.example.org
```

You can also create *directories* named after your groups or hosts. Ansible will read all the files in these directories in lexicographical order.

- `/etc/ansible/group_vars/raleigh/db_settings`
- `/etc/ansible/group_vars/raleigh/cluster_settings`

All hosts in the ‘raleigh’ group will have the variables defined in these files available to them. This can be very useful to keep your variables organized when a single file gets too big, or when you want to use Ansible Vault on some group variables.

## variables & vault

For general maintenance, it is often easier to use `grep`, or similar tools, to find variables in your Ansible setup. Since vaults obscure these variables, it is best to work with a layer of  indirection. When running a playbook, Ansible finds the variables in the unencrypted file and all sensitive variables come from the encrypted  file.

A best practice approach for this is to start with a `group_vars/` subdirectory named after the group. Inside of this subdirectory, create two files named `vars` and `vault`. Inside of the `vars` file, define all of the variables needed, including any sensitive ones. Next, copy all of the sensitive variables over to the `vault` file and prefix these variables with `vault_`. You should adjust the variables in the `vars` file to point to the matching `vault_` variables using jinja2 syntax, and ensure that the `vault` file is vault encrypted.

### layer of indirection

- `~/.ansible/inventory/group_vars/<goup>/vars.yml`

  ```sh
  ---
  # This is a cleartext file with Jinja2 templating using placeholders for `vault.yml`
  ansible_user: "{{ vault_ansible_user }}"
  ansible_password: "{{ vault_ansible_password }}"
  ansible_connection: "{{ vault_ansible_connection }}"
  ```

- `~/.ansible/inventory/group_vars/<goup>/vault.yml`

  ```sh
  # This file contains the secrets to be encrypted
  ansible_connection: winrm
  ansible_user: user
  ansible_password: strongpass
  ```

### input variables

if a role has an input variable called “`mysql_root_password”`, you’ll want to encrypt and store it as “`vault_mysql_root_password”`.

Then, pass that in among other related variables:

```yaml
- ansible.builtin.import_role: 
    name: test
  vars:
    mysql_package_version: "5.7.31-0ubuntu0.18.04.1"
    mysql_config_file: /etc/mysql/our-special-config.cnf
    mysql_root_password: "{{ vault_mysql_root_password }}"
```

## gather_facts

```sh
ansible test -m ansible.builtin.setup # gather facts via cli
```

```ini
# Default to no fact gathering because it's slow and "explicit is better than implicit". Depending how you use variables, you may rather explicitly define variables instead of relying on facts. You can enable this on a per-playbook basis with 
# gather_facts: true
gathering = explicit
```

You can reference the model of the first disk in the facts shown above in a template or playbook as:

```yml
{{ ansible_facts['devices']['xvda']['model'] }}
```
