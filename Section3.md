# Section 3 Ansibleによる自動化とテスト

## 3-0 Ansibleのインストール

コマンド実行
***
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt-get update
$ sudo apt-get install ansible
***

### vagrantの初期設定
作業用ディレクトリを作成して
Vagrantfileを開き以下を追記する。
```
vagrant init
```

```
config.vm.box = "CentOS7"
config.vm.network "private_network",

```

### プロキシの設定
Vagrantfileを開き以下を追記

***
if Vagrant.has_plugin?("vagrant-proxyconf")
config.proxy.http = ENV['http_proxy']
config.proxy.https = ENV['https_proxy']
config.proxy.no_proxy = ENV['no_proxy']
end

***
コマンド実行
***
vagrant plugin install vagrant-proxyconf
vagrant plugin install vagrant-vbguest
***

Vagrantfileを開き追記
```
config.vm.provision "ansible" do |ansible|
ansible.playbook = "playbook.yml"
end
```
```
vagrant provision
```

### playbookの作成
playbookを作成。
実行する。
```
ansible-playbook -i hosts -u vagrant -k playbook.yml
```

### Wordpress動作確認

