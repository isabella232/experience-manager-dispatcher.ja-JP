---
title: Dispatcher のインストール
seo-title: AEMディスパッチャーのインストール
description: ディスパッチャーモジュールをMicrosoft Internet Information Server、Apache Web Server、Sun Java Web Server- iPlanetにインストールする方法について説明します。
seo-description: AEMディスパッチャーモジュールをMicrosoft Internet Information Server、Apache Web Server、Sun Java Web Server- iPlanetにインストールする方法について説明します。
uuid: 2384b907-1042-4707- b02f- fba2125618cf
contentOwner: ユーザーは、
converted: 'true'
topic-tags: dispatcher
content-type: リファレンス
discoiquuid: f00ad751-6b95-4365-8500- e1e0108d9536
translation-type: tm+mt
source-git-commit: ec5342f5737f54937493edb0384cdc1d91b13d7c

---


# Dispatcher のインストール {#installing-dispatcher}

<!-- 

Comment Type: draft

<h2>Introduction</h2>

 -->

>[!NOTE]
>
>Dispatcher のバージョンは AEM とは無関係です。以前のバージョンの AEM のドキュメントに組み込まれている Dispatcher のドキュメントへのリンクをたどると、このページにリダイレクトされる可能性があります。

[ディスパッチャーリリースノート](release-notes.md) ページを使用して、オペレーティングシステムおよびWebサーバー用の最新のディスパッチャーインストールファイルを取得します。Dispatcher のリリース番号は Adobe Experience Manager のリリース番号とは無関係で、Adobe Experience Manager 6.x、5.x および Adobe CQ 5.x リリースと互換性があります。

次のファイル命名規則が使用されます。

`dispatcher-<web-server>-<operating-system>-<dispatcher-version-number>.<file-format>`

例えば、ファイルに `dispatcher-apache2.4-linux-x86_64-ssl-4.3.1.tar.gz` は、Linux i686で実行されるApache2.4Webサーバー用のディスパッチャーバージョン4.3.1と **tar** 形式を使用してパッケージ化されたものがあります。

各 Web サーバーのファイル名に使用される Web サーバー識別子を以下の表に示します。

| Web サーバー | インストールキット |
|--- |--- |
| Apache 2.4 | dispatcher- apache **2.4**-&lt; other parameters&gt; |
| Apache 2.2 | dispatcher- apache **2.2**-&lt; other parameters&gt; |
| Microsoft Internet Information Server7.5，8，8.5 | dispatcher-**iis**-&lt;other parameters&gt; |
| Sun Java Web Server iPlanet | dispatcher-**ns**-&lt;other parameters&gt; |

>[!NOTE]
>
>使用するプラットフォームで利用可能な最新バージョンの Dispatcher をインストールしてください。強化された機能を利用できるよう、毎年 Dispatcher インスタンスをアップグレードして、最新バージョンを使用してください。

アーカイブごとに次のファイルが含まれています。

* Dispatcher モジュール
* サンプルの設定ファイル
* インストール手順と最新の情報を含む README ファイル
* 現在および過去のリリースで修正された問題を一覧表示する変更ファイル

>[!NOTE]
>
>インストールを開始する前に、最新の変更やプラットフォーム固有の注意事項がないか README ファイルを確認してください。

<!-- 

Comment Type: draft

<h3>Supported Web Servers</h3>

 -->

<!-- 

Comment Type: draft

<p>The following web servers are supported for use with Dispatcher version 4.1.12:</p>

 -->

<!-- 

Comment Type: draft

<p>The following sections detail the specific web server installation procedures.</p>

 -->

## Microsoft Internet Information Server {#microsoft-internet-information-server}

この Web サーバーのインストール方法については、次のリソースを参照してください。

