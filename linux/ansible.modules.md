# MODULES

## _common

### apt | apt_repository

#### third party repo

##### style 1

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_key_module.html#id7

```yml
# additional apt repo
    - name: add typora key & repo
      block:
        - name: get typora repo key
          ansible.builtin.get_url:
            url: https://typora.io/linux/public-key.asc
            dest: /etc/apt/keyrings/typora.asc

        - name: add typora apt repo
          ansible.builtin.apt_repository:
            repo: 'deb https://typora.io/linux ./'
            state: present
    - name: add typora key & repo
      block:
        - name: get typora repo key
          ansible.builtin.get_url:
            url: https://typora.io/linux/public-key.asc
            dest: /etc/apt/keyrings/typora.asc

        - name: add typora apt repo
          ansible.builtin.apt_repository:
            repo: 'deb https://typora.io/linux ./'
            state: present
```

##### style 2

* https://peterbabic.dev/blog/how-to-install-caddy-using-ansible/

```yml
---
- name: Install Caddy web server
  hosts: my_hosts
  become: true
  become_user: root

  tasks:
    - name: Install required packages
      apt:
        update_cache: yes
        name:
          - debian-keyring
          - debian-archive-keyring
          - apt-transport-https
        state: present

    - name: Add Cloudsmith repository
      apt_key:
        url: "https://dl.cloudsmith.io/public/caddy/stable/gpg.key"
        state: present

    - name: Add Caddy repository to sources list
      apt_repository:
        repo:
          "deb https://dl.cloudsmith.io/public/caddy/stable/deb/debian
          any-version main"
        state: present
        filename: caddy-stable

    - name: Install Caddy
      apt:
        update_cache: yes
        name: caddy
        state: present

    - name: Enable and start Caddy service
      service:
        name: caddy
        enabled: yes
        state: started
```

### command | shell

* https://ansible.readthedocs.io/projects/lint/rules/no-changed-when/

You should always use the `changed_when` clause on tasks that do not naturally detect if a change has occurred or not. Some of the most common examples are shell and command modules, which run arbitrary commands.

#### problematic

```
---
- name: Example playbook
  hosts: localhost
  tasks:
    - name: Does not handle any output or return codes
      ansible.builtin.command: cat {{ my_file | quote }} # <- Does not handle the command output.
```

#### correct

```
---
- name: Example playbook
  hosts: localhost
  tasks:
    - name: Handle shell output with return code
      ansible.builtin.command: cat {{ my_file | quote }}
      register: my_output # <- Registers the command output.
      changed_when: my_output.rc != 0 # <- Uses the return code to define when the task has changed.
```

## ansible.builtin.apt

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_module.html#parameters

```yml
ansible.builtin.apt
	autoremove: true # remove unused dependency packages for all module states except build-dep
	deb : <path> 
	# Path to a .deb package on the remote machine. 
	# If :// in the path, ansible will attempt to download deb before installing
	# Requires the xz-utils package to extract the control file of the deb package to install
	purge: true # Will force purging of configuration files if state=absent or autoremove=yes
	
	upgrade: yes #  aptitude safe-upgrade
	upgrade: safe # aptitude safe-upgrade
	upgrade: full # aptitude full-upgrade
	upgrade: dist # apt-get dist-upgrade
	# Note: This does not upgrade a specific package, use state=latest for that
```

## ansible.builtin.apt_repository

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_repository_module.html

```yaml
# Add specified repository into sources list using specified filename.
- apt_repository:
    repo: deb http://dl.google.com/linux/chrome/deb/ stable main
    state: present
    filename: google-chrome
```

## ansible.builtin.apt_key

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_key_module.html#requirements
* **2023 Update**: Ansible now has the `ansible.builtin.deb822_repository` module, which can add keys *and* repositories in one task.

```yaml
# How to import a GPG key from a URL
- name: import the elasticsearch apt key
  apt_key:
    url: https://artifacts.elastic.co/GPG-KEY-elasticsearch
    state: present
  become: true
```

