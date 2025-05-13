# PLAYBOOK

* Plays are maps of host patterns to roles.

## become

* `become`: set to `true` to activate privilege escalation.
* `become_user`: set to user with desired privileges — the user you become, NOT the user you login as. 
  Does NOT imply `become: true`, to allow it to be set at the host level. The default value is `root`.

## blocks

* **create logical groups of tasks**
* offer ways to handle task errors, similar to exception handling in many programming languages
* All tasks in a block inherit directives applied at the block level
* `name` statement will not be printed

## description

```yaml
- name: |
    Show task name
    Descriptipon: Provides a description of the task
  debug:
    msg: "Hello World!"
  tags: multiline
```

## examples

### variables

```yaml
---
- name: Variables playbook
  hosts: all
  vars:
      state: latest
      user: bob
  tasks:
  - name: Add the user {{ user }}
    ansible.builtin.user:
      name: "{{ user }}"
  - name: Upgrade all apt packages
    apt:
      force_apt_get: yes
      upgrade: dist
  - name: Install the {{ state }} of package "nginx"
    apt:
      name: "nginx"
      state: "{{ state }}"
```

## handlers

* https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_handlers.html

Sometimes you want a task to run only when a change is made on a machine. For example, you may want to restart a service if a task  updates the configuration of that service, but not if the configuration is unchanged. Ansible uses handlers to address this use case. **Handlers are tasks that only run when notified**.

### in task file

```yml
---
- name: Verify apache installation
  hosts: webservers
  vars:
    http_port: 80
    max_clients: 200
  remote_user: root
  
  tasks:
    - name: Write the apache config file
      ansible.builtin.template:
        src: /srv/httpd.j2
        dest: /etc/httpd.conf
      notify:
        - Restart apache

  handlers:
    - name: Restart apache
      ansible.builtin.service:
        name: httpd
        state: restarted
```

### separate file

```yml
---
- name: Install latest version of Apache
  apt:
    name: apache2
    update_cache: yes
    state: present
  notify: start-service # this will call handler task of same name (once you install apache2, you need to start service.)
```

```yml
---
# handlers file for apache
- name: start-service # name must be similar to notify task
  service:
    name: apache2
    state: started
```

## keywords

* https://docs.ansible.com/ansible/latest/reference_appendices/playbooks_keywords.html
* vars: Dictionary/map of variables
* vars_files: List of files that contain vars to include in the play.

## loops

* must be aligned with the module

```yml
---
- name: Add several users
  loop:
    - testuser1
    - testuser2
  ansible.builtin.user:
    name: "{{ item }}"
    state: present
    groups: "wheel"
```

You can define the list in a variables file, or in the ‘vars’ section of your play, then refer to the name of the list in the task.

```yml
---
- name: tests
  hosts: 127.0.0.1
  connection: local
  vars_files:
    - /home/ursula/ansible/users.yml
  
  tasks:
    # print
    - name: Print out all user names
      loop: "{{ users }}"
      ansible.builtin.debug:
        msg: "{{ item }}"
```

```yml
---
# ~/ansible/users.yml
users:
  - frida
  - ursula
  - lem
  - karl
```

### loop over a dictionary

To loop over a dict, use the `dict2items`:

```yml
- name: Using dict2items
  ansible.builtin.debug:
    msg: "{{ item.key }} - {{ item.value }}"
  loop: "{{ tag_data | dict2items }}"
  vars:
    tag_data:
      Environment: dev
      Application: payment
```

### looper over a list of dictionaries

```yml
 # copy config files
    - name: copy config files
      tags:
        - copy
      vars:
        config_files:
          - src: "{{ path_files }}/etc_apache_sites-available_resources.unonweb.local.conf"
            dest: /etc/apache/sites-available/resources.unonweb.local.conf
          - src: "{{ path_files }}/home_user_.npmrc"
            dest: ~/.npmrc
          - src: "{{ path_files }}/etc_hosts"
            dest: /etc/hosts
      loop: "{{ config_files }}"
      ansible.builtin.debug:
        msg: "src: {{ item.src }} dest: {{ item.dest }}"
```

### loops & roles

* You cannot use loops on 'import_role' statements. You should use 'include_role' instead!

```yml
- name: myrole
  with_items:
    - "aone"
    - "atwo"
  include_role:
    name: myrole
  vars:
    thing: "{{ item }}"
```

## roles

* https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html
* group related tasks and associated variables, files, handlers, and other assets in a known file structure
* A role should be kept as simple as possible and concentrate on one thing.
* The separation what to be done from which hosts that applies to, enables the reuse of roles and inventory.

```sh
ansible-galaxy init <role_name> # generate a default role directory layout according to Ansible Galaxy’s standards
```

### roles vs plays

* Tasks can be organized into a play - a grouping of one or more tasks mapped to a specific host and executed in order. 
* A playbook can contain 1 or more plays, offering a flexible mechanism for executing Ansible automation in a single file.

### usecase

In instances where scope and variables are complex and reusability is helpful, creating most of your automation content in Ansible Roles and calling them within a playbook may be the more appropriate choice.

The following example illustrates the use of a role, `linux-systemr-roles.timesync`, within a playbook. In this instance, over 4 tasks would be required to achieve what the single role accomplishes. 

```yml
- name: Manage timesync with 3 servers
  hosts: targets
  vars:
     timesync_ntp_servers:
        - hostname: foo.example.com
          iburst: true
        - hostname: bar.example.com
          iburst: true
        - hostname: baz.example.com
          iburst: true
  roles:
     - linux-system-roles.timesync
```