* インターネットインフォメーションサービスに関する Microsoft 独自のドキュメント
* [Microsoft IIS 公式サイト](https://www.iis.net/)

### 必要な IIS コンポーネント {#required-iis-components}

IISバージョン8.5および10では、次のIISコンポーネントをインストールする必要があります。

* ISAPI 拡張機能

Web Server（IIS）役割も追加する必要があります。役割とコンポーネントを追加するには、Server Manager を使用します。

## Microsoft IIS - Dispatcher モジュールのインストール {#microsoft-iis-installing-the-dispatcher-module}

Microsoft インターネットインフォメーションサービスには次のようなアーカイブファイルが必要です。

* `dispatcher-iis-<operating-system>-<dispatcher-release-number>.zip`

ZIP ファイルには以下のファイルが含まれます。

| ファイル | 説明 |
|--- |--- |
| `disp_iis.dll` | Dispatcher のダイナミックリンクライブラリファイル。 |
| `disp_iis.ini` | IIS 用の設定ファイル。このサンプルを要件に合わせて更新できます。**注意**:iniファイルは、dllと同じ名前ルートを持つ必要があります。 |
| `dispatcher.any` | Dispatcher 用のサンプルの設定ファイル。 |
| `author_dispatcher.any` | オーサーインスタンスで動作する Dispatcher 用のサンプルの設定ファイル。 |
| README | インストール手順と最新の情報を含む Readme ファイル。**注意**：インストールを開始する前に、このファイルを確認してください。 |
| CHANGES | 現在および過去のリリースで修正された問題を記載した Changes ファイル。 |

以下の手順を実行して、Dispatcher ファイルを適切な場所にコピーします。

1. 例えば、Windowsエクスプローラーを使用して `<IIS_INSTALLDIR>/Scripts` ディレクトリを作成 `C:\inetpub\Scripts`します。

1. ディスパッチャーパッケージから次のファイルをこのスクリプトディレクトリに抽出します。

   * `disp_iis.dll`
   * `disp_iis.ini`
   * Dispatcher が AEM オーサーインスタンスで動作しているか、パブリッシュインスタンスで動作しているかによって、以下のファイルのどちらか
      * オーサーインスタンス: `author_dispatcher.any`
      * パブリッシュインスタンス: `dispatcher.any`

## Microsoft IIS - Dispatcher の INI ファイルの設定 {#microsoft-iis-configure-the-dispatcher-ini-file}

ファイルを `disp_iis.ini` 編集してディスパッチャーのインストールを設定します。`.ini` ファイルの基本形式は次のとおりです。

```xml
[main]
configpath=<path to dispatcher.any>
loglevel=1|2|3
servervariables=0|1
replaceauthorization=0|1
```

各プロパティについて、以下の表に説明します。

| パラメーター | 説明 |
|--- |--- |
| configpath | ローカルファイルシステム内の `dispatcher.any` の場所（絶対パス）。 |
| logfile | `dispatcher.log` ファイルの場所。これが設定されていない場合、ログメッセージはWindowsイベントログに送信されます。 |
| loglevel | イベントログへのメッセージ出力に使用するログレベルを定義します。次の値を指定できます。ログファイルのログレベル: <br/>0-エラーメッセージのみ。<br/>1 - エラーと警告が表示されます。<br/>2 - エラー、警告および情報メッセージ <br/>3-エラー、警告、情報およびデバッグメッセージ。<br/>**注意**:インストールおよびテスト中にログレベルを3に設定し、実稼働環境で実行する場合は0に設定することをお勧めします。 |
| replaceauthorization | HTTP 要求内の承認ヘッダーの処理方法を指定します。有効な値は次のとおりです。<br/>0 - 認証ヘッダーは変更されません。<br/>1 - &quot;Basic&quot;以外の&quot;Authorization&quot;という名前のヘッダーを、同等の `Basic <IIS:LOGON\_USER>` ものに置き換えます。<br/> |
| servervariables | サーバー変数の処理方法を定義します。<br/>0 - IISサーバー変数は、ディスパッチャーおよびAEMのいずれにも送信されません。<br/>1 - すべてのIISサーバー変数（ `LOGON\_USER, QUERY\_STRING, ...`など）は、ディスパッチャーに送信され、リクエストヘッダー（キャッシュされていない場合はAEMインスタンス）と共に一緒に送信されます。<br/>サーバー変数には、その `AUTH\_USER, LOGON\_USER, HTTPS\_KEYSIZE` 他多数があります。すべてのサーバー変数とその詳細の一覧は、IIS のドキュメントを参照してください。 |
| enable_ chunked_ transfer | クライアント応答の一括転送を有効（1）または無効（0）にするかどうかを定義します。デフォルト値は 0 です。 |

設定例：

```xml
[main]
configpath=C:\Inetpub\Scripts\dispatcher.any
loglevel=1
servervariables=1
replaceauthorization=0
```

### Microsoft IIS の設定 {#configuring-microsoft-iis}

Dispatcher ISAPI モジュールを統合するように IIS を設定します。IIS では、ワイルドカードアプリケーションマッピングを使用します。

### 匿名アクセスの設定- IIS8.5および10 {#configuring-anonymous-access-iis-and}

オーサーインスタンス上のデフォルトのフラッシュレプリケーションエージェントは、フラッシュ要求と一緒にセキュリティ資格情報を送信しないように設定されています。したがって、Dispatcher キャッシュとして使用する Web サイトでは、匿名アクセスを許可する必要があります。

Web サイトが認証メソッドを使用する場合は、それに応じてフラッシュレプリケーションエージェントを設定する必要があります。

1. IIS Manager を開き、Dispatcher キャッシュとして使用する Web サイトを選択します。
1. 機能ビューモードを使用して、「IIS」セクションで「認証」をダブルクリックします。
1. 匿名認証が有効でない場合は「匿名認証」を選択し、「操作」領域で「有効」をクリックします。

### ディスパッチャーISAPIモジュールの統合- IIS8.5および10 {#integrating-the-dispatcher-isapi-module-iis-and}

以下の手順を実行して、Dispatcher ISAPI モジュールを IIS に追加します。

1. IIS Manager を開きます。
1. Dispatcher キャッシュとして使用する Web サイトを選択します。
1. 機能ビューモードを使用して、「IIS」セクションで「ハンドラーマッピング」をダブルクリックします。
1. ハンドラーマッピングページの操作パネルで、「ワイルドカードスクリプトマップの追加」をクリックし、以下のプロパティ値を追加して、「OK」をクリックします。

   * 要求パス：*
   * 実行可能ファイル:例えば、disp_ iis. dllファイルの絶対パス `C:\inetpub\Scripts\disp_iis.dll`です。
   * 名前:ハンドラーマッピングのわかりやすい名前など `Dispatcher`です。

1. 表示されるダイアログボックスで、disp_iis.dll ライブラリを ISAPI および CGI の制限リストに追加して、「はい」をクリックします。

   IIS 7.0 および 7.5 の場合、設定は完了です。IIS 8.0 を設定する場合は、残りの手順を続行します。

1. （IIS 8.0）ハンドラーマッピングのリストで、作成したハンドラーマッピングを選択し、「操作」領域で「編集」をクリックします。
1. （IIS8.0）スクリプトマップを編集ダイアログボックスで、「要求の制限」ボタンをクリックします。
1. （IIS8.0）まだキャッシュされていないファイルおよびフォルダーにハンドラーを確実に使用するには、&quot;Request Is Mapped To&quot;の選択を解除し、&quot;OK&quot;をクリックします。
1. （IIS 8.0）スクリプトマップの編集ダイアログボックスで、「OK」をクリックします。

### キャッシュへのアクセスの設定- IIS8.5および10 {#configuring-access-to-the-cache-iis-and}

ディスパッチャーのキャッシュとして使用されているフォルダーへの書き込みアクセス権を持つデフォルトのApp Poolユーザーを指定します。

1. ディスパッチャーのキャッシュとして使用しているWebサイトのルートフォルダを右クリックし、「プロパティ」をクリック `C:\inetpub\wwwroot`します。
1. 「セキュリティ」タブで「編集」をクリックし、アクセス許可ダイアログボックスで「追加」をクリックします。ユーザーアカウントを選択するためのダイアログボックスが表示されます。「ロケーション」ボタンをクリックし、コンピューター名を選択して、「OK」をクリックします。

   次の手順が完了するまで、このダイアログボックスは開いたままにします。

1. 」で、ディスパッチャーのキャッシュに使用するIISサイトを選択し、ウィンドウ右側の&quot;Advanced Settings&quot;をクリックします。
1. Application Pool プロパティの値を選択し、クリップボードにコピーします。
1. 開いているダイアログボックスに戻ります。&quot;Enter The Object Names To Select&quot;ボックスに、クリップボードの内容を入力 `IIS AppPool\` して貼り付けます。値は次の例のようになります。

   `IIS AppPool\DefaultAppPool`

1. 「名前の確認」ボタンをクリックします。Windows によってユーザーアカウントが解決されたら、「OK」をクリックします。
1. ディスパッチャーフォルダーの権限ダイアログボックスで、追加したアカウントを選択し、「フルコントロール」を除くアカウント **のすべての権限を有効にして、&quot;OK&quot;をクリック** します。&quot;OK&quot;をクリックしてフォルダのプロパティダイアログボックスを閉じます。

### JSON MIMEタイプの登録- IIS8.5および10 {#registering-the-json-mime-type-iis-and}

Dispatcher で JSON 呼び出しを許可する場合、以下の手順を実行して、JSON の MIME の種類を登録します。

1. IIS Manager で、Web サイトを選択し、機能ビューを使用して、「MIME の種類」をダブルクリックします。
1. JSON の拡張子がリストにない場合は、操作パネルで「追加」をクリックし、以下のプロパティ値を入力して、「OK」をクリックします。

   * File Name Extension: `.json`
   * MIME Type: `application/json`

### bin Hiddenセグメントの削除- IIS8.5および10 {#removing-the-bin-hidden-segment-iis-and}

以下の手順を実行して、非表示セグメント `bin` を削除します。新しくない Web サイトには、この非表示セグメントが含まれていることがあります。

1. IIS Manager で、Web サイトを選択し、機能ビューを使用して、「要求のフィルタリング」をダブルクリックします。
1. `bin` セグメントを選択し、「削除」をクリックして、確認ダイアログボックスで「はい」をクリックします。

### IISメッセージのファイルへのロギング- IIS8.5および10 {#logging-iis-messages-to-a-file-iis-and}

以下の手順を実行して、Dispatcher のログメッセージを Windows のイベントログではなくログファイルに書き込みます。Dispatcher がログファイルを使用するように設定し、そのファイルへの書き込みアクセス権を IIS に付与する必要があります。

1. Windowsエクスプローラーを使用して、IISインストールのlogsフォルダーの `dispatcher` 下にフォルダを作成します。一般的なインストールのためのこのフォルダーのパス `C:\inetpub\logs\dispatcher`です。

1. dispatcher フォルダーを右クリックして、「プロパティ」をクリックします。
1. 「セキュリティ」タブで「編集」をクリックし、アクセス許可ダイアログボックスで「追加」をクリックします。ユーザーアカウントを選択するためのダイアログボックスが表示されます。「ロケーション」ボタンをクリックし、コンピューター名を選択して、「OK」をクリックします。

   次の手順が完了するまで、このダイアログボックスは開いたままにします。

1. 」で、ディスパッチャーのキャッシュに使用するIISサイトを選択し、ウィンドウ右側の&quot;Advanced Settings&quot;をクリックします。
1. Application Pool プロパティの値を選択し、クリップボードにコピーします。
1. 開いているダイアログボックスに戻ります。&quot;Enter The Object Names To Select&quot;ボックスに、クリップボードの内容を入力 `IIS AppPool\` して貼り付けます。値は次の例のようになります。

   `IIS AppPool\DefaultAppPool`

1. 「名前の確認」ボタンをクリックします。Windows によってユーザーアカウントが解決されたら、「OK」をクリックします。
1. ディスパッチャーフォルダーの権限ダイアログボックスで、追加したアカウントを選択し、「フルコントロール」を除くアカウント**のすべての権限を有効にして、&quot;OK&quot;をクリックします。&quot;OK&quot;をクリックしてフォルダのプロパティダイアログボックスを閉じます。
1. テキストエディターを使用して `disp_iis.ini` ファイルを開きます。
1. 次の例のようなテキスト行を追加して、ログファイルの場所を設定し、ファイルを保存します。

   ```xml
   logfile=C:\inetpub\logs\dispatcher\dispatcher.log
   ```

### 次の手順 {#next-steps}

ディスパッチャーの使用を開始する前に、以下を知っておく必要があります。

* [ディスパッチャーの設定](dispatcher-configuration.md)
* [ディスパッチャーと連携](page-invalidate.md) するためのCongure AEM。

## Apache Web サーバー {#apache-web-server}

>[!CAUTION]
>
>インストール手順は、**Windows** の場合と **Unix** の場合の両方について記載しています。手順は慎重に実行してください。

### Apache Web サーバーのインストール {#installing-apache-web-server}

Apache Web サーバーのインストールについては、[オンライン](https://httpd.apache.org/)またはディストリビューション内のインストールマニュアルを参照してください。

>[!CAUTION]
>
>ソースファイルをコンパイルして Apache バイナリファイルを作成する場合は、必ず**動的モジュールサポート**を有効にしてください。有効にするには、**--enable-shared** オプションのいずれかを使用します。少なくとも `mod_so` モジュールを含めます。
>
>詳しくは、Apache Web サーバーのインストールマニュアルを参照してください。

Apache HTTP Server [Security Tips](https://httpd.apache.org/docs/2.2/misc/security_tips.html) および [Security Reportsも参照](https://httpd.apache.org/security_report.html)してください。

### Apache Web サーバー - Dispatcher モジュールの追加 {#apache-web-server-add-the-dispatcher-module}

Dispatcher は次のいずれかの形式で提供されます。

* **Windows**:Dynamic Link Library（DLL）
* **Unix**:動的共有オブジェクト（DTO）

インストールアーカイブファイルには、次のファイルが含まれています。選択した環境が Windows か Unix かによって異なります。

| ファイル | 説明 |
|--- |--- |
| disp_apache&lt;x.y&gt;.dll | Windows：Dispatcher のダイナミックリンクライブラリファイル。 |
| dispatcher- apache&lt; x. y&gt;-&lt; rel- nr&gt;. so | UNIX：Dispatcher の共有オブジェクトライブラリファイル。 |
| mod_dispatcher.so | UNIX：サンプルリンク。 |
| http.conf.disp&lt;x&gt; | Apache サーバー用のサンプル設定ファイル。 |
| dispatcher.any | Dispatcher 用のサンプルの設定ファイル。 |
| README | インストール手順と最新の情報を含む Readme ファイル。**注意**：インストールを開始する前に、このファイルを確認してください。 |
| CHANGES | 現在および過去のリリースで修正された問題を記載した Changes ファイル。 |

次の手順を実行して、Apache Web サーバーに Dispatcher を追加します。

1. ディスパッチャーファイルを適切なApacheモジュールディレクトリに配置します。

   * **Windows**:配置 `disp_apache<x.y>.dll``<APACHE_ROOT>/modules`
   * **Unix**:インストールに従って、 `<APACHE_ROOT>/libexec` また `<APACHE_ROOT>/modules`はディレクトリを検索します。\
      このディレクトリ `dispatcher-apache<options>.so` にコピーします。\
      長期保守を簡素化するには、ディスパッチャーという名前 `mod_dispatcher.so` のシンボリックリンクを作成することもできます。\
      `ln -s dispatcher-apache<x>-<os>-<rel-nr>.so mod_dispatcher.so`

1. dispatcher. anyファイルをディレクトリに `<APACHE_ROOT>/conf` コピーします。

   **注意：** Dispatcher モジュールの DispatcherLog プロパティが適切に設定されていれば、このファイルを別の場所に配置できます（以下の Dispatcher 固有の設定エントリを参照してください）。

### Apache Web サーバー - SELinux プロパティの設定 {#apache-web-server-configure-selinux-properties}

RedHat Linux Kernel 2.6 上で SELinux を有効にして Dispatcher を実行する場合、Dispatcher のログファイルに次のようなエラーメッセージが書き込まれることがあります。

`Mon Jun 30 00:03:59 2013] [E] [16561(139642697451488)] Unable to connect to backend rend01 (10.122.213.248:4502): Permission denied`

これは、SELinux セキュリティの有効化が原因の可能性があります。その場合、以下のタスクを実行する必要があります。

* Dispatcher モジュールファイルの SELinux コンテキストを設定する。
* ネットワーク接続をおこなう HTTPD スクリプトおよびモジュールを有効にする。
* キャッシュされたファイルを保存するドキュメントルートの SELinux コンテキストを設定する。

ターミナルウィンドウで次のコマンドを入力し、Apache Webサーバーにインストールしたディスパッチャーモジュールのパス `[path to the dispatcher.so file]` と、docrootが配置されているパス *`path to the docroot`* （ `/opt/cq/cache`例:）を入力します。

```shell
semanage fcontext -a -t httpd_modules_t [path to the dispatcher.so file]
setsebool -P httpd_can_network_connect on
chcon -R --type httpd_sys_content_t [path to the docroot]
semanage fcontext -a -t httpd_sys_content_t "[path to the docroot](/.*)?"
```

### Apache Web サーバー - Dispatcher 用の Apache Web サーバーの設定 {#apache-web-server-configure-apache-web-server-for-dispatcher}

Apache Web サーバーは、`httpd.conf` を使用して設定する必要があります。ディスパッチャーインストールキットで、サンプルの設定ファイルがあり `httpd.conf.disp<x>`ます。

以下の手順は必ずおこなってください。

1. 先に移動 `<APACHE_ROOT>/conf`します。
1. 編集 `httpd.conf`用に開く
1. 次の設定エントリを追加する順序で追加する必要があります。

   * **起動** 時にモジュールを読み込むにはloadModuleを使用します。
   * ディスパッチャー固有の設定エントリ（ **DispatcherConfig、DispatcherLog** 、 **DispatcherLogLevelなど**）。
   * **ディスパッチャーをアクティブ化** するためのsetHandler。**LoadModule**.
   * **modmMeusePathInfo** でmod_ mimeの **動作を設定**します。

1. （オプション）htdocs ディレクトリの所有者を変更することをお勧めします。

   * Apache サーバーはルートとして起動しますが、子プロセスはデーモンとして起動します（セキュリティのため）。documentRoot（`<APACHE_ROOT>/htdocs`）は、ユーザーデーモンに属している必要があります。

      ```xml
      cd <APACHE_ROOT>  
      chown -R daemon:daemon htdocs
      ```

**LoadModule**

次の表に、使用例を示します。実際のエントリは、使用する Apache Web サーバーによって異なります。

|  |
|--- |--- |
| Windows | `... LoadModule dispatcher_module modules\disp_apache.dll ...` |
| Unix（シンボリックリンク） | `... LoadModule dispatcher_module libexec/mod_dispatcher.so ...` |

>[!NOTE]
>
>各ステートメントの最初のパラメーターは、以上の例のとおりに記述する必要があります。
>
>このコマンドについて詳しくは、設定ファイルの例とApache Web Serverのドキュメントを参照してください。

**ディスパッチャー固有の設定エントリ**

ディスパッチャー固有の設定エントリは、LoadModuleエントリの後に配置されます。次の表に、UnixとWindowsの両方に適用できる設定例を示します。

**Windows&amp; Unix**

```
...
<fModule disp_apache2.c>
DispatcherConfig conf/dispatcher.any
DispatcherLog logs/dispatcher.log DispatcherLogLevel 3
DispatcherNoServerHeader 0 DispatcherDeclineRoot 0
DispatcherUseProcessedURL 0
DispatcherPassError 0
DispatcherKeepAliveTimeout 60
</IfModule>
...
```

個別の設定パラメーターの内容：

| パラメーター | 説明 |
|--- |--- |
| DispatcherConfig | Dispatcher 設定ファイルの場所と名前。<br/>このプロパティがメインサーバー設定にある場合、すべての仮想ホストがプロパティ値を継承します。ただし、仮想ホストに DispatcherConfig プロパティを含めて、メインサーバー設定をオーバーライドできます。 |
| DispatcherLog | ログファイルの場所と名前。 |
| DispatcherLogLevel | ログファイルのログレベル：<br/> 0 - エラー<br/> 1 - 警告<br/> 2 - 情報<br/> 3 - デバッグ<br/> **注意**：インストールおよびテスト時はログレベルを 3 に設定し、実稼動環境で実行する場合は 0 に設定することをお勧めします。 |
| DispatcherNoServerHeader | *このパラメーターは廃止されており、何も効果がありません。*<br/><br/> 使用するサーバーヘッダーを定義します。 <br/><ul><li>undefinedまたは0- HTTPサーバーヘッダーにAEMバージョンが含まれています。 </li><li> 1 - Apache サーバーヘッダーを使用します。</li></ul> |
| DispatcherDeclineRoot | ルート&quot;/&quot;へのリクエストを拒否するかどうかを定義します。 <br/>**0** -/ <br/>**1へのリクエストを受け入れます** 。また、/はディスパッチャーによって処理されません。正しいマッピングにはmod_ aliasを使用します。 |
| DispatcherUseProcessedURL | Dispatcher によるすべての詳細な処理に事前処理された URL を使用するかどうかを定義します。<br/> **0** - Web サーバーに渡された元の URL を使用します。<br/>**1** -ディスパッチャーは、Webサーバーに渡される元のURLの代わりに、ディスパッチャーの前にあるハンドラー（つまり `mod_rewrite`、）より前のハンドラーによって既に処理されているURLを使用します。 例えば、元の URL または処理された URL のどちらかが Dispatcher のフィルターと一致する場合などです。URL は、キャッシュファイル構造の基礎としても使用されます。mod_ rewriteについて詳しくは、Apache Webサイトのドキュメントを参照してください。（例: Apache2.2）mod_ rewriteを使用する場合、フラグ&quot;passthrough&quot;を使用することをお勧めします | PT&#39;（次のハンドラーに渡す）。rewriteエンジンは、内部リクエスト_ Rec構造のuriフィールドをファイル名フィールドの値に設定するように強制します。 |
| DispatcherPassError | ErrorDocument 処理のエラーコードのサポート方法を定義します。<br/> **0** - Dispatcher はクライアントへのすべてのエラー応答をスプールします。<br/> **1** - Dispatcher はクライアントへのエラー応答（ステータスコードが 400 以上）をスプールしませんが、ステータスコードを Apache に渡します。Apache では、ErrorDocument 命令によってそのようなステータスコードを処理できます。<br/>**コード範囲** -応答がApacheに渡されるエラーコードの範囲を指定します。その他のエラーコードはクライアントに渡されます。例えば、次の設定では、エラー412の応答がクライアントに渡され、その他すべてのエラーがApacheに渡されます。DispatcherPasError400-411,413-417 |
| DispatcherKeepAliveTimeout | キープアライブタイムアウトを秒単位で指定します。ディスパッチャーバージョン4.2.0から、デフォルトのキープアライブ値は60です。値 0 はキープアライブを無効にします。 |
| DispatcherNoCanURL | このパラメーターをOnに設定すると、canonicalizedの代わりに生のURLがバックエンドに渡され、DispatcherUseProcessedURLの設定がオーバーライドされます。デフォルト値はOffです。<br/>**注意**:ディスパッチャー設定のフィルタールールは、生のURLではなく、常に匿名URLに対して評価されます。 |




>[!NOTE]
>
>パスエントリには、Apache Web サーバーのルートディレクトリの相対パスを指定します。

>[!NOTE]
>
>サーバーヘッダーのデフォルト設定は次のとおりです。 `  
ServerTokens Full``  
DispatcherNoServerHeader 0`\
AEMバージョン（統計的目的用）を示します。このような情報を無効にしたい場合は、次のように設定します。 `  
ServerTokens Prod`\
詳しくは、ServerTokensディレクティブに関する [Apacheドキュメント（例えばApache2.2の場合](https://httpd.apache.org/docs/2.2/mod/core.html) ）を参照してください。

**SetHandler**

これらのエントリの後、ディスパッチャー **** の設定（ `<Directory>`， `<Location>`）のコンテキストにsetHandlerステートメントを追加して、受信要求を処理する必要があります。次の例では、Web サイト全体に対する要求を処理するように Dispatcher を設定します。

**Windows および UNIX**

```
...  
<Directory />  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
  