```yaml
# How to import a GPG key from a keyserver
# You can import a GPG key directly from the keyserver (usually keyserver.ubuntu.com) by setting the id and keyserver parameters, if you have the ID of the GPG key

- name: import the elasticsearch apt key from the keyserver
  apt_key:
    id: D88E42B4
    keyserver: keyserver.ubuntu.com
    state: present
  become: true
```

```yaml
# How to import a GPG key from a file
# You can import a GPG key from a local file by passing the file path to the file parameter

- name: download the elasticsearch apt key
  get_url:
    url: https://artifacts.elastic.co/GPG-KEY-elasticsearch
    dest: /etc/elasticsearch.key
  become: true

- name: install elasticsearch apt key from a file
  apt_key:
    file: /etc/elasticsearch.key
    state: present
  become: true
```

```yaml
# additional apt repo
    - name: add typora key & repo
      block:
        - name: get typora repo key
          ansible.builtin.get_url:
            url: https://typora.io/linux/public-key.asc
            dest: /etc/apt/keyrings/typora.asc

        - name: add typora apt repo
          ansible.builtin.apt_repository:
            repo: 'deb https://typora.io/linux ./'
            state: present
    - name: add typora key & repo
      block:
        - name: get typora repo key
          ansible.builtin.get_url:
            url: https://typora.io/linux/public-key.asc
            dest: /etc/apt/keyrings/typora.asc

        - name: add typora apt repo
          ansible.builtin.apt_repository:
            repo: 'deb https://typora.io/linux ./'
            state: present
```

## ansible.builtin.blockinfile

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/blockinfile_module.html
* This module will **insert/update/remove a block of multi-line text surrounded by customizable marker lines**.

* `path`

  The file to modify.

* `block`

  The text to insert inside the marker lines.

* `marker`

  The marker line template.

  {mark} will be replaced with the values in marker_begin (default=”BEGIN”) and marker_end (default=”END”).

  Using a custom marker without the {mark} variable may result in the block being repeatedly inserted on subsequent playbook runs.

  Multi-line markers are not supported and will result in the block being repeatedly inserted on subsequent playbook runs.

  A newline is automatically appended by the module to marker_begin and marker_end.

  Default: `"# {mark} ANSIBLE MANAGED BLOCK"`

```yml
- name: Insert/Update "Match User" configuration block in /etc/ssh/sshd_config prepending and appending a new line
  ansible.builtin.blockinfile:
    path: /etc/ssh/sshd_config
    append_newline: true
    prepend_newline: true
    block: |
      Match User ansible-agent
      PasswordAuthentication no
```

## ansible.builtin.command

If you're using the "command" function of ansible, you're not getting any of those benefits. The command function is about as useful as shell scripts, in general. It's often useful in handlers, so I'm not  saying you should never use it, but the benefits of ansible come from the functions that describe state, and "command" doesn't. 

* The command(s) will not be processed through the shell, so variables like `$HOSTNAME` and BASH features (like shell expansion or pipes) will not work. 
  Use the `ansible.builtin.shell` module if you need these features.
* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/command_module.html

```yml
- name: Return motd to registered var
  ansible.builtin.command: cat /etc/motd
  register: mymotd
```

### cli

```sh
ansible all --list-hosts
```

```sh
ansible atlanta -a "/sbin/reboot" # reboot all the servers in the [atlanta] group

ansible atlanta -a "/sbin/reboot" -f 10 # By default, Ansible uses only five simultaneous processes. If you have more hosts than the value set for the fork count, it can increase the time it takes for Ansible to communicate with the hosts. To reboot the [atlanta] servers with 10 parallel forks


ansible atlanta -a "/sbin/reboot" -f 10 -u <username> # connect as a different user (default: ansible runs from your user account)

ansible atlanta -a "/sbin/reboot" -f 10 -u username --become [--ask-become-pass] # Rebooting probably requires privilege escalation. You can connect to the server as username and run the command as the root user by using the become keyword.
# If you add --ask-become-pass ansible prompts you for the password to use for privilege escalation
```

