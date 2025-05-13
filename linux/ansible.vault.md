
# VAULT

* encrypts **variables and files**
* use encrypted variables and files in ad hoc commands and playbooks by supplying the passwords you used to encrypt them
* Each time you encrypt a variable or file with Ansible Vault, you must provide a password. When you use an encrypted variable or file in a  command or playbook, you must provide the same password that was used to encrypt it
* To store a vault **password in a file**, enter the password as a string on a single line in the file. Make sure the permissions on the file are appropriate. Do not add password files to source control.
* Encrypted content always includes the `!vault` tag, which tells Ansible and YAML that the content needs to be decrypted, and a `|` character, which allows multi-line strings

## encrypt

```sh
ansible-vault encrypt foo.yml bar.yml baz.yml # encrypt existing files
```

## view

* better don't use! logs secret to shell

```sh
ansible-vault view foo.yml bar.yml baz.yml
```

## edit

This command decrypts the file to a temporary file, allows you to edit the content, then saves and re-encrypts the content and removes the temporary file when you close the editor. For example:

```sh
ansible-vault edit foo.yml # edit an encrypted file in place,
```

## decrypt

```yml
ansible-playbook --ask-vault-pass site.yml # prompt for the password
```

## rekey

```sh
ansible-vault rekey inventory/group_vars/all/vault.yml # change password of vault file
```
