Role Name
=========

A role for user management.

Requirements
------------

XXX: Any pre-requisites that may not be covered by Ansible itself or the role should be mentioned here. For instance, if the role uses the EC2 module, it may be a good idea to mention in this section that the boto package is required.

* Root privileges (for creating/removing/updating users or groups)

Role Variables
--------------

XXX: A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.

本ロールで定義されている変数は以下の通りです。

| variable | description | default value |
|----------|-------------|---------------|
| users_generic_user_map | 全ユーザのリスト (詳細は後述) | [] |
| users_user_list | 作成/削除するユーザーのリスト (詳細は後述) | [] |
| users_group_list | 作成/削除するグループのリスト (詳細は後述) | [] |

### users_generic_user_map

ansibleで管理する全ユーザのハッシュ(マッピング)を定義します。
キーはユーザ名、値はハッシュでuserモジュールと同じパラメータ(一部未定
義)を持ちます。詳細は以下の通りです。

| key | required | default value |
|-----|----------|---------------|
| name | yes |
| uid | yes |
| comment no |  |
| state | no | present |
| group | yes |
| groups | no |  |
| append | no | no |
| createhome | no | yes |  |
| force | no |  |
| home | no |  |
| move_home | no | no |
| password | no |  |
| remove | no | no |
| shell | no |  |
| update_password | no | always |

#### Example users_generic_user_map

```
users_generic_user_map:
  test1:
    name: test1
    uid: 1200
    group: test1
  test2:
    name: test2
    uid: 1202
    comment: "test2 user"
    group: test2
    groups: sudo
  test3:
    name: test3
    uid: 1203
    group: test3
    groups: sudo
    comment: "test3 user"
```

### users_user_list

作成/削除するユーザのリストを定義します。
リストの要素は users_generic_user_map で定義したユーザ、またはそれと同じ構造のハッシュです。

```
users_user_list:
  - '{{ users_generic_user_map["test1"] }}'
  - name: test2
    state: absent
    remove: yes
  - name: users_generic_user_map["test3"]["name"]
    uid: 1303
    group: users_generic_user_map["test3"]["group"]
    groups: users_generic_user_map["test3"]["groups"]
    comment: "test3 user2"
  - name: test4
    uid: 1204
    group: test4
```

### users_group_list

作成/削除するグループのリストを定義します。
XXX:

Dependencies
------------

XXX: A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

License
-------

XXX: BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