### directory structure

Roles let you **automatically load related vars, files, tasks, handlers** based on a known file structure:

```yml
roles/
    common/               # this hierarchy represents a "role"
        tasks/            #
            main.yml      #  <-- tasks file can include smaller files if warranted
        handlers/         #
            main.yml      #  <-- handlers file
        templates/        #  <-- files for use with the template resource
            ntp.conf.j2   #  <------- templates end in .j2
        files/            #
            bar.txt       #  <-- files for use with the copy resource
            foo.sh        #  <-- script files for use with the script resource
        vars/             #
            main.yml      #  <-- variables associated with this role
        defaults/         #
            main.yml      #  <-- default lower priority variables for this role
        meta/             #
            main.yml      #  <-- role dependencies
        library/          # roles can also include custom modules
        module_utils/     # roles can also include custom module_utils
        lookup_plugins/   # or other types of plugins, like lookup in this case

    webtier/              # same kind of structure as "common" was above, done for the webtier role
    monitoring/           # ""
    fooapp/               # ""
```

By default, Ansible will look in most role directories for a `main.yml` file for relevant content.

#### defaults

* set default values for the variables used in the tasks. If there is no other definition for these variables, they will be picked up and used by the role, but usually, they are meant to be easily overwritten.

```yml
---
# roles/frontend/defaults/main.yml
# defaults file for frontend
  app:
    version: 1.5
```

```yml
# roles/frontend/tasks/main.yml
- name: Download and extract the release
  unarchive:
    src: https://github.com/devopsdemoapps/devops-demo-app/archive/{{ app.version }}.tar.gz
    dest: /opt/app/release
    owner: apache
    group: apache
    creates: /opt/app/release/devops-demo-app-{{ app.version }}
    remote_src: yes

- name: create a symlink
  file:
    src: /opt/app/release/devops-demo-app-{{ app.version }}
    dest: /var/www/html/app
    owner: apache
    group: apache
    state: link
```

### location

By default, Ansible looks for roles in the following locations:

- in collections, if you are using them
- in a directory called `roles/`, relative to the playbook file
- in the configured roles_path
- in the directory where the playbook file is located

### call

You can use roles in the following ways:

- at the play level with the `roles` option: This is the classic way of using roles in a play.
- at the tasks level with `include_role`: You can reuse roles dynamically anywhere in the `tasks` section of a play using `include_role`.
- at the tasks level with `import_role`: You can reuse roles statically anywhere in the `tasks` section of a play using `import_role`.
- as a dependency of another role (see the `dependencies` keyword in `meta/main.yml` in this same page)

#### roles keyword

```yml
---
- hosts: webservers
  roles:
    - common
    - webservers
```

You can also call a role with a fully qualified path:

```yml
---
- hosts: webservers
  roles:
    - role: '/path/to/my/roles/common'
```

#### import / include

When using roles from playbooks, use the `import_role` or `include_role` task rather than the older `roles` keyword. The latter will make it painful to handle the order of execution of roles with other tasks such as `set_fact` which you’ll likely have despite having already implemented everything  in roles. You’ll also want to keep roles loosely coupled and limit the  dependencies from one role to another. Playbooks can be used to pull in  many roles to accomplish a whole.

**Prefer import_role or include_role:** To better control the execution order of roles and tasks, prefer using `import_role` or `include_role` over the classic `roles` option.

```yml
# possible to use without name
- ansible.builtin.import_role: 
    name: test
```

### variables

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

```yml
---
- name: Start critical web servers 
  hosts: all

  roles:
    - role: setup
    - role: webserver
      vars:
        port: 8080
        config: "path/to/config1.cfg"
      tags: ServerA
    - role: webserver
      vars:
        port: 8081
        config: "path/to/config2.cfg"
      tags: ServerB
```

## strategy

By default, Ansible runs each task on all hosts affected by a play  before starting the next task on any host, using **5 forks**. If you want to change this default behavior, you can use a different strategy plugin,  change the number of forks, or apply one of several keywords like `serial`.

## tags

Ansible reserves several tag names for special behavior, two of which are `always` and `never`.

```yml
tags: [ "favorit-apps" ]
```

```yml
tasks:
- name: Install the servers
  ansible.builtin.yum:
    name:
    - httpd
    - memcached
    state: present
  tags:
  - packages
  - webservers
```

## tasks

### abort task on purpose

```yml
- ansible.builtin.fail:
      msg: Exit task
```

### import / include

```yml
tasks:
- import_tasks: common_tasks.yml
# or
- include_tasks: common_tasks.yml
```

**Pass variables** into imports and includes:

```yml
tasks:
- import_tasks: wordpress.yml
  vars:
    wp_user: timmy
- import_tasks: wordpress.yml
  vars:
    wp_user: alice
- import_tasks: wordpress.yml
  vars:
    wp_user: bob
```

### difference

* Imports are static, includes are dynamic.
* Imports happen at parsing time, includes at runtime 
  (processed as they encountered during the execution of the playbook)

**Limitations of imports:**

- can't be used with `with_*` or `loop` attributes
- can't import a file, which name depends on a variable

**Limitations of includes:**

- `--list-tags` doesn't show tags from included files
- `--list-tasks` doesn't show tasks from included files
- you cannot use `notify` to trigger a handler name which comes from inside a dynamic include
- you cannot use `--start-at-task` to begin execution at a task inside a dynamic include

## user

```yml
# change user for this play
- name: checkout repo
  git: repo=https://github.com/some/repo.git version=master dest={{ dst }}
  become: yes
  become_user: some_user
```

