# vagrant開発環境

### 環境構成
##### OS
- CentOS 7.7.1908

##### インストール済みパッケージ
- Development Tools
- git
- docker
- docker-compose
- apache2.4
- nginx
- mariadb
- php7.4
- nodejs/npm


# ディレクトリ構成
```
├─ .git                                       
├─ .gitignore                                 
├─ .vagrant                                   
├─ ps_run.bat                                 パワーシェルを立ち上げるための.batファイルです。管理者権限で実行してください。
├─ boxes                                      ボックスファイルを格納するためのディレクトリです。
│   ├─ .gitkeep                              
│   └─ {{CentOS7_x8664_devenv.box}}          git管理ではありません。指定のダウンロード箇所からダウンロードし配置してください。
├─ share                                      ホストマシンとゲストマシンの共有ディレクトリです。
│   └─ .gitkeep                              
├─ ssh                                        ゲストマシンへSSH接続するための鍵やSSHクライアントマクロを格納するためのディレクトリです。
│   ├─insecure_private_key                   TeraTerm用の鍵
│   ├─private_key.ppk                        PuTTY形式の鍵
│   └─login.ttl                              ログイン用のTeraTermマクロ
├─ guest                                      ゲストマシンに配布するファイル等を格納するディレクトリ
│   ├─ config                                
│   │   ├─ etc                              OSにインストールしたパッケージの設定ファイルを格納するためのディレクトリです。
│   │   │   └─ .gitkeep                    
│   │   └─ home                             
│   │        └─ vagrant                     ユーザ個人の設定ファイルを格納するためのディレクトリです。dotfile等
│   │             └─ .gitkeep               
│   ├─ tmp                                   一時ファイルを格納するためのディレクトリです。
│   │   ├─ .gitkeep                         
│   │   └─ user_env                         user_envで作成した一時ファイルを格納するためのディレクトリです。
│   │        ├─ .gitkeep                    
│   │        ├─ {{user_env_setup.sh}}       user_envで作成したセットアップシェルです。
│   │        └─ tmp                         
│   │             └─ .gitkeep               
│   └─ bin                                   ゲストマシンに配布するbinファイルを格納するためのディレクトリです。
│        └─ .gitkeep                         
├─ user_env.default                           ユーザ環境の設定ファイルのデフォルトです。
├─ {{user_env}}                               git管理ではありません。user_env.defaultをコピーして作成してください。
├─ vagrant_config.default                     VMのの設定ファイルのデフォルトです。
├─ {{vagrant_config}}                         git管理ではありません。vagrant_config.defaultをコピーして作成してください。
├─ Vagrantfile                                vagrantファイルです。
└─ README.md                                  本ドキュメントです。
```


# 環境構築手順
ホスト環境はWindows10 PowerShellで検証しています。
*PowerShellは管理者権限で実行してください。*
___

