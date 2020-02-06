# Manages Nextcloud with Ansible

## 1.Diagram

| 項目 | 詳細 |
| :--: | :--: |
| OS | CentOS 7 |
| Ansible | ansible 2.9.3 |
| python | 2.7.5 |

## Install packages

```terminal
# yum -y inistall epel-release
# yum -y install ansible
# ansible --version
ansible 2.9.3
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/root/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/site-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.5 (default, Aug  7 2019, 00:51:29) [GCC 4.8.5 20150623 (Red Hat 4.8.5-39)]
```

## Set ssh config

Case1: SSH Authentication

(./ssh/config)

```txt
Host nextcloud-server
  HostName (接続先のホスト名 or IPアドレス)
  User (接続先のユーザー名)
  Port (sshのポート番号)
  IdentityFile ~/.ssh/(秘密鍵)

Host mariadb-server
  HostName (接続先のホスト名 or IPアドレス)
  User (接続先のユーザー名)
  Port (sshのポート番号)
  IdentityFile ~/.ssh/(秘密鍵)
```

(hosts)

```txt
[nextcloud]
nextcloud-server

[mariadb]
mariadb-server
```

Case2: Password Authentication

```terminal
# ansible-playbook -i hosts (.yml file) --ask-pass
```

## 参考資料

1. [ansible-playbookでサーバーを構築する時のssh接続に関するメモ ...(https://qiita.com/tomson784/items/8086b46097ff5d4cfeb6)](https://qiita.com/tomson784/items/8086b46097ff5d4cfeb6)