Options FollowSymLinks  
AllowOverride None  
</Directory>  
...
```

次の例では、仮想ドメインに対する要求を処理するように Dispatcher を設定します。

**Windows**

```
...  
<VirtualHost 123.45.67.89>  
ServerName www.mycompany.com  
DocumentRoot _\[cache-path\]_\\docs  
<Directory _\[cache-path\]_\\docs>  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
AllowOverride None  
</Directory>  
</VirtualHost>  
...
```

**UNIX**

```
...  
<VirtualHost 123.45.67.89>  
ServerName www.mycompany.com  
DocumentRoot /usr/apachecache/docs  
<Directory /usr/apachecache/docs>  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
AllowOverride None  
</Directory>  
</VirtualHost>  
...
```

>[!NOTE]
**SetHandler** ステートメントのパラメーターは、モジュールで定義されているハンドラーの名前なので、以上の例のとおりに記述する必要があります。**
このコマンドについて詳しくは、設定ファイルの例とApache Web Serverのドキュメントを参照してください。

**modmMeusePathInfo**

**setHandler** ステートメントの後に **、modmMeusePathInfo** 定義も追加する必要があります。

>[!NOTE]
`ModMimeUsePathInfo` このパラメーターは、ディスパッチャーバージョン4.0.9以降を使用している場合にのみ使用し、設定する必要があります。
（Dispatcher バージョン 4.0.9 は 2011 年にリリースされました。それ以前のバージョンを使用している場合は、最新の Dispatcher バージョンにアップグレードすることをお勧めします。）

すべての Apache 設定で、次のように **ModMimeUsePathInfo** パラメーターを `On` にする必要があります。

`ModMimeUsePathInfo On`

mod_ mimeモジュール（例えば [、Apacheモジュールmod_ mime](https://httpd.apache.org/docs/2.2/mod/mod_mime.html)）を使用して、HTTP応答用に選択したコンテンツにコンテンツメタデータを割り当てます。デフォルト設定では、mod_ mimeがコンテンツタイプを決定するときに、ファイルまたはディレクトリにマップされるURLの一部のみが考慮されます。

この `On``ModMimeUsePathInfo` 場合、パラメーターは `mod_mime`*、完全* なURLに基づいてコンテンツタイプを決定することを指定します。つまり、仮想リソースは拡張子に基づいてメタデータを適用します。

次の例で **は、modmitMeusepathInfo**をアクティブにします。

**Windows および UNIX**

```
...  
<Directory />  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
ModMimeUsePathInfo On  
</IfModule>  
  
