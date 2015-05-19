# Section 1 基本のサーバー構築

## 1-1 CentOS 7のインストール

### VirtualBoxへのインストール

*VirtualBoxをインストールするために
　http://172.16.40.7/yonashiro/にてCentOS-7-x86_64-Minimal-1503-01.iso を
インストールする


//CentOSの公式サイトよりCentOS 7 Minimal ISO(x86_64)のISOファイルをダウンロードし、

VirtualBox上にインストールしてください。

VirtualBoxで作成する仮想マシンのメモリのサイズは1GBにします。また、ストレージの容量は8GB程度に設定してください。

*VirtulBoxのメモリサイズを1024MBに設定してストレージサイズを設定する

ネットワークアダプター2を設定します。割り当てを「ホストオンリーアダプター」にします。
(ネットワークアダプター1はデフォルト(NAT)で問題ありません)

*VirtuaBoxを開いたら設定にてネットワークを選択する。
 アダプター1/2の設定を行う。
	*1はデフォルト
	*2は割り当てを”ホストオンリーアダプター”にする

インストール中に指示されるパーティションの設定は特に指定しません。

*設定が終わったらそのまま完了してインストール。

インストール中、root以外の作業用(管理者)のユーザーを作成してください。

*インストール中に
	ユーザー名を設定
	パスワードの設定

### ネットワークアダプター1/2へのIPアドレスの設定とssh接続の確認

/etc/sysconfig/network-scriptにifcfg-enp0s?というファイルがあるので、
そのファイルを編集してネットワーク接続ができるように設定します。

*VM VirtualBox を起動させるとCUI的なものが出た来るので
	/etc/sysconfig/network-script に移動して
 中にある
	ifcfg-enp0s3 ifcfg-enp0s8 の設定を行う
 中にある ONBOOT="no" を ONBOOT="yes" にかえる
 

DHCPでIPアドレスを取得できますので、[RedHat Enterprise Linux 7のマニュアル(英語)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html-single/Networking_Guide/index.html#sec-Configuring_a_Network_Interface_Using_ifcg_Files)を読んで設定してください。

### SSH接続の確認

*接続の確認
 ssh administrator@192.168.56.101


### インストール後の設定

yumやwgetを使用する時のproxyの設定を行なってください。

*vimで/etc/profileを開いて最終行に
 MY_PROXY_URL="http://172.16.40.1:8888"
 HTTP_PROXY=$MY_PROXY_URL
 HTTPS_PROXY=$MY_PROXY_URL
 FTP_PROXY=$MY_PROXY_URL
 http_proxy=$MY_PROXY_URL
 https_proxy=$MY_PROXY_URL
 ftp_proxy=$MY_PROXY_URL
 export HTTP_PROXY HTTPS_PROXY FTP_PROXY http_proxy https_proxy ftp_proxy
　を追記する

*その後に
 source /etc/profile
 を実行

*vimで /etc/yum.conf に
 proxy=http://172.16.40.1:8888 を書く

### アップデート

プロキシの設定後、アップデートができるようになっているのでアップデートを行なってください。

*sudo yum update を実行
*sudo yum install wget をする

## 1-2 Wordpressを動かす(1)

Wordpressを動作させるためには下記のソフトウェアが必要になります。 [※1](#LAMP)

* Apache HTTP Server
	sudo yum -y install httpd
* MySQL
	sudo yum -y install http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
	sudo yum -y install mysql-community-sercer
* PHP
	sudo yum -y install php php-mysql

*Wordpressをダウンロード
 - wget https://ja.wordpress.org/wordpress-4.2.2-ja.tar.gz
 - tar -xzvf wordpress-4.2.2-ja.tar.gz

*MYSQLのユーザーなどの設定
 define('DB_NAME', 'db_wordpress');
 define('DB_USER', 's13002');
 define('DB_PASSWORD', 'kame');

*UbuntuからCentOS Wrodpressを開くために
 - sudo systemctl disable firewall.service 
 でファイアーウォール無効

*SELinuxを無効にする

*Wordpressのインストール
 - http://192.168.56.101/wordpress/wp-admin/install.php
 全部出来ていればインストールできるはず
 あとは必要なものを記入してログインできれば完了
