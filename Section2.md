# Section 2 その他のWebサーバー環境

 2-1 Vagrantを使用したCentOS 7環境の起動

 VagrantでCentOS 7の登録

 * `vagrant box add CentOS7 取得したファイルのパス --force`

 Vagrantの設定
 作業用ディレクトリを作成
 * `mkdir -p ~/ServerBuilding/CentOS7`
 設定を行います。
 * `vagrant init`
 作成された”Vagrantfile”をvimで開き、CentOS6.5が起動するようにします。
```
　config.vm.box = "base"
```

```
　config.vm.box = "CentOS7"
```

### 仮想マシンの起動
　`vagrant up`

### 仮想マシンの停止
　`vagrant halt`

### 仮想マシンの一時停止
　`vagrant suspend`

### 仮想マシンの破棄
　`vagrant destroy`

### 仮想マシンへ接続
　仮想マシンへsshで接続します。

　`vagrant ssh`

### ホストオンリーアダプターの設定
 NICを追加します。
　
 * config.vm.box = "CentOS7"
 * config.vm.network "private_network", ip:"68.56.130"

を加える

# 2-2 Wordpressを動かす(2)
Nginx + PHP + MariaDBで動作させる

### wgetのインストール
 * `sudo yum install wget`

### Nginxのインストール
 http://nginx.org/en/linux_packages.html#stableからrpmをダウンロードします。
 * `wget http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm`
 リポジトリを追加
 * `sudo yum -y install nginx-release-centos-7-0.el7.ngx.noarch.rpm`
 インストール
 * `sudo yum install nginx`
 起動します
 * `sudo systemctl enable nginx.service`
 * `sudo systemctl start nginx.service`

# php-fpmをインストール
 * `sudo yum install php-fpm php-mbstring php-mysql`
 nginxの設定でphp-fpmが動くようにする

sudo vi /etc/nginx/conf.d/default.conf 

 付け加える
```
server {
listen       80;
server_name  localhost;

#charset koi8-r;
#access_log  /var/log/nginx/log/host.access.log  main;

location / {
root   /usr/share/nginx/wordpress;
index  index.html index.htm index.php;
}

#error_page  404              /404.html;

# redirect server error pages to the static page /50x.html
#
error_page   500 502 503 504  /50x.html;
location = /50x.html {
root   /usr/share/nginx/html;
}

# proxy the PHP scripts to Apache listening on 127.0.0.1:80
#
#location ~ \.php$ {
#    proxy_pass   http://127.0.0.1;
#}

# pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
#
location ~ \.php$ {
root           /usr/share/nginx/wordpress;
fastcgi_pass   127.0.0.1:9000;
fastcgi_index  index.php;
fastcgi_param  SCRIPT_FILENAME  /usr/share/nginx/wordpress$fastcgi_script_name;
include        fastcgi_params;
}

# deny access to .htaccess files, if Apache's document root
# concurs with nginx's one
#
#location ~ /\.ht {
#    deny  all;
#}
}

sudo vi /etc/php-fpm.d/www.conf
```
書き換える
user = nginx
group = nginx

## MariaDBをインストール
 インストールする
 * `sudo yum install mariadb mariadb-server`


 mariadbを有効にする
 * `sudo systemctl enable mariadb.service`
 * `sudo systemctl start mariadb.service`
 MariaDBの初期設定を対話形式で行います。
 * `mysql_secure_installation`   

## ユーザ・データベース作成
 データーベースにログインします。
 * `mysql -u root -p`
 * Enter password: 
 データーベースを作成
 * `create database db_wordpress;`
 ユーザの権限を編集
 * `grant all on データベース名.* to ユーザー名@localhost identified by 'パスワード';`

## Wordpressをインストール
 wgetでWordpressをダウンロード
 * `wget https://ja.wordpress.org/latest-ja.zip`
 unzipをインストール
 * `sudo yum install unzip`
 ダウンロードしたWordpressのデータを解凍する
 * `unzip latest-ja.zip`
 
 ブラウザからアクセスし、wp-config.phpファイルの設定する

 インストールの実行をクリックする

 必要情報を入れて終了

## 2-3 Wordpressを動かす(3)
Apache HTTP Server 2.2とPHP5.5の環境を構築し、Wordpressを動かす
### MySQLのインストール
今回はデーターベースにMySQLを使用します。

 MySQLのリポジトリを追加する
 * `sudo yum -y install http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm`
 MySQL Community Serverをインストールする
 * `sudo yum -y install mysql-community-server`
 確認
 * `mysqld --version`
 MySQLを有効にして起動します
 * sudo systemctl enable mysqld.service
 * sudo systemctl start mysqld.service


## データーベースとユーザーを作成
 データベースにログインする
 * `mysqld -u root -p`   
 データベースを作成する
 ユーザー権限の設定をする

## Apache HTTP Server 2.2のインストール
 Apache HTTP Server 2.2をダウンロードする
 * `wget http://ftp.jaist.ac.jp/pub/apache//httpd/httpd-2.2.29.tar.gz`
 ダウンロードしたファイルを解凍する
 Makefileを作成する
 * `./configure`
 ビルドするう
 * `make`
 インストールする
 * `make install`
 内容を編集する
sudo vi /usr/local/apache2/conf/httpd.conf

ServerName localhost:80
DirectoryIndex index.html index.php

 バージョンを確認
 * `/usr/local/apache2/bin/apachectl -v
 サーバーを起動
 * `sudo /usr/local/apache2/bin/apachectl -k start`
 サーバーを停止
 * `sudo /usr/local/apache2/bin/apachectl -k stop`

## libxml2ソースファイルのインストール
PHP5.5インストール時に必要なlibxml2をインストールする

 Python関連のパッケージをインストール
 * `sudo yum -y install python-devel`
 libxml2ソースファイルをダウンロード
 * `wget http://xmlsoft.org/sources/libxml2-2.9.2.tar.gz`
 ファイルを解凍して移動します

tar xzf libxml2-2.9.2.tar.gz
cd  libxml2-2.9.2

 Makefileを作成し、ビルドとインストール

./configure
make
sudo make install



## PHP5.5のインストール
 PHPのソースファイルをダウロード
 * `wget http://jp2.php.net/get/php-5.5.25.tar.gz/from/this/mirror`
 ダウンロードしたファイルを解凍する
 * `tar xzf mirror`
 解凍後ディレクトリを移動する
 * `cd php-5.5.25`
 コンパイルオプションを指定して
 ビルドしてインストールします

make
sudo make install

## Wordpressのインストール
 Wordpress最新版をダウンロードする
 * `wget https://ja.wordpress.org/latest-ja.zip`
 wgetをインストールする
 * `sudo yum -y install latest-ja.zip`
 解凍したディレクトリを公開ディレクトリに移動させる
 * `sudo mv wordpress/ /usr/local/apache2/htdocs/`
 ブラウザでアクセスして必要情報を入力していきます
 wp-config.php ファイルを手動で作成し、書きうつす。

　インストール実行


