---
title: Dispatcher での SSL の使用
seo-title: Using SSL with Dispatcher
description: SSL 接続を使用して AEM と通信するよう Dispatcher を設定する方法について説明します。
seo-description: Learn how to configure Dispatcher to communicate with AEM using SSL connections.
uuid: 1a8f448c-d3d8-4798-a5cb-9579171171ed
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: 771cfd85-6c26-4ff2-a3fe-dff8d8f7920b
index: y
internal: n
snippet: y
exl-id: ec378409-ddb7-4917-981d-dbf2198aca98
source-git-commit: e87af532ee3268f0a45679e20031c3febc02de58
workflow-type: tm+mt
source-wordcount: '1355'
ht-degree: 65%

---

# Dispatcher での SSL の使用 {#using-ssl-with-dispatcher}

Dispatcher とレンダーコンピューター間には次の SSL 接続を使用します。

* [一方向 SSL](#use-ssl-when-dispatcher-connects-to-aem)
* [相互 SSL](#configuring-mutual-ssl-between-dispatcher-and-aem)

>[!NOTE]
>
>SSL 証明書に関連する操作は、サードパーティ製品に結び付けられます。 Adobe Platinum Maintenance and Support 契約の対象ではありません。

## Dispatcher が AEM に接続するときに SSL を使用する {#use-ssl-when-dispatcher-connects-to-aem}

Dispatcher が SSL 接続を使用して AEM または CQ レンダーインスタンスと通信するように設定します。

Dispatcher を設定する前に、SSL を使用するように AEM または CQ を設定してください。

* AEM 6.2: [HTTP over SSL の有効化](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=ja)
* AEM 6.1: [HTTP over SSL の有効化](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=ja)
* 以前のAEMバージョン：参照 [このページ](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=ja).

### SSL に関連する要求ヘッダー {#ssl-related-request-headers}

Dispatcher が HTTPS 要求を受け取ると、Dispatcher は、AEMまたは CQ に送信する次の要求に次のヘッダーを含めます。

* `X-Forwarded-SSL`
* `X-Forwarded-SSL-Cipher`
* `X-Forwarded-SSL-Keysize`
* `X-Forwarded-SSL-Session-ID`

Apache-2.4 と `mod_ssl` を使用した要求には、次の例のようなヘッダーが含まれます。

```shell
X-Forwarded-SSL: on
X-Forwarded-SSL-Cipher: DHE-RSA-AES256-SHA
X-Forwarded-SSL-Session-ID: 814825E8CD055B4C166C2EF6D75E1D0FE786FFB29DEB6DE1E239D5C771CB5B4D
```

### SSL を使用するように Dispatcher を設定する {#configuring-dispatcher-to-use-ssl}

SSL 経由で AEM または CQ と接続するように Dispatcher を設定するには、[dispatcher.any](dispatcher-configuration.md) ファイルに以下のプロパティが必要です。

* HTTPS 要求を処理する仮想ホスト。
* 仮想ホストの `renders` セクションに、HTTPS を使用する CQ または AEM インスタンスのホスト名およびポートを識別するアイテムを含めます。
* `renders` アイテムには、`secure` というプロパティ（値：`1`）が含まれます。

注意：必要に応じて、HTTP 要求を処理する別の仮想ホストを作成します。

次の例 `dispatcher.any` ファイルは、ホスト上で実行されている CQ インスタンスに HTTPS を使用して接続するためのプロパティ値を示します `localhost` およびポート `8443`:

```
/farms
{
   /secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTPS requests
         "https://*"
      }
      /renders
      {
      /0001
         {
            # hostname or IP of the render
            /hostname "localhost"
            # port of the render
            /port "8443"
            # connect via HTTPS
            /secure "1"
         }
      }
     # the rest of the properties are omitted
   }

   /non-secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTP requests
         "http://*"
      }
      /renders
      {
         /0001
      {
         # hostname or IP of the render
         /hostname "localhost"
         # port of the render
         /port "4503"
      }
   }
    # the rest of the properties are omitted
}
```

## Dispatcher と AEM 間の相互 SSL の設定 {#configuring-mutual-ssl-between-dispatcher-and-aem}

相互 SSL を使用するには、Dispatcher とレンダーコンピューター ( 通常はAEMまたは CQ パブリッシュインスタンス ) 間の接続を設定します。

* Dispatcher が SSL 経由でレンダーインスタンスに接続します。
* レンダーインスタンスが Dispatcher の証明書の有効性を確認します。
* Dispatcher は、レンダーインスタンスの証明書の CA が信頼されていることを検証します。
* （オプション）Dispatcher が、レンダーインスタンスの証明書がレンダーインスタンスのサーバーアドレスと一致することを確認します。

相互 SSL を設定するには、信頼済みの証明機関（CA）によって署名されている証明書が必要です。自己署名証明書では不十分です。証明書に署名するために、CA の機能を果たすことも、サードパーティ CA のサービスを利用することもできます。相互 SSL を設定するには、以下のアイテムが必要です。

* レンダーインスタンスおよび Dispatcher 用の署名済み証明書
* CA 証明書（CA の機能を果たしている場合）
* CA、証明書および証明書要求を生成するための OpenSSL ライブラリ

相互 SSL を設定するには、次の手順を実行します。

1. 使用するプラットフォームに適した最新バージョンの Dispatcher を[インストール](dispatcher-install.md)します。SSL をサポートしている Dispatcher バイナリを使用してください（dispatcher-apache2.4-linux-x86-64-ssl10-4.1.7.tar のように、SSL がファイル名に含まれています）。
1. Dispatcher およびレンダーインスタンス用に [CA 署名済みの証明書を作成または取得](dispatcher-ssl.md#main-pars-title-3)します。
1. [レンダー証明書を格納したキーストアの作成](dispatcher-ssl.md#main-pars-title-6) レンダーの HTTP サービスを設定します。
1. 相互 SSL 用に [Dispatcher の Web サーバーモジュールを設定](dispatcher-ssl.md#main-pars-title-4)します。

### CA 署名済み証明書の作成または取得 {#creating-or-obtaining-ca-signed-certificates}

パブリッシュインスタンスおよび Dispatcher を認証する、CA 署名済み証明書を作成または取得します。

#### CA の作成 {#creating-your-ca}

CA の機能を果たしている場合は、[OpenSSL](https://www.openssl.org/) を使用して、サーバーとクライアントの証明書に署名する証明機関を作成します（OpenSSL ライブラリがインストールされている必要があります）。サードパーティ CA を利用する場合は、この手順を実行しないでください。

1. ターミナルを開き、現在のディレクトリを `CA.sh` ファイル： `/usr/local/ssl/misc`.
1. CA を作成するには、次のコマンドを入力し、プロンプトに従って値を指定します。

   ```shell
   ./CA.sh -newca
   ```

   >[!NOTE]
   >
   >の複数のプロパティ `openssl.cnf` ファイルは、CA.sh スクリプトの動作を制御します。 CA を作成する前に、必要に応じてこのファイルを編集します。

#### 証明書の作成 {#creating-the-certificates}

OpenSSL を使用して証明書要求を作成し、サードパーティ CA に送信するか、自身の CA によって署名します。

証明書を作成する際、OpenSSL では Common Name プロパティを使用して証明書保持者を識別します。レンダーインスタンスの証明書については、証明書を受け入れるように Dispatcher を設定している場合は、インスタンスコンピューターのホスト名を共通名として使用し、パブリッシュインスタンスのホスト名と一致する場合にのみ使用します。 （[DispatcherCheckPeerCN](dispatcher-ssl.md#main-pars-title-11) プロパティを参照）。

1. ターミナルを開き、現在のディレクトリを OpenSSL ライブラリの CH.sh ファイルを含むディレクトリに変更します。
1. 次のコマンドを入力し、指示に従って値を指定します。必要に応じて、パブリッシュインスタンスのホスト名を共通名として使用します。 ホスト名は、レンダーの IP アドレスに対して DNS 解決可能な名前です。

   ```shell
   ./CA.sh -newreq
   ```

   サードパーティ CA を利用する場合は、newreq.pem ファイルを署名する CA に送信します。CA の機能を果たしている場合は、手順 3 に進みます。

1. CA の証明書を使用して証明書に署名するには、次のコマンドを入力します。

   ```shell
   ./CA.sh -sign
   ```

   次の名前の 2 つのファイル： `newcert.pem` および `newkey.pem` は、CA 管理ファイルを含むディレクトリに作成されます。 これら 2 つのファイルは、それぞれレンダーコンピューターの公開証明書と秘密鍵です。

1. 名前を変更 `newcert.pem` から `rendercert.pem`、名前を変更 `newkey.pem` から `renderkey.pem`.
1. 手順 2 と 3 を繰り返して、Dispatcher モジュールの証明書と公開鍵を作成します。 必ず Dispatcher インスタンスに固有の Common Name を使用してください。
1. 名前を変更 `newcert.pem` から `dispcert.pem`、名前を変更 `newkey.pem` から `dispkey.pem`.

### レンダーコンピューター上の SSL の設定 {#configuring-ssl-on-the-render-computer}

を使用してレンダーインスタンスで SSL を設定する `rendercert.pem` および `renderkey.pem` ファイル。

#### レンダリング証明書の JKS(Java™ KeyStore) 形式への変換 {#converting-the-render-certificate-to-jks-format}

次のコマンドを使用して、PEM ファイルであるレンダー証明書を PKCS#12 ファイルに変換します。 レンダー証明書に署名した CA の証明書も含めます。

1. ターミナルウィンドウで、現在のディレクトリをレンダー証明書と秘密鍵のある場所に変更します。
1. PEM ファイルであるレンダー証明書を PKCS#12 ファイルに変換するには、次のコマンドを入力します。 レンダー証明書に署名した CA の証明書も含めます。

   ```shell
   openssl pkcs12 -export -in rendercert.pem -inkey renderkey.pem  -certfile demoCA/cacert.pem -out rendercert.p12
   ```

1. PKCS#12 ファイルを Java™ KeyStore (JKS) 形式に変換するには、次のコマンドを入力します。

   ```shell
   keytool -importkeystore -srckeystore servercert.p12 -srcstoretype pkcs12 -destkeystore render.keystore
   ```

1. Java™キーストアはデフォルトのエイリアスを使用して作成されます。 必要に応じてエイリアスを変更してください。

   ```shell
   keytool -changealias -alias 1 -destalias jettyhttp -keystore render.keystore
   ```

#### CA 証明書のレンダーのトラストストアへの追加 {#adding-the-ca-cert-to-the-render-s-truststore}

CA の機能を果たしている場合は、CA 証明書をキーストアに読み込みます。次に、キーストアを信頼するように、レンダーインスタンスを実行している JVM を設定します。

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2014-08-12T13:11:21.401-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The jetty http service has properties to specify trusted CA certificates for mutual SSL for 6.0. Whether they are operable is undetetermined. See https://issues.adobe.com/browse/DOC-4738.</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;"> </p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For 5.6.1, you would specify the system property javax.net.ssl.trustStore, using the path to cacerts as value.</p>

 -->

1. テキストエディターを使用して、 cacert.pem ファイルを開き、次の行の前にあるすべてのテキストを削除します。

   `-----BEGIN CERTIFICATE-----`

1. 次のコマンドを使用して、証明書をキーストアに読み込みます。

   ```shell
   keytool -import -keystore cacerts.keystore -alias myca -storepass password -file cacert.pem
   ```

1. キーストアを信頼するように、レンダーインスタンスを実行している JVM を設定するには、次のシステムプロパティを使用します。

   ```shell
   -Djavax.net.ssl.trustStore=<location of cacerts.keystore>
   ```

   例えば、crx-quickstart/bin/quickstart スクリプトを使用してパブリッシュインスタンスを起動する場合は、CQ_JVM_OPTS プロパティを次のように変更します。

   ```shell
   CQ_JVM_OPTS='-server -Xmx2048m -XX:MaxPermSize=512M -Djavax.net.ssl.trustStore=/usr/lib/cq6.0/publish/ssl/cacerts.keystore'
   ```

#### レンダーインスタンスの設定 {#configuring-the-render-instance}

SSL を使用するようにレンダーインスタンスの HTTP サービスを設定するには、 *パブリッシュインスタンスでの SSL の有効化* セクション：

* AEM 6.2: [HTTP over SSL の有効化](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=ja)
* AEM 6.1: [HTTP over SSL の有効化](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=ja)
* 以前のAEMバージョン：参照 [このページ。](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=ja)

### Dispatcher モジュール用の SSL の設定 {#configuring-ssl-for-the-dispatcher-module}

相互 SSL を使用するように Dispatcher を設定するには、Dispatcher 証明書を準備して、Web サーバーモジュールを設定します。

### Dispatcher の統合証明書の作成 {#creating-a-unified-dispatcher-certificate}

Dispatcher の証明書と暗号化されていない秘密鍵を組み合わせて、1 つの PEM ファイルにします。 テキストエディターまたは `cat` コマンドを使用して、以下のサンプルのようなファイルを作成します。

1. ターミナルを開き、現在のディレクトリを dispkey.pem ファイルのある場所に変更します。
1. 秘密鍵を復号化するには、次のコマンドを入力します。

   ```shell
   openssl rsa -in dispkey.pem -out dispkey_unencrypted.pem
   ```

1. テキストエディターまたは `cat` コマンドを使用して、暗号化されていない秘密鍵と証明書を組み合わせ、次のサンプルのような単一のファイルにします。

   ```xml
   -----BEGIN RSA PRIVATE KEY-----
   MIICxjBABgkqhkiG9w0B...
   ...M2HWhDn5ywJsX
   -----END RSA PRIVATE KEY-----
   -----BEGIN CERTIFICATE-----
   MIIC3TCCAk...
   ...roZAs=
   -----END CERTIFICATE-----
   ```

### Dispatcher 用に使用する証明書の指定 {#specifying-the-certificate-to-use-for-dispatcher}

以下のプロパティを（`httpd.conf` の）[Dispatcher モジュールの設定](dispatcher-install.md#main-pars-55-35-1022)に追加します。

* `DispatcherCertificateFile`：公開証明書と暗号化されていない秘密鍵を含む、Dispatcher の統合証明書ファイルへのパス。このファイルは、SSL サーバーが Dispatcher のクライアント証明書を要求する場合に使用します。
* `DispatcherCACertificateFile`：SSL サーバーが提示する CA がルート証明機関によって信頼されていない場合に使用する、CA 証明書ファイルへのパス。
* `DispatcherCheckPeerCN`：リモートサーバー証明書のホスト名チェックを有効（`On`）にするか無効（`Off`）にするか。

以下のコードは設定のサンプルです。

```xml
<IfModule disp_apache2.c>
  DispatcherConfig conf/dispatcher.any
  DispatcherLog    logs/dispatcher.log
  DispatcherLogLevel 3
  DispatcherNoServerHeader 0
  DispatcherDeclineRoot 0
  DispatcherUseProcessedURL 0
  DispatcherPassError 0
  DispatcherCertificateFile disp_unified.pem
  DispatcherCACertificateFile cacert.pem
  DispatcherCheckPeerCN On
</IfModule>
```
