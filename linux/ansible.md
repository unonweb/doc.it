

# LINKS

* https://www.digitalocean.com/community/tutorials/how-to-use-ansible-to-install-and-set-up-apache-on-ubuntu-18-04
* https://polarsquad.com/blog/ansible-best-practices-part-2
* https://andidog.de/blog/2017-04-24-ansible-best-practices

# INSTALL

```sh
# This also installs ansible-core if it is not already installed
pip3 install ansible-dev-tools
```

## pipx

```sh
sudo apt update
sudo apt remove ansible
sudo apt install python3 pipx
pipx ensurepath
pipx install --include-deps ansible
```

# BASICS

Ansible connects to managed nodes and pushes out small programs—called  Ansible modules—to them. These programs are written to be resource  models of the desired state of the system. Ansible then executes these  modules (over SSH by default), and removes them when finished. These  modules are designed to be [idempotent](https://docs.ansible.com/ansible/latest/reference_appendices/glossary.html#term-Idempotency) when possible, so that they only make changes to a system when necessary.

## bash script vs ansible playbook

BASH is imperative; Ansible is declarative.  

The way I often explain the difference is this:  

- Bash (or another scripting language) describes actions. Do this thing. Now do this other thing.  
- Ansible (and other configuration management systems) describe the  desired state. This file should exist here, owned by this user, with  these permissions. This package should be installed. This line in this  config file should appear like this.

By describing the desired state of things it lets you get away from  worrying about how to make that happen and focus on what needs to  happen. For example, if I need file A to be in location B, I can just  say "file A should be at B". I don't have to write the code to check if  the file already exists at B, and if not then copy from A to B, but if  it did exist to check if the file at B matches A and if not do the copy  anyway. I can just say "file A should be at B".  

Another benefit of Ansible is you get a listing of what is already  in the desired state and what changed. You can re-run it periodically to prevent configuration drift, and see what changed. Did some coworker  edit a config file to test something and forgot to change it back?  Configuration management would catch that and put it back the way it  should be. Did you re-run it and got 100% OKs and no changes? Enjoy that warm fuzzy because now you KNOW your environment is how you want it to  be.

## guidelines

### ansiblejunky.com

* [https://www.ansiblejunky.com](https://www.ansiblejunky.com/blog/ansible-101-standards/)

> Group hosts for easier inventory selection and less conditional tasks – the more groups the better.

* Groups are powerful in the Ansible inventory and a Playbook can  easily target a group of hosts using the group name which can be freely  defined

> Avoid defining host variables

* Always define variables for a group. There may be exceptions that  highlight the fact that the host is more of a unicorn host and not  aligned to any standard operating environment.

> **Plays should do nothing more than include a list of roles**

* Plays attempt to answer the `Where?` and the `What?`.  We answer the `Where?` question by defining the managed nodes that we are targeting using the `hosts:`. We answer the `What?` question by providing the workflow of roles which state what we are  trying to do. It should be simple and readable, so just a workflow of  Roles in a specific order.

> **Use `include_role` instead of the traditional `roles:` section**

* Using the `tasks:` section and defining `include_role` tasks allows for more control with the order, ability to add `when` clauses, etc.

> **Roles should answer the question `How?`**

- Recall that a Playbook answers the questions of `Where?` and `What?`, but it’s the Role that answers the question of `How?`

> **Keep roles self contained and focused**

- Think about the full life-cycle of a service, microservice or container — not a whole stack or environment
- Roles should be loosely-coupled — limit hard dependencies on other roles or external variables
- Roles should avoid including tasks from other roles when possible
- Roles should NOT perform `include_role` to include another external role
- Variables needed by the Role should all be defined within the Role (using `defaults/main.yml` or `vars/main.yml`)
- Pass variables to the role when calling `include_role:`

> **Roles should define public variables using `defaults/main.yml`**

- For those variables that can or should be overridden by the user of the Role, they should be defined with default values in the `defaults/main.yml` file. This is generally where new users should look in order to quickly understand what can be overrriden or customized with a Role. When  variables are defined in here, they have the lowest precedence and are  easily overridden by other variables defined elsewhere - that is the  intention as they are only defining the default value.

> **Roles do not always need the `tasks/main.yml` tasks file**

- A default Role file structure contains the `tasks/main.yml` as the common entry point for a Role. This is used when referencing a Role using `roles:` section in a Playbook or by simply including a Role using `include_role` or `import_role` tasks. However in some cases it might not make sense to have a  “default” behavior for a Role. For example, the Role might support  “install” and “configure”, in which case you can create the Role this  way:

```yml
defaults/
   main.yml
vars/
   main.yml
tasks/
   configure.yml
   install.yml
```

And then call the Role using a specific task:

```yml
include_role:
   name: apache
   tasks_from: configure.yml
```

This is perfectly acceptable and in fact it forces the user of the  Role to select the function (using the task file) instead of guessing or checking what behavior will happen by default. In many cases, I prefer  this style for Roles. It’s good to know you have the option to design it this way as well.

## yaml

- The `>` is a folding block operator.  That is, it joins multiple lines together by spaces. The following syntax:

  ```bash
  key: >
    This text
    has multiple
    lines
  ```

  Would assign the value `This text has multiple lines\n` to `key`.

- The `|` character is a literal block operator.  This is probably what you want for multi-line shell scripts. The following syntax:

  ```bash
  key: |
    This text
    has multiple
    lines
  ```

  Would assign the value `This text\nhas multiple\nlines\n` to `key`

# HOW TO

```yml
# better use tests as they're always executed on localhost!
- name: Check {{ filename }}
  delegate_to: localhost
  ansible.builtin.raw: "test -e {{ role_path }}/files/{{ filename }} && return 0 || return 1"
  ignore_errors: true
  register: _service
```

# STRUCTURE

## directory layout

```yml
production                # inventory file for production servers
staging                   # inventory file for staging environment

group_vars/
   group1.yml             # here we assign variables to particular groups
   group2.yml
host_vars/
   hostname1.yml          # here we assign variables to particular systems
   hostname2.yml
site.yml                  # master playbook
webservers.yml            # playbook for webserver tier
dbservers.yml             # playbook for dbserver tier
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

## playbooks

In `site.yml`, we import a playbook that defines our entire infrastructure.

```yml
---
# file: site.yml
- import_playbook: webservers.yml
- import_playbook: dbservers.yml
```

In a file like `webservers.yml` (also at the top level), we map the configuration of the webservers group to the roles performed by the webservers group:

```yml
---
# file: webservers.yml
- hosts: webservers
  roles:
    - common
    - webtier
```

The idea here is that we can choose to configure our whole  infrastructure by “running” site.yml or we could just choose to run a  subset by running webservers.yml.

## use

If I want to reconfigure my whole infrastructure, it’s just:

```sh
ansible-playbook -i production site.yml
```

To reconfigure NTP on everything:

```sh
ansible-playbook -i production site.yml --tags ntp
```

To reconfigure just my webservers:

```sh
ansible-playbook -i production webservers.yml
```

For just my webservers in Boston:

```sh
ansible-playbook -i production webservers.yml --limit boston
```

# LINT

* https://ansible.readthedocs.io/projects/lint/rules/
* lint **playbooks, roles and collections**