## ansible.builtin.copy

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/copy_module.html#parameters

* `mode` 
  * may be specified as a symbolic mode:`u+rwx` or `u=rw,g=r,o=r`
  * `mode: u=rw,go=` # does not work
  * `mode: u=rw,g=,o=` # works
  * `preserve` means that the file will be given the same permissions as the source file

- `dest`: Remote absolute path where the file should be copied to.
  - If src is a directory, this must be a directory too.
  - If dest is a non-existent path and if either dest ends with “/” or src is a directory, dest is created.
  - If dest is a relative path, the starting directory is determined by the remote host.
  - If src and dest are files, the parent directory of dest is not created and the task fails if it does not already exist.

```yaml
# copy multiple files to the same destination
- name: Copy CA files
  copy:
    src: '{{item}}'
    dest: '/etc/pki/ca-trust/source/anchors'
    owner: root
    group: root
    mode: 0644
  loop:
    - symantec-private.crt
    - verisignclass3g2.crt
```

```yml
# copy multiple files to multiple directories
- name: Copy multiple files to multiple directories
  copy: src={{ item.0 }} dest={{ item.1 }}
  with_together:
    - [ 'file1', 'file2', 'file3' ]
    - [ '/dir1/', '/dir2/', '/dir3/' ]
```

### create a file with content

`content`
* When used instead of src, sets the contents of a file directly to the specified value.
* Works only when dest is a file.
* Creates the file if it does not exist.
* For advanced formatting or if content contains a variable, use the `ansible.builtin.template` module.

## ansible.builtin.cron

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/cron_module.html



## ansible.builtin.deb822_repository

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/deb822_repository_module.html#id3

* You will need to use an additional module only if you want to place the keyring file elsewhere than `/etc/apt/keyrings`. In that case you can download the keyring file using a different module, and then point the `signed_by` field (of the `debdeb822_repository` module) to the absolute path of the keyring file.

* `signed_by`: 

  * URL to a GPG key

  * absolute path to a keyring file

  * one or more fingerprints of keys either in the trusted.gpg keyring or in the keyrings in the trusted.gpg.d/ directory

  * an ASCII armored GPG public key block

* `uris`: The URIs must specify the base of the Debian distribution archive, from which APT finds the information it needs.

* The old source format looks like this:

  ```
  deb [signed-by=/usr/share/keyrings/example.gpg] https://example.com/dev foo bar
  ```

  * `https://example.com/dev` should go into `uris`
  * `foo` should go into `suites`
  * `bar`  should go into `components`
  * The path for `signed-by` should be ignored as the deb822_repository module figures out the path based on where it downloads the key to.

```yml
block:
- name: Add VSCode APT repository
  ansible.builtin.deb822_repository:
    name: vscode
    types: [deb]
    uris: "https://packages.microsoft.com/repos/code"
    signed_by: "https://packages.microsoft.com/keys/microsoft.asc"
    suites: [stable]
    components: [main]
    state: present
    enabled: yes
```

```yml
- name: Add Jenkins repo using key from URL.
  ansible.builtin.deb822_repository:
    name: jenkins
    types: [deb]
    uris: "https://pkg.jenkins.io/debian-stable"
    components: [binary]
    signed_by: https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
    state: present
    enabled: true
```

