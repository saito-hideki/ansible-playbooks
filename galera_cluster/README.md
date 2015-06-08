# Playbookの利用方法
このPlaybookは、CentOS 7がインストールされている３台のサーバ上に、Galera Clusterを利用してMariaDBのActive-Activeクラスタを構築します。

## 実行方法

    $ ansible-playbook -i production -u {{ユーザ名}} -k -K site.yml

## 環境にあわせて変更が必要
### インベントリファイルの設定
- production
- dbmsグループに３台のDBサーバのIPアドレスを設定する
- ここで設定したIPアドレスはgalera cluster用のmy.cnf内で設定するためホスト名ではなくIPアドレスを設定すること

### hostsファイルの設定
- roles/common/files/hosts
- galera clusterのメンバーをhostsに記載する

### NTPサーバの設定
- roles/common/vars/main.yml
- ntpserver パラメータに環境にあわせたNTPサーバのホスト名またはIPアドレスを設定する

### Galera ClusterのメンバーとなるDBサーバの設定
- roles/dbms/vars/main.yml
- galera_nodesのパラメータmaster_nodenameに、galera clusterのマスターノードとなるノード名を設定する
- galera_nodesのパラメータnode1,node2,node3にDBサーバのホスト名またはIPアドレスを設定する