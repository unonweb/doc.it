# CLI

## ansible-doc

```sh
ansible-doc -l # list of available modules
```

## ansible-playbook

```sh
ansible-playbook --ask-become-pass <playbook>
ansible-playbook --ask-vault-pass
ansible-playbook --ask-pass # ask for connection password

ansible-playbook -i INVENTORY
ansible-playbook --inventory INVENTORY # specify inventory host path or comma separated host list

ansible-playbook --tags <tags> --ask-become-pass <playbook> # 
          
ansible-playbook --syntax-check <playbook>
ansible-playbook --check <playbook> # dry-run
```

```sh
ansible-playbook --list-tags <playbook>
ansible-playbook --list-hosts # outputs a list of matching hosts; does not execute anything else
ansible-playbook --list-tasks # list all tasks that would be executed
```

```sh
ansible-playbook playbook.yml -l server1 -u sammy # execute the playbook only on server1, connecting as sammy

ansible-playbook -l 'SUBSET'
ansible-playbook --limit 'SUBSET' # further limit selected hosts to an additional pattern
ansible-playbook -u 'REMOTE_USER'
ansible-playbook --user 'REMOTE_USER' # connect as this user
```

## ansible-galaxy

```sh
ansible-galaxy collection install community.general
```

## ansible-inventory

```bash
ansible-inventory -i inventory --list
```

## ansible-lint

```sh
pip3 install ansible-dev-tools
```

## ansible-vault

* **Typing secret content directly at the command line (without a prompt) leaves the secret string in your shell history. Do not do this outside  of testing.**

### encrypt variables

* https://docs.ansible.com/ansible/latest/vault_guide/vault_encrypting_content.html#creating-encrypted-variables

```sh
ansible-vault encrypt_string --vault-password-file a_password_file 'foobar' --name 'the_secret' # Encrypt the string ‘foobar’ using the only password  stored in ‘a_password_file’ and name the variable ‘the_secret’:
```

```sh
ansible-vault encrypt_string --vault-password-file a_password_file 'foobar' --name 'the_secret' # encrypt the string ‘foobar’ using the only password  stored in ‘a_password_file’ and name the variable ‘the_secret’:
```

The command above creates this content:

```
the_secret: !vault |
       $ANSIBLE_VAULT;1.1;AES256
       62313365396662343061393464336163383764373764613633653634306231386433626436623361
       6134333665353966363534333632666535333761666131620a663537646436643839616531643561
       63396265333966386166373632626539326166353965363262633030333630313338646335303630
       3438626666666137650a353638643435666633633964366338633066623234616432373231333331
       6564
```

The command above creates this content:

```yml
 the_secret: !vault |
       $ANSIBLE_VAULT;1.1;AES256
       62313365396662343061393464336163383764373764613633653634306231386433626436623361
       6134333665353966363534333632666535333761666131620a663537646436643839616531643561
       63396265333966386166373632626539326166353965363262633030333630313338646335303630
       3438626666666137650a353638643435666633633964366338633066623234616432373231333331
       6564
```

### encrypt new files

```sh
ansible-vault create --vault-id test@multi_password_file foo.yml # create a new encrypted data file called ‘foo.yml’ with the ‘test’ vault password from ‘multi_password_file’:
```

The tool launches an editor (whatever editor you  have defined with $EDITOR, the default editor is vi). Add the content. When you close the editor session, the file is saved as encrypted data. The file header reflects the vault ID used to create it:

```yml
``$ANSIBLE_VAULT;1.2;AES256;test``
```

```sh
ansible-vault create --vault-id my_new_password@prompt foo.yml # create a new encrypted data file with the vault ID ‘my_new_password’ assigned to it and be prompted for the password.
```

Again, add  content to the file in the editor and save. Be sure to store the new  password you created at the prompt, so you can find it when you want to  decrypt that file.

### encrypt existing files

```sh
ansible-vault encrypt foo.yml bar.yml baz.yml
```

### view encrypted files

```sh
ansible-vault view foo.yml bar.yml baz.yml # view the contents of an encrypted file without editing it
```

### edit encrypted files

```sh
ansible-vault edit foo.yml # edit an encrypted file in place
```

This command decrypts the file to a temporary file, allows you to edit the content, then saves and re-encrypts the content and removes the temporary file when you close the editor.

## ansible

* modules: https://docs.ansible.com/ansible/latest/collections/ansible/builtin/index.html#plugins-in-ansible-builtin

```sh
ansible -m <module>
ansible --module-name <module>

ansible -k
ansible --ask-pass # ask for connection password

ansible test -m ansible.builtin.setup # gather facts
ansible all --key-file ~./.ssh/ansible -i inventory -m ping
```

```sh
ansible localhost --module-name include_role --args name=<role_name>
ansible localhost -m include_role -a name=myrole
```
