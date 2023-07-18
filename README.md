# env-for-java
## 概要
ローカルとは独立した環境、かつEclipseを使わずJavaを学習したいと思い、VSCodeでDockerコンテナの中で直接開発できる環境を作りました。このリポジトリは環境構築の備忘録であると同時に、これから同様な環境を作りたい人の助けになることを期待して作りました。

env-for-javaを使うことで以下のメリットがあると思われます。

- 軽量かつ拡張機能が豊富なエディタ、VSCodeを使うことで学習、開発の生産性が上がる。
- Dockerコンテナの独立した環境で作業をすることで、ローカルのPCを汚さない。
- Dockerで必要なソフトウェア・ツールなどをコードで定義するので管理が楽になる。

## 事前準備
### VSCodeのインストールと拡張機能の取得
[こちら](https://code.visualstudio.com/download)からVSCodeをダウンロード＆インストールしてください。インストールが完了したら以下の拡張機能を取得します。

- Japanese Language Pack for Visual Studio Code

VSCodeの日本語化に必要な拡張機能です。

- Docker

Dockerfileの編集などに役立つ拡張機能です。

- Dev Containers

VSCodeとコンテナを接続し、コンテナ内で直接ファイル編集やプログラム実行などを操作できるようにするための拡張機能です。仕組みと詳細については[VSCodeの公式サイト](https://code.visualstudio.com/docs/devcontainers/containers)を確認してください。

### Docker Desktopのインストール
[こちら](https://www.docker.com/products/docker-desktop/)からDocker Desktopをダウンロード＆インストールしてください。Windowsの場合、WSL2が事前にインストールされている必要があります。Docker Desktopは個人的に利用するのは無料ですが、商用利用する場合は有料プランの登録が必要な場合があるので注意してください。以下[Dockerの公式サイト](https://www.docker.com/blog/updating-product-subscriptions/)からの引用（翻訳）です。
> Docker Desktopは、中小企業（従業員数250人未満、年間売上1000万ドル未満）、個人利用、教育、非商用のオープンソースプロジェクト向けには無料のままです。
大企業でのプロフェッショナルな使用には、1ユーザーあたり月額5ドルからの有料サブスクリプション（Pro、Team、Business）が必要です。

## 資材ダウンロードとコンテナ起動
### 資材ダウンロード
GitHubからenv-for-javaをダウンロードします。「Code」のボタンから「Download ZIP」をクリックして、ダウンロード・展開してください。展開したフォルダを任意の場所に配置します。

展開フォルダ直下に「projects」というフォルダを作成します。構成は以下のようになります。
```
env-for-java
.
├── README.md
├── .devcontainer
│   └── devcontainer.json
├── .gitignore
├── ap_server
│   ├── Dockerfile
│   ├── settings.xml
│   └── tomcat-users.xml
├── db_server
│   ├── Dockerfile
│   └── my.cnf
├── docker-compose.yml
└── projects
```
Web3層アーキテクチャのうち、プレゼンテーション層とアプリケーション層をap_server（Tomcat）コンテナが、データベース層をdb_server（MySQL）コンテナが役割を担う構成としています。

ローカルの「projects」フォルダがコンテナの「/opt/projects」にバインドされます。「/opt/projects」配下がローカルの領域を一時的に借りているイメージです。コンテナが停止した後でも「/opt/projects」配下の変更はローカルの「projects」配下に引き継がれます。その逆も然りです。また、拡張機能とMySQLのデータ部のボリュームが作られているため、コンテナを停止または削除しても、再起動時にデータが引き継がれます。

コンテナのネットワークは192.168.0.0/24の範囲で定義しています。ネットワークの定義は「docker-compose.yml」を確認してください。コンテナ内に入るソフトウェア・ツールはDockerfileで定義されています。ソフトウェア・ツールの追加・削除・変更をしたい場合はDockerfileを編集してください。

### コンテナ起動
VSCodeのウィンドウの一番左下に「><」のようなボタンがあると思います。それを押下して、上方に表示される「コンテナーで再度開く」を選択してください。そうするとコンテナが起動します。初回はしばらく時間がかかります。

起動中に右下に表示されるポップアップの文字をクリックすると、起動ログを見ることができます。起動状況や拡張機能のインストール状況などが確認できます。

「devcontainer.json」で起動時に自動インストールされる拡張機能が定義されています。デフォルトでインストールされる拡張機能は以下の3つです。

- Extension Pack for Java

Javaアプリケーションを作成、テスト、デバッグするのに人気な拡張機能のコレクションです。

- Java Server Pages (JSP)

JSPファイルを見やすくするための拡張機能です。非推奨となっていますが、問題なく機能しています。

- MySQL (Weijan Chen)

MySQLをGUIで操作できる拡張機能です。MySQLという名前の拡張機能は複数ありますが、発行者がWeijan Chenという方のものを使ってください。

>**Warning**
>コンテナ起動後に拡張機能が自動インストールされない、インストールが完了しない、インストールされても拡張機能が使えない・有効化されない、といった事象が何度か確認されています。もしそうなってしまった場合は、コンテナから一旦接続を切り、再接続（再起動）をしてみてください。

>Dockerデスクトップアプリのウィンドウ右上の歯車アイコンからの「Resources」の設定で、メモリの割り当て上限を増やすことでこの問題が改善する場合があります。

## Mavenプロジェクト立ち上げとHello World
これ以降はあくまで一例として参照してください。
### Mavenプロジェクト立ち上げ＆各種設定
左サイドバー内で右クリックをし「Create Maven Project」をして「projects」内にMavenプロジェクトを作ります。「maven-arhetype-webapp」→「1.4」→「デフォルトのグループID（com.example）」→「デフォルトのアーティファクトID（demo）」を選択・入力して作成します。

pom.xmlの各種設定を変更します。pom.xmlはMavenプロジェクトの依存関係を定義しているファイルです。
デフォルトのイメージはJDK11を採用しているのでmaven.compiler.sourceとmaven.compiler.targetを11に変更します。
```
<properties>
  <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  <maven.compiler.source>11</maven.compiler.source>
  <maven.compiler.target>11</maven.compiler.target>
</properties>
```
dependenciesタグの中にサーブレット、JSPとJDBCドライバを利用するための記述を追加します。
```
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>javax.servlet-api</artifactId>
  <version>4.0.1</version>
</dependency>
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>jstl</artifactId>
  <version>1.2</version>
</dependency>
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>8.0.33</version>
</dependency>
```
pluginsタグの中にtomcat9のプラグインの記述を追加します。
```
<plugin>
  <groupId>org.opoo.maven</groupId>
  <artifactId>tomcat9-maven-plugin</artifactId>
  <version>3.0.1</version>
  <configuration>
    <server>tomcat</server>
    <path>/${project.artifactId}</path>
    <update>true</update>
  </configuration>
</plugin>
```
変更・追加が完了したら、pom.xmlを右クリックして「Reload Projects」をします。

### Hello Worldの確認
立ち上げたMavenプロジェクトにはデフォルトで「Hello World!」とだけ表示する「index.jsp」が作られているのでこれを確認します。

まず、ブラウザで[http://localhost:8080](http://localhost:8080)にアクセスします。ここでTomcatのウェルカムページが表示されると思います。

次にウィンドウ下方のパネルのターミナルで以下の操作をします。

```
/opt/projects# cd demo
/opt/projects/demo# mvn tomcat9:deploy
```
Mavenプロジェクトのルートディレクトリに移動し、Tomcatにデプロイしています。デプロイが成功したら、[http://localhost:8080/demo](http://localhost:8080/demo)にアクセスします。「Hello World!」と表示されていれば成功です。

## データベース接続
拡張機能を使ってMySQLを操作します。アクティビティバーのデータベースのアイコンをクリックします。+ボタンから接続するデータベースの情報を入力していきます。以下に必要な情報をまとめています。

|  要素  |  値  |
| ---- | ---- |
|  Server Type  |  MySQL  |
|  Host  |  192.168.0.3  |
|  Port  |  3306  |
|  Username  |  root  |
|  Password  |  12345  |
|  Database  |  testdb  |

入力が完了したら、「+ Connect」ボタンで接続してください。接続に成功したら、データベースをGUIで操作できます。
