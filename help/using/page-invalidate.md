---
title: AEM からのキャッシュされたページの無効化
seo-title: Adobe AEM からのキャッシュされたページの無効化
description: 効率的なキャッシュ管理を確保するため、Dispatcher と AEM の間のインタラクションを設定する方法について説明します。
seo-description: 効率的なキャッシュ管理を確保するため、Adobe AEM Dispatcher と AEM の間のインタラクションを設定する方法について説明します。
uuid: 66533299-55c0-4864-9beb-77e281af9359
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: 79cd94be-a6bc-4d34-bfe9-393b4107925c
translation-type: tm+mt
source-git-commit: 85497651ce29c8564da4b52c60819a48b776af7b

---


# AEM からのキャッシュされたページの無効化 {#invalidating-cached-pages-from-aem}

Dispatcher を AEM と共に使用する際は、キャッシュが効果的に管理されるようにインタラクションを設定する必要があります。環境によっては、この設定でパフォーマンスを向上させることができます。

## AEM ユーザーアカウントの設定 {#setting-up-aem-user-accounts}

デフォルトの `admin` ユーザーアカウントを使用して、デフォルトでインストールされているレプリケーションエージェントを認証します。レプリケーションエージェントで使用する専用のユーザーアカウントを作成する必要があります。

For more information see the [Configure Replication and Transport Users](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps) section of the AEM Security Checklist.

## オーサリング環境からの Dispatcher キャッシュの無効化 {#invalidating-dispatcher-cache-from-the-authoring-environment}

ページが公開されると、AEM オーサーインスタンス上のレプリケーションエージェントがキャッシュの無効化要求を Dispatcher に送信します。この要求により、新しいコンテンツが公開されたときに Dispatcher が最終的にキャッシュ内のファイルを更新します。

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-0436B4A35714BFF67F000101@AdobeID)
Last Modified Date: 2017-05-25T10:37:23.679-0400

<p>Hiding this information due to <a href="https://jira.corp.adobe.com/browse/CQDOC-5805">CQDOC-5805</a>.</p>

 -->

以下の手順を実行して、ページのアクティベート時に Dispatcher のキャッシュを無効化するよう、AEM オーサーインスタンス上のレプリケーションエージェントを設定します。

1. AEM ツールのコンソールを開きます（`https://localhost:4502/miscadmin#/etc`）。
1. オーサーインスタンスの Tools/replication/Agents の下にある、必要なレプリケーションエージェントを開きます。デフォルトでインストールされている Dispatcher フラッシュエージェントを使用できます。
1. 「編集」をクリックし、「設定」タブで「**有効**」が選択されていることを確認します。

1. （オプション）エイリアスまたはバニティーパスの無効化要求を有効にするには、「**エイリアスの更新**」オプションを選択します。
1. 「トランスポート」タブで、Dispatcher へのアクセスに必要な URI を入力します。\
   標準の Dispatcher フラッシュエージェントを使用している場合は、ホスト名とポートを更新する必要がある可能性があります。例：https://&lt;*dispatcherHost*>:&lt;*portApache*>/dispatcher/invalidate.cache

   **注意：** Dispatcher フラッシュエージェントの場合は、パスベースの仮想ホストエントリを使用してファームを区別する場合にのみ URI プロパティが使用されます。このフィールドを使用して、無効化するファームをターゲット設定します。例えば、ファーム #1 の仮想ホストは `www.mysite.com/path1/*` で、ファーム #2 の仮想ホストは `www.mysite.com/path2/*` です。この場合、`/path1/invalidate.cache` の URL を使用して最初のファームをターゲット設定し、`/path2/invalidate.cache` を使用して 2 つ目のファームをターゲット設定できます。詳しくは、[複数ドメインでの Dispatcher の使用](dispatcher-domains.md)を参照してください。 

1. 必要に応じて、その他のパラメーターを設定します。
1. 「OK」をクリックして、エージェントをアクティベートします。

