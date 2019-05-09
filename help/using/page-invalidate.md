---
title: AEM からのキャッシュされたページの無効化
seo-title: Adobe AEMからキャッシュされたページの無効化
description: 効率的なキャッシュ管理を実現するために、ディスパッチャーとAEM間のインタラクションを設定する方法について説明します。
seo-description: Adobe AEMディスパッチャーとAEMのインタラクションを設定して、キャッシュ管理を有効にする方法について説明します。
uuid: 66533299-55c0-4864-9beb-77e281af9359
cmgrlastmodified: 01.11.2007082229[ahtmoz]
pageversionid: '1193211344162'
template: /apps/docs/templates/contentpage
contentOwner: ユーザーは、
products: SG_ PERANDATEMENTMANAGER/CLACKER
topic-tags: dispatcher
content-type: リファレンス
discoiquuid: 79cd94be- a6bc-4d34- bfe9-393b4107925c
translation-type: tm+mt
source-git-commit: 76cffbfb616cd5601aed36b7076f67a2faf3ed3b

---


# AEM からのキャッシュされたページの無効化 {#invalidating-cached-pages-from-aem}

AEMでディスパッチャーを使用する場合、有効なキャッシュ管理を有効にするには、インタラクションを設定する必要があります。環境によっては、この設定でパフォーマンスを向上させることができます。

## AEM ユーザーアカウントの設定 {#setting-up-aem-user-accounts}

デフォルトの `admin` ユーザーアカウントを使用して、デフォルトでインストールされているレプリケーションエージェントを認証します。複製エージェントで使用する専用ユーザーアカウントを作成する必要があります。 [](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps)

詳しくは、AEM [Securityチェックリストの&quot;Configure Replication and Transport Users](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps) 」セクションを参照してください。

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

ページのアクティベーション時にディスパッチャーのキャッシュを無効にするために、AEM作成者インスタンス上で複製エージェントを設定するには、次の手順を使用します。

1. AEMツールコンソールを開きます。（`https://localhost:4502/miscadmin#/etc`）
1. 作成者のツール/レプリケーション/エージェントの下に、必要な複製エージェントを開きます。デフォルトでインストールされているディスパッチャーフラッシュエージェントを使用できます。
1. 「編集」をクリックし、「設定」タブで「**有効**」が選択されていることを確認します。

1. （オプション）エイリアスまたはバニティパスの無効化要求を有効にするには **、エイリアスの更新** オプションを選択します。
1. 「トランスポート」タブで、Dispatcher へのアクセスに必要な URI を入力します。\
   標準のディスパッチャーフラッシュエージェントを使用している場合、おそらくホスト名とポートを更新する必要があります。例えば、https://&lt;*DispatcherHost*&gt;:&lt;*portApache*&gt;/dispatcher/invalidate. cache

   **注意:** ディスパッチャーフラッシュエージェントの場合、URIプロパティはパスベースの仮想ホストエントリを使用してファームを区別する場合にのみ使用されます。このフィールドを使用して、無効にするファームをターゲット設定します。例えば、&quot;farm#1&quot;には仮想ホストが `www.mysite.com/path1/*` あり、&quot;farm#2&quot;には仮想ホスト `www.mysite.com/path2/*`があります。最初のファームをターゲット `/path1/invalidate.cache` にし、2番目のファームをターゲット `/path2/invalidate.cache` にするためのURLを使用できます。詳しくは、[複数ドメインでの Dispatcher の使用](dispatcher-domains.md)を参照してください。 

1. 必要に応じて、その他のパラメーターを設定します。
1. &quot;OK&quot;をクリックしてエージェントをアクティブ化します。