```yml
- name: Add debian repo
  deb822_repository:
    name: debian
    types: deb
    uris: http://deb.debian.org/debian
    suites: stretch
    components:
      - main
      - contrib
      - non-free

- name: Add debian repo with key
  deb822_repository:
    name: debian
    types: deb
    uris: https://deb.debian.org
    suites: stable
    components:
      - main
      - contrib
      - non-free
    signed_by: |-
      -----BEGIN PGP PUBLIC KEY BLOCK-----

      mDMEYCQjIxYJKwYBBAHaRw8BAQdAD/P5Nvvnvk66SxBBHDbhRml9ORg1WV5CvzKY
      CuMfoIS0BmFiY2RlZoiQBBMWCgA4FiEErCIG1VhKWMWo2yfAREZd5NfO31cFAmAk
      IyMCGyMFCwkIBwMFFQoJCAsFFgIDAQACHgECF4AACgkQREZd5NfO31fbOwD6ArzS
      dM0Dkd5h2Ujy1b6KcAaVW9FOa5UNfJ9FFBtjLQEBAJ7UyWD3dZzhvlaAwunsk7DG
      3bHcln8DMpIJVXht78sL
      =IE0r
      -----END PGP PUBLIC KEY BLOCK-----

- name: Add repo using key from URL
  deb822_repository:
    name: example
    types: deb
    uris: https://download.example.com/linux/ubuntu
    suites: '{{ ansible_distribution_release }}'
    components: stable
    architectures: amd64
    signed_by: https://download.example.com/linux/ubuntu/gpg
```

### add repo key

So traditionally, I would use a task like the following in my Ansible roles and playbooks:

```yml
- name: Add Jenkins apt repository key.
  ansible.builtin.apt_key:
    url: https://pkg.jenkins.io/debian-stable/jenkins.io.key
    state: present

- name: Add Jenkins apt repository.
  ansible.builtin.apt_repository:
    repo: "deb https://pkg.jenkins.io/debian-stable binary/"
    state: present
```

The new way to do this without adding an extra `gpg --dearmor` task is to use `get_url` to download the file into the `trusted.gpg.d` folder with the `.asc` filename. Therefore the first task above can be replaced with:

```yml
- name: Add Jenkins apt repository key.
  ansible.builtin.get_url:
    url: "{{ jenkins_repo_key_url }}"
    dest: /etc/apt/trusted.gpg.d/jenkins.asc
    mode: '0644'
    force: true
```

## ansible.builtin.debug

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/debug_module.html

```yml
---
- hosts: test
  tasks:
  - name: Test task
    when: false
    become: true
    ansible.builtin.import_tasks: ./tasks/apt.yml
  - name: Print debug message
    ansible.builtin.debug:
      msg:
      - "user_name: {{ user_name }}"
      - "inventory_hostname: {{ inventory_hostname }}"
```

## ansible.builtin.file

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/file_module.html
* Set attributes of files, directories, or symlinks and their targets.
* Alternatively, remove files, symlinks or directories.
* Many other modules support the same options as the ansible.builtin.file module - including ansible.builtin.copy, ansible.builtin.template, and ansible.builtin.assemble.

## ansible.builtin.get_url

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/get_url_module.html

* `checksum`:

  Additionally, if a checksum is passed to this parameter, and the file exist under the `dest` location, the `destination_checksum` would be calculated, and if checksum equals `destination_checksum`, the file download would be skipped (unless `[force]` is `true`). If the checksum does not equal `destination_checksum`, the destination file is deleted.



## ansible.builtin.group

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/group_module.html

## ansible.builtin.import_playbook

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/import_playbook_module.html

```yml
- hosts: localhost
  tasks:
    - ansible.builtin.debug:
        msg: play1

- name: Include a play after another play
  ansible.builtin.import_playbook: otherplays.yaml

- name: Set variables on an imported playbook
  ansible.builtin.import_playbook: otherplays.yml
  vars:
    service: httpd
```

## ansible.builtin.import_role

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/import_role_module.html
* Much like the `roles:` keyword, this task loads a role, but it allows you to control when the role tasks run in between other tasks of the play.

```yml
# pass variable to imported role
- ansible.builtin.import_role:
      name: test
    vars:
      test_var: "TEST"
```

### use only specifc task of role

