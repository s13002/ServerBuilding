# Section 6 AWS(Amazon Web Services)

このセクションではAWS(Amazon Web Services)を使用したサーバー構築を行ないます。

## 講義関連リンク

* [AWS公式サイト](http://aws.amazon.com/jp/)
* [Cloud Design Pattern](http://aws.clouddesignpattern.org/index.php/%E3%83%A1%E3%82%A4%E3%83%B3%E3%83%9A%E3%83%BC%E3%82%B8)

## 6-0 AWSコマンドラインインターフェイスのインストール

[公式サイト](http://aws.amazon.com/jp/cli/)参照。

## 6-1	AWS EC2 + Ansible


### AMI(Amazon Machine Image)を作る
AWSアカウントの作成し、先生から鍵のようなものをメールでもらう。
AWS-CLIのセットアップをする。
```console
$ aws configure

AWS Access Key ID: [Access Key]
AWS Secret Access Key: [Secret Access Key]
Default region name: ap-northeast-1
Default output format [None]: [Enter]
```


### Amazon EC2 インスタンス用キーペアを作成
Amazon EC2 のコンソールを開く
左側の「キーペア」を開く
「キーペアの作成」をクリックする

### Amazon EC2 インスタンスの起動
次のコマンドを実行してインスタンスを起動します
```
aws ec2 run-instances \
--image-id ami-cbf90ecb \
--key-name 'キーペア名' \
--instance-type t2.micro \
--region "ap-northeast-1" \
--count 1
```

### Amazon EC2 インスタンスにNameタグ付け
インスタンス起動コマンドを叩いた後にインスタンスID(InstanceId)を確認し、Nameタグをつけます
```
aws ec2 create-tags --resources 'インスタンスID' --tags Key=Name,Value='Nameタグ名'
```

### 作成したインスタンスにSSH接続
IPv4のゲートウェイを`172.16.40.10`に変更しておきます。
インスタンスのPublic IPを確認します
```
$ aws ec2 describe-instances --instance-ids 'インスタンスID' | grep PublicIp
```
キーのパーミッションを変更し、SSH接続をします
```
$ chmod 400 '.pemファイル'
$ ssh -i '.pemファイル' ec2-user@パブリックIPアドレス
```

### playbookを編集
playbook に内容を書き込む

### hostsファイルの作成
`vi hosts`を実行し、書き込む
```
[all]
インスタンスのパブリックIP

```

### wordpressのインストール
ansible-playbookコマンドを実行する
```
ansible-playbook -i hosts -u ec2 playbook.yml --private-key [.pemファイル]
```
ブラウザでサーバー`http://PublicIp`にアクセスして動作確認をします。

### AMI(Amazon Machine Image)を作る
環境の構築が終わったら、AMIを作成します。AMIを作成後、同じマシンを2つ起動して、コピーができていることを確認します。

イメージの作成をクリックし、必要項目を入力します。

 * イメージ名: [任意のイメージ名]
 * イメージの説明: [任意のイメージの説明]

イメージの作成をクリックします。
作成したAMIを使って、同じマシンを起動するため、次のコマンドを実行
```
aws ec2 run-instances \
--image-id '作成したAMIのイメージID' \
--key-name 'キーペア名' \
--instance-type t2.micro \
--region "ap-northeast-1" \
--count 1
```


## 6-2 AWS EC2(AMIMOTO)

6-1では自力(?)で環境構築を行ない、AMIを作成したが、別の人が作ったAMIを使用してサーバーを起動することもできる。

AMIMOTOのWordpressを起動してWordpressが見れることを確認する。

## 6-3 Route53

Route53はAWSが提供するDNSサービス。

5-1で作ったDNSの情報をRoute53に突っ込んでみよう。

## 6-4 S3

S3はSimple Storage Service。その名の通り、ファイルを保存し、(状況によっては)公開するサービス。

てきとーにWebサイトを作り、それをS3にアップロードし、公開してみよう。

S3にアップロードする際にはAWSコマンドラインインターフェイスを使ってね。

## 6-5 CloudFront

CloudFrontはCDNサービスです。CDNって何って?ggrましょう(ちゃんと講義では説明するので聞いてね)。

6-1で作ったAMIを起動し、CloudFrontに登録します。登録して直接アクセスするのとCloudFront経由するのどっちが速いかベンチマークを取ってみましょう。

また、CloudFrondを経由することで、地域ごとにアクセス可能にしたり不可にしたりできるので、それを試してみましょう。

## 6-6 RDS

RDSは…MySQLっぽい奴です。

RDSを立ち上げて、6-1で作ったAMIのWordpressのDBをRDSに向けてみよう。

## 6-7 ELB

ELBはロードバランサーです。すごいよ。

6-1で作ったAMIを3台ぶんくらい立ち上げてELBに登録し、負荷が割り振られているか確認してみよう。

## 6-8 API叩いてみよう

AWSは自分で作ったプログラムからもいろいろ制御できます!
なんでもいいのでがんばってプログラム書いてみてね(おすすめはSES)。
