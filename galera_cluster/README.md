# Playbookの利用方法
このPlaybookは、CentOS 7がインストールされている３台のサーバ上に、Galera Clusterを利用してMariaDBのActive-Activeクラスタを構築します。

## 実行方法

    $ ansible-playbook -i production -u {{ユーザ名}} -k -K site.yml

## 環境にあわせて変更が必要
### インベントリファイルの設定
- file: production
- dbmsグループに３台のDBサーバのIPアドレスを設定する
- ここで設定したIPアドレスはgalera cluster用のmy.cnf内で設定するためホスト名ではなくIPアドレスを設定すること

### hostsファイルの設定
- file: roles/common/files/hosts
- galera clusterのメンバーをhostsに記載する

### NTPサーバの設定
- file: roles/common/vars/main.yml
- ntpserver パラメータに環境にあわせたNTPサーバのホスト名またはIPアドレスを設定する

### Galera ClusterのメンバーとなるDBサーバの設定
- file: roles/dbms/vars/main.yml
- galera_nodesのパラメータmaster_nodenameに、galera clusterのマスターノードとなるノード名を設定する
- galera_nodesのパラメータnode1,node2,node3にDBサーバのホスト名またはIPアドレスを設定する

## MariaDBの起動
１台めのノードでbootstrapを実行する。

    [root@ctrl00 ~]# service mysql bootstrap
    Bootstrapping the cluster.. Starting MySQL.... SUCCESS!

その他のノードでは通常通りサービスを起動する。

    [root@ctrl01 ~]# service mysql start
    Starting MySQL..... SUCCESS!

    [root@ctrl02 ~]# service mysql start
    Starting MySQL..... SUCCESS!

wsrep_cluster_size(３ノードであれば３となっている)を確認する。

    [root@ctrl00 ~]# mysql -u root
    Welcome to the MariaDB monitor.  Commands end with ; or \g.
    Your MariaDB connection id is 6
    Server version: 5.5.43-MariaDB-wsrep MariaDB Server, wsrep_25.11.r4026

    Copyright (c) 2000, 2015, Oracle, MariaDB Corporation Ab and others.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    MariaDB [(none)]> SHOW STATUS LIKE 'wsrep_cluster_size';
    +--------------------+-------+
    | Variable_name      | Value |
    +--------------------+-------+
    | wsrep_cluster_size | 3     |
    +--------------------+-------+

