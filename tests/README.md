Requirements
------------

Testing this role is required root privilege.

How to test
-----------

Create symlink as below:

```
$ ln -s ../../../roles roles
```

exec ansible-playbook:

```
$ ansible-playbook -i hosts test.yml --ask-sudo-pass --check
$ ansible-playbook -i hosts test.yml --ask-sudo-pass

```

or

```
$ ansible-playbook -i hosts test.yml --check
$ ansible-playbook -i hosts test.yml
```
