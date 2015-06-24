# Section 5 DNSサーバーを動作させる

## 5-1 bindのインストール

最初に環境を作ったらVagrantfileがある場所で
`vagrant up`
を実行

## 5-2 bindの設定

### named.confの編集
 master-named.confとslave-named.confを編集します。

### ゾーンファイルの作成
 ゾーンファイルを参照してゾーンファイルを作成し、レコードを返すように設定します。

### playbookの編集
playbook.yamlを編集します。

### 設定の反映
変更した設定を反映させるために次のコマンドを実行します
```
vagrant reload
vagrant provision

```

### 動作確認
digコマンドして動作確認をします
```
#マスターサーバー
$ dig @マスターサーバーのIPアドレス ns s13002.com
```

```
#スレーブサーバー
$ dig @スレーブサーバーのIPアドレス ns s13002.com
```

