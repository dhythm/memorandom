# 開発環境構築手順

会社のゆるふわ勉強会で使う環境構築メモです。

認証プロキシがやっかいです。セキュリティ的には必要なんでしょうけど、生産性は犠牲になりますね。  

## 共通

### パッケージマネージャー chocolatey のインストール

まず最初にコマンドプロンプトを管理者モードで起動します。(Ctrl + Shift + Enter)  

[参考ページ](https://qiita.com/keita69sawada/items/5b7af117a313aae02399)  

上記に従い、[install.ps1](https://chocolatey.org/install.ps1)をダウンロードして、ファイルを書き換えます。  
```text
    $explicitProxy = "<proxy-domain>:<port>"
    $explicitproxyUser = "<user>"
    $explicitproxyPassword = "<password>"
```
インストールを実行します。  

```sh
$ choco
```  
を実行して、バージョン情報が表示されればインストール成功です。  

プロキシ環境下で利用するための設定もしておきます。  
```sh
$ choco config set proxy "<proxy-domain>:<port>"
$ choco config set proxyUser "<user>"
$ choco config set proxyPassword "<password>"
```

### Git のインストール

```sh
$ choco install git -Y
```

プロキシ設定をします。  
```sh
$ git config --global http.proxy http://<user>:<password>@<proxy-domain>:<port>
$ git config --global http.proxy https://<user>:<password>@<proxy-domain>:<port>
```
※パスワードに"@"を利用している場合はサニタイズが必要。  

## フロントエンド

### Node.js のインストール

```sh
$ choco install nodejs -Y
```

Node.js をインストールすると`npm`も利用できるようになります。  
ついでに`yarn`もインストールします。  

プロキシ設定を入れます。  

```sh
$ npm config set proxy http://<user>:<password>@<proxy-domain>:<port>
$ npm config set https-proxy http://<user>:<password>@<proxy-domain>:<port>
```

最近人気の`yarn`もインストールしておきましょう。  
```sh
$ npm install -g yarn
```

Web 開発に必要なツールを導入します。  
```sh
$ npm -g install pm2 create-react-app
```

準備が完了したら作業用フォルダを作成して、開発を始めていきます。  
```sh
$ mkdir Sandbox
$ cd Sandbox
$ git init
```

### React 導入

React 開発のツールを導入します。  
今回はFacebookから提供されている`create-react-app`を利用します。  
  ※あくまで導入向け…。  
```sh
$ create-react-app <app-name>
$ cd <app-name>
```

サーバを起動して、[http://localhost:3000](http://localhost:3000) をアプリの確認をします。  
```sh
$ npm start
```

継続して開発する場合、サーバをデーモン化しておくと便利です。
その場合、`pm2`を使います。  
```sh
$ pm2 start node_modules\react-scripts\scripts\start.js --watch
```

### フロントエンド開発

加筆予定...  

## バックエンド

### Python のインストール

Python は 2 と 3 で記法が変わります。  
今回は 3 を使います。  
```sh
$ choco install python -Y
```

python のパッケージ管理ツール pip をインストールします。  
```sh
$ choco install pip
```

環境変数にプロキシ設定をしておきます。  
```sh
$ set HTTP_PROXY=http://<user>:<password>@<proxy-domain>:<port>
$ set HTTP_PROXYS=http://<user>:<password>@<proxy-domain>:<port>
```

### サンプルスクレイピング

サーバ上で動かす(予定の) Python スクリプトを作成していきます。  

```sh
$ mkdir python
$ cd python
```

スクレイピング用のツールを導入します。  
```sh
$ pip install requests lxml beautifulsoup4
```
selenium を認証プロキシ上で動かす場合は chrome-extension の background を利用します。  
headless-mode は現状 extension に未対応なのであわせ技は出来ませんでした。。無念。  