```yaml
- hosts: all

  tasks:

    - import_role:
        name: snapshots
        tasks_from: create

    - import_role:
        name: something

    - import_role:
        name: snapshots
        tasks_from: remove
```

## ansible.builtin.import_tasks

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/import_tasks_module.html

```yml
---
- hosts: all
  tasks:
    - name: Apply conditional to all imported tasks
      ansible.builtin.import_tasks: stuff.yaml
      when: hostvar is defined
```

## ansible.builtin.include_role

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_role_module.html

## ansible.builtin.include_tasks

- https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_tasks_module.html
- Includes a file with a list of tasks to be executed in the current playbook.

```yml
---
- hosts: all
  tasks:
    - name: Include task list in play only if the condition is true
      ansible.builtin.include_tasks: "{{ hostvar }}.yaml"
      when: hostvar is defined
      
- name: Apply tags to tasks within included file when using free-form
  ansible.builtin.include_tasks: install.yml
  args:
    apply:
      tags:
        - install
```

## ansible.builtin.include_vars

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_vars_module.html#parameters

## ansible.builtin.lineinfile

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/lineinfile_module.html

* `line` 

  The line to insert/replace into the file.

  Required for state=present.

* `path` 

  The file to modify.

* `regexp` 

  The regular expression to look for in every line of the file.

  For `state=present`, the pattern to replace if found. Only the last line found will be replaced.

  For `state=absent`, the pattern of the line(s) to remove.

  If the regular expression is not matched, the line will be added to the file in keeping with `insertbefore` or `insertafter` settings.

  When modifying a line the regexp should typically match both the initial state of the line as well as its state after replacement by line to ensure idempotence.

* `create`	

  Used with state=present.

  If specified, the file will be created if it does not already exist.

  By default it will fail if the file is missing.

```yml
- name: Make sure group wheel is not in the sudoers configuration
  ansible.builtin.lineinfile:
    path: /etc/sudoers
    state: absent
    regexp: '^%wheel'
    
- name: Replace a localhost entry with our own
  ansible.builtin.lineinfile:
    path: /etc/hosts
    regexp: '^127\.0\.0\.1'
    line: 127.0.0.1 localhost
    owner: root
    group: root
    mode: '0644'

- name: Add a line to a file if the file does not exist, without passing regexp
  ansible.builtin.lineinfile:
    path: /tmp/testfile
    line: 192.168.1.99 foo.lab.net foo
    create: yes
```

## ansible.builtin.ping

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/ping_module.html

## ansible.builtin.script

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/script_module.html#parameters

```yml
- name: Run a script with arguments (free form)
  ansible.builtin.script: /some/local/script.sh --some-argument 1234
```

## ansible.builtin.service

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html#parameters
* `state`: 
  * `"reloaded"`
  * `"restarted"`
  * `"started"`
  * `"stopped"`

```yml
- name: Start service httpd, if not started
  ansible.builtin.service:
    name: httpd
    state: started

- name: Enable service httpd, and not touch the state
  ansible.builtin.service:
    name: httpd
    enabled: yes
```

## ansible.builtin.set_fact

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/set_fact_module.html
* allows setting variables associated to the current host
* These variables will be available to subsequent plays during an ansible-playbook run via the host they were set on.
* other types of variables have a higher priority, so this value may be overridden.

```yml
- name: Setting host facts using complex arguments
  ansible.builtin.set_fact:
    one_fact: something
    other_fact: "{{ local_var * 2 }}"
    another_fact: "{{ some_registered_var.results | map(attribute='ansible_facts.some_fact') | list }}"

- name: Setting facts so that they will be persisted in the fact cache
  ansible.builtin.set_fact:
    one_fact: something
    other_fact: "{{ local_var * 2 }}"
    cacheable: yes
```

## ansible.builtin.setup

```sh
ansible all -m ansible.builtin.setup
ansible all -m ansible.builtin.setup --limit 192.168.178.102 | grep "distribution"
```

