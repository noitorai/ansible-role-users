Role Name
=========

A role for user management.

Requirements
------------

* Root privileges (for creating/removing/updating users or groups)

Role Variables
--------------

本ロールで定義されている変数は下表の通りです。

| variable                  | description                                                       | default value              |
|---------------------------|-------------------------------------------------------------------|----------------------------|
| users_generic_user_list   | 全ユーザのリスト (詳細は後述)                                     | []                         |
| users_generic_group_list  | 全グループのリスト (詳細は後述)                                   | []                         |
| users_username_list       | 作成/削除するユーザのリスト (詳細は後述)                          | []                         |
| users_group_list          | 作成/削除するグループのリスト (詳細は後述)                        | []                         |
| users_extra_user_list     | users_username_list以外に作成/削除するユーザのリスト (詳細は後述) | []                         |
| users_extra_group_list    | users_group_list以外に作成/削除するグループのリスト (詳細は後述)  | []                         |
| users_passwords           | ユーザのパスワードを記載したマッピング (詳細は後述)               | {}                         |
| users_authorized_keys_dir | 参照するauthorized_keysの配置場所                                 | "files/authorized_keys.d/" |
| users_sudoers_filename    | 各サーバに配置するsudoersファイルの名前                           | "ansible_managed"          |


### users_generic_user_list

ansible管理下のサーバ共通で使用するユーザを定義します。
userモジュールと同じパラメータ(一部未定義)名のものは、値をそのままuser
モジュールに渡します。使用可能なパラメータは下表の通りです。

| key             | required | default value |
|-----------------|----------|---------------|
| name            | yes      |               |
| uid             | yes      |               |
| comment         | no       |               |
| state           | no       | present       |
| group           | yes      |               |
| groups          | no       |               |
| append          | no       | no            |
| createhome      | no       | yes           |
| force           | no       |               |
| home            | no       |               |
| move_home       | no       | no            |
| password        | no       |               |
| remove          | no       | no            |
| shell           | no       |               |
| update_password | no       | always        |
| sudo            | no       | no            |
| authorized_keys | no       | no            |

※ authorized_keysがyesの場合、users_authorized_keys_dir以下のユーザ名
   のファイルの中身をauthorized_keysとして配置します。
   
※ sudoがyesの場合、/etc/sudoers.d配下のファイルにsudo可能にする設定を
   追加します。

#### Example users_generic_user_list

```
users_generic_user_list:
  - { name: test1, uid: 1200, group: test1,  authorized_keys: "yes", sudo: "yes" }
  - { name: test2, uid: 1202, group: test2,  groups: wheel, comment: "test2 user" }
  - { name: test3, uid: 1203, group: test3,  groups: wheel, comment: "test3 user", authorized_keys: "no" }
```

### users_generic_group_list

ansible管理下のサーバ共通で使用するグループを定義します。
groupモジュールと同じパラメータ(一部未定義)名のものは、値をそのままgroup
モジュールに渡します。使用可能なパラメータは下表の通りです。

| key  | required | default value |
|------|----------|---------------|
| name | yes      |               |
| gid  | yes      |               |
| sudo | no       | no            |

※ sudoがyesの場合、/etc/sudoers.d配下のファイルにsudo可能にする設定を
   追加します。

#### Example users_generic_user_list

```
users_generic_group_list:
  - { name: admin, gid: 1101 }
  - { name: test1, gid: 1200, sudo: no }
  - { name: test2, gid: 1202, sudo: yes }
  - { name: test3, gid: 1203 }
```

### users_username_list

作成/削除するユーザのリストを定義します。users_generic_user_listに記載
のユーザ名のみ指定できます。

#### Example users_username_list

```
users_username_list:
  - test1
  - test2
  - test3
```

### users_group_list

作成/削除するグループのリストを定義します。users_generic_group_listに
記載のグループ名のみ指定できます。

#### Example users_group_list

```
users_group_list:
  - admin
  - test1
  - test2
```

### users_extra_user_list

users_generic_user_listに記載のないユーザを作成する場合に使用します。
フォーマットはusers_generic_user_listと同じです。そちらを参照してくだ
さい。

#### Example users_extra_user_list

```
users_extra_user_list:
  - { name: extrauser, uid: 1305, group: extragroup, comment: "extrauser", authorized_keys: yes,  }
```

### users_extra_group_list

users_group_listに記載のないグループを作成する場合に使用します。
フォーマットはusers_generic_group_listと同じです。そちらを参照してくだ
さい。

#### Extrauser users_extra_group_list

```
users_extra_group_list:
  - { name: extragroup, gid: 1300, sudo: yes }
```
### users_passwords

ユーザのパスワードを記載したマッピングです。キーにユーザ名、値にパスワー
ドを指定します。独立したファイルに定義して暗号化しておくことをお勧めし
ます。

#### Example users_passwords

```
users_passwords:
  test1: '$6$rounds=100000$FMS0M3TZnxT9.4k/$neBNNdKuuFkpR3nTqchYPHc7u7DhYQrLXPnrlZJ6x2wVT1L5AVYm1CtsIKx8wPXi2CKlmLF9xEo9BLTWspol.0'
  test2: '$6$rounds=100000$3GP02reKif3EQP7A$lYS6jpxcPkZj6tTZx9ULDe/XIhcYFIra6nHAXcHvwQLs6oQLQKV9.BJpd7..X8igzqZHWfp9wbFiQlYAQnB9M/'
```

パスワード文字列は以下のコマンドで生成できます。これにはpython-passlib
が必要です。aptやyumからインストールできます。

```
python -c "from passlib.hash import sha512_crypt; import getpass; print sha512_crypt.encrypt(getpass.getpass())"
```

そのまま設定ファイルに記載しても良いですが、念の為別ファイルに記載して
暗号化しておくとより安心です。以下はsecret.ymlを暗号化する場合の例です。
暗号化した後は「ansible-vault edit」で編集することができます。

```
$ cat secret.yml
users_passwords:
  test1: '$6$rounds=100000$FMS0M3TZnxT9.4k/$neBNNdKuuFkpR3nTqchYPHc7u7DhYQrLXPnrlZJ6x2wVT1L5AVYm1CtsIKx8wPXi2CKlmLF9xEo9BLTWspol.0'
  test2: '$6$rounds=100000$3GP02reKif3EQP7A$lYS6jpxcPkZj6tTZx9ULDe/XIhcYFIra6nHAXcHvwQLs6oQLQKV9.BJpd7..X8igzqZHWfp9wbFiQlYAQnB9M/'
  
$ ansible-vault encrypt secret.yml
New Vault password: 
Confirm New Vault password: 
Encryption successful

$ cat secret.yml
$ANSIBLE_VAULT;1.1;AES256
30366563306366376466666163313732333032383738636264363635356566326336373861313438
(...snip...)
```

Dependencies
------------

なし

Example Playbook
----------------

```
    - hosts: servers
      become: yes
      vars_file:
        - secret.yml
      roles:
        - users
```