1. PowerShellをインストールする  

    [最新PowerShellインストール手順](https://www.virment.com/how-to-install-latest-powershell-in-windows10/)  

1. gitクライアントをインストールする  

    [Git For Windowsのインストール手順](https://qiita.com/toshi-click/items/dcf3dd48fdc74c91b409)  
    WindowsGUI環境でのGit操作を行いたい場合は、下記を参考にインストールしてください。  
    [TortoiseGitのインストール方法（日本語化まで）＠Windows](https://qiita.com/Shi-nakaya/items/43c858ea707770c03b17)  

1. VirtualBoxをインストールする  

    対応しているvagrantに合わせるため**必ずVer6.0をインストールすること**  
    [VirtualBoxダウンロードページ](https://www.virtualbox.org/wiki/Download_Old_Builds_6_0)  

1. vagrantをインストールする  

    対応しているVirtualBoxに合わせるために**必ずVer2.2.6をインストールすること**  
    [vagrantダウンロードページ](https://www.vagrantup.com/downloads.html)  

1. githubからソースをクローンする  

          # 環境を構築する場所はどこに作成しても良いです。
          # 本ドキュメントでは、C:\Users\User\workを作業ディレクトリとして作成しています。
          cd C:\Users\User
          mkdir work
          cd work

          # githubからソースをクローンする
          git clone https://github.com/yukichi0707/vm.template.git


1. vagrant-pluginをインストールする

          # 自身で作成した作業ディレクトリに移動してください。
          cd C:\Users\User\work\vm.template

          # vagrant-vbguestは
          # ホストマシンのVitualBoxのバージョンと
          # ゲストマシンにインストールされているGuest Additionsのバージョンが異なる場合に、
          # 自動でインストールしてくれるプラグインです。
          vagrant plugin install vagrant-vbguest

          # vagrant-proxyconfは
          # ゲストマシンのプロクシの設定を自動で行ってくれるプラグインです。
          vagrant plugin install vagrant-proxyconf

          # vagrant-triggersは
          # vagrantコマンドをトリガーとして指定した処理を実行するためのプラグインです。
          vagrant plugin install vagrant-triggers

1. ボックスイメージをダウンロードし配置する  

    [ボックスイメージ](https://drive.google.com/open?id=1ubn7_ZjV167glSgsGAXnD7OTjR73gODr)をダウンロードし  
    `C:\Users\User\work\vm.template\boxes`ディレクトリ配下に配置してください。

1. 設定ファイルを作成する

        # 自身で作成した作業ディレクトリに移動してください。
        cd C:\Users\User\work\vm.template

        # proxyの設定などユーザ情報を設定するファイル。
        # user_envについては別項記載
        cp user_env.default user_env

        # VMの設定などプロジェクト情報を設定するファイル。
        # vagrant_configについては別項記載
        cp vagrant_config.default vagrant_config

1. vagrantを立ち上げる

        # 自身で作成した作業ディレクトリに移動してください。
        cd C:\Users\User\work\vm.template

        # 仮想マシンを立ち上げる
        vagrant up

1. 仮想マシンに接続できれば環境構築成功

        # 自身で作成した作業ディレクトリに移動してください。
        cd C:\Users\User\work\vm.template

        # 仮想マシンにSSHで接続する
        vagrant ssh

1. 仮想マシン環境のユーザ設定user_env_setup.shを実行する

        # user_env_setup.shを実行する
        # user_env_setup.shはuser_envで出力を行った場合に存在する。
        # また、個人環境構築にこの処理が不要なら実行する必要はない。
        ./user_env_setup.sh
        # 環境変数等必要ならば更新
        . .bash_profile


# vagrant_configの設定一覧

変数$vagrant_config以外の処理については自由に書き換えて良いです。
___

|フィールド名  |型  |デフォルト値  |説明  |
|:---|:---|:---|:---|
|[:vm]  |array  |  |仮想マシンの設定を定義するための配列。  |
|[:vm][\*]  |hash  |  |※0番以外有効ではないことに注意。  |
|[:vm][\*][:provider]  |hash  |  |  |
|[:vm][\*][:provider][:name]  |string  |  |  |
|[:vm][\*][:provider][:gui]  |bool  |  |  |
|[:vm][\*][:provider][:default_nic_type]  |string  |  |  |
|[:vm][\*][:provider][:linked_clone]  |bool  |  |
|[:vm][\*][:provider][:custmize]  |array  |  |  |
|[:vm][\*][:provider][:memory]  |int  |  |  |
|[:vm][\*][:provider][:cpus]  |int  |  |  |
|[:vm][\*][:usable_port_range]  |string  |  |  |
|[:vm][\*][:network]  |array  |  |  |
|[:vm][\*][:network][\*]  |hash  |  |  |
|[:vm][\*][:network][\*][:forwarded_port]  |bool  |  |  |
|[:vm][\*][:network][\*][:private_network]  |bool  |  |  |
|[:vm][\*][:network][\*][:public_network]  |bool  |  |  |
|[:vm][\*][:network][\*][:auto_correct]  |string  |  |  |
|[:vm][\*][:network][\*][:guest]  |string  |  |  |
|[:vm][\*][:network][\*][:guest_ip]  |string  |  |  |
|[:vm][\*][:network][\*][:host]  |string  |  |  |
|[:vm][\*][:network][\*][:host_ip]  |string  |  |  |
|[:vm][\*][:network][\*][:protocol]  |string  |  |  |
|[:vm][\*][:network][\*][:id]  |string  |  |  |
|[:vm][\*][:network][\*][:ip]  |string  |  |  |
|[:vm][\*][:network][\*][:netmask]  |string  |  |  |
|[:vm][\*][:network][\*][:auto_config]  |string  |  |  |
|[:vm][\*][:network][\*][:type]  |string  |  |  |
|[:vm][\*][:network][\*][:bridge]  |string  |  |  |
|[:vm][\*][:network][\*][:use_dhcp_assigned_default_route]  |string  |  |  |
|[:vm][\*][:network][\*][:host_fp]  |array  |  |  |
|[:vm][\*][:network][\*][:host_fp][*]  |hash  |  |  |
|[:vm][\*][:network][\*][:host_fp][*][:ip]  |string  |  |  |
|[:vm][\*][:network][\*][:host_fp][*][:ports]  |array  |  |  |
|[:vm][\*][:network][\*][:host_fp][*][:ports][*]  |hash  |  |  |
|[:vm][\*][:network][\*][:host_fp][*][:ports][*][:host]  |string  |  |  |
|[:vm][\*][:network][\*][:host_fp][*][:ports][*][:guest]  |string  |  |  |
|[:vm][\*][:provision]  |array  |  |  |
|[:vm][\*][:provision][\*]  |hash  |  |  |
|[:vm][\*][:provision][\*][:type]  |string  |  |'shell'固定  |
|[:vm][\*][:provision][\*][:privileged]  |bool  |true  |root権限で実行するか？  |
|[:vm][\*][:provision][\*][:inline]  |string  |  |仮想マシン起動時のプロビジョニングを記述できます。  |
___

### 参考URL
[vagrantドキュメント](https://www.vagrantup.com/docs/)


# user_envの設定一覧

変数$user_env以外の処理については自由に書き換えて良いです。
___

|フィールド名  |型  |デフォルト値  |説明  |
|:---|:---|:---|:---|
|[:proxy]  |hash  |{}  |プロクシ設定を定義するためのハッシュ  |
|[:proxy][:use]  |bool  |  |プロクシを使用するかどうかのフラグ。trueの場合、proxyを設定します。  |
|[:proxy][:host]  |string  |  |プロクシのホストを指定してください。 例: '127.0.0.1'  |
|[:proxy][:port]  |string  |  |プロクシのポートを指定してください。 例: '8080'  |
|[:proxy][:user]  |string  |  |プロクシのユーザを指定してください。 例: 'user'  |
|[:proxy][:pass]  |string  |  |プロクシのパスワードを指定してください。 例: 'password'  |
|[:vagraint_config]  |hash  |  |vagrantの設定を定義するためのハッシュ  |
|[:vagraint_config][:vm]  |array  |  |仮想マシンの設定を定義するための配列。  |
|[:vagraint_config][:vm][\*]  |hash  |  |※0番以外有効ではないことに注意。  |
|[:vagraint_config][:vm][\*][:provision]  |array  |  |  |
|[:vagraint_config][:vm][\*][:provision][\*]  |hash  |  |  |
|[:vagraint_config][:vm][\*][:provision][\*][:type]  |string  |  |'shell'固定  |
|[:vagraint_config][:vm][\*][:provision][\*][:privileged]  |bool  |true  |root権限で実行するか？  |
|[:vagraint_config][:vm][\*][:provision][\*][:inline]  |string  |  |仮想マシン起動時のプロビジョニングを記述できます。  |


# sshクライアントで接続

### PuTTY

1. PuTTYをインストールする

    [Windowsでsshクライアント「PuTTY」を使う](https://www.atmarkit.co.jp/ait/articles/1006/25/news095.html)

2. PuTTYでSSH接続する

    `C:\Users\User\work\vm.template\ssh\private_key.ppk`がPuTTY用の秘密鍵になります。  
    [PuTTYで公開鍵認証方式でのSSH接続を行う手順まとめ](https://qiita.com/sugar_15678/items/55cb79d427b9ec21bac2)

### TeraTerm

1. TeraTermをインストールする

    [【ゼロからわかる】TeraTermのインストールと使い方](https://eng-entrance.com/teraterm-install)  
    ※追加タスクの選択時に「.ttlファイルをttpmacro.exeに関連付ける」をチェックすること

1. TeraTermでSSH接続する

    `C:\Users\User\work\vm.template\ssh\login.ttl`をダブルクリックしてログインする。


# tips

### Vagrant コマンド一覧
```
# 仮想マシンの起動
vagrant up

# 仮想マシンの終了(シャットダウン)
vagrant halt

# 仮想マシンの削除
vagrant destroy

# 仮想マシンの一時停止
vagrant suspend

# 仮想マシンの一時停止から復帰
vagrant suspend

# 仮想マシンにログイン
vagrant ssh
```

##### 参考URL

[【まとめ】Vagrant コマンド一覧](https://qiita.com/oreo3@github/items/4054a4120ccc249676d9)
___

### Vagrant ボックス作成

Vagrant ボックスを作成するためには、VirtualBoxで仮想マシンを作成する必要があります。
このプロジェクトで使っている仮想マシンイメージは
[CentOS7_x8664_devenv.boxダウンロード](https://drive.google.com/open?id=1ReF0Ytj1y2GpCFO-pecNowf1-j9g5FO-)
から作成しました。

##### 参考URL
[Vagrant　Box作成手順](https://qiita.com/edward999th/items/0b1809745f2a0a31b7b2)