## ansible.builtin.shell

```sh
ansible raleigh -m ansible.builtin.shell -a 'echo $TERM'
```

When running any command with the Ansible ad hoc CLI (as opposed to Playbooks), pay particular attention to **shell quoting rules**, so the local shell retains the variable and passes it to Ansible. For example, using double rather than single quotes in the above example would evaluate the variable on the box you were on.

## ansible.builtin.stat

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/stat_module.html

```yaml
# check if file exists
- hosts: servers
  tasks:
    - name: Ansible check file exists.
      stat:
        path: /etc/issue
      register: p
    - debug:
        msg: "File exists..."
      when: p.stat.exists
    - debug:
        msg: "File not found"
      when: p.stat.exists == False
```

## ansible.builtin.systemd_service

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/systemd_service_module.html



## ansible.builtin.template

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html

```yml
- name: Template a file to /etc/file.conf
  ansible.builtin.template:
    src: /mytemplates/foo.j2
    dest: /etc/file.conf
    owner: bin
    group: wheel
    mode: '0644'
```

## ansible.builtin.unarchive

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/unarchive_module.html



## ansible.builtin.user

* https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html#parameters
* https://earlruby.org/2021/01/generate-a-crypted-password-for-ansible/
* **manage user accounts and attributes**
* need to add the user's groups first if not present yet

### parameters

* `password`

  Linux/Unix/POSIX: Enter the hashed password as the value.

* `update_password`

  `on_create`, means that you can no longer update the password using Ansible

### examples

```yml
- name: Add the user 'johnd' with a specific uid and a primary group of 'admin'
  ansible.builtin.user:
    name: johnd
    group: admin
    create_home: yes
    shell: /bin/bash
    generate_ssh_key: yes
    ssh_key_bits: 2048
    ssh_key_file: .ssh/id_rsa

- name: Remove the user 'johnd'
  ansible.builtin.user:
    name: johnd
    state: absent
    remove: yes
```

## community.general.dconf

* https://docs.ansible.com/ansible/latest/collections/community/general/dconf_module.html
* modify and read dconf database

## community.general.flatpak

* https://docs.ansible.com/ansible/latest/collections/community/general/flatpak_module.html#parameters

## community.general.flatpak_remote

* https://docs.ansible.com/ansible/latest/collections/community/general/flatpak_remote_module.html

* ```sh
  ansible-galaxy collection install community.general
  ```

## community.general.nmcli

* https://docs.ansible.com/ansible/latest/collections/community/general/nmcli_module.html

```yml
- name: Create a wireguard connection
  community.general.nmcli:
    type: wireguard
    conn_name: my-wg-provider
    ifname: mywg0
    wireguard:
        listen-port: 51820
        private-key: my-private-key
    autoconnect: true
    state: present
```

## community.general.npm

* https://docs.ansible.com/ansible/latest/collections/community/general/npm_module.html#parameters

```yml
- name: Install "coffee-script" node.js package.
  community.general.npm:
    name: coffee-script
    path: /app/location

- name: Install "coffee-script" node.js package globally.
  community.general.npm:
    name: coffee-script
    global: true
```

## ansible.posix.authorized_key

* https://docs.ansible.com/ansible/latest/collections/ansible/posix/authorized_key_module.html

* **adds or removes SSH authorized keys** for particular user accounts

### parameters

* `key`

  The SSH public key(s)

* `user` 

  The username on the remote host whose authorized_keys file will be modified.

## ansible.posix.mount

* https://docs.ansible.com/ansible/latest/collections/ansible/posix/mount_module.html
* controls active and configured mount points in `/etc/fstab`



## ansible.posix.synchronize

* a wrapper around rsync
* rsync must be installed on both the local and remote host.

## ansible.posix.sysctl

* https://docs.ansible.com/ansible/latest/collections/ansible/posix/sysctl_module.html
