---
title: Dispatcher の設定
seo-title: Dispatcher の設定
description: ディスパッチャーを設定する方法について説明します。
seo-description: ディスパッチャーを設定する方法について説明します。
uuid: 253ef0f7-2491-4cec- ab22-97439df29fd6
cmgrlastmodified: 01.11.2007082229[ahtmoz]
pageversionid: '1193211344162'
topic-tags: dispatcher
content-type: リファレンス
discoiquuid: aepee8e- bb34-42a7-9a5e- b7d0e848391a
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Dispatcher の設定{#configuring-dispatcher}

>[!NOTE]
>
>Dispatcher のバージョンは AEM とは無関係です。以前のバージョンの AEM のドキュメントに組み込まれている Dispatcher のドキュメントへのリンクをたどると、このページにリダイレクトされる可能性があります。

以下の節では、Dispatcher の様々な設定について説明します。

## IPv4 と IPv6 のサポート {#support-for-ipv-and-ipv}

AEM と Dispatcher のすべての要素は、IPv4 と IPv6 の両方のネットワークにインストールできます。[IPv4 と IPv6](https://helpx.adobe.com/experience-manager/6-3/sites/deploying/using/technical-requirements.html#AdditionalPlatformNotes) を参照してください。

## Dispatcher の設定ファイル {#dispatcher-configuration-files}

デフォルトでは、ディスパッチャーの設定は `dispatcher.any` テキストファイルに格納されますが、インストール時にこのファイルの名前と場所を変更できます。

設定ファイルには、Dispatcher の動作を制御する一連の単一値または複数値プロパティが含まれます。

* プロパティ名にはスラッシュが付いてい `/`ます。
* 複数値のプロパティでは、子項目を括弧で囲み `{ }`ます。

設定例を次に示します。

```xml
# name of the dispatcher
/name "internet-server"

# each farm configures a set off (loadbalanced) renders
/farms
 {
  # first farm entry (label is not important, just for your convenience)
   /website 
     {  
     /clientheaders
       {
       # List of headers that are passed on
       }
     /virtualhosts
       {
       # List of URLs for this Web site
       }
     /sessionmanagement 
       {
       # settings for user authentification
       }
     /renders
       {
       # List of AEM instances that render the documents
       }
     /filter
       {
       # List of filters
       }
     /vanity_urls
       {
       # List of vanity URLs
       }
     /cache
       {
       # Cache configuration
       /rules
         {
         # List of cachable documents
         }
       /invalidate
         {
         # List of auto-invalidated documents
         }
       }
     /statistics
       {
       /categories
         {
         # The document categories that are used for load balancing estimates
         }
       }
     /stickyConnectionsFor "/myFolder"
     /health_check
       {
       # Page gets contacted when an instance returns a 500
       }
     /retryDelay "1"
     /numberOfRetries "5"
     /unavailablePenalty "1"
     /failover "1"
     }
 }
```

設定に影響を与える他のファイルを含めることができます。

* 設定ファイルが大きい場合は、それを複数の小さなファイル（管理しやすい）に分割することができます。
* 自動生成されたファイル。

例えば、MyFarmファイルを含めるには、/farmsの設定に次のコードを使用します。

```xml
/farms
  {
  $include "myFarm.any"
  }
```

含めるファイルの範囲を指定するには、アスタリスク（「*」）をワイルドカードとして使用します。

例えば、`farm_1.any` ～ `farm_5.any` のファイルにファーム 1 ～ 5 が設定されている場合、これらのファイルを次のように含めることができます。

```xml
/farms
  {
  $include "farm_*.any"
  }
```

## 環境変数の使用 {#using-environment-variables}

値を直接記述する代わりに、dispatcher.any ファイルの単一値プロパティに環境変数を使用できます。環境変数の値を含めるには、形式を使用 `${variable_name}`します。

例えば、dispatcher. anyファイルがキャッシュディレクトリと同じディレクトリにある場合、 [docroot](dispatcher-configuration.md#main-pars-title-30) プロパティの次の値を使用できます。

```xml
/docroot "${PWD}/cache"
```

もう 1 つの例として、AEM パブリッシュインスタンスのホスト名を格納する `PUBLISH_IP` という環境変数を作成した場合は、[/renders](dispatcher-configuration.md#main-pars-127-25-0008) プロパティの次の設定を使用できます。

```xml
/renders {
  /0001 {
    /hostname "${PUBLISH_IP}"
    /port "8443"
  }
}
```

## ディスパッチャーインスタンスの名前付け {#naming-the-dispatcher-instance-name}

プロパティを `/name` 使用して、ディスパッチャーインスタンスを識別する一意の名前を指定します。`/name` プロパティは、設定構造の最上位プロパティです。

## ファームの定義 {#defining-farms-farms}

`/farms` このプロパティは、各セットが異なるWebサイトまたはURLに関連付けられている1つまたは複数のディスパッチャー動作を定義します。`/farms` このプロパティには、単一のファームまたは複数のファームを含めることができます。

* Dispatcher ですべての Web ページまたは Web サイトを同じ方法で処理する場合は、単一のファームを使用します。
* Web サイトの異なる領域または異なる Web サイトに異なる Dispatcher 動作が必要な場合は、複数のファームを作成します。

`/farms` プロパティは、設定構造の最上位プロパティです。ファームを定義するには `/farms` 、プロパティに子プロパティを追加します。Dispatcher インスタンス内でファームを一意に識別するプロパティ名を使用してください。

`/*farmname*` プロパティは複数値であり、ディスパッチャーの動作を定義する他のプロパティが含まれています。

* ファームを適用するページの URL。
* ドキュメントのレンダリングに使用する 1 つまたは複数のサービス URL（一般的には AEM パブリッシュインスタンスの URL）。
* 複数のドキュメントレンダラーのロードバランシングに使用する統計。
* キャッシュするファイルおよびその場所など、その他の動作。

値には、任意の英数字（a ～ z、0 ～ 9）を含めることができます。次の例は、2つのファームという名前 `/daycom` の2つの農場の定義を示し `/docsdaycom`ています。

```xml
#name of dispatcher
/name "day sites"

#farms section defines a list of farms or sites
/farms
{
   /daycom
   {
       ...
   }
   /docdaycom
   {
      ...
   }
}
```

>[!NOTE]
>
>複数のレンダリングファームを使用する場合、リストはボトムアップします。これは、Webサイトの [仮想ホスト](dispatcher-configuration.md#main-pars-117-15-0006) を定義するときに特に重要です。

各ファームプロパティには、以下の子プロパティを含めることができます。

| プロパティ名 | 説明 |
|--- |--- |
| [/homepage](#specify-a-default-page-iis-only-homepage) | デフォルトのホームページ（オプション）（IIS のみ） |
| [/clientheaders](#specifying-the-http-headers-to-pass-through-clientheaders) | 通過させるクライアント HTTP 要求のヘッダー。 |
| [/virtualhosts](#identifying-virtual-hosts-virtual-hosts) | このファームの仮想ホスト。 |
| [/sessionmanagement](#enabling-secure-sessions-session-management) | セッション管理および認証のサポート。 |
| [/renders](#defining-page-renderers-renders) | レンダリングされたページを提供するサーバー（一般的には AEM パブリッシュインスタンス）。 |
| [/filter](#configuring-access-to-content-filter) | Dispatcher がアクセスできる URL を定義します。 |
| [/vanity_urls](#enabling-access-to-vanity-urls-vanity-urls) | バニティー URL へのアクセスを設定します。 |
| [/propagateSyndPost](#forwarding-syndication-requests-propagate-syndpost) | シンジケーション要求の転送のサポート。 |
| [/cache](#configuring-the-dispatcher-cache-cache) | キャッシュ動作を設定します。 |
| [/statistics](#configuring-load-balancing-statistics) | ロードバランシング計算用の統計カテゴリの定義。 |
| [/stickyConnectionsFor](#identifying-a-sticky-connection-folder-sticky-connections-for) | スティッキードキュメントを格納するフォルダー。 |
| [/health_check](#specifying-a-health-check-page) | サーバーの可用性を判断するために使用する URL。 |
| [/retryDelay](#specifying-the-page-retry-delay) | 失敗した接続を再試行するまでの遅延。 |
| [/unavailablePenalty](#reflecting-server-unavailability-in-dispatcher-statistics) | ロードバランシング計算用の統計に影響を与えるペナルティ。 |
| [/failover](#using-the-fail-over-mechanism) | 元の要求が失敗した場合に異なるレンダーに要求を再送信します。 |

## デフォルトページの指定（IIS のみ） - /homepage {#specify-a-default-page-iis-only-homepage}

>[!CAUTION]
>
>`/homepage`パラメーター（IISのみ）が動作しなくなりました。代わりに [、IIS URL Rewrite Moduleを使用](https://docs.microsoft.com/en-us/iis/extensions/url-rewrite-module/using-the-url-rewrite-module)してください。
>
>Apacheを使用している場合は `mod_rewrite` 、モジュールを使用する必要があります。詳しくは、Apache `mod_rewrite` Webサイトのドキュメント（ [例: Apache2.4](https://httpd.apache.org/docs/current/mod/mod_rewrite.html)）を参照してください。使用する場合 `mod_rewrite`、フラグ** [のパスを使用することをお勧めします| PT&#39;（パスを次のハンドラーに渡す）](https://helpx.adobe.com/dispatcher/kb/DispatcherModReWrite.html)**を使用して、書き直しエンジン `uri` を強制的に `request_rec``filename` フィールドの値に設定します。

<!-- 

Comment Type: draft

<p>The optional /homepage parameter specifies the page that Dispatcher returns when a client requests an undeterminable page or file.</p> 
<p>Typically this situation occurs when a user specifies an URL for which neither IIS or AEM provides an automatic redirection target. For example, if the AEM render instance is shut down after the content is cached, the content redirect URL is unavailable.</p> 
<p>The following example configuration displays the <span class="code">index.html</span> page in such circumstances:</p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  /homepage&nbsp;"/index.html" 
</codeblock>

 -->

<!-- 

Comment Type: draft

<p>The <span class="code">/homepage</span> section is located inside the <span class="code">/farms</span> section, for example:<br /> </p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  #name&nbsp;of&nbsp;dispatcher!!discoiqbr!!/name&nbsp;"day&nbsp;sites"!!discoiqbr!!!!discoiqbr!!#farms&nbsp;section&nbsp;defines&nbsp;a&nbsp;list&nbsp;of&nbsp;farms&nbsp;or&nbsp;sites!!discoiqbr!!/farms!!discoiqbr!!{!!discoiqbr!!&nbsp;&nbsp;&nbsp;/daycom!!discoiqbr!!&nbsp;&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;/homepage&nbsp;"/index.html"!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...!!discoiqbr!!&nbsp;&nbsp;&nbsp;}!!discoiqbr!!&nbsp;&nbsp;&nbsp;/docdaycom!!discoiqbr!!&nbsp;&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...!!discoiqbr!!&nbsp;&nbsp;&nbsp;}!!discoiqbr!!} 
</codeblock>

 -->

## 通過させる HTTP ヘッダーの指定 {#specifying-the-http-headers-to-pass-through-clientheaders}

`/clientheaders` このプロパティは、ディスパッチャーがクライアントHTTPリクエストからレンダラー（AEMインスタンス）に渡すHTTPヘッダーのリストを定義します。

デフォルトでは、Dispatcher から AEM インスタンスに標準の HTTP ヘッダーが転送されます。インスタンスによっては、追加のヘッダーを転送したり、特定のヘッダーを削除したりできます。

* AEM インスタンスが HTTP 要求で予期するヘッダー（カスタムヘッダーなど）を追加します。
* ヘッダー（Web サーバーに対してのみ重要な認証ヘッダーなど）を削除します。

通過させる一連のヘッダーをカスタマイズした場合は、ヘッダーの完全なリスト（通常はデフォルトで含まれるヘッダーを含む）を指定する必要があります。

例えば、発行インスタンスのページアクティベート要求を処理するディスパッチャーインスタンスには、セクション内の `PATH` ヘッダーが必要 `/clientheaders` です。`PATH` ヘッダーは、複製エージェントとディスパッチャー間の通信を有効にします。

次のコードは、次のような設定例 `/clientheaders`です。

```shell
/clientheaders
  {
  "CSRF-Token"
  "X-Forwarded-Proto"
  "referer"
  "user-agent"
  "authorization"
  "from"
  "content-type"
  "content-length"
  "accept-charset"
  "accept-encoding"
  "accept-language"
  "accept"
  "host"
  "if-match"
  "if-none-match"
  "if-range"
  "if-unmodified-since"
  "max-forwards"
  "proxy-authorization"
  "proxy-connection"
  "range"
  "cookie"
  "cq-action"
  "cq-handle"
  "handle"
  "action"
  "cqstats"
  "depth"
  "translate"
  "expires"
  "date"
  "dav"
  "ms-author-via"
  "if"
  "lock-token"
  "x-expected-entity-length"
  "destination"
  "PATH"
  }
```

## 仮想ホストの識別 {#identifying-virtual-hosts-virtualhosts}

`/virtualhosts` このプロパティは、ディスパッチャーがこのファームで受け付けるすべてのホスト名とURIの組み合わせのリストを定義します。ワイルドカードとしてアスタリスク（「*」）文字を使用できます。/`virtualhosts` プロパティの値には、次の形式を使用します。

```xml
[scheme]host[uri][*]
```

* `scheme`:（オプション `https://` ） `https://.`
* `host`：ホストコンピューターの名前または IP アドレスと、必要な場合はポート番号（ [https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23)を参照）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23)
* `uri`：（オプション）リソースへのパス。

次の例の設定では、myCompanyの.comドメインおよび. chドメインおよびmyCategoriesのすべてのドメインに対するリクエストを処理します。

```xml
   /virtualhosts
    {
    "www.myCompany.com"
    "www.myCompany.ch"
    "www.mySubDivison.*"
    }
```

次の設定は *、すべて* のリクエストを処理します。

```xml
   /virtualhosts
    {
    "*"
    }
```

### 仮想ホストの解決 {#resolving-the-virtual-host}

ディスパッチャーがHTTPまたはHTTPSリクエストを受信すると、リクエストのヘッダーと `host,``uri``scheme` ヘッダーに最も一致する仮想ホスト値が検出されます。ディスパッチャーは、次の順序で `virtualhosts` プロパティの値を評価します。

* dispatcher.any ファイル内の一番下のファームから始め、上方向へ進みます。
* ファームごとに、`virtualhosts` プロパティの最上位の値から始め、値のリストの下方向へ進みます。

Dispatcher は、以下の方法で最良一致の仮想ホスト値を探します。

* 要求の `host`、`scheme`、`uri` の 3 つすべてに一致する、最初に見つかった仮想ホストを使用します。
* 値と `virtualhosts` リクエスト `scheme` の両方に一致する `uri` 部分と、両方 `scheme` の `uri` 値が含まれていない場合は、リクエストと `host` 一致する最初の仮想ホストが使用されます。
* 要求の host に一致する host 部を持つ `virtualhosts` 値がない場合は、最上位ファームの最上位の仮想ホストを使用します。

したがって、デフォルトの仮想ホストは dispatcher.any ファイルの最上位ファームの `virtualhosts` プロパティの一番上に配置する必要があります。

### 仮想ホストの解決の例 {#example-virtual-host-resolution}

次の例は、ディスパッチャーの2つのファームを定義するdispatcher. anyファイルからのスニペットを表し、各ファームに `virtualhosts` プロパティを定義しています。

```xml
/farms
  {
  /myProducts 
    { 
    /virtualhosts
      {
      "www.mycompany.com"
      }
    /renders
      {
      /hostname "server1.myCompany.com"
      /port "80"
      }
    }
  /myCompany 
    { 
    /virtualhosts
      {
      "www.mycompany.com/products/*"
      }
    /renders
      {
      /hostname "server2.myCompany.com"
      /port "80"
      }
    }
  }
```

以下の表は、この例の場合に、指定された HTTP 要求がどの仮想ホストへと解決されるかを示しています。

| 要求 URL | 解決される仮想ホスト |
|---|---|
| `https://www.mycompany.com/products/gloves.html` | `www.mycompany.com/products/*;` |
| `https://www.mycompany.com/about.html` | `www.mycompany.com` |

## セキュアセッションの有効化 - /sessionmanagement {#enabling-secure-sessions-sessionmanagement}

>[!CAUTION]
>
>`/allowAuthorized`****`"0"``/cache` を指定します。

セキュリティで保護されたセッションを作成して、ユーザーがログインしてファーム内の任意のページにアクセスする必要があるようにします。ログインすると、ユーザーはファームのすべてのページにアクセスできます。閉じられたユーザーグループ（CUG）でのこの機能の使用については、[閉じられたユーザーグループの作成](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/cug.html#CreatingTheUserGroupToBeUsed)を参照してください。

`/sessionmanagement` プロパティは、サブプロパティ `/farms`です。

>[!CAUTION]
>
>Web サイトのセクションによってアクセス要件が異なる場合は、複数のファームを定義する必要があります。

**/sessionmanagement** には、いくつかのサブパラメーターがあります。

**/directory** （必須）

セッション情報を格納するディレクトリ。ディレクトリが存在しない場合は、作成されます。

**/encode** （オプション）

セッション情報のエンコード方法。md5 アルゴリズムを使用した暗号化には &quot;md5&quot; を、16 進エンコーディングには &quot;hex&quot; を使用します。セッションデータを暗号化すると、ファイルシステムにアクセスできるユーザーでも、セッション内容を読み取れなくなります。デフォルトは &quot;md5&quot; です。

**/header** （オプション）

認証情報を保存するHTTPヘッダーまたはcookieの名前。httpヘッダーに情報を格納する場合は、を使用 `HTTP:<*header-name*>`します。情報をcookieに保存するには、を使用 `Cookie:<header-name>`します。値 `HTTP:authorization` を指定しない場合は、使用します。

**/timeout** （オプション）

セッションが最後に使用された後にタイムアウトした秒数。指定がない場合は &quot;800&quot; が適用され、ユーザーからの最後の要求から約 13 分でセッションがタイムアウトします。

以下に、設定例を示します。

```xml
/sessionmanagement 
  { 
  /directory "/usr/local/apache/.sessions" 
  /encode "md5" 
  /header "HTTP:authorization" 
  /timeout "800" 
  }
```

## ページレンダラーの定義 {#defining-page-renderers-renders}

/renders プロパティは、ドキュメントをレンダリングするために Dispatcher が要求を送信する URL を定義します。次の例 `/renders` では、レンダリングのための単一のAEMインスタンスを指定します。

```xml
/renders
  {
    /myRenderer
      {
      # hostname or IP of the renderer
      /hostname "aem.myCompany.com"
      # port of the renderer
      /port "4503"
      # connection timeout in milliseconds, "0" (default) waits indefinitely
      /timeout "0"
      }
  }
```

次の /renders セクションの例では、Dispatcher と同じコンピューター上で動作する AEM インスタンスを識別しています。

```xml
/renders
  {
    /myRenderer
     {
     /hostname "127.0.0.1"
     /port "4503"
     }
  }
```

次の /renders セクションの例では、2 つの AEM インスタンス間で平等にレンダリング要求を分配しています。

```xml
/renders
  {
    /myFirstRenderer
      {
      /hostname "aem.myCompany.com"
      /port "4503"
      }
    /mySecondRenderer
      {
      /hostname "127.0.0.1"
      /port "4503"
      }
  }
```

### レンダーのオプション {#renders-options}

**/timeout**

AEMインスタンスにアクセスする接続タイムアウトをミリ秒単位で指定します。デフォルトは &quot;0&quot; で、Dispatcher は無制限に待機します。

**/receiveTimeout**

応答を許可する時間をミリ秒単位で指定します。デフォルトは &quot;600000&quot; で、Dispatcher は 10 分間待機します。&quot;0&quot; に設定すると、タイムアウトが完全になくなります。\
応答ヘッダーの解析中にタイムアウトに達した場合は、HTTP ステータス 504（Bad Gateway）が返されます。応答本文の読み取り中にタイムアウトに達した場合は、Dispatcher は不完全な応答をクライアントに返しますが、作成されたキャッシュファイルがあれば削除します。

**/ipv4**

ディスパッチャーがレンダリングのIPアドレスを取得するために `getaddrinfo` 関数（IPv6の場合）または `gethostbyname` 関数（IPv4の場合）を使用するかどうかを指定します。値 0 を指定すると、`getaddrinfo` が使用されます。値 1 を指定すると、`gethostbyname` が使用されます。デフォルト値は 0 です。

getaddrinfo 関数は、IP アドレスのリストを返します。Dispatcher は、TCP/IP 接続を確立するまで、そのアドレスのリストを繰り返します。したがって、常に同じ順序で IP アドレスのリストを返す getaddrinfo 関数に応じてレンダーホスト名が複数の IP アドレスおよびホストと関連付けられている場合は、ipv4 プロパティが重要になります。このような状況では、Dispatcher が接続する IP アドレスがランダムになるように、gethostbyname 関数を使用してください。

Amazon Elastic Load Balancing（ELB）は、同じ順序になる可能性がある IP アドレスのリストを使用して、getaddrinfo に応答するサービスです。

**/secure**

プロパティが `/secure` &quot;1&quot;の場合、ディスパッチャーはHTTPSを使用してAEMインスタンスと通信します。詳しくは、&quot;Use Dispatcher to [Use SSL](dispatcher-ssl.md#configuring-dispatcher-to-use-ssl)」も参照してください。

**/always-resolve**

ディスパッチャーバージョン **4.1.6**では、次の `/always-resolve` ようにプロパティを設定できます。

* &quot;1&quot;に設定すると、リクエストごとにホスト名が解決されます（ディスパッチャーはIPアドレスをキャッシュしません）。リクエストごとにホスト情報を取得するのに必要な追加呼び出しによって、多少のパフォーマンスが低下する可能性があります。
* プロパティが設定されていない場合、IPアドレスはデフォルトでキャッシュされます。

また、次のサンプルに示すように、このプロパティは、動的IP解像度の問題に実行する場合にも使用できます。

```xml
/rend {
  /0001 {
     /hostname "host-name-here"
     /port "4502"
     /ipv4 "1"
     /always-resolve "1"
     }
  }
```

## コンテンツへのアクセスの設定 {#configuring-access-to-content-filter}

セクションを `/filter` 使用して、ディスパッチャーが受け付けるHTTPリクエストを指定します。それ以外のすべての要求は、エラーコード 404（ページが見つかりません）で Web サーバーに返送されます。セクションが `/filter` 存在しない場合は、すべてのリクエストが受け入れられます。

**注意：**[statfile](dispatcher-configuration.md#main-pars-title-28) に対する要求は常に拒否されます。

>[!CAUTION]
>
>ディスパッチャーを使用してアクセスを制限する場合は [、ディスパッチャーセキュリティチェックリスト](security-checklist.md) を参照してください。AEMのインストールに関するセキュリティの詳細については [、AEM Security Cheklist](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html) もお読みください。

/filter セクションは、HTTP 要求の要求行部分のパターンに応じてコンテンツへのアクセスを拒否または許可する一連のルールで構成されます。/filter セクションに対しては、ホワイトリスト戦略を使用してください。

* まず、すべての要素へのアクセスを拒否します。
* 必要に応じて、コンテンツへのアクセスを許可します。

### フィルターの定義 {#defining-a-filter}

`/filter` セクション内の各項目には、リクエストラインの特定の要素と一致するタイプと、リクエストライン全体に一致するパターンが含まれています。各フィルターには、次のアイテムを含めることができます。

* **タイプ**:これ `/type` は、パターンに一致するリクエストのアクセスを許可または拒否するかどうかを示します。値のいずれか `allow` また `deny`はいずれかを指定できます。

* **リクエストラインの要素:** HTTPリクエスト `/method``/url``/query`の `/protocol` 要求ライン部分のこれらの特定の部分に従ってリクエストをフィルタリングするためのパターン、およびパターンを含めます。要求行全体ではなく、要求行の要素に対してフィルタリングすることをお勧めします。

* **globbプロパティ**: `/glob` このプロパティは、HTTPリクエストの要求行全体と一致するために使用されます。

/glob プロパティについて詳しくは、[glob プロパティのパターンのデザイン](#designing-patterns-for-glob-properties)を参照してください。/glob プロパティでのワイルドカード文字の使用ルールも、要求行の要素を照合するためのパターンに適用されます。

>[!NOTE]
>
>ディスパッチャーバージョン4.2.0では、フィルター設定およびログ機能に関するいくつかの機能強化が追加されました。
>
>* [POSIX の正規表現のサポート](dispatcher-configuration.md#main-pars-title-1996763852)
>* [要求 URL の追加要素のフィルタリングのサポート](dispatcher-configuration.md#main-pars-title-694578373)
>* [トレースログ](dispatcher-configuration.md#main-pars-title-1950006642)
>



#### HTTP 要求の要求行部分 {#the-request-line-part-of-http-requests}

HTTP/1.1では [、リクエストラインを次の](https://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html) ように定義します。

*Method Request-URI HTTP-Version*&lt;CRLF&gt;

&lt; CRLF&gt;文字がキャリッジリターンに続いて、改行が続いています。次の例は、クライアントが Geometrixx-Outdoors サイトの英語ページを要求したときに受信する要求行です。

GET /content/geometrixx-outdoors/en.html HTTP.1.1&lt;CRLF&gt;

パターンでは、リクエストラインのスペース文字と&lt; CRLF&gt;文字を考慮する必要があります。

#### サンプルフィルター：すべて拒否 {#example-filter-deny-all}

次のサンプルのフィルターセクションによって、Dispatcher がすべてのファイルに対する要求を拒否します。すべてのファイルへのアクセスを拒否してから、特定の領域へのアクセスを許可してください。

```xml
  /0001  { /glob "*" /type "deny" }
```

明示的に拒否された領域への要求に対して、「404 error code (page not found)」が返されます。

#### サンプルフィルター：特定の領域へのアクセスを拒否 {#example-filter-deny-acess-to-specific-areas}

フィルターを使用して、サンプルの ASP ページの各種要素と、パブリッシュインスタンス内の機密領域へのアクセスを拒否することもできます。次のフィルターは、ASPページへのアクセスを拒否します。

```xml
/0002  { /type "deny" /url "*.asp"  }
```

#### サンプルフィルター：POST 要求の有効化 {#example-filter-enable-post-requests}

次のサンプルフィルターを使用して、POST メソッドでフォームデータを送信できます。

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002 { /type "allow" /method "POST" /url "/content/[.]*.form.html" }
}
```

#### サンプルフィルター：ワークフローコンソールへのアクセスを許可 {#example-filter-allow-access-to-the-workflow-console}

次の例は、Workflow コンソールへの外部アクセスを拒否するためのフィルターです。

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002  {  /type "allow"  /url "/libs/cq/workflow/content/console*"  }
}
```

パブリッシュインスタンスで Web アプリケーションコンテキスト（例えば publish）を使用する場合は、このコンテキストをフィルター定義に追加できます。

```xml
/0003   { /type "deny"  /url "/publish/libs/cq/workflow/content/console/archive*"  }
```

制限された領域内の単独のページにアクセスする必要がある場合は、そのページへのアクセスを許可できます。例えば、ワークフローコンソール内の「アーカイブ」タブへのアクセスを許可する場合は、次のセクションを追加します。

```xml
/0004  { /type "allow"  /url "/libs/cq/workflow/content/console/archive*"   }
```

>[!NOTE]
>
>複数のフィルターパターンを 1 つの要求に適用した場合は、最後に適用されたフィルターパターンが有効になります。

#### サンプルフィルター：正規表現の使用 {#example-filter-using-regular-expressions}

このフィルターは、ここで単一引用符の間に定義されている正規表現を使用して、非公開のコンテンツディレクトリで拡張子を有効にします。

```xml
/005  {  /type "allow" /extension '(css|gif|ico|js|png|swf|jpe?g)' }
```

#### サンプルフィルター：要求 URL の追加要素のフィルタリング {#example-filter-filter-additional-elements-of-a-request-url}

ディスパッチャー4.2.0で導入された機能強化の1つは、リクエストURLの追加要素をフィルタリングする機能です。導入された新しい要素には、次のものがあります。

* path
* セレクター
* 拡張子
* サフィックス

これらは、フィルタリングルールに同じ名前のプロパティを追加することで設定できます。 `/path`、 `/selectors`および `/extension``/suffix` 、

パス、セレクターおよび拡張子のフィルターを使用して `/content` 、パスとサブツリーからコンテンツグラビングをブロックするルールの例を以下に示します。

```xml
/006 {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy)'
        /extension '(json|xml|html)'
        }
```

### サンプルの /filter セクション {#example-filter-section}

Dispatcher を設定する際は、できる限り外部アクセスを制限してください。次の例では、外部の訪問者に最小限のアクセス権を付与します。

* `/content`
* その他のコンテンツ（デザインやクライアントライブラリなど）例えば、次のように指定します。

   * `/etc/designs/default*`
   * `/etc/designs/mydesign*`

フィルターを作成した後、ページアクセス [](dispatcher-configuration.md#main-pars-title-19) をテストしてAEMインスタンスが安全であることを確認します。

ディスパッチャーの次の/filterセクションは、 [ディスパッチャー設定](dispatcher-configuration.md) ファイルのベースとして使用できます。

このサンプルは、Dispatcher に付属するデフォルトの設定ファイルをベースとしており、実稼動環境での使用例の役割を果たすことを目的としています。#のプレフィックスが付いた項目は非アクティブ化（コメントアウト）されています。これらの項目のいずれかをアクティブにすると、セキュリティへの影響を持つ可能性があるので、これらの行をアクティブ化することに注意してください。

すべてに対するアクセスを拒否してから、特定の（限られた）要素へのアクセスを許可してください。

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:32:37.986-0400

<p>We should mention the config files that are shipped with the dispatcher distribution and only give a few examples here. This aims to avoid confusion and reduce content maintenance.<br /> </p>

 -->

```xml
  /filter
      {
      # Deny everything first and then allow specific entries
      /0001 { /type "deny" /glob "*" }
      
      # Open consoles
#     /0011 { /type "allow" /url "/admin/*"  }  # allow servlet engine admin
#     /0012 { /type "allow" /url "/crx/*"    }  # allow content repository
#     /0013 { /type "allow" /url "/system/*" }  # allow OSGi console
        
      # Allow non-public content directories
#     /0021 { /type "allow" /url "/apps/*"   }  # allow apps access
#     /0022 { /type "allow" /url "/bin/*"    }
      /0023 { /type "allow" /url "/content*" }  # disable this rule to allow mapped content only
      
#     /0024 { /type "allow" /url "/libs/*"   }
#     /0025 { /type "deny"  /url "/libs/shindig/proxy*" } # if you enable /libs close access to proxy

#     /0026 { /type "allow" /url "/home/*"   }
#     /0027 { /type "allow" /url "/tmp/*"    }
#     /0028 { /type "allow" /url "/var/*"    }

      # Enable extensions in non-public content directories, using a regular expression
      /0041
        {
        /type "allow"
        /extension '(css|gif|ico|js|png|swf|jpe?g)'
        }

      # Enable features 
      /0062 { /type "allow" /url "/libs/cq/personalization/*"  }  # enable personalization

      # Deny content grabbing, on all accessible pages, using regular expressions
      /0081
        {
        /type "deny"
        /selectors '((sys|doc)view|query|[0-9-]+)'
        /extension '(json|xml)'
        }
      # Deny content grabbing for /content and its subtree
      /0082
        {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy)'
        /extension '(json|xml|html)'
        }

#     /0087 { /type "allow" /method "GET" /extension 'json' "*.1.json" }  # allow one-level json requests
}
```

>[!NOTE]
>
>Apache と共に使用する場合は、Dispatcher モジュールの DispatcherUseProcessedURL プロパティに応じてフィルター URL パターンをデザインしてください[（Apache Web Server-ディスパッチャー用のApache Web Serverの設定を参照](dispatcher-install.md#main-pars-55-35-1022)）。

>[!NOTE]
>
>ダイナミックメディアに関するフィルター 0030 および 0031 は、AEM 6.0 以上に適用できます。

アクセスを拡張する場合は、以下の推奨事項について検討します。

* CQバージョン5.4または `/admin` それ以前のバージョン *を使用している場合、外部アクセスは常に* 完全に無効にする必要があります。

* ファイルへのアクセスを許可する場合は、注意が必要 `/libs`です。アクセスは個別に許可する必要があります。
* レプリケーション設定へのアクセスを拒否して、表示できないようにします。

   * `/etc/replication.xml*`
   * `/etc/replication.infinity.json*`

* Google Gadgets のリバースプロキシへのアクセスを拒否します。

   * `/libs/opensocial/proxy*`

インストールによっては、追加のリソースが存在 `/libs`するか `/apps` 、その他の場所にある必要があります。外部からアクセスされるリソースを判別するための方法として、`access.log` ファイルを使用することができます。

>[!CAUTION]
>
>コンソールおよびディレクトリへのアクセスによって、実稼動環境でセキュリティのリスクが発生する可能性があります。このようなアクセスに関する正当な理由が明確でない場合は、これらのエントリを非アクティブ化（コメントアウト）のままにしておいてください。

>[!CAUTION]
>
>発行環境でレポート [を使用する場合は、外部訪問者](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/reporting.html#UsingReportsinaPublishEnvironment)`/etc/reports` へのアクセスを拒否するようにディスパッチャーを設定する必要があります。

### クエリ文字列の制約 {#restricting-query-strings}

ディスパッチャーバージョン4.1.5以降は、セクションを `/filter` 使用してクエリ文字列を制限します。クエリ文字列を明示的に許可し、フィルター要素を使用 `allow` して汎用的な許可を除外することを強くお勧めします。

1つのエントリに *は、gbと* いう *メソッド*、*URL*、*クエリ* 、 *バージョンの組み合わせを含めることができますが、両方ではありませ* ん。次の例では、クエリ文字列を `a=*` 使用して `/etc` 、ノードに解決されるURLに対してその他のクエリ文字列をすべて拒否します。

```xml
/filter {
 /0001 { /type "deny" /method "POST" /url "/etc/*" }
 /0002 { /type "allow" /method "GET" /url "/etc/*" /query "a=*" }
}
```

>[!NOTE]
>
>ルールにa `/query`が含まれている場合、クエリ文字列を含むリクエストのみが一致し、指定されたクエリパターンに一致します。
>
>上記の例では、クエリ `/etc` 文字列のないリクエストを許可する場合、次のルールが必要になります。


```xml
/filter {  
>/0001 { /type "deny" /method “*" /url "/path/*" }  
>/0002 { /type "allow" /method "GET" /url "/path/*" }  
>/0003 { /type “deny" /method "GET" /url "/path/*" /query "*" }  
>/0004 { /type "allow" /method "GET" /url "/path/*" /query "a=*" }  
}  
```

### Dispatcher のセキュリティのテスト {#testing-dispatcher-security}

Dispatcher のフィルターは、AEM パブリッシュインスタンス上の以下のページおよびスクリプトへのアクセスをブロックする必要があります。Web ブラウザーを使用して、サイト訪問者として以下のページを開こうと試み、コード 404 が返されることを確認してください。それ以外の結果が得られた場合は、フィルターを調整してください。

/content/add_valid_page.html?debug=layout に対しては、通常のページレンダリングが表示されます。


* /admin
* /system/console
* /dav/crx.default
* /crx
* /bin/crxde/logs
* /jcr:system/jcr:versionStorage.json
* /_jcr_system/_jcr_versionStorage.json
* /libs/wcm/core/content/siteadmin.html
* /libs/collab/core/content/admin.html
* /libs/cq/ui/content/dumplibs.html
* /var/linkchecker.html
* /etc/linkchecker.html
* /home/users/a/admin/profile.json
* /home/users/a/admin/profile.xml
* /libs/cq/core/content/login.json
* /content/../libs/foundation/components/text/text.jsp
* /content/.{.}/libs/foundation/components/text/text.jsp
* /apps/sling/config/org.apache.felix.webconsole.internal.servlet.OsgiManager.config/jcr%3acontent/jcr%3adata
* /libs/foundation/components/primary/cq/workflow/components/participants/json.GET.servlet
* /content.pages.json
* /content.languages.json
* /content.blueprint.json
* /content.-1.json
* /content.10.json
* /content.infinity.json
* /content.tidy.json
* /content.tidy.-1.blubber.json
* /content/dam.tidy.-100.json
* /content/content/geometrixx.sitemap.txt
* /content/add_valid_page.query.json?statement=//*
* /content/add_valid_page.qu%65ry.js%6Fn?statement=//*
* /content/add_valid_page.query.json?statement=//*[@ TransportPassword]/（@ TransportPassword%20）|%20@transportUri%20|%20@transportUser）
* /content/add_valid_path_to_a_page/_jcr_content.json
* /content/add_valid_path_to_a_page/jcr:content.json
* /content/add_valid_path_to_a_page/_jcr_content.feed
* /content/add_valid_path_to_a_page/jcr:content.feed
* /content/add_valid_path_to_a_page/pagename._jcr_content.feed
* /content/add_valid_path_to_a_page/pagename.jcr:content.feed
* /content/add_valid_path_to_a_page/pagename.docview.xml
* /content/add_valid_path_to_a_page/pagename.docview.json
* /content/add_valid_path_to_a_page/pagename.sysview.xml
* /etc.xml
* /content.feed.xml
* /content.rss.xml
* /content.feed.html
* /content/add_valid_page.html?debug=layout
* /projects
* /tagging
* /etc/replication.html
* /etc/cloudservices.html
* /ようこそ

端末またはコマンドプロンプトで次のコマンドを発行して、匿名での書き込みアクセスが可能かどうかを判断します。ノードにはデータを書き込みできません。

`curl -X POST "https://anonymous:anonymous@hostname:port/content/usergenerated/mytestnode"`

端末またはコマンドプロンプトで次のコマンドを発行して、Dispatcher キャッシュの無効化を試み、コード 404 の応答を受信することを確認します。

`curl -H "CQ-Handle: /content" -H "CQ-Path: /content" https://yourhostname/dispatcher/invalidate.cache`

## バニティURLへのアクセスの有効化 {#enabling-access-to-vanity-urls-vanity-urls}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2015-03-25T14:23:05.185-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For https://jira.corp.adobe.com/browse/DOC-4812</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The com.adobe.granite.dispatcher.vanityurl.content package needs to be made public before publishing this contnet.</p>

 -->

CQ または AEM ページ用に設定されているバニティー URL へのアクセスを有効にするように Dispatcher を設定します。

バニティー URL へのアクセスが有効になると、レンダーインスタンス上で実行されているサービスを Dispatcher が定期的に呼び出して、バニティー URL のリストを取得します。Dispatcher がこのリストをローカルファイルに保存します。`/filter` セクションのフィルターによってページへのリクエストが拒否されると、ディスパッチャーはバニティURLのリストを表示します。拒否された URL がリストにある場合、Dispatcher はバニティー URL へのアクセスを許可します。

バニティURLへのアクセスを有効にするには、セクションに `/vanity_urls` セクションを追加します。 `/farms` 次の例を参照してください。

```xml
 /vanity_urls {
      /url "/libs/granite/dispatcher/content/vanityUrls.html"
      /file "/tmp/vanity_urls"
      /delay 300
 }
```

`/vanity_urls` この節では、以下のプロパティについて説明します。

* `/url`:レンダリングインスタンスで実行されるバニティURLサービスへのパスです。このプロパティの値は必須 `"/libs/granite/dispatcher/content/vanityUrls.html"`です。

* `/file`:バニティURLのリストが格納されるローカルファイルへのパス。Dispatcher がこのファイルに対する書き込みアクセス権を持っていることを確認してください。
* `/delay`:（秒）バニティURLサービスの呼び出し間隔。

>[!NOTE]
>
>レンダリングがAEMのインスタンスの場合、バニティURLサービスをインストールする [には、バニティーURL- Components](https://www.adobeaemcloud.com/content/marketplace/marketplaceProxy.html?packagePath=/content/companies/public/adobe/packages/cq600/component/vanityurls-components) パッケージをインストールする必要があります。（Package Shareへ [のサインインを参照](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/package-manager.html#SigningIntoPackageShare)）。

バニティー URL へのアクセスを有効にするには、以下の手順を実行します。

1. レンダリングサービスがAEMインスタンスの場合は、パブリッシュインスタンスにcom. adobe. granite. dispatcher. vavityurl. contentパッケージをインストールします（上記のメモを参照）。
1. AEMまたはCQページ用に設定したバニティURLごとに、設定が ` [/filter](dispatcher-configuration.md#main-pars_134_32_0009)` URLを拒否することを確認します。必要に応じて、URLを拒否するフィルターを追加します。
1. 以下 `/vanity_urls` のセクションを追加 `/farms`します。
1. Apache Web サーバーを再起動します。

## シンジケーション要求の転送 - /propagateSyndPost {#forwarding-syndication-requests-propagatesyndpost}

シンジケーション要求は、通常、Dispatcher のみを対象としているので、デフォルトではレンダラー（AEM インスタンスなど）に送信されません。

必要に応じて、シンジケーション要求を Dispatcher に転送するために、/propagateSyndPost プロパティを &quot;1&quot; に設定します。設定した場合、フィルターセクションでPOSTリクエストが拒否されないようにする必要があります。

## Dispatcher キャッシュの設定 - /cache {#configuring-the-dispatcher-cache-cache}

`/cache` このセクションでは、ディスパッチャーがドキュメントをキャッシュする方法を制御します。キャッシュ戦略を実装するためのサブプロパティをいくつか設定します。


* /docroot
* /statfile
* /serveStaleOnError
* /allowAuthorized
* /rules
* /statfileslevel
* /invalidate
* /invalidateHandler
* /allowedClients
* /ignoreUrlParams
* /headers
* /mode
* /gracePeriod


キャッシュセクションの例を次に示します。

```xml
/cache
  {
  /docroot "/opt/dispatcher/cache"
  /statfile  "/tmp/dispatcher-website.stat"          
  /allowAuthorized "0"
      
  /rules
    {
    # List of files that are cached
    }

  /invalidate
    {
    # List of files that are auto-invalidated
    }
  }
  
```

>[!NOTE]
>
>権限を区別するキャッシュについては、[セキュリティ保護されたコンテンツのキャッシュ](permissions-cache.md)をお読みください。

### キャッシュディレクトリの指定 {#specifying-the-cache-directory}

`/docroot` このプロパティは、キャッシュされたファイルが格納されるディレクトリを識別します。

>[!NOTE]
>
>Dispatcher と Web サーバーが同じファイルを処理できるよう、この値は Web サーバーのドキュメントルートとまったく同じパスの必要があります。\
>Dispatcher のキャッシュファイルが使用されると、Web サーバーは適切なステータスコードを配信します。そのため、キャッシュファイルを見つけられることが重要になります。

複数のファームを使用する場合、各ファームで異なるドキュメントルートを使用する必要があります。

### statfile の命名 {#naming-the-statfile}

`/statfile` このプロパティは、statfileとして使用するファイルを指定します。Dispatcher は、このファイルを使用して、最も新しいコンテンツ更新時刻を登録します。statfile には、Web サーバー上の任意のファイルを指定できます。

statfile にはコンテンツがありません。コンテンツが更新されると、Dispatcher がタイムスタンプを更新します。デフォルトのstatfileは. statという名前で、docrootに保存されています。Dispatcher は、statfile へのアクセスをブロックします。

>[!NOTE]
>
>を設定した場合 `/statfileslevel` 、ディスパッチャーは `/statfile` プロパティを無視し、名前として. statを使用します。

### エラー発生時の古くなったドキュメントの返送 {#serving-stale-documents-when-errors-occur}

`/serveStaleOnError` このプロパティは、レンダリングサーバーがエラーを返すときに、無効なドキュメントを返すかどうかを制御します。デフォルトでは、statfile にアクセスし、キャッシュされたコンテンツが無効になると、Dispatcher は次回要求時にキャッシュされたコンテンツを削除します。

「 `/serveStaleOnError` 1&quot;に設定した場合、レンダリングサーバーが成功した応答を返しない限り、ディスパッチャーは無効なコンテンツをキャッシュから削除しません。AEMまたは接続タイムアウトから5xxの応答が発生すると、ディスパッチャーによって古いコンテンツが提供され、応答が111（Revalidation Failed）になります。

### 認証使用時のキャッシュ {#caching-when-authentication-is-used}

`/allowAuthorized` このプロパティは、次の認証情報を含むリクエストをキャッシュするかどうかを制御します。

* `authorization` ヘッダーです。
* 名前 `authorization`が付いたcookie。
* 名前 `login-token`が付いたcookie。

デフォルトでは、この認証情報を含む要求はキャッシュされません。キャッシュされたドキュメントをクライアントに返す場合、認証は実行されないからです。この設定によって、Dispatcher は、必要な権限を持たないユーザーにキャッシュされたドキュメントを返さなくなります。

ただし、認証済みドキュメントのキャッシュを要件によって承認する場合は、/allowAuthorized を &quot;1&quot; に設定します。

`/allowAuthorized "1"`

>[!NOTE]
>
>（ `/sessionmanagement` プロパティを使用して）セッション管理を有効にするには、プロパティを `/allowAuthorized` 設定する必要 `"0"`があります。

### キャッシュするドキュメントの指定 {#specifying-the-documents-to-cache}

`/rules` このプロパティは、ドキュメントパスに従ってキャッシュされるドキュメントを制御します。/rules プロパティにかかわらず、Dispatcher は以下の状況にあるドキュメントをキャッシュしません。

* 要求 URI に疑問符（「?」）が含まれている場合。\
   疑問符は通常、キャッシュの必要がない、検索結果などの動的ページを指します。
* ファイル拡張子が不明の場合。\
   Web サーバーでドキュメントのタイプ（MIME タイプ）を判別するために、拡張子が必要です。
* 認証ヘッダー（設定可）が設定されている場合。
* AEMインスタンスが次のヘッダーを使用して応答する場合:

   * `no-cache`
   * `no-store`
   * `must-revalidate`

>[!NOTE]
>
>GETまたはHEAD（HTTPヘッダー）メソッドは、ディスパッチャーによってキャッシュ可能です。応答ヘッダーのキャッシュについて詳しくは、&quot;HTTP応答ヘッダーの [キャッシュ](dispatcher-configuration.md#caching-http-response-headers) 」セクションを参照してください。

/rulesプロパティの各項目に [は、グローブ](#designing-patterns-for-glob-properties) パターンとタイプが含まれています。

* gbパターンはドキュメントのパスと一致するために使用されます。
* このタイプは、グローブパターンに一致するドキュメントをキャッシュするかどうかを示します。値によって、ドキュメントのキャッシュ（ドキュメントのキャッシュ）または拒否（常にドキュメントをレンダリング）できます。

以上のルールで除外されるもの以外にも動的ページがない場合、Dispatcher ですべてのドキュメントをキャッシュできます。この場合、ルールセクションは次のようになります。

```xml
/rules
  { 
    /0000  {  /glob "*"   /type "allow" }
  }
```

グローブプロパティについて詳しくは、「グローブプロパティのパターン [のデザイン](#designing-patterns-for-glob-properties)」を参照してください。

動的なページ（ニュースアプリケーションなど）または閉じられたユーザーグループ内の一部のセクションがある場合、例外を定義できます。

>[!NOTE]
>
>ユーザー権限がキャッシュされたページについてチェックされていない場合、閉じたユーザーグループはキャッシュできません。

```xml
/rules
  {
   /0000  { /glob "*" /type "allow" }
   /0001  { /glob "/en/news/*" /type "deny" }
   /0002  { /glob "*/private/*" /type "deny"  }   
  }
```

**圧縮**

Apache Web サーバーでは、キャッシュされたドキュメントを圧縮することができます。圧縮すると、Apacheは、クライアントから要求された場合に、圧縮形式のドキュメントを返すことができます。例えば、Apacheモジュール `mod_deflate`を有効にして、圧縮を自動的に実行します。

```xml
AddOutputFilterByType DEFLATE text/plain
```

モジュールはデフォルトでApache2. xにインストールされています。

<!-- 

Comment Type: draft

<note type="note"> 
 <p>Depending on the Apache web server version you can enable <span class="code">gzip</span> compression as follows:</p> 
 <ul> 
  <li>For Apache 1.3 you can enable the <span class="code">mod_gzip </span>module. The module must be downloaded and installed.</li> 
  <li>For Apache 2.x you can enable the <span class="code">mod_deflate</span> module. The module is installed by default with Apache 2.x.<br /> </li> 
 </ul> 
 <p> </p> 
</note>

 -->

<!-- 

Comment Type: draft

<p>The following rule caches all documents in compressed form; Apache can return either the uncompressed or the compressed form to the client:</p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  /rules!!discoiqbr!!&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;/rulelabel&nbsp;&nbsp;{&nbsp;&nbsp;/glob&nbsp;"*"&nbsp;/type&nbsp;"allow"&nbsp;&nbsp;/compress&nbsp;"gzip"&nbsp;}!!discoiqbr!!&nbsp;&nbsp;} 
</codeblock>

 -->

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-11-13T09:23:24.326-0500

<p>Hidden the <span class="code">mod_gzip</span> content as requested in CQDOC-11124.</p>

 -->

### フォルダーレベルでのファイルの無効化 {#invalidating-files-by-folder-level}

プロパティを `/statfileslevel` 使用して、キャッシュされたファイルをパスに従って無効にします。

* ディスパッチャーは、docrootフォルダーから指定したレベルまで各フォルダーに `.stat`ファイルを作成します。ドキュメントルートはレベル 0 です。
* ファイルにタッチすると、ファイルが無効 `.stat` になります。`.stat` ファイルの最終変更日が、キャッシュされたドキュメントの最終変更日と比較されます。ファイルが `.stat` 新しい場合は、ドキュメントが再取得されます。

* 特定のレベルにあるファイルが無効になると、docroot **から無効化されたファイルのレベルまで**`.stat`**** のすべてのファイルまたは設定済み `statsfilevel` （いずれか小さい方）が表示されます。

   * 次に例を示します。プロパティを `statfileslevel` 6に設定し、レベル5でファイルを無効にすると、docrootから5までの `.stat` すべてのファイルがタッチされます。この例では、ファイルがレベル7で無効になっている場合に続きます。`stat` ファイルにアクセス `/statfileslevel = "6"`できます。

無効化されたファイルに対するパス**に沿ってリソース**のみが影響を受けます。次の例を考えてみましょう。Webサイトで構造を使用する場合 `/content/myWebsite/xx/.` 、次の `statfileslevel` ように `.stat`ファイルが作成されます。

* `docroot`
* `/content`
* `/content/myWebsite`
* `/content/myWebsite/*xx*`

ファイル `/content/myWebsite/xx` が無効になっている場合、docrootから下の `.stat` すべてのファイルが `/content/myWebsite/xx`アクセスされます。これは、例えば、forなど `/content/myWebsite/xx` の場合にのみ当てはまり `/content/myWebsite/yy``/content/anotherWebSite`ます。

>[!NOTE]
>
>無効化は、追加のヘッダーを送信することで禁止 `CQ-Action-Scope:ResourceOnly`できます。これは、キャッシュの他の部分を無効にすることなく、特定のリソースをフラッシュするために使用できます。詳細については、 [このページ](https://adobe-consulting-services.github.io/acs-aem-commons/features/dispatcher-flush-rules/index.html) および [ディスパッチャーキャッシュ](https://helpx.adobe.com/experience-manager/dispatcher/using/page-invalidate.html) の手動無効化を参照してください。

>[!NOTE]
>
>`/statfileslevel` プロパティの値を指定した場合、 `/statfile` プロパティは無視されます。

### キャッシュされたファイルの自動無効化 {#automatically-invalidating-cached-files}

`/invalidate` このプロパティは、コンテンツの更新時に自動的に無効にするドキュメントを定義します。

自動無効化を使用する場合、Dispatcher はキャッシュされたファイルをコンテンツの更新後に削除しませんが、次回要求されたときにその有効性を確認します。自動無効化されない、キャッシュ内のドキュメントは、コンテンツの更新によって明示的に削除されるまでキャッシュに残ります。

自動無効化は通常、HTML ページに対して使用されます。多くの場合、HTML ページには他のページへのリンクが含まれるので、コンテンツの更新がページに影響を与えるかどうかを判断することが困難です。コンテンツの更新時にすべての関連ページが無効になるように、すべてのHTMLページを自動的に無効にします。以下の設定は、すべての HTML ページを無効化します。

```xml
  /invalidate
  {
   /0000  { /glob "*" /type "deny" }
   /0001  { /glob "*.html" /type "allow" }
  }
```

グローブプロパティについて詳しくは、「グローブプロパティのパターン [のデザイン](#designing-patterns-for-glob-properties)」を参照してください。

この設定により、/content/geometrixx/enがアクティブ化されると次のアクティビティが発生します。

* パターン en.* を持つすべてのファイルが /content/geometrixx/ フォルダーから削除されます。
* /content/geometrixx/en/_jcr_contentフォルダーが削除されます。
* /invalidate 設定に一致するその他すべてのファイルは、即座には削除されません。これらのファイルは、次回の要求が発生すると削除されます。この例では、/content/geometrixx.html は削除されません。/content/geometrixx.html が要求されると、削除されます。

自動生成した PDF や ZIP ファイルをダウンロード用に提供する場合は、これらのファイルも自動的に無効化する必要があります。次のような設定例があります。

```xml
/invalidate
  {
   /0000 { /glob "*" /type "deny" }
   /0001 { /glob "*.html" /type "allow" }
   /0002 { /glob "*.zip" /type "allow" }
   /0003 { /glob "*.pdf" /type "allow" }
  }
```

Adobe Analytics との AEM 統合によって、Web サイトの analytics.sitecatalyst.js ファイルに設定データが提供されます。Dispatcher に付属のサンプルの dispatcher.any ファイルには、このファイル用として次の無効化ルールが含まれています。

```xml
{
   /glob "*/analytics.sitecatalyst.js"  /type "allow"
}
```

### カスタム無効化スクリプトの使用 {#using-custom-invalidation-scripts}

/invalidateHandler プロパティを使用して、Dispatcher が無効化要求を受信するたびに呼び出されるスクリプトを定義できます。

このスクリプトは、以下の引数と共に呼び出されます。

* 取り扱い\
   無効化するコンテンツのパス
* アクション\
   レプリケーションアクション（Activate、Deactivate）
* Action Scope\
   レプリケーションアクションの範囲（ヘッダー `CQ-Action-Scope: ResourceOnly` が送信されない限りは空です。詳しくは、「[AEM からキャッシュされたページの無効化](page-invalidate.md)」を参照してください）。

このスクリプトは、他のアプリケーションに固有のキャッシュの無効化など、多種多様なユースケースを扱ったり、外部化されたページの URL とドキュメントルート内のその場所がコンテンツパスと一致しないケースを扱ったりするのに利用できます。

以下のサンプルスクリプトは、各無効化要求をファイルに記録します。

```xml
/invalidateHandler "/opt/dispatcher/scripts/invalidate.sh"
```

#### サンプルの無効化ハンドラースクリプト {#sample-invalidation-handler-script}

```shell
#!/bin/bash

printf "%-15s: %s %s" $1 $2 $3>> /opt/dispatcher/logs/invalidate.log
```

### キャッシュをフラッシュできるクライアントの制限 {#limiting-the-clients-that-can-flush-the-cache}

/allowedClients プロパティは、キャッシュのフラッシュを許可する特定のクライアントを定義されます。グローバルパターンは、IPと照合されます。

次に例を示します。

1. クライアントへのアクセスを拒否
1. localhostへのアクセスを明示的に許可します

```xml
/allowedClients
  {
   /0001 { /glob "*.*.*.*"  /type "deny" }
   /0002 { /glob "127.0.0.1" /type "allow" }
  }
```

グローブプロパティについて詳しくは、「グローブプロパティのパターン [のデザイン](#designing-patterns-for-glob-properties)」を参照してください。

>[!CAUTION]
>
>/allowedClients を定義することをお勧めします。
>
>定義しない場合は、任意のクライアントからキャッシュ消去を呼び出せますが、繰り返しおこなうとサイトのパフォーマンスに深刻な影響を及ぼす場合があります。

### URL パラメーターの無視 {#ignoring-url-parameters}

`ignoreUrlParams` セクションでは、ページをキャッシュするかキャッシュから提供するかを判断するときにどの URL パラメーターを無視するかを定義します。

* 要求 URL に含まれるすべてのパラメーターを無視する場合は、ページがキャッシュされます。
* 要求 URL に含まれる 1 つ以上のパラメーターを無視しない場合は、ページはキャッシュされません。

あるページに対してパラメーターを無視する場合は、そのページが初めて要求されたときにページがキャッシュされます。そのページに対する以降の要求には、要求内のパラメーターの値にかかわらず、キャッシュされたページが返されます。

無視するパラメーターを指定するには、`ignoreUrlParams` プロパティに glob ルールを追加します。

* パラメーターを無視するには、そのパラメーターを許可する glob プロパティを作成します。
* ページがキャッシュされないようにするには、そのパラメーターを拒否する glob プロパティを作成します。

次の例では、Dispatcher が &quot;q&quot; パラメーターを無視するので、q パラメーターを含む要求 URL はキャッシュされます。

```xml
/ignoreUrlParams
{
    /0001 { /glob "*" /type "deny" }
    /0002 { /glob "q" /type "allow" }
}
```

この例 `ignoreUrlParams` の値を使用すると、次のHTTPリクエストでは、パラメーターが `q` 無視されるのでページがキャッシュされます。

```xml
GET /mypage.html?q=5
```

この例 `ignoreUrlParams` の値を使用すると、次のHTTPリクエストでは、パラメーターが無視されないため、ページがキャッシュ **され**`p` ません。

```xml
GET /mypage.html?q=5&p=4
```

グローブプロパティについて詳しくは、「グローブプロパティのパターン [のデザイン](#designing-patterns-for-glob-properties)」を参照してください。

### HTTP 応答ヘッダーのキャッシュ {#caching-http-response-headers}

>[!NOTE]
>
>この機能は、Dispatcher のバージョン **4.1.11** で利用できます。

`/headers` このプロパティを使用すると、ディスパッチャーによってキャッシュされるHTTPヘッダータイプを定義できます。キャッシュされていないリソースへの最初のリクエストでは、設定済みの値（以下の設定サンプルを参照）のいずれかに一致するすべてのヘッダーが、キャッシュファイルの横に別のファイルに格納されます。キャッシュされたリソースへの後続のリクエストでは、保存されたヘッダーが応答に追加されます。

以下に、デフォルト設定のサンプルを示します。

```xml
/cache {
  ...
  /headers {
    "Cache-Control"
    "Content-Disposition"
    "Content-Type"
    "Expires"
    "Last-Modified"
    "X-Content-Type-Options"
    "Last-Modified"
  }
}
```

>[!NOTE]
>
>また、ファイルグロビング文字は許可されていないことに注意してください。詳しくは、「グローブプロパティのパターン [のデザイン](#designing-patterns-for-glob-properties)」を参照してください。

>[!NOTE]
>
>AEMからeTag応答ヘッダーを保存および配信するには、以下の手順を実行します。
>
>* `/cache/headers`セクションにヘッダー名を追加します。
>* ディスパッチャー関連の節で [以下のApacheディレクティブ](https://httpd.apache.org/docs/2.4/mod/core.html#fileetag) を追加します。
>



```xml
FileETag none
```

### ディスパッチャーキャッシュファイルの権限 {#dispatcher-cache-file-permissions}

`mode` このプロパティは、新しいディレクトリおよびキャッシュ内のファイルに適用されるファイル権限を指定します。この設定は、呼び出し元のプロセス `umask` によって制限されます。これは、次の値のうち1つ以上の合計から構築された8進数です。

* 0400所有者による読み取りを許可します。
* 0200所有者による書き込みを許可
* 0100所有者がディレクトリを検索できるようにします。
* 0040グループメンバーによる読み取りを許可します。
* 0020グループメンバーによる書き込みを許可
* 0010グループメンバーにディレクトリ内の検索を許可します。
* 0004他のユーザーによる読み取りを許可します。
* 0002他のユーザーによる書き込みを許可
* 0001他のユーザーがディレクトリを検索できるようにします。

デフォルト値は0755で、所有者は読み取り、書き込みまたは検索、グループおよび他のユーザーの読み取りまたは検索を実行できます。

### ジョブ数. statファイルのタッチ {#throttling-stat-file-touching}

デフォルト `/invalidate` のプロパティでは、すべてのアクティブ化が効果的に無効 `.html` になります（パスが `/invalidate` セクションと一致する場合）。トラフィック量が多いWebサイトでは、それ以降のアクティブ化によってバックエンドでのCPU負荷が増加します。このようなシナリオでは、Webサイトのレスポンシブを維持するために「スロットル」 `.stat` ファイルを使用することをお勧めします。これを行うには `/gracePeriod` 、プロパティを使用します。

`/gracePeriod` このプロパティでは、古くなった自動無効化されたリソースが、最後のアクティブ化後にキャッシュから引き続き提供されることがあります。このプロパティは、アクティベートのバッチがキャッシュ全体を繰り返し無効にする設定で使用できます。推奨値は2秒です。

詳細については、上記の `/invalidate` 節も `/statfileslevel`参照してください。

## 時間に基づくキャッシュの無効化の設定 - /enableTTL {#configuring-time-based-cache-invalidation-enablettl}

設定した場合、`enableTTL` プロパティはバックエンドからの応答ヘッダーを評価し、`Cache-Control` max-age または `Expires` 日付が含まれる場合は、有効期限と同じ変更時刻を持つ予備の空のファイルがキャッシュファイルの隣に作成されます。変更時刻以降にキャッシュされたファイルが要求されると、自動的にバックエンドから再要求されます。

次の行を `dispatcher.any` ファイルに追加することで、この機能を有効にできます。

```xml
/enableTTL "1"
```

>[!NOTE]
>
>この機能は、Dispatcher のバージョン **4.1.11** で利用できます。

## ロードバランシングの設定 - /statistics {#configuring-load-balancing-statistics}

`/statistics` このセクションでは、各レンダリングの応答性をディスパッチャーがスコアするためのファイルのカテゴリを定義します。Dispatcher は、このスコアを使用して、要求を送信するレンダーを決定します。

作成したカテゴリごとに glob パターンを定義します。Dispatcher は、要求されたコンテンツの URI とこれらのパターンを比較して、要求されたコンテンツのカテゴリを決定します。

* カテゴリの順序によって、URI と比較する順序が決まります。
* URI と一致する最初のカテゴリパターンがファイルのカテゴリになります。それ以上のカテゴリパターンは評価されません。

Dispatcher は、最大 8 個の統計カテゴリをサポートします。9 個以上のカテゴリを定義した場合は、最初の 8 個のカテゴリのみが使用されます。

**レンダーの選択**

レンダリングされたページを要求するたびに、Dispatcher は以下のアルゴリズムを使用してレンダーを選択します。

1. 要求の `renderid` cookie にレンダー名が格納されている場合、Dispatcher はそのレンダーを使用します。
1. 要求に `renderid` cookie が含まれていない場合、Dispatcher はレンダー統計を比較します。

   1. Dispatcher は、要求 URI のカテゴリを判断します。
   1. Dispatcher は、そのカテゴリで最も応答スコアが低いレンダーを判断して、そのレンダーを選択します。

1. まだレンダーが選択されていない場合は、リストの先頭のレンダーを使用します。

レンダーのカテゴリのスコアは、以前の応答時間と Dispatcher が以前試みた接続の失敗および成功に基づきます。試行ごとに、要求された URI のカテゴリのスコアが更新されます。

>[!NOTE]
>
>ロードバランシングを使用しない場合は、このセクションを省略できます。

### 統計カテゴリの定義 {#defining-statistics-categories}

レンダーを選択するための統計を保持するドキュメントのタイプごとにカテゴリを定義します。/statistics セクションには、/categories セクションが含まれます。カテゴリを定義するには、/categories セクションの下に次の形式の行を追加します。

`/name { /glob "pattern"}`

カテゴリ `name` は、ファームに固有である必要があります。に `pattern` ついては、「グローブプロパティの [パターンのデザイン」](#designing-patterns-for-glob-properties) セクションで説明しています。

URI のカテゴリを判断するために、Dispatcher は一致が見つかるまで URI と各カテゴリのパターンを比較します。Dispatcher は、リストの先頭のカテゴリから始め、順序に従って比較を続けます。したがって、より具体的なパターンを持つカテゴリを先頭に配置してください。

例えば、ディスパッチャーによってデフォルトのdispatcher. anyファイルがHTMLカテゴリと他のカテゴリを定義します。HTML カテゴリのほうが具体的なので、先頭に配置されています。

```xml
/statistics
  {
  /categories
    {
      /html { /glob "*.html" }
      /others  { /glob "*" }
    }
  }
```

以下の例には、検索ページ用のカテゴリも含まれています。

```xml
/statistics
  {
  /categories
    {
      /search { /glob "*search.html" }
      /html { /glob "*.html" }
      /others  { /glob "*" }
    }
  }
```

### Dispatcher 統計へのサーバー使用不可能状態の反映 {#reflecting-server-unavailability-in-dispatcher-statistics}

`/unavailablePenalty` このプロパティは、レンダリングへの接続が失敗したときにレンダリング統計に適用される時間（10分の1秒単位）を設定します。Dispatcher は、要求された URI に一致する統計カテゴリにこの時間を追加します。

例えば、AEM が実行されていない（したがってリッスンしていない）か、ネットワーク関連の問題が発生したので、指定したホスト名およびポートへの TCP/IP 接続を確立できない場合にペナルティが適用されます。

`/unavailablePenalty` プロパティは `/farm` セクションの直接の子です（ `/statistics` セクションの兄弟）。

プロパティが `/unavailablePenalty` 存在しない場合、&quot;1&quot;の値が使用されます。

```xml
/unavailablePenalty "1"
```

## スティッキー接続フォルダーの識別 - /stickyConnectionsFor {#identifying-a-sticky-connection-folder-stickyconnectionsfor}

`/stickyConnectionsFor` プロパティは、定着ドキュメントを含む1つのフォルダーを定義します。URLを使用してアクセスします。Dispatcher によって、このフォルダーにある、単一のユーザーからのすべての要求が同一のレンダーインスタンスに送信されます。スティッキー接続は、すべてのドキュメントに必ず一貫したセッションデータが存在することを保証します。このメカニズムは、`renderid` cookie を利用しています。

次の例では、/productsフォルダーへの定着接続を定義します。

```xml
/stickyConnectionsFor "/products"
```

ページが複数のコンテンツノードからのコンテンツで構成されている場合、コンテンツへのパスを一覧表示する `/paths` プロパティを含めます。例えば、ページにコンテンツが `/content/image`含まれているとし `/content/video``/var/files/pdfs`ます。以下の設定を使用すると、ページ上のすべてのコンテンツに対してスティッキー接続が有効になります。

```xml
/stickyConnections {
  /paths {
    "/content/image"
    "/content/video"
    "/var/files/pdfs"
  }
}
```

### HttpOnly {#httponly}

スティッキー接続が有効になっている場合、ディスパッチャーモジュールはcookieを `renderid` 設定します。このcookieに `httponly` はフラグがありません。セキュリティを強化するために追加する必要があります。これを行うには、設定ファイル `httpOnly` の `/stickyConnections` ノードでプロパティ `dispatcher.any` を設定します。プロパティの値（0または1）は、cookieに `renderid``HttpOnly` 属性が付加されているかどうかを定義します。デフォルト値は0で、属性が追加されないことを意味します。

フラグに `httponly` ついて詳しくは、このページを参照 [](https://www.owasp.org/index.php/HttpOnly)してください。

### セキュア {#secure}

スティッキー接続が有効になっている場合、ディスパッチャーモジュールはcookieを `renderid` 設定します。このcookieに **はセキュリティ** 保護されたフラグがありません。セキュリティを強化するために追加する必要があります。これを行うには、設定ファイル `secure` の `/stickyConnections` ノードでプロパティ `dispatcher.any` を設定します。プロパティの値（0または1）は、cookieに `renderid``secure` 属性が付加されているかどうかを定義します。デフォルト値は0です。これは、受信要求が安全な場合に属性が追加されることを意味します。値が1に設定されている場合、受信リクエストがセキュリティ保護されているかどうかに関係なく、セキュリティ保護されたフラグが追加されます。

## レンダー接続エラーの処理 {#handling-render-connection-errors}

レンダーサーバーが 500 エラー、すなわち使用不可を返した場合の Dispatcher の動作を設定します。

### ヘルスチェックページの指定 {#specifying-a-health-check-page}

プロパティを `/health_check` 使用して、500件のステータスコードが発生したときにチェックされるURLを指定します。このページが500件のステータスコードも返す場合、インスタンスは使用できなくなり、設定可能なタイムペナルティ（ `/unavailablePenalty`）がレンダリングに適用されてから再試行されます。

```xml
/health_check
  {
  # Page gets contacted when an instance returns a 500
  /url "/health_check.html"
  }
```

### ページ再試行遅延の指定 {#specifying-the-page-retry-delay}

/ `retryDelay` propertyは、ファームがレンダリングを試行したときに、ディスパッチャーが接続試行のラウンド間を待機する時間（秒単位）を設定します。周期ごとに、Dispatcher が 1 つのレンダーに対して接続を試行する最大回数は、ファーム内のレンダーの数です。

ディスパッチャーは、明示的に定義されてい `"1"` ない場合 `/retryDelay` に値を使用します。デフォルト値は、ほとんどのケースに適しています。

```xml
/retryDelay "1"
```

### 再試行回数の設定 {#configuring-the-number-of-retries}

`/numberOfRetries` このプロパティは、ディスパッチャーがレンダリングで実行する接続の最大回数を設定します。この再試行回数内で Dispatcher がレンダーに接続できなかった場合、Dispatcher は失敗応答を返します。

周期ごとに、Dispatcher が 1 つのレンダーに対して接続を試行する最大回数は、ファーム内のレンダーの数です。したがって、ディスパッチャーが接続を試行する最大回数は（ `/numberOfRetries`レンダリングの数）です。

値が明示的に定義されていない場合、デフォルト値 **は5**です。

```xml
/numberOfRetries "5"
```

### フェイルオーバーメカニズムの使用 {#using-the-failover-mechanism}

Dispatcher ファーム上でフェイルオーバーメカニズムを有効にして、元の要求の失敗時に異なるレンダーに要求を再送信します。フェイルオーバーを有効にすると、Dispatcher は以下の動作をおこないます。

* レンダーへの要求が HTTP ステータス 503（UNAVAILABLE）を返した場合、Dispatcher は異なるレンダーに要求を送信します。
* レンダリングへの要求によってHTTPステータス50x（503以外）が返されると、ディスパッチャーは `health_check` そのプロパティ用に設定されたページに対するリクエストを送信します。

   * ヘルスチェックが 500（INTERNAL_SERVER_ERROR）を返した場合、Dispatcher は元の要求を異なるレンダーに送信します。
   * ヘルスチェックが HTTP ステータス 200 を返した場合、Dispatcher は最初の HTTP 500 エラーをクライアントに返します。

フェイルオーバーを有効にするには、次の行をファーム（または Web サイト）に追加します。

```xml
/failover "1" 
```

>[!NOTE]
>
>本文を含むHTTPリクエストを再試行するために、ディスパッチャーは、実際のコンテンツをスプールする前にリクエストヘッダーを `Expect: 100-continue` レンダリングに送信します。すると、CQSE を含む CQ 5.5 が、100（CONTINUE）またはエラーコードで即座に応答します。その他のサーブレットコンテナも、このメカニズムをサポートする必要があります。

## 中断エラーの無視 - /ignoreEINTR {#ignoring-interruption-errors-ignoreeintr}

>[!CAUTION]
>
>このオプションは、通常は必要ありません。このオプションを使用する必要があるのは、次のログメッセージが表示された場合だけです。
>
>`Error while reading response: Interrupted system call`

システムコールの対象が NFS 経由でアクセスするリモートシステム上にある場合、ファイルシステムからのシステムコールはすべて `EINTR` で中断される可能性があります。これらのシステムコールがタイムアウトするか中断されるかは、基盤となるファイルシステムがローカルマシンにどのようにマウントされたかに基づきます。

インスタンスにそのような設定があり、ログに次のメッセージが含まれる場合は、/ignoreEINTR パラメーターを使用してください。

`Error while reading response: Interrupted system call`

内部的に、Dispatcher は次のように表現できるループを使用して、リモートサーバー（AEM）からの応答を読み込みます。

`while (response not finished) {  
read more data  
}`

このようなメッセージは、「`EINTR`」セクションで `read more data` が発生した場合に生成されることがあります。原因は、データの受信前にシグナルを受信したことです。

このような中断を無視するには、次のパラメーターを（前に `dispatcher.any``/farms`）追加します。

`/ignoreEINTR "1"`

完全な応答 `/ignoreEINTR` が読み取られるまで、ディスパッチャーによってデータの読み取りが続行さ `"1"` れるように設定します。デフォルト値は 0 で、このオプションを無効にします。

## glob プロパティのパターンのデザイン {#designing-patterns-for-glob-properties}

Dispatcher 設定ファイルのいくつかのセクションでは、`glob` プロパティをクライアント要求の選択条件として使用します。glob プロパティの値は、要求されたリソースのパスやクライアントの IP アドレスなど、Dispatcher が要求の要素と比較するパターンです。例えば `/filter` 、セクション内の項目では、グローブパターンを使用して、ディスパッチャーが作用または拒否するページのパスを識別します。

glob の値にワイルドカード文字と英数字を含めて、パターンを定義することができます。

| ワイルドカード文字 | 説明 | 例 |
|--- |--- |--- |
| `*` | 文字列に含まれる任意の文字の 0 個以上の連続するインスタンスに一致します。一致の最後の文字は、次のどちらかの状況によって判断されます。<br/>文字列内の文字はパターン内の次の文字と一致し、パターン文字には次の特徴があります。<br/><ul><li>* 以外</li><li>? 以外</li><li>リテラル文字（スペースを含む）または文字クラス。</li><li>パターンの終わりに達している。</li></ul>文字クラス内のこの文字は、リテラルとして解釈されます。 | `*/geo*``/content/geometrixx` ノードと `/content/geometrixx-outdoors` ノードの下のすべてのページに一致します。次のHTTPリクエストは、グローブパターンに一致します。 <br/><ul><li>`"GET /content/geometrixx/en.html"`</li><li>`"GET /content/geometrixx-outdoors/en.html"` </li></ul><br/> `*outdoors/*`<br/>`/content/geometrixx-outdoors` ノードの下にあるすべてのページに一致します。例えば、次のHTTPリクエストは、グローブパターンと一致します。 <br/><ul><li>`"GET /content/geometrixx-outdoors/en.html"`</li></ul> |
| `?` | 任意の 1 文字に一致します。文字クラス外で使用します。文字クラス内のこの文字は、リテラルとして解釈されます。 | `*outdoors/??/*`<br/>geometrixx-outdoors サイトのすべての言語のページに一致します。例えば、次のHTTPリクエストは、グローブパターンと一致します。 <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>次のリクエストは、グローブパターンと一致しません。 <br/><ul><li>&quot;GET /content/geometrixx-outdoors/en.html&quot;</li></ul> |
| `[ and ]` | 文字クラスの最初と最後を定めます。文字クラスには、1 つまたは複数の文字範囲および単一の文字を含めることができます。<br/>ターゲット文字が文字クラス内または定義されている範囲内のいずれかの文字に一致する場合、一致が発生します。<br/>閉じ角括弧が含まれない場合、パターンによって一致は発生しません。 | `*[o]men.html*`<br/> 次のHTTPリクエストに一致します。<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>次の HTTP 要求に一致しません。<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/> `*[o/]men.html*`<br/>次のHTTPリクエストに一致します。 <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `-` | 文字の範囲を定めます。文字クラス内で使用します。文字クラス外のこの文字は、リテラルとして解釈されます。 | `*[m-p]men.html*` 次のHTTPリクエストに一致します。 <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul>次の HTTP 要求に一致しません。<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `!` | 続く文字または文字クラスを打ち消します。文字クラス内の文字および文字範囲の打ち消しにのみ使用してください。ワイルドカード文字 `^ wildcard`. <br/>文字クラス外のこの文字は、リテラルとして解釈されます。 | `*[!o]men.html*`<br/> 次のHTTPリクエストに一致します。 <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>次のHTTPリクエストに一致しません。 <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>`*[!o!/]men.html*`<br/> 次のHTTPリクエストに一致しません。<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"` または `"GET /content/geometrixx-outdoors/en/men. html"`</li></ul> |
| `^` | 続く文字または文字範囲を打ち消します。文字クラス内の文字および文字範囲の打ち消しにのみ使用してください。`!` ワイルドカード文字に相当します。<br/>文字クラス外のこの文字は、リテラルとして解釈されます。 | `!` ワイルドカード文字の例は、例のパターン内の `!` 文字を文字で `^` 置換します。 |


<!--- need to troubleshoot table

The following table describes the wildcard characters.

<table border="1" cellpadding="1" cellspacing="0" width="100%"> 
 <tbody> 
  <tr> 
   <th>Wildcard character</th> 
   <th>Description</th> 
   <th>Examples</th> 
  </tr> 
  <tr> 
   <td>*</td> 
   <td><p>Matches zero or more contiguous instances of any character in the string. The final character of the match is determined by either of the following situations:</p> 
    <ul> 
     <li>A character in the string matches the next character in the pattern, and the pattern character has the following characteristics: 
      <ul> 
       <li>Not a *</li> 
       <li>Not a ?</li> 
       <li>A literal character (including a space) or a character class.</li> 
      </ul> </li> 
     <li>The end of the pattern is reached.</li> 
    </ul> <p>Inside a character class, the character is interpreted literally.</p> </td> 
   <td><p>*/geo*</p> <p>Matches any page below the /content/geometrixx node and the /content/geometrixx-outdoors node. The following HTTP requests match the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx/en.html"</li> 
     <li>"GET /content/geometrixx-outdoors/en.html" </li> 
    </ul> <p>*outdoors/*</p> <p>Matches any page below the /content/geometrixx-outdoors node. For example, the following HTTP request matches the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en.html"</li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>?</td> 
   <td><p>Matches any single character. Use outside character classes.</p> <p>Inside a character class, this character is interpreted literally.</p> </td> 
   <td><p>*outdoors/??/*</p> <p>Matches the pages for any language in the geometrixx-outdoors site. For example, the following HTTP request matches the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p>The following request does not match the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en.html" </li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>[ and ]</td> 
   <td><p>Demarks the beginning and end of a character class.</p> <p>Character classes can include one or more character ranges and single characters.</p> <p>A match occurs if the target character matches any of the characters in the character class, or within a defined range.</p> <p>If the closing bracket is not included, the pattern produces no matches.</p> <p></p> <p></p> <p></p> </td> 
   <td><p>*[o]men.html*</p> <p>Matches the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html"</li> 
    </ul> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html" </li> 
    </ul> <p>*[o/]men.html*</p> <p>Matches the following HTTP requests: </p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" </li> 
     <li> "GET /content/geometrixx-outdoors/en/men.html" </li> 
    </ul> <p></p> </td> 
  </tr> 
  <tr> 
   <td>-</td> 
   <td><p>Denotes a range of characters. For use in character classes.<br /> </p> <p>Outside of a character class, this character is interpreted literally.</p> <p></p> </td> 
   <td><p>*[m-p]men.html*</p> <p>Matches the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html"</li> 
    </ul> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p></p> </td> 
  </tr> 
  <tr> 
   <td>!</td> 
   <td><p>Negates the character or character class that follows. Use only for negating characters and character ranges inside character classes. Equivalent to the ^ wildcard.</p> <p>Outside of a character class, this character is interpreted literally.</p> <p></p> </td> 
   <td><p>*[!o]men.html*</p> <p>Matches the following HTTP request: </p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p>Does not match the following HTTP request</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" </li> 
    </ul> <p>*[!o!/]men.html*</p> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" or "GET /content/geometrixx-outdoors/en/men. html" </li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>^</td> 
   <td><p>Negates the character or character range that follows. Use for negating only characters and character ranges inside character classes. Equivalent to the ! wildcard character.</p> <p>Outside of a character class, this charcter is interpreted literally.</p> </td> 
   <td>The examples for the ! wildcard character apply, substituting the ! characters in the example patterns with ^ characters.</td> 
  </tr> 
 </tbody> 
</table>
-->

## ログ {#logging}

Webサーバー設定では、次の設定を行うことができます。

* ディスパッチャーログファイルの場所。
* ログレベル。

詳しくは、Webサーバーのドキュメントおよびディスパッチャーインスタンスのお読みくださいファイルを参照してください。

**Apache回転/ピップログ**

**Apache** Webサーバーを使用している場合は、回転ログまたはピニングログに標準機能を使用できます。例えば、pidログを使用するとします。

`DispatcherLog "| /usr/apache/bin/rotatelogs logs/dispatcher.log%Y%m%d 604800"`

これは自動的に回転します。

* ディスパッチャーログファイル拡張子内にタイムスタンプがあります（logs/dispatcher. log% Y% m% d）。
* 週単位（60 x 60 x 24 x 7 = 604800 秒）で交替されます。

ログの交替やパイプ経由のログについては、Apache Web サーバーのドキュメント（[Apache 2.2](https://httpd.apache.org/docs/2.2/logs.html) など）を参照してください。

>[!NOTE]
>
>インストール時には、デフォルトのログレベルがhigh（つまり、level3= Debug）になり、ディスパッチャーはすべてのエラーと警告を記録します。これは、最初のステージで非常に役立ちます。
>
>ただし、追加のリソースが必要となるので、Dispatcher が現在の要件で円滑に動作している場合は、ログレベルを低くしてください。**

### トレースログ {#trace-logging}

ディスパッチャーのその他の機能強化の間に、バージョン4.2.0ではトレースログも導入されています。

トレースログはデバッグログよりレベルが高く、ログに追加情報が表示されます。以下に関するログが追加されます。

* 転送されたヘッダーの値
* 特定のアクションに対して適用されるルール

Web サーバーでログレベルを `4` に設定して、トレースログを有効にすることができます。

トレースを有効にしたログの例を以下に示します。

```xml
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Host] = "localhost:8443"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[User-Agent] = "curl/7.43.0"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Accept] = "*/*"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Client-Cert] = "(null)"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Via] = "1.1 localhost:8443 (dispatcher)"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-For] = "::1"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL] = "on"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Cipher] = "DHE-RSA-AES256-SHA"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Session-ID] = "ba931f5e4925c2dde572d766fdd436375e15a0fd24577b91f4a4d51232a934ae"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-Port] = "8443"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Server-Agent] = "Communique-Dispatcher"
```

ブロックルールに一致するファイルが要求されると、イベントがログに記録されます。

```xml
[Thu Mar 03 14:42:45 2016] [T] [11831] 'GET /content.infinity.json HTTP/1.1' was blocked because of /0082
```

## 基本操作の確認 {#confirming-basic-operation}

Web サーバー、Dispatcher および AEM インスタンスの基本の操作とやり取りを確認するには、次の手順を実行します。

1. `loglevel` to `3`.

1. Webサーバーを起動します。これにより、ディスパッチャーも起動します。
1. AEM インスタンスを起動します。
1. Web サーバーと Dispatcher のログファイルおよびエラーファイルを確認します。\
   Webサーバーに応じて、次のようなメッセージが表示されます。\
   `[Thu May 30 05:16:36 2002] [notice] Apache/2.0.50 (Unix) configured`\
   および:\
   `[Fri Jan 19 17:22:16 2001] [I] [19096] Dispatcher initialized (build XXXX)`

1. WebサーバーからWebサイトをサーフします。コンテンツが必要に応じて表示されることを確認します。\
   例えば、AEMがポート `4502` 上で実行されるローカルインストールでは、Webサイトコンソールに `80` アクセスして、次の両方を使用します。\
   ` https://localhost:4502/libs/wcm/core/content/siteadmin.html  
https://localhost:80/libs/wcm/core/content/siteadmin.html  
`結果は同じにする必要があります。同じ方法で、他のページへのアクセスも確認します。

1. キャッシュディレクトリがいっぱいになっていることを確認します。
1. ページをアクティベートし、キャッシュが正常にフラッシュされるかを確認します。
1. すべて適切に動作している場合は、`loglevel` を再び `0` に設定します。

## 複数の Dispatcher の使用 {#using-multiple-dispatchers}

複雑な設定をおこなう場合は、複数の Dispatcher を使用できます。例えば、次のことができます。

* 1 つ目の Dispatcher を、イントラネット上での Web サイトの公開に使用
* 2 つ目の Dispatcher を、異なるアドレスと異なるセキュリティ設定で、インターネット上での同じコンテンツの公開に使用

この場合、各リクエストが1つのディスパッチャーのみを通過することを確認します。ディスパッチャーは、別のディスパッチャーからの要求を処理しません。したがって、どちらの Dispatcher も AEM Web サイトに直接アクセスするようにしてください。

## デバッグ {#debugging}

ヘッダーをリクエスト `X-Dispatcher-Info` に追加する場合、ディスパッチャーは、ターゲットがキャッシュされたか、キャッシュから返されたか、キャッシュから返されなかったかを一致させます。応答ヘッダーには、この情報 `X-Cache-Info` が読み取り可能なフォームで格納されます。これらの応答ヘッダーを使用して、ディスパッチャーによってキャッシュされた応答に関する問題をデバッグできます。

この機能はデフォルトで有効になっていないので、応答ヘッダー `X-Cache-Info` を含めるには、ファームに次のエントリが含まれている必要があります。

```xml
/info "1"
```

以下に例を挙げます。  

```xml
/farm
{
    /mywebsite
    {
        # Include X-Cache-Info response header if X-Dispatcher-Info is in request header
        /info "1"
    }
}
```

また、ヘッダーに `X-Dispatcher-Info` 値は必要ありませんが、テストに使用する `curl` 場合は、次のようなヘッダーを送信するために値を指定する必要があります。

```xml
curl -v -H "X-Dispatcher-Info: true" https://localhost/content/we-retail/us/en.html
```

以下は、返さ `X-Dispatcher-Info` れる応答ヘッダーを含むリストです。

* **キャッシュ**\
   ターゲットファイルはキャッシュに含まれており、ディスパッチャーは配信に有効であることを判断しました。
* **キャッシング**\
   ターゲットファイルはキャッシュに含まれていないので、ディスパッチャーは出力をキャッシュして配信する有効であると判断しました。
* **キャッシュ:stotingファイルは、より新しい**キャッシュに格納されますが、より新しい統計ファイルによって無効になります。ディスパッチャーはターゲットファイルを削除し、出力から再作成して配信します。
* **canchable not cachable:ドキュメントルート**がない場合、そのファームの設定にドキュメントルート（設定要素 `cache.docroot`）が含まれていません。
* **canchable not cachable:キャッシュファイルパスが長すぎます**\
   ターゲットファイル（ドキュメントルートとURLファイルの連結）が、システムで最も長いファイル名を超えています。
* **canchable not cachable:一時ファイルパスが長すぎます**\
   一時ファイル名テンプレートが、システムで最も長いファイル名を超えています。ディスパッチャーは、まず、キャッシュされたファイルを作成または上書きする前に、一時ファイルを作成します。一時ファイル名は、そのファイルに付加された文字 `_YYYYXXXXXX` を持つファイル名で、 `Y``X` そこには一意の名前が付けられます。
* **canchable not cachable:リクエストURLに拡張子がありません**\
   リクエストURLに拡張子がないか、ファイル拡張子の後にパスがあります。 `/test.html/a/path`を参照してください。
* **canchable not cachable:request was&#39;not a GET or HEAD**&#39;HTTPメソッドはGETまたはHEADではありません。ディスパッチャーは、出力にキャッシュされないダイナミックデータが含まれていることを前提としています。
* **canchable not cachable:リクエストにクエリ文字列が含まれている**\
   リクエストにクエリ文字列が含まれていました。ディスパッチャーは、出力が与えられたクエリ文字列に依存し、キャッシュされないことを前提としています。
* **canchable not cachable:セッションマネージャーが認証できませんでした**\
   ファームのキャッシュは、セッションマネージャーによって管理され（設定に `sessionmanagement` ノードが含まれています）、リクエストに適切な認証情報が含まれていません。
* **canchable not cachable:リクエストに認証を含む**\
   ファームに出力（ `allowAuthorized 0`）が許可されておらず、リクエストに認証情報が含まれている。
* **canchable not cachable:targetはディレクトリです**\
   ターゲットファイルはディレクトリです。これは、URLと一部のサブURLのいずれかにキャッシュ可能な出力が含まれている場合、例えば、最初に取得さ `/test.html/a/file.ext` れ、キャッシュ可能な出力が含まれている場合などに、その後の要求の出力をディスパッチャーがキャッシュできないという概念的誤りがあります `/test.html`。
* **canchable not cachable:リクエストURLに末尾のスラッシュが付いています**\
   リクエストURLにはスラッシュが付いています。
* **canchable not cachable:リクエストURLがキャッシュルールにない**\
   このファームのキャッシュルールは、一部のリクエストURLの出力を明示的に拒否することを明示的に拒否します。
* **canchable not cachable:認証チェッカーがアクセスを拒否**\
   ファームの認証チェッカーは、キャッシュされたファイルへのアクセスを拒否しました。
* **canchable not cachable:セッションが有効**でない場合、ファームのキャッシュがセッションマネージャーによって管理され（設定 `sessionmanagement` はノードを含む）、ユーザーのセッションは無効になります。
* **canchable not cachable:response:リモート`no_cache `**サーバーがヘッダーを `Dispatcher: no_cache` 返し、ディスパッチャーが出力をキャッシュすることを禁止します。
* **canchable not cachable:response content length is zero**
is content length of the response is zero;ディスパッチャーは、ゼロ長のファイルを作成しません。
