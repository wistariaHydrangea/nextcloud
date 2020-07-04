# Manages Nextcloud with Ansible

## Chapter structure

1. Diagram
2. Install packages
3. Authentication format

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

## Authentication format

Case1: SSH Authentication

Create Key

```terminal
# mkdir ~/.ssh
# cd ~/.ssh
# ~./ssh $ ssh-keygen -t rsa -b 4096
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): mariadb-server <--任意の文字を入力
Enter passphrase (empty for no passphrase): <--任意の文字を入力
Enter same passphrase again: <--もう一度入力
Your identification has been saved in mariadb-server.
Your public key has been saved in mariadb-server.pub.
```

(~/.ssh/config)

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

(/etc/ansible/ansible.cnf)

```terminal

~省略~

_pass=True

~省略~

```

## 参考資料

1. [ansible-playbookでサーバーを構築する時のssh接続に関するメモ ...(https://qiita.com/tomson784/items/8086b46097ff5d4cfeb6)](https://qiita.com/tomson784/items/8086b46097ff5d4cfeb6)