Options FollowSymLinks  
AllowOverride None  
</Directory>  
...
```

### HTTPSのサポートを有効にする（UnixおよびLinux） {#enable-support-for-https-unix-and-linux}

Dispatcher は、OpenSSL を使用して HTTP 経由でのセキュアな通信を実装します。Dispatcher バージョン **4.2.0** からは、OpenSSL 1.0.0 および OpenSSL 1.0.1 がサポートされています。デフォルトでは、Dispatcher は OpenSSL 1.0.0 を使用します。OpenSSL1.0.1を使用するには、次の手順を使用して、ディスパッチャーがインストールされているOpenSSLライブラリを使用するようにシンボリックリンクを作成します。

1. ターミナルを開き、現在のディレクトリを OpenSSL ライブラリがインストールされているディレクトリに変更します。例を以下に示します。

   ```shell
   cd /usr/lib64
   ```

1. シンボリックリンクを作成するには、以下のコマンドを入力します。

   ```shell
   ln -s libssl.so libssl.so.1.0.1
   ln -s libcrypto.so libcrypto.so.1.0.1
   ```

>[!NOTE]
カスタマイズバージョンのApacheを使用している場合は、同じバージョン [のOpenSSLを使用してApacheおよびディスパッチャーがコンパイルされていることを確認](https://www.openssl.org/source/)してください。

### 次の手順 {#next-steps-1}

Dispatcher の使用を始める前に、次の作業を実行する必要があります。

* [ディスパッチャーの設定](dispatcher-configuration.md)
* [ディスパッチャーと連携](page-invalidate.md) するためのCongure AEM。

## Sun Java System Web Server／iPlanet {#sun-java-system-web-server-iplanet}

>[!NOTE]
Windows環境とUNIX環境の両方について説明します。
実行するタイミングを選択してください。

### Sun Java System Web Server／iPlanet - Web サーバーのインストール {#sun-java-system-web-server-iplanet-installing-your-web-server}

次の Web サーバーのインストール方法について詳しくは、各サーバーのドキュメントを参照してください。

* Sun Java System Web Server
* iPlanet Web Server

### Sun Java System Web Server／iPlanet - Dispatcher モジュールの追加 {#sun-java-system-web-server-iplanet-add-the-dispatcher-module}

Dispatcher は次のいずれかの形式で提供されます。

* **Windows**:Dynamic Link Library（DLL）
* **Unix**:動的共有オブジェクト（DTO）

インストールアーカイブファイルには、次のファイルが含まれています。選択した環境が Windows か Unix かによって異なります。

| ファイル | 説明 |
|---|---|
| `disp_ns.dll` | Windows：Dispatcher のダイナミックリンクライブラリファイル。 |
| `dispatcher.so` | UNIX：Dispatcher の共有オブジェクトライブラリファイル。 |
| `dispatcher.so` | UNIX：サンプルリンク。 |
| `obj.conf.disp` | iPlanet／Sun Java System Web Server 用のサンプルの設定ファイル。 |
| `dispatcher.any` | Dispatcher 用のサンプルの設定ファイル。 |
| README | インストール手順と最新の情報を含む Readme ファイル。注意：インストールを開始する前に、このファイルを確認してください。 |
| CHANGES | 現在および過去のリリースで修正された問題を記載した Changes ファイル。 |

以下の手順を使用して、ディスパッチャーをWebサーバーに追加します。

1. ディスパッチャーファイルをWebサーバーの `plugin` ディレクトリに配置します。

### Sun Java System Web Server／iPlanet - Dispatcher 用の設定 {#sun-java-system-web-server-iplanet-configure-for-the-dispatcher}

Webサーバーを設定する必要 `obj.conf`があります。ディスパッチャーインストールキットで、サンプルの設定ファイルがあり `obj.conf.disp`ます。

1. 先に移動 `<WEBSERVER_ROOT>/config`します。
1. 編集 `obj.conf`用に開く
1. 開始する行をコピーします。\
   `Service fn="dispService"`\
   `obj.conf.disp``obj.conf`をクリックします。

1. 変更内容を保存します。
1. 編集 `magnus.conf` 用に開く
1. 開始する2行をコピーします。\
   `Init funcs="dispService, dispInit"`\
   、および \
   `Init fn="dispInit"`\
   `obj.conf.disp``magnus.conf`をクリックします。

1. 変更内容を保存します。

>[!NOTE]
以下の設定は、すべて1行で、それぞれ `$(SERVER_ROOT)` の値に置き換える `$(PRODUCT_SUBDIR)` 必要があります。

**Init**

次の表に、使用できる例を示します。正確なエントリは、特定のWebサーバーに従います。

**Windows および UNIX**

```
...  
Init funcs="dispService,dispInit" fn="load-modules" shlib="$(SERVER\_ROOT)/plugins/dispatcher.so"  
Init fn="dispInit" config="$(PRODUCT\_SUBDIR)/dispatcher.any" loglevel="1" logfile="$(PRODUCT\_SUBDIR)/logs/dispatcher.log"  
keepalivetimeout="60"  
...
```

各パラメーターの意味は次のとおりです。

| パラメーター | 説明 |
|--- |--- |
| config | 設定ファイル `dispatcher.any.` の場所と名前。 |
| logfile | ログファイルの場所と名前。 |
| loglevel | ログファイルにメッセージを書き込むときのログレベル: <br/>**0** Errors <br/>**1** Warnings <br/>**2** Infos <br/>**3** Debug <br/>**注意:** インストールおよびテスト中にログレベルを3に設定し、実稼働環境で実行する場合は0に設定することをお勧めします。 |
| keepalivetimeout | キープアライブタイムアウトを秒単位で指定します。ディスパッチャーバージョン4.2.0から、デフォルトのキープアライブ値は60です。値 0 はキープアライブを無効にします。 |

必要に応じて、ディスパッチャーをオブジェクトのサービスとして定義できます。Webサイト全体のディスパッチャーを設定するには、デフォルトのオブジェクトを変更します。


**Windows**

```
...  
NameTrans fn="document-root" root="$(PRODUCT\_SUBDIR)\\dispcache"  
...  
Service fn="dispService" method="(GET|HEAD|POST)" type="\*\\\*"  
...
```

**UNIX**

```
...  
NameTrans fn="document-root" root="$(PRODUCT\_SUBDIR)/dispcache"  
...  
Service fn="dispService" method="(GET|HEAD|POST)" type="\*/\*"  
...
```

### 次の手順 {#next-steps-2}

Dispatcher の使用を始める前に、次の作業を実行する必要があります。

* [ディスパッチャーの設定](dispatcher-configuration.md)
* [ディスパッチャーと連携](page-invalidate.md) するためのCongure AEM。
