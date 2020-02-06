# Don't be tied to capacity with "Nextcloud"

## Chapter structure

1. What is tha Nextcloud
2. Diagram
3. Nextcloud construcyion  
    3-1. Install of MariaDB (DB server side)  
    3-2. Prepare for DB (DB server side)  
    3-3. Install of Nextcloud, httpd and PHP and DB (Nextcloud server side)  
    3-4. Prepare for DB (Nextcloud server side)  
    3-5. Prepare of Nextcloud server (Nextcloud server side)  
    3-6. Setup of Nextcloud  
4. Trouble management

## 1.What is tha Nextcloud

"Nextcloud" は使いやすく安全性が高いように設計された、自己ホスト型のオープンソースのファイル同期および共有ソリューションです。
写真や動画、書類などのデータを保存することができ、メンバーとの共有することもできます。DropBoxとほぼ同じ機能が備わっています。

## 2.Diagram

| 項目 | 詳細 |
| :--: | :--: |
| OS | CentOS 7 |
| Nextcloud | 17.0.2 |
| httpd | Apache/2.4.6 |
| PHP | 7.3.13 |
| MariaDB | 10.4.11 |

## 3.Nextcloud construcyion

The procedure up to the construction of "Nextcloud" is shown.

### 3-1.Install of MariaDB (DB server side)

Prepare a repository.

```terminal
# cat <<EOF | sudo tee /etc/yum.repos.d/MariaDB.repo
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.4/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
EOF
```

Install necessary packages MariaDB.

```terminal
# yum -y install MariaDB-server MariaDB-client
```

Release for port of mariadb.

```terminal
# firewall-cmd --add-service=mysql
# firewall-cmd --runtime-to-permanent
# firewall-cmd --reload
```

To start MariaDB.

```terminal
# systemctl enable mariadb
# systemctl start mariadb
```

### 3-2.Prepare for DB

Setup for password of "root".

```terminal
# mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none):  <--Enter
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] y <--
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y <--
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] y <--
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y <--
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y <--
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```

```terminal
# mysql -u root -p
Enter password:  <-- Enter password
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 9
Server version: 10.4.11-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```

Create a user for "Nextcloud".
DBUserName: nextcloud
DBpassword: password

```terminal
MariaDB [(none)]> CREATE DATABASE nextcloud;
MariaDB [(none)]> CREATE USER 'nextcloud'@'Nextclooud server ip address' IDENTIFIED BY 'password';
```

Gives access to the database.

```terminal
MariaDB [(none)]> GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextcloud'@'Nextclooud server ip address' with grant option;
MariaDB [(none)]> FLUSH PRIVILEGES;
```

### 3-3.Install of httpd and PHP and DB (Nextcloud server side)

Install the necessary packages for building Nextcloud.

```terminal
# yum -y update
# yum -y install epel-release yum-utils
# yum -y install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
# yum -y install --enablerepo=remi,remi-php72 php php-gd php-json php-mysql php-curl php-mbstring
# yum -y install --enablerepo=remi,remi-php72 php-intl php-mcrypt php-imagick php-xml php-zip php-process php-apcu
# yum -y install vim unzip wget httpd bash-completion policycoreutils-python mlocate bzip2
# wget https://download.nextcloud.com/server/releases/nextcloud-17.0.2.zip
```

```terminal
# php -v
PHP 7.2.27 (cli) (built: Jan 22 2020 09:31:55) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies
```

Release for port of httpd and Mariadb.

```terminal
# firewall-cmd --add-service={http,https}
# firewall-cmd --runtime-to-permanent
# firewall-cmd --reload
```

(Release SELinux)

```termial
# sudo setenforce 0
# sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
```

### 3-4.Prepare for DB

Prepare a repository.

```terminal
# cat <<EOF | sudo tee /etc/yum.repos.d/MariaDB.repo
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.4/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
EOF
```

Install necessary packages MariaDB.

```terminal
# yum -y install MariaDB-server MariaDB-client
```

Release for port of mariadb.

```terminal
# firewall-cmd --add-service=mysql
# firewall-cmd --runtime-to-permanent
# firewall-cmd --reload
```

To start MariaDB.

```terminal
# systemctl enable mariadb
# systemctl start mariadb
```

```terminal
# mysql -u nextcloud -p -h (MariaDB server IPaddress)
password:  <-- CREATE USER の際に設定したパスワード
```

### 3-5.Prepare of Nextcloud server

Install of "Nextcloud".

```terminal
# unzip nextcloud-17.0.2.zip
```

Copy to /var/www/html/ deployed nextcloud.

```terminal
# cp -R nextcloud/ /var/www/html/
```

Create datadirectory of "Nextcloud".

```terminal
# mkdir /var/www/html/nextcloud/data
```

Change the owner information so that you can read and write in the entire Nextcloud directory.

```terminal
# chown -R apache:apache /var/www/html/nextcloud
```

Start httpd.

```terminal
# systemctl start httpd
```

## 3-6.Store to NFS

Installing to client side neccessary packages of nfs.

```terminal
# yum -y install nfs-utils
```

Mount to client side.

```teminal
# mount -t nfs (nfs server 'IP address' or 'hostname'):/mnt/nextcloud/data /mnt/nextcloud/data
```

(/etc/fstab)

```terminal
/dev/mapper/centos-root /                       xfs     defaults        1 1
UUID=a18716b4-cd67-4aec-af91-51be7bce2a0b /boot xfs     defaults        1 2
/dev/mapper/centos-swap swap                    swap    defaults        0 0

(nfs server 'IP address' or 'hostname'):/mnt/nextcloud/data /mnt/nextcloud/data                   nfs     defaults        0 0
```

### 3-6.Setup of Nextcloud

Access to server ip address of Nextcloud.

![image-01](images/nextcloud-images01.png)

## 4.Trouble management

## 参考資料

1. [https://docs.nextcloud.com/server/17/admin_manual/installation/source_installation.html#example-installation-on-centos-7-server](https://docs.nextcloud.com/server/17/admin_manual/installation/source_installation.html#example-installation-on-centos-7-server)
2. [https://nextcloud.com/changelog/#latest17](https://nextcloud.com/changelog/#latest17)
3. [https://computingforgeeks.com/install-nextcloud-on-centos-with-php-apache-mariadb/](https://computingforgeeks.com/install-nextcloud-on-centos-with-php-apache-mariadb/)
4. [https://qiita.com/S_Katz/items/9402ae8e104b8ac5702b](https://qiita.com/S_Katz/items/9402ae8e104b8ac5702b)
5. [https://www.marksei.com/how-to-install-nextcloud-17-server-on-centos-7/](https://www.marksei.com/how-to-install-nextcloud-17-server-on-centos-7/)
6. [https://www.gwtcenter.com/owncloud-installation](https://www.gwtcenter.com/owncloud-installation)
7. [https://saka24.blue/index.php/2017/10/12/nextcloud_install/](https://saka24.blue/index.php/2017/10/12/nextcloud_install/)