Alternatively, you can also access and configure the Dispatcher Flush agent from the [AEM Touch UI](https://helpx.adobe.com/experience-manager/6-2/sites/deploying/using/replication.html#ConfiguringaDispatcherFlushagent).

バニティー URL へのアクセスを有効にする方法について詳しくは、[バニティー URL へのアクセスの有効化](dispatcher-configuration.md#enabling-access-to-vanity-urls-vanity-urls)を参照してださい。

>[!NOTE]
>
>Dispatcher のキャッシュをフラッシュするエージェントにユーザー名とパスワードを設定する必要はありませんが、これらを設定した場合は基本認証付きで送信されます。

この方法には、次の 2 つの問題が発生する可能性があります。

* オーサーインスタンスから Dispatcher へのアクセスが可能である必要があります。ただし、使用するネットワーク（ファイアウォールなど）の設定で、この 2 つの間のアクセスが制限されると、アクセスできない場合があります。

* 公開とキャッシュの無効化は同時に行われます。ただし、タイミングによっては、キャッシュからページが削除された直後、新しいページが公開される直前に、削除されたページが要求される場合があります。その場合は、AEM から古いページが返され、Dispatcher で再びキャッシュされます。大規模なサイトの場合は、これが大きな問題になります。

## パブリッシュインスタンスからの Dispatcher キャッシュの無効化 {#invalidating-dispatcher-cache-from-a-publishing-instance}

キャッシュ管理をオーサー環境からパブリッシュインスタンスに移行すると、パフォーマンスが向上する場合があります。この場合、公開されたページを受信したときにキャッシュの無効化要求を Dispatcher に送信するのは、AEM オーサー環境でなくパブリッシュ環境となります。

次のような場合が考えられます。

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

* Dispatcher とパブリッシュインスタンス間で起こりうるタイミングの衝突を防ぐ（[オーサリング環境からの Dispatcher キャッシュの無効化](#invalidating-dispatcher-cache-from-the-authoring-environment)を参照）。
* 高パフォーマンスサーバー上にある複数のパブリッシュインスタンスと、唯一のオーサーインスタンスがシステムに含まれる。

>[!NOTE]
>
>この方法を使用するかどうかは、経験のある AEM 管理者が判断してください。

Dispatcher のフラッシュは、パブリッシュインスタンスで動作するレプリケーションエージェントによって制御されます。ただし、この機能の設定はオーサー環境でおこないます。その後、次のようにエージェントをアクティベートして設定を移行します。

1. AEM のツールコンソールを開きます。
1. パブリッシュインスタンスの Tools/replication/Agents の下にある、必要なレプリケーションエージェントを開きます。デフォルトでインストールされている Dispatcher フラッシュエージェントを使用できます。
1. 「編集」をクリックし、「設定」タブで「**有効**」が選択されていることを確認します。
1. （オプション）エイリアスまたはバニティーパスの無効化要求を有効にするには、「**エイリアスの更新**」オプションを選択します。
1. 「トランスポート」タブで、Dispatcher へのアクセスに必要な URI を入力します。\
   標準の Dispatcher フラッシュエージェントを使用している場合は、ホスト名とポートを更新する必要がある可能性があります。例：`http://<dispatcherHost>:<portApache>/dispatcher/invalidate.cache`

   **注意：** Dispatcher フラッシュエージェントの場合は、パスベースの仮想ホストエントリを使用してファームを区別する場合にのみ URI プロパティが使用されます。このフィールドを使用して、無効化するファームをターゲット設定します。例えば、ファーム #1 の仮想ホストは `www.mysite.com/path1/*` で、ファーム #2 の仮想ホストは `www.mysite.com/path2/*` です。この場合、`/path1/invalidate.cache` の URL を使用して最初のファームをターゲット設定し、`/path2/invalidate.cache` を使用して 2 つ目のファームをターゲット設定できます。詳しくは、[複数ドメインでの Dispatcher の使用](dispatcher-domains.md)を参照してください。 

1. 必要に応じて、その他のパラメーターを設定します。
1. 適用するパブリッシュインスタンスごとに、手順を繰り返します。

設定後、オーサーインスタンスからパブリッシュインスタンスにページをアクティベートすると、このエージェントが標準のレプリケーションを開始します。ログには、次の例のような、パブリッシュサーバーからの要求を示すメッセージが含まれます。

1. `<publishserver> 13:29:47 127.0.0.1 POST /dispatcher/invalidate.cache 200`

## 手動での Dispatcher キャッシュの無効化 {#manually-invalidating-the-dispatcher-cache}

ページをアクティベートせずに Dispatcher キャッシュを無効化（またはフラッシュ）するには、Dispatcher に HTTP 要求を発行します。例えば、管理者や他のアプリケーションによるキャッシュのフラッシュを可能にする AEM アプリケーションを作成できます。

この HTTP 要求によって、Dispatcher が特定のファイルをキャッシュから削除します。その後、オプションで、Dispatcher が新しいコピーを使用してキャッシュを更新します。

### キャッシュされたファイルの削除 {#delete-cached-files}

HTTP 要求を発行することによって、Dispatcher がキャッシュからファイルを削除します。Dispatcher は、そのページに対するクライアント要求を受信した場合にのみ、ファイルを再びキャッシュします。このようなやり方でキャッシュファイルを削除する方法は、同じページに対する要求を同時に受信する可能性が低い Web サイトに適しています。

HTTP 要求の形式は以下のとおりです。

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
CQ-Handle: path-pattern
Content-Length: 0
```

Dispatcher は、`CQ-Handler` ヘッダーの値に一致する名前を持つ、キャッシュされたファイルおよびフォルダーをフラッシュ（削除）します。例えば、`/content/geomtrixx-outdoors/en` という `CQ-Handle` は、以下のアイテムに一致します。

* `en` ディレクトリ内の `geometrixx-outdoors` という名前を持つ（あらゆるファイル拡張子の）すべてのファイル

* en ディレクトリ（存在する場合、キャッシュされたページのサブノードのレンダリングを格納）の下にある「`_jcr_content`」という名前のすべてのディレクトリ

Dispatcher キャッシュ内のその他すべてのファイル（または、`/statfileslevel` 設定によっては特定のレベルまで）は、`.stat` ファイルにアクセスすることによって無効化されます。このファイルの最終変更日が、キャッシュされたドキュメントの最終更新日と比較され、`.stat` ファイルのほうが新しい場合はドキュメントが再取得されます。詳しくは、[フォルダーレベルでのファイルの無効化](dispatcher-configuration.md#main-pars_title_26)を参照してください。

追加の `CQ-Action-Scope: ResourceOnly` ヘッダーを送信することによって、無効化（つまり .stat ファイルへのアクセス）を防ぐことができます。この方法を使用すると、動的に作成され、キャッシュから独立して定期的にフラッシュする必要がある JSON データのように、キャッシュの他の部分を無効化することなく、特定のリソースをフラッシュできます（ニュースや株式相場などを表示するためにサードパーティシステムから取得されるデータなど）。

### ファイルの削除と再キャッシュ {#delete-and-recache-files}

HTTP 要求を発行することによって、Dispatcher がキャッシュされているファイルを削除し、そのファイルを即座に取得して再キャッシュします。Web サイトが同じページに対するクライアント要求を同時に受信する可能性が高い場合は、ファイルを削除して即座に再キャッシュします。即時再キャッシュにより、Dispatcher は同時のクライアント要求それぞれに対して一度ずつではなく、1 回だけページを取得してキャッシュします。

**注意：**&#x200B;ファイルの削除と再キャッシュは、パブリッシュインスタンス上でのみ実行してください。オーサーインスタンスから実行すると、リソースを公開する前に再キャッシュが試みられた場合に、競合状態が発生します。

HTTP 要求の形式は以下のとおりです。

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate   
`Content-Type: text/plain  
CQ-Handle: path-pattern  
Content-Length: numchars in bodypage_path0

page_path1 
...  
page_pathn
```

即時再キャッシュするページのパスは、メッセージ本文に別々の行として一覧表示されます。`CQ-Handle` の値は、再キャッシュするページを無効化するページのパスです。（[キャッシュ](dispatcher-configuration.md#main-pars_146_44_0010)設定項目の `/statfileslevel` パラメーターを参照してください）。以下の HTTP 要求メッセージの例では、`/content/geometrixx-outdoors/en.html page` ページを削除して再キャッシュします。

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
Content-Type: text/plain   
CQ-Handle: /content/geometrixx-outdoors/en/men.html  
Content-Length: 36

/content/geometrixx-outdoors/en.html
```

### サンプルのフラッシュサーブレット {#example-flush-servlet}

以下のコードは、無効化要求を Dispatcher に送信するサーブレットを実装するものです。このサーブレットは、`handle` パラメーターと `page` パラメーターを含む要求メッセージを受信します。これらのパラメーターはそれぞれ、`CQ-Handle` ヘッダーの値と再キャッシュするページのパスを提供します。サーブレットは、その値を使用して、Dispatcher 向けの HTTP 要求を組み立てます。

サーブレットをパブリッシュインスタンスにデプロイすると、以下の URL によって、Dispatcher が /content/geometrixx-outdoors/en.html ページを削除し、新しいコピーをキャッシュします。

`10.36.79.223:4503/bin/flushcache/html?page=/content/geometrixx-outdoors/en.html&handle=/content/geometrixx-outdoors/en/men.html`

>[!NOTE]
>
>このサンプルのサーブレットは安全ではありません。HTTP POST 要求メッセージの使用法を説明することだけを目的としたものです。実際のソリューションでは、サーブレットへのアクセスをセキュリティ保護してください。


```java
package com.adobe.example;

import org.apache.felix.scr.annotations.Component;
import org.apache.felix.scr.annotations.Service;
import org.apache.felix.scr.annotations.Property;

import org.apache.sling.api.SlingHttpServletRequest;
import org.apache.sling.api.SlingHttpServletResponse;
import org.apache.sling.api.servlets.SlingSafeMethodsServlet;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import org.apache.commons.httpclient.*;
import org.apache.commons.httpclient.methods.PostMethod;
import org.apache.commons.httpclient.methods.StringRequestEntity;

@Component(metatype=true)
@Service
public class Flushcache extends SlingSafeMethodsServlet {

 @Property(value="/bin/flushcache")
 static final String SERVLET_PATH="sling.servlet.paths";

 private Logger logger = LoggerFactory.getLogger(this.getClass());

 public void doGet(SlingHttpServletRequest request, SlingHttpServletResponse response) {
  try{ 
      //retrieve the request parameters
      String handle = request.getParameter("handle");
      String page = request.getParameter("page");

      //hard-coding connection properties is a bad practice, but is done here to simplify the example
      String server = "localhost"; 
      String uri = "/dispatcher/invalidate.cache";

      HttpClient client = new HttpClient();

      PostMethod post = new PostMethod("https://"+host+uri);
      post.setRequestHeader("CQ-Action", "Activate");
      post.setRequestHeader("CQ-Handle",handle);
   
      StringRequestEntity body = new StringRequestEntity(page,null,null);
      post.setRequestEntity(body);
      post.setRequestHeader("Content-length", String.valueOf(body.getContentLength()));
      client.executeMethod(post);
      post.releaseConnection();
      //log the results
      logger.info("result: " + post.getResponseBodyAsString());
      }
  }catch(Exception e){
      logger.error("Flushcache servlet exception: " + e.getMessage());
  }
 }
}
```