または、 [AEM Touch UIからディスパッチャーフラッシュエージェントにアクセスして設定すること](https://helpx.adobe.com/experience-manager/6-2/sites/deploying/using/replication.html#ConfiguringaDispatcherFlushagent)もできます。

バニティURLへのアクセスを有効にする方法について詳しくは、&quot;Vanity URLへのアクセス [の有効化」を参照](dispatcher-configuration.md#enabling-access-to-vanity-urls-vanity-urls)してください。

>[!NOTE]
>
>Dispatcher のキャッシュをフラッシュするエージェントにユーザー名とパスワードを設定する必要はありませんが、これらを設定した場合は基本認証付きで送信されます。

このアプローチには、次の2つの問題があります。

* オーサーインスタンスから Dispatcher へのアクセスが可能である必要があります。その2つの間のアクセスが制限されているネットワーク（ファイアウォールなど）が制限されている場合、この問題は発生しない可能性があります。

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
1. ツール/レプリケーション/エージェントの下に、必要な複製エージェントを下に開きます。デフォルトでインストールされているディスパッチャーフラッシュエージェントを使用できます。
1. 「編集」をクリックし、「設定」タブで「**有効**」が選択されていることを確認します。
1. （オプション）エイリアスまたはバニティパスの無効化要求を有効にするには **、エイリアスの更新** オプションを選択します。
1. 「トランスポート」タブで、Dispatcher へのアクセスに必要な URI を入力します。\
   標準のディスパッチャーフラッシュエージェントを使用している場合、おそらくホスト名とポートを更新する必要があります。例えば、 `http://<dispatcherHost>:<portApache>/dispatcher/invalidate.cache`

   **注意:** ディスパッチャーフラッシュエージェントの場合、URIプロパティはパスベースの仮想ホストエントリを使用してファームを区別する場合にのみ使用されます。このフィールドを使用して、無効にするファームをターゲット設定します。例えば、&quot;farm#1&quot;には仮想ホストが `www.mysite.com/path1/*` あり、&quot;farm#2&quot;には仮想ホスト `www.mysite.com/path2/*`があります。最初のファームをターゲット `/path1/invalidate.cache` にし、2番目のファームをターゲット `/path2/invalidate.cache` にするためのURLを使用できます。詳しくは、[複数ドメインでの Dispatcher の使用](dispatcher-domains.md)を参照してください。 

1. 必要に応じて、その他のパラメーターを設定します。
1. 適用するパブリッシュインスタンスごとに、手順を繰り返します。

設定後、オーサーインスタンスからパブリッシュインスタンスにページをアクティベートすると、このエージェントが標準のレプリケーションを開始します。ログには、次の例のような、パブリッシュサーバーからの要求を示すメッセージが含まれます。

1. `<publishserver> 13:29:47 127.0.0.1 POST /dispatcher/invalidate.cache 200`

## 手動での Dispatcher キャッシュの無効化 {#manually-invalidating-the-dispatcher-cache}

ページをアクティベートせずに Dispatcher キャッシュを無効化（またはフラッシュ）するには、Dispatcher に HTTP 要求を発行します。例えば、管理者や他のアプリケーションによるキャッシュのフラッシュを可能にする AEM アプリケーションを作成できます。

この HTTP 要求によって、Dispatcher が特定のファイルをキャッシュから削除します。その後、オプションで、Dispatcher が新しいコピーを使用してキャッシュを更新します。

### キャッシュされたファイルの削除 {#delete-cached-files}

HTTP 要求を発行することによって、Dispatcher がキャッシュからファイルを削除します。ディスパッチャーは、そのページのクライアントリクエストを受信した場合にのみ、ファイルを再度キャッシュします。この方法でキャッシュされたファイルを削除すると、同じページの要求を同時に受け取らないWebサイトに適しています。

HTTP 要求の形式は以下のとおりです。

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
CQ-Handle: path-pattern
Content-Length: 0
```

Dispatcher は、`CQ-Handler` ヘッダーの値に一致する名前を持つ、キャッシュされたファイルおよびフォルダーをフラッシュ（削除）します。例えば、 `CQ-Handle` 次の項目は `/content/geomtrixx-outdoors/en` 、次の項目と一致します。

* `en` ディレクトリ内の `geometrixx-outdoors` という名前を持つ（あらゆるファイル拡張子の）すべてのファイル

* 「」（存在する場合）の下に「 `_jcr_content`」という名前のディレクトリ（存在する場合、ページのサブノードのキャッシュされたレンダリングが含まれる）

ディスパッチャーキャッシュ内のその他すべてのファイル（ `/statfileslevel` または設定に応じて特定のレベル）は、ファイルに `.stat` タッチすることで無効になります。このファイルの最終変更日は、キャッシュされたドキュメントの最終変更日と比較され、ファイルが `.stat` 新しい場合に再取得されます。詳しくは、[フォルダーレベルでのファイルの無効化](dispatcher-configuration.md#main-pars_title_26)を参照してください。

追加の `CQ-Action-Scope: ResourceOnly` ヘッダーを送信することによって、無効化（つまり .stat ファイルへのアクセス）を防ぐことができます。この方法を使用すると、動的に作成され、キャッシュから独立して定期的にフラッシュする必要がある JSON データのように、キャッシュの他の部分を無効化することなく、特定のリソースをフラッシュできます（ニュースや株式相場などを表示するためにサードパーティシステムから取得されるデータなど）。

### ファイルの削除と再キャッシュ {#delete-and-recache-files}

HTTP 要求を発行することによって、Dispatcher がキャッシュされているファイルを削除し、そのファイルを即座に取得して再キャッシュします。Web サイトが同じページに対するクライアント要求を同時に受信する可能性が高い場合は、ファイルを削除して即座に再キャッシュします。即時再キャッシュにより、Dispatcher は同時のクライアント要求それぞれに対して一度ずつではなく、1 回だけページを取得してキャッシュします。

**注意:** ファイルの削除と再作成は、パブリッシュインスタンスでのみ実行する必要があります。作成者インスタンスから実行すると、リソースが発行される前に、リソースの再カウントを試みると競合条件が発生します。

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

即時再キャッシュするページのパスは、メッセージ本文に別々の行として一覧表示されます。`CQ-Handle` の値は、再キャッシュするページを無効化するページのパスです`/statfileslevel`[（Cache](dispatcher-configuration.md#main-pars_146_44_0010) 設定項目のパラメーターを参照）。次の例のHTTPリクエストメッセージでは、このメッセージを削除および再分類 `/content/geometrixx-outdoors/en.html page`します。

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
Content-Type: text/plain   
CQ-Handle: /content/geometrixx-outdoors/en/men.html  
Content-Length: 36

/content/geometrixx-outdoors/en.html
```

### サンプルのフラッシュサーブレット {#example-flush-servlet}

以下のコードは、無効化要求を Dispatcher に送信するサーブレットを実装するものです。サーブレットは、パラメーターを含む `handle` リクエストメッセージを `page` 受け取ります。これらのパラメーターはそれぞれ、`CQ-Handle` ヘッダーの値と再キャッシュするページのパスを提供します。サーブレットは、その値を使用して、Dispatcher 向けの HTTP 要求を組み立てます。

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

