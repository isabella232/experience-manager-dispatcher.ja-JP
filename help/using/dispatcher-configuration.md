---
title: Dispatcher の設定
description: Dispatcher の設定方法について説明します。 IPv4 および IPv6 のサポート、構成ファイル、環境変数、インスタンス名の設定、ファームの定義、仮想ホストの識別などについて説明します。
exl-id: 91159de3-4ccb-43d3-899f-9806265ff132
source-git-commit: 410346694a134c0f32a24de905623655f15269b4
workflow-type: tm+mt
source-wordcount: '8857'
ht-degree: 40%

---

# Dispatcher の設定 {#configuring-dispatcher}

>[!NOTE]
>
>Dispatcher のバージョンはAEMとは無関係です。 以前のバージョンの AEM のドキュメントに組み込まれている Dispatcher ドキュメントへのリンクをたどった場合は、このページにリダイレクトされている可能性があります。

以下の節では、Dispatcher の様々な側面を設定する方法について説明します。

## IPv4 および IPv6 のサポート {#support-for-ipv-and-ipv}

AEM と Dispatcher のすべての要素は、IPv4 と IPv6 の両方のネットワークにインストールできます。詳しくは、 [IPV4 と IPV6](https://experienceleague.adobe.com/docs/experience-manager-65/deploying/introduction/technical-requirements.html?lang=en#ipv-and-ipv).

## Dispatcher の設定ファイル {#dispatcher-configuration-files}

デフォルトでは、Dispatcher の設定は `dispatcher.any` テキストファイルに格納されますが、インストール時にこのファイルの名前と場所を変更することができます。

設定ファイルには、Dispatcher の動作を制御する一連の単一値または複数値プロパティが含まれます。

* プロパティ名の前にはフォワードスラッシュ（「`/`」）が付きます。
* 複数値プロパティは、中括弧（「`{ }`」）を使用して子アイテムを囲みます。

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

* 設定ファイルのサイズが大きい場合は、ファイルを複数の小さなファイルに分割し（管理しやすく）、各ファイルを含めることができます。
* 自動生成されたファイル。

例えば、myFarm.any ファイルを /farms の設定に含めるには、次のコードを使用します。

```xml
/farms
  {
  $include "myFarm.any"
  }
```

含めるファイルの範囲を指定するには、アスタリスク (`*`) をワイルドカードとして使用できます。

例えば、`farm_1.any` ～ `farm_5.any` のファイルにファーム 1 ～ 5 が設定されている場合、これらのファイルを次のように含めることができます。

```xml
/farms
  {
  $include "farm_*.any"
  }
```

## 環境変数の使用 {#using-environment-variables}

値を直接記述する代わりに、dispatcher.any ファイルの単一値プロパティに環境変数を使用できます。環境変数の値を含めるには、`${variable_name}` という形式を使用します。

例えば、dispatcher.any ファイルがキャッシュディレクトリと同じディレクトリにある場合、 [docroot](#specifying-the-cache-directory) プロパティは次の場合に使用できます。

```xml
/docroot "${PWD}/cache"
```

もう 1 つの例として、AEM パブリッシュインスタンスのホスト名を格納する `PUBLISH_IP` という環境変数を作成した場合は、[/renders](#defining-page-renderers-renders) プロパティの次の設定を使用できます。

```xml
/renders {
  /0001 {
    /hostname "${PUBLISH_IP}"
    /port "8443"
  }
}
```

## Dispatcher インスタンスの命名 {#naming-the-dispatcher-instance-name}

Dispatcher インスタンスを識別する一意の名前を指定するには、`/name` プロパティを使用します。`/name` プロパティは、設定構造の最上位プロパティです。

## ファームの定義 {#defining-farms-farms}

`/farms` プロパティは、1 つまたは複数の Dispatcher の動作セットを定義します。各セットは、異なる Web サイトまたは URL に関連付けられます。`/farms` プロパティには、1 つまたは複数のファームを含めることができます。

* Dispatcher がすべての Web ページまたは Web サイトを同じ方法で処理する場合は、単一のファームを使用します。
* Web サイトの異なる領域や異なる Web サイトで異なる Dispatcher の動作が必要な場合は、複数のファームを作成します。

`/farms` プロパティは、設定構造の最上位プロパティです。ファームを定義するには、`/farms` プロパティに子プロパティを追加します。Dispatcher インスタンス内でファームを一意に識別するプロパティ名を使用してください。

`/farmname` プロパティは複数値で、次のような Dispatcher 動作を定義する他のプロパティを含みます。

* ファームが適用されるページの URL。
* ドキュメントのレンダリングに使用する 1 つ以上のサービス URL ( 通常はAEMパブリッシュインスタンス )。
* 複数のドキュメントレンダラーのロードバランシングに使用する統計。
* キャッシュするファイルやキャッシュする場所など、その他の動作。

値には、任意の英数字 (a ～ z、0 ～ 9) を含めることができます。 `/daycom` および `/docsdaycom` という 2 つのファームのスケルトン定義の例を次に示します。

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
>レンダーファームを複数使用する場合、リストは下から順に評価されます。このフローは、 [仮想ホスト](#identifying-virtual-hosts-virtualhosts) を参照してください。

各ファームプロパティには、以下の子プロパティを含めることができます。

| プロパティ名 | 説明 |
|--- |--- |
| [/homepage](#specify-a-default-page-iis-only-homepage) | デフォルトのホームページ（オプション）（IIS のみ） |
| [/clientheaders](#specifying-the-http-headers-to-pass-through-clientheaders) | 通過させるクライアント HTTP 要求のヘッダー。 |
| [/virtualhosts](#identifying-virtual-hosts-virtualhosts) | このファームの仮想ホスト。 |
| [/sessionmanagement](#enabling-secure-sessions-sessionmanagement) | セッション管理および認証のサポート。 |
| [/renders](#defining-page-renderers-renders) | レンダリングされたページを提供するサーバー ( 通常、AEMパブリッシュインスタンス )。 |
| [/filter](#configuring-access-to-content-filter) | Dispatcher がアクセスを有効にする URL を定義します。 |
| [/vanity_urls](#enabling-access-to-vanity-urls-vanity-urls) | バニティー URL へのアクセスを設定します。 |
| [/propagateSyndPost](#forwarding-syndication-requests-propagatesyndpost) | シンジケーション要求の転送のサポート。 |
| [/cache](#configuring-the-dispatcher-cache-cache) | キャッシュ動作を設定します。 |
| [/statistics](#configuring-load-balancing-statistics) | ロードバランシング計算用の統計カテゴリの定義。 |
| [/stickyConnectionsFor](#identifying-a-sticky-connection-folder-stickyconnectionsfor) | スティッキードキュメントを格納するフォルダーです。 |
| [/health_check](#specifying-a-health-check-page) | サーバーの可用性を判断するために使用する URL です。 |
| [/retryDelay](#specifying-the-page-retry-delay) | 失敗した接続を再試行するまでの遅延時間です。 |
| [/unavailablePenalty](#reflecting-server-unavailability-in-dispatcher-statistics) | 負荷分散計算の統計に影響を与えるペナルティ。 |
| [/failover](#using-the-failover-mechanism) | 元の要求が失敗した場合に、別のレンダーに要求を再送信します。 |
| [/auth_checker](permissions-cache.md) | 権限を区別するキャッシュについては、 [セキュリティ保護されたコンテンツのキャッシュ](permissions-cache.md). |

## デフォルトページの指定（IIS のみ） - /homepage {#specify-a-default-page-iis-only-homepage}

>[!CAUTION]
>
>`/homepage` パラメーター（IISのみ）は機能しなくなりました。代わりに、 [IIS URL 書き換えモジュール](https://learn.microsoft.com/en-us/iis/extensions/url-rewrite-module/using-the-url-rewrite-module).
>
>Apache を使用している場合は `mod_rewrite` モジュールを使用する必要があります。詳しくは、Apache Web サイトのドキュメントを参照してください。 `mod_rewrite` ( 例： [Apache 2.4](https://httpd.apache.org/docs/current/mod/mod_rewrite.html)) をクリックします。 を使用する場合 `mod_rewrite`の場合は、フラグ「passthrough|PT」（次のハンドラーにパススルー）を使用して、書き換えエンジンに対して `uri` 内部のフィールド `request_rec` の値に対する構造 `filename` フィールドに入力します。

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

`/clientheaders` プロパティでは、Dispatcher がクライアント HTTP 要求からレンダラー（AEM インスタンス）に渡す HTTP ヘッダーのリストを定義します。

デフォルトでは、Dispatcher は標準の HTTP ヘッダーをAEMインスタンスに転送します。 場合によっては、追加のヘッダーを転送したり、特定のヘッダーを削除したりすることがあります。

* AEMインスタンスが HTTP リクエストで想定するヘッダー（カスタムヘッダーなど）を追加します。
* Web サーバーにのみ関連する認証ヘッダーなどのヘッダーを削除する。

通過させる一連のヘッダーをカスタマイズした場合は、ヘッダーの完全なリスト（通常はデフォルトで含まれるヘッダーを含む）を指定する必要があります。

例えば、パブリッシュインスタンス向けのページアクティベーション要求を処理する Dispatcher インスタンスには、`PATH` セクションに `/clientheaders` ヘッダーが必要です。The `PATH` ヘッダーは、レプリケーションエージェントと Dispatcher 間の通信を可能にします。

次のコードは、`/clientheaders` の設定例です。

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

`/virtualhosts` プロパティは、Dispatcher がこのファームに受け入れるすべてのホスト名と URI の組み合わせのリストを定義します。アスタリスク (`*`) 文字をワイルドカードとして使用できます。 /`virtualhosts` プロパティの値には、次の形式を使用します。

```xml
[scheme]host[uri][*]
```

* `scheme`：（オプション） `https://` または `https://.`
* `host`：ホストコンピューターの名前または IP アドレスと、必要な場合はポート番号( 詳しくは、 [https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23))
* `uri`：（オプション）リソースへのパス。

次の設定例では、 `.com` および `.ch` myCompany のドメイン、および mySubDivision のすべてのドメイン：

```xml
   /virtualhosts
    {
    "www.myCompany.com"
    "www.myCompany.ch"
    "www.mySubDivison.*"
    }
```

次の設定は、*すべての*&#x200B;要求を処理します。

```xml
   /virtualhosts
    {
    "*"
    }
```

### 仮想ホストの解決 {#resolving-the-virtual-host}

HTTP 要求または HTTPS 要求を受信すると `host,`、`uri` および `scheme` ヘッダーに最適な仮想ホスト値を探します。Dispatcher は、次の順序で `virtualhosts` プロパティの値を評価します。

* dispatcher.any ファイル内の一番下のファームから始め、上方向へ進みます。
* ファームごとに、`virtualhosts` プロパティの最上位の値から始め、値のリストの下方向へ進みます。

Dispatcher は、以下の方法で最良一致の仮想ホスト値を探します。

* 要求の `host`、`scheme`、`uri` の 3 つすべてに一致する、最初に見つかった仮想ホストを使用します。
* いいえの場合 `virtualhosts` 値は、 `scheme` および `uri` 両方とも一致する部分 `scheme` および `uri` リクエストの最初に見つかった仮想ホストで、 `host` の値が使用されます。
* 要求の host に一致する host 部を持つ `virtualhosts` 値がない場合は、最上位ファームの最上位の仮想ホストを使用します。

したがって、デフォルトの仮想ホストを `virtualhosts` プロパティを `dispatcher.any` ファイル。

### 仮想ホストの解決の例 {#example-virtual-host-resolution}

次の例は、 `dispatcher.any` 2 つの Dispatcher ファームを定義し、各ファームは `virtualhosts` プロパティ。

```xml
/farms
  {
  /myProducts
    {
    /virtualhosts
      {
      "www.mycompany.com/products/*"
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
      "www.mycompany.com"
      }
    /renders
      {
      /hostname "server2.myCompany.com"
      /port "80"
      }
    }
  }
```

この例を使用して、次の表に、指定された HTTP 要求に対して解決される仮想ホストを示します。

| リクエスト URL | 解決された仮想ホスト |
|---|---|
| `https://www.mycompany.com/products/gloves.html` | `www.mycompany.com/products/` |
| `https://www.mycompany.com/about.html` | `www.mycompany.com` |

## セキュアセッションの有効化 — /sessionmanagement {#enabling-secure-sessions-sessionmanagement}

>[!CAUTION]
>
>`/allowAuthorized` をに設定します。 `"0"` （内） `/cache` セクションでこの機能を有効にします。 詳しくは、 [認証使用時のキャッシュ](#caching-when-authentication-is-used) 」セクションに、 `/allowAuthorized 0 ` 認証情報を含むリクエストは、 **not** キャッシュ済み 権限を区別するキャッシュが必要な場合は、 [セキュリティ保護されたコンテンツのキャッシュ](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/permissions-cache.html?lang=ja) ページに貼り付けます。

レンダーファームにアクセスするためのセキュアセッションを作成し、ユーザーがファーム内のページにアクセスするためにログインする必要があるようにします。 ログイン後、ユーザーはファーム内のページにアクセスできます。詳しくは、 [閉じられたユーザーグループの作成](https://experienceleague.adobe.com/docs/experience-manager-65/administering/security/cug.html?lang=en#creating-the-user-group-to-be-used) を参照してください。 また、運用を開始する前に、Dispatcher の[セキュリティチェックリスト](/help/using/security-checklist.md)を参照してください。

`/sessionmanagement` プロパティは `/farms` のサブプロパティです。

>[!CAUTION]
>
>Web サイトのセクションごとに異なるアクセス要件がある場合は、複数のファームを定義する必要があります。

**/sessionmanagement** には、いくつかのサブパラメーターがあります。

**/directory**（必須）

セッション情報を格納するディレクトリ。ディレクトリが存在しない場合は自動的に作成されます。

>[!CAUTION]
>
> ディレクトリサブパラメーターを設定する場合、 **しない** ルートフォルダー (`/directory "/"`) を返します。 セッション情報を保存するフォルダーのパスを常に指定します。 次に例を示します。

```xml
/sessionmanagement
  {
  /directory "/usr/local/apache/.sessions"
  }
```

**/encode**（オプション）

セッション情報のエンコード方法。用途 `md5` md5 アルゴリズムを使用した暗号化の場合、または `hex` を 16 進数エンコーディング用に作成します。 セッションデータを暗号化すると、ファイルシステムにアクセスできるユーザーはセッションの内容を読み取れません。 デフォルトは、`md5` です。

**/header**（オプション）

認証情報を格納する HTTP ヘッダーまたは cookie の名前。ヘッダーに情報を格納する場合は、`HTTP:<header-name>` を適用します。cookie に情報を格納する場合は、`Cookie:<header-name>` を適用します。値を指定しない場合、 `HTTP:authorization` が使用されます。

**/timeout**（オプション）

最後の使用から、セッションのタイムアウトまでの秒数。指定されていない場合 `"800"` が使用されているので、ユーザーの最後の要求の少し後、セッションが 13 分以上タイムアウトします。

設定例を次に示します。

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

/renders プロパティは、ドキュメントをレンダリングするために Dispatcher が要求を送信する URL を定義します。次の `/renders` セクションの例では、レンダリングのために単一の AEM インスタンスを識別しています。

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

### レンダリングオプション {#renders-options}

**/timeout**

AEM インスタンスにアクセスする接続タイムアウトをミリ秒単位で指定します。デフォルトはです。 `"0"`を呼び出し、Dispatcher が無期限に待機します。

**/receiveTimeout**

応答が返るまでに許容される時間をミリ秒単位で指定します。デフォルトはです。 `"600000"`:Dispatcher が 10 分間待機します。 設定 `"0"` タイムアウトを排除します。

応答ヘッダーの解析中にタイムアウトに達した場合は、HTTP ステータス 504（Bad Gateway）が返されます。応答本文が読み取られる間にタイムアウトに達した場合、Dispatcher は不完全な応答をクライアントに返します。 また、書き込まれた可能性のあるキャッシュファイルも削除します。

**/ipv4**

レンダーの IP アドレスを取得するために Dispatcher が `getaddrinfo` 関数（IPv6 用）を使用するか `gethostbyname` 関数（IPv4 用）を使用するかを指定します。値 0 を指定すると、`getaddrinfo` が使用されます。値： `1` 原因 `gethostbyname` を使用します。 デフォルト値は `0` です。

The `getaddrinfo` 関数は、IP アドレスのリストを返します。 Dispatcher は、TCP/IP 接続が確立されるまで、アドレスのリストを反復します。 したがって、 `ipv4` プロパティは、レンダーホスト名が、 `getaddrinfo` 関数を使用すると、常に同じ順序の IP アドレスのリストを返します。 この場合、 `gethostbyname` 関数を使用して、Dispatcher が接続する IP アドレスがランダム化されるようにします。

Amazon Elastic Load Balancing(ELB) は、getaddrinfo に対応するサービスで、IP アドレスの同じ順序のリストを使用します。

**/secure**

次の場合、 `/secure` プロパティの値は `"1"`の場合、Dispatcher は HTTPS を使用してAEMインスタンスと通信します。 詳しくは、[Using SSL with Dispatcher](dispatcher-ssl.md#configuring-dispatcher-to-use-ssl) を参照してください。

**/always-resolve**

Dispatcher バージョン **4.1.6** では、次のように `/always-resolve` プロパティを設定できます。

* に設定する場合 `"1"`に設定されている場合、要求ごとにホスト名が解決されます（Dispatcher は IP アドレスをキャッシュしません）。 リクエストごとにホスト情報を取得するために追加の呼び出しが必要となるため、パフォーマンスが多少低下する可能性があります。
* プロパティを設定しない場合、IP アドレスはデフォルトでキャッシュされます。

また、次の例に示すように、このプロパティは動的な IP 解決の問題が発生した場合にも使用できます。

```xml
/renders {
  /0001 {
     /hostname "host-name-here"
     /port "4502"
     /ipv4 "1"
     /always-resolve "1"
     }
  }
```

## コンテンツへのアクセスの設定 {#configuring-access-to-content-filter}

Dispatcher が受け入れる HTTP 要求を指定するには、`/filter` セクションを使用します。それ以外のすべての要求は、エラーコード 404（ページが見つかりません）で Web サーバーに返送されます。`/filter` セクションが存在しない場合は、すべての要求が受け入れられます。

**注意：**[statfile](#naming-the-statfile) に対する要求は常に拒否されます。

>[!CAUTION]
>
>Dispatcher を使用してアクセスを制限する場合の詳しい考慮事項については、[Dispatcher セキュリティチェックリスト](security-checklist.md)を参照してください。また、 [AEMセキュリティチェックリスト](https://experienceleague.adobe.com/docs/experience-manager-65/administering/security/security-checklist.html?lang=ja#security) AEMのインストールに関するセキュリティの詳細については、を参照してください。

The `/filter` セクションは、HTTP 要求の要求行部分のパターンに従ってコンテンツへのアクセスを拒否または許可する一連のルールで構成されます。 に対して許可リストに加える戦略を使用 `/filter` セクション：

* まず、すべてに対するアクセスを拒否します。
* 必要に応じて、コンテンツへのアクセスを許可します。

>[!NOTE]
>
>フィルタールールに変更が生じた場合は常にキャッシュをパージします。

### フィルターの定義 {#defining-a-filter}

`/filter` の各アイテムには、要求行の特定の要素または要求行全体と照合するタイプとパターンが含まれます。各フィルターには、次のアイテムを含めることができます。

* **タイプ**：`/type` は、パターンに一致する要求へのアクセスを許可するか、拒否するかを示します。値は、`allow`または `deny` のどちらかです。

* **要求行の要素：**`/query`、`/method`、`/url` または `/protocol` と、HTTP 要求の要求行の特定部分に応じて要求をフィルタリングするためのパターンを含めます。要求行全体ではなく、要求行の要素に対してフィルタリングすることをお勧めします。

* **要求行の高度な要素：** Dispatcher 4.2.0 から 4 つの新しいフィルター要素を使用できます。次の新しい要素が追加されました。 `/path`, `/selectors`, `/extension`、および `/suffix` それぞれ。 これらを 1 つ以上含めると、URL パターンをさらに制御することができます。

>[!NOTE]
>
>要求行のどの部分を参照するかについて詳しくは、 [Sling URL の分解](https://sling.apache.org/documentation/the-sling-engine/url-decomposition.html) wiki ページを開きます。

* **glob プロパティ：** HTTP 要求の要求行全体と照合するには、`/glob` プロパティを使用します。

>[!CAUTION]
>
>Dispatcher では、glob を使用したフィルタリングは廃止されました。そのため、セキュリティ上の問題につながる可能性があるので、`/filter` セクションで glob を使用しないようにしてください。つまり、次の代わりに、
>
>`/glob "* *.css *"`
>
>use
>
>`/url "*.css"`

#### HTTP 要求の要求行部分 {#the-request-line-part-of-http-requests}

HTTP/1.1 では [リクエストライン](https://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html) 次のように指定します。

`Method Request-URI HTTP-Version<CRLF>`

The `<CRLF>` 文字は、キャリッジリターンとそれに続くラインフィードを表します。 次の例は、クライアントが WKND サイトの US-English ページをリクエストしたときに受け取るリクエスト行です。

`GET /content/wknd/us/en.html HTTP.1.1<CRLF>`

パターンは、要求行と `<CRLF>` 文字。

#### 二重引用符と一重引用符 {#double-quotes-vs-single-quotes}

フィルタールールを作成する場合、単純なパターンには二重引用符を使用します（例：`"pattern"`）。Dispatcher 4.2.0 以降を使用しており、パターンに正規表現が含まれている場合は、正規表現パターンを一重引用符で囲む必要があります（例：`'(pattern1|pattern2)'`）。

#### 正規表現 {#regular-expressions}

4.2.0 より後のバージョンの Dispatcher では、フィルターパターンに POSIX 拡張正規表現を含めることができます。

#### フィルターのトラブルシューティング {#troubleshooting-filters}

フィルターが想定どおりにトリガーされない場合は、を有効にします。 [トレースログ](#trace-logging) Dispatcher 上で実行し、要求を傍受しているフィルターを確認できるようにします。

#### サンプルフィルター：すべて拒否 {#example-filter-deny-all}

以下のサンプルのフィルターセクションを使用すると、Dispatcher がすべてのファイルに対する要求を拒否します。 すべてのファイルへのアクセスを拒否し、特定の領域へのアクセスを許可します。

```xml
/0001  { /type "deny" /url "*"  }
```

明示的に拒否された領域への要求に対して、404 エラーコード（ページが見つかりません）が返される。

#### サンプルフィルター：特定の領域へのアクセスを拒否 {#example-filter-deny-access-to-specific-areas}

また、フィルターを使用して、ASP ページやパブリッシュインスタンス内の機密領域など、様々な要素へのアクセスを拒否することもできます。 次のフィルターは、ASP ページへのアクセスを拒否するものです。

```xml
/0002  { /type "deny" /url "*.asp"  }
```

#### サンプルフィルター：POST要求の有効化 {#example-filter-enable-post-requests}

次のサンプルフィルターを使用して、POST メソッドでフォームデータを送信できます。

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002 { /type "allow" /method "POST" /url "/content/[.]*.form.html" }
}
```

#### サンプルフィルター：ワークフローコンソールへのアクセスを許可 {#example-filter-allow-access-to-the-workflow-console}

次の例は、Workflow コンソールへの外部アクセスを許可するために使用するフィルターを示しています。

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002  {  /type "allow"  /url "/libs/cq/workflow/content/console*"  }
}
```

パブリッシュインスタンスで Web アプリケーションコンテキスト（パブリッシュなど）を使用する場合は、フィルター定義に追加することもできます。

```xml
/0003   { /type "deny"  /url "/publish/libs/cq/workflow/content/console/archive*"  }
```

制限された領域内の単一のページにアクセスする必要がある場合は、それらのページへのアクセスを許可できます。 例えば、ワークフローコンソール内の「アーカイブ」タブにアクセスできるようにするには、次のセクションを追加します。

```xml
/0004  { /type "allow"  /url "/libs/cq/workflow/content/console/archive*"   }
```

>[!NOTE]
>
>1 つの要求に複数のフィルターパターンが適用される場合は、最後に適用されたフィルターパターンが有効です。

#### サンプルフィルター：正規表現の使用 {#example-filter-using-regular-expressions}

このフィルターは、一重引用符で囲んで定義された正規表現を使用して、非公開コンテンツディレクトリ内の拡張を有効にします。

```xml
/005  {  /type "allow" /extension '(css|gif|ico|js|png|swf|jpe?g)' }
```

#### サンプルフィルター：要求 URL の追加要素のフィルタリング {#example-filter-filter-additional-elements-of-a-request-url}

以下に、 `/content` パスとそのサブツリー（path、selector および extensions に対するフィルターを使用）:

```xml
/006 {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy|sysview|docview|query|jcr:content|_jcr_content|search|childrenlist|ext|assets|assetsearch|[0-9-]+)'
        /extension '(json|xml|html|feed))'
        }
```

### /filter セクションの例 {#example-filter-section}

Dispatcher を設定する場合は、外部アクセスをできるだけ制限する必要があります。 次の例では、外部訪問者に対するアクセスを最小限に抑えます。

* `/content`
* デザインやクライアントライブラリなどのその他のコンテンツ。 次に例を示します。

   * `/etc/designs/default*`
   * `/etc/designs/mydesign*`

フィルターを作成した後、 [ページアクセスのテスト](#testing-dispatcher-security) AEMインスタンスのセキュリティを確保するため。

次の `/filter` のセクション `dispatcher.any` ファイルは、 [Dispatcher 設定ファイル。](#dispatcher-configuration-files)

このサンプルは、Dispatcher に付属するデフォルトの設定ファイルをベースとしており、実稼動環境での使用例の役割を果たすことを目的としています。プレフィックスが付いた項目 `#` は非アクティブ化（コメントアウト）されています。 これらの項目をアクティベートする場合 ( `#` を設定します )。 これは、セキュリティに影響を与える可能性があります。

すべてに対するアクセスを拒否し、特定の（制限された）要素へのアクセスを許可します。

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
      /0001  { /type "deny" /url "*"  }

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
>Apache と共に使用する場合は、Dispatcher モジュールの DispatcherUseProcessedURL プロパティに応じてフィルター URL パターンをデザインしてください（「[Apache Web サーバー - Dispatcher 用の Apache Web サーバーの設定](dispatcher-install.md##apache-web-server-configure-apache-web-server-for-dispatcher)」を参照。)

<!----
>[!NOTE]
>
>Filters `0030` and `0031` regarding Dynamic Media are applicable to AEM 6.0 and higher. -->

アクセスを拡張する場合は、次の推奨事項を考慮してください。

* への外部アクセスを無効にする `/admin` CQ バージョン 5.4 以前を使用している場合。

* `/libs` 内にあるファイルへのアクセスを許可する場合は、注意が必要です。アクセスは、個別に許可する必要があります。
* レプリケーション複製の設定が表示されないよう、この設定へのアクセスを拒否してください。

   * `/etc/replication.xml*`
   * `/etc/replication.infinity.json*`

* Google Gadgets のリバースプロキシへのアクセスを拒否します。

   * `/libs/opensocial/proxy*`

インストールによっては、`/apps`、`/libs` またはその他の場所（使用可能にする必要があります）の下に、追加のリソースが存在する場合があります。外部からアクセスされるリソースを判別するための方法として、`access.log` ファイルを使用することができます。

>[!CAUTION]
>
>コンソールやディレクトリにアクセスすると、実稼動環境のセキュリティリスクが高まる可能性があります。 明示的な正当化がない限り、非アクティブ化（コメントアウト）のままにする必要があります。

>[!CAUTION]
>
>次の場合、 [パブリッシュ環境でのレポートの使用](https://experienceleague.adobe.com/docs/experience-manager-65/administering/operations/reporting.html?lang=en#using-reports-in-a-publish-environment)を使用する場合は、へのアクセスを拒否するように Dispatcher を設定する必要があります。 `/etc/reports` 外部の訪問者用。

### クエリ文字列の制限 {#restricting-query-strings}

Dispatcher バージョン 4.1.5 以降では、`/filter` セクションを使用してクエリ文字列を制約します。`allow` フィルター要素を使用して、クエリ文字列を明示的に許可し、一般的な許可を除外することを強くお勧めします。

1 つのエントリに、 `glob` または組み合わせ `method`, `url`, `query`、および `version`ですが、両方ではありません。 以下の例では、`a=*` ノードに解決される URL に対してクエリ文字列 `/etc` を許可し、その他すべてのクエリ文字列を拒否しています。

```xml
/filter {
 /0001 { /type "deny" /method "POST" /url "/etc/*" }
 /0002 { /type "allow" /method "GET" /url "/etc/*" /query "a=*" }
}
```

>[!NOTE]
>
>ルールに `/query`の場合は、クエリ文字列を含む要求のみに一致し、指定されたクエリパターンに一致します。
>
>上記の例で、クエリ文字列を持たない `/etc` への要求も許可する場合は、以下のルールが必要になります。
>

```xml
/filter {  
>/0001 { /type "deny" /method "*" /url "/path/*" }  
>/0002 { /type "allow" /method "GET" /url "/path/*" }  
>/0003 { /type "deny" /method "GET" /url "/path/*" /query "*" }  
>/0004 { /type "allow" /method "GET" /url "/path/*" /query "a=*" }  
}  
```

### Dispatcher のセキュリティのテスト {#testing-dispatcher-security}

AEMパブリッシュインスタンス上の次のページおよびスクリプトへのアクセスは、Dispatcher フィルターによってブロックされる必要があります。 Web ブラウザーを使用して、サイト訪問者がおこなうように次のページを開き、コード 404 が返されることを確認します。 他の結果が得られた場合は、フィルタを調整します。

次の場合、通常のページレンダリングが表示されます。 `/content/add_valid_page.html?debug=layout`.

* `/admin`
* `/system/console`
* `/dav/crx.default`
* `/crx`
* `/bin/crxde/logs`
* `/jcr:system/jcr:versionStorage.json`
* `/_jcr_system/_jcr_versionStorage.json`
* `/libs/wcm/core/content/siteadmin.html`
* `/libs/collab/core/content/admin.html`
* `/libs/cq/ui/content/dumplibs.html`
* `/var/linkchecker.html`
* `/etc/linkchecker.html`
* `/home/users/a/admin/profile.json`
* `/home/users/a/admin/profile.xml`
* `/libs/cq/core/content/login.json`
* `/content/../libs/foundation/components/text/text.jsp`
* `/content/.{.}/libs/foundation/components/text/text.jsp`
* `/apps/sling/config/org.apache.felix.webconsole.internal.servlet.OsgiManager.config/jcr%3acontent/jcr%3adata`
* `/libs/foundation/components/primary/cq/workflow/components/participants/json.GET.servlet`
* `/content.pages.json`
* `/content.languages.json`
* `/content.blueprint.json`
* `/content.-1.json`
* `/content.10.json`
* `/content.infinity.json`
* `/content.tidy.json`
* `/content.tidy.-1.blubber.json`
* `/content/dam.tidy.-100.json`
* `/content/content/geometrixx.sitemap.txt `
* `/content/add_valid_page.query.json?statement=//*`
* `/content/add_valid_page.qu%65ry.js%6Fn?statement=//*`
* `/content/add_valid_page.query.json?statement=//*[@transportPassword]/(@transportPassword%20|%20@transportUri%20|%20@transportUser)`
* `/content/add_valid_path_to_a_page/_jcr_content.json`
* `/content/add_valid_path_to_a_page/jcr:content.json`
* `/content/add_valid_path_to_a_page/_jcr_content.feed`
* `/content/add_valid_path_to_a_page/jcr:content.feed`
* `/content/add_valid_path_to_a_page/pagename._jcr_content.feed`
* `/content/add_valid_path_to_a_page/pagename.jcr:content.feed`
* `/content/add_valid_path_to_a_page/pagename.docview.xml`
* `/content/add_valid_path_to_a_page/pagename.docview.json`
* `/content/add_valid_path_to_a_page/pagename.sysview.xml`
* `/etc.xml`
* `/content.feed.xml`
* `/content.rss.xml`
* `/content.feed.html`
* `/content/add_valid_page.html?debug=layout`
* `/projects`
* `/tagging`
* `/etc/replication.html`
* `/etc/cloudservices.html`
* `/welcome`

匿名書き込みアクセスが有効かどうかを判断するには、ターミナルまたはコマンドプロンプトで次のコマンドを実行します。 ノードにデータを書き込めないはずです。

`curl -X POST "https://anonymous:anonymous@hostname:port/content/usergenerated/mytestnode"`

Dispatcher キャッシュを無効にして、コード 403 の応答を受け取るようにするには、ターミナルまたはコマンドプロンプトで次のコマンドを発行します。

`curl -H "CQ-Handle: /content" -H "CQ-Path: /content" https://yourhostname/dispatcher/invalidate.cache`

## バニティー URL へのアクセスの有効化 {#enabling-access-to-vanity-urls-vanity-urls}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2015-03-25T14:23:05.185-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For https://jira.corp.adobe.com/browse/DOC-4812</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The "com.adobe.granite.dispatcher.vanityurl.content" package needs to be made public before publishing this contnet.</p>
 -->

AEMページ用に設定されたバニティー URL へのアクセスを有効にするように Dispatcher を設定します。

バニティー URL へのアクセスが有効な場合、Dispatcher は定期的にレンダーインスタンス上で実行されるサービスを呼び出し、バニティー URL のリストを取得します。 Dispatcher はこのリストをローカルファイルに保存します。 ページの要求が `/filter` セクションのフィルターによって拒否されると、Dispatcher はバニティー URL のリストを調べます。拒否された URL がリストにある場合、Dispatcher はバニティー URL へのアクセスを許可します。

バニティー URL へのアクセスを有効にするには、以下の例のような `/vanity_urls` セクションを `/farms` セクションに追加します。

```xml
 /vanity_urls {
      /url "/libs/granite/dispatcher/content/vanityUrls.html"
      /file "/tmp/vanity_urls"
      /delay 300
 }
```

`/vanity_urls` セクションには、以下のプロパティが含まれます。

* `/url`：レンダーインスタンス上で実行されているバニティー URL サービスへのパス。このプロパティの値は、`"/libs/granite/dispatcher/content/vanityUrls.html"` である必要があります。

* `/file`：Dispatcher がバニティー URL のリストを保存するローカルファイルへのパス。Dispatcher がこのファイルに対する書き込みアクセス権を持っていることを確認してください。
* `/delay`：バニティー URL サービスの呼び出し間隔（秒単位）。

>[!NOTE]
>
>レンダリングがAEMのインスタンスである場合は、 [ソフトウェア配布からの VanityURLS-Components パッケージ](https://experience.adobe.com/#/downloads/content/software-distribution/en/aem.html?package=/content/software-distribution/en/details.html/content/dam/aem/public/adobe/packages/granite/vanityurls-components) バニティー URL サービスを有効にする。 ( 詳しくは、 [ソフトウェア配布](https://experienceleague.adobe.com/docs/experience-manager-65/administering/contentmanagement/package-manager.html?lang=en#software-distribution) 詳細はこちら )。

バニティー URL へのアクセスを有効にするには、以下の手順を実行します。

1. レンダーサービスがAEMインスタンスの場合、 `com.adobe.granite.dispatcher.vanityurl.content` パッケージをパブリッシュインスタンス上に作成します（上記の注意を参照）。
1. AEM または CQ ページ向けに設定したバニティー URL ごとに、[`/filter`](#configuring-access-to-content-filter) 設定がその URL を拒否していることを確認します。必要に応じて、この URL を拒否するフィルターを追加します。
1. `/farms` の下に `/vanity_urls` セクションを追加します。
1. Apache Web サーバーを再起動します。

## シンジケーション要求の転送 — /propagateSyndPost {#forwarding-syndication-requests-propagatesyndpost}

シンジケーション要求は Dispatcher 専用なので、デフォルトではレンダラー (AEMインスタンスなど ) に送信されません。

必要に応じて、 `/propagateSyndPost` プロパティを `"1"` シンジケーション要求を Dispatcher に転送する場合。 設定する場合、フィルターセクションで POST 要求が拒否されていないことを確認する必要があります。

## Dispatcher キャッシュの設定 — /cache {#configuring-the-dispatcher-cache-cache}

`/cache` セクションは、Dispatcher によるドキュメントのキャッシュ方法を制御します。次のいくつかのサブプロパティを設定して、キャッシュ戦略を実装します。

* `/docroot`
* `/statfile`
* `/serveStaleOnError`
* `/allowAuthorized`
* `/rules`
* `/statfileslevel`
* `/invalidate`
* `/invalidateHandler`
* `/allowedClients`
* `/ignoreUrlParams`
* `/headers`
* `/mode`
* `/gracePeriod`
* `/enableTTL`

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
>権限を区別するキャッシュについては、[セキュリティ保護されたコンテンツのキャッシュ](permissions-cache.md)を参照してください。

### キャッシュディレクトリの指定 {#specifying-the-cache-directory}

`/docroot` プロパティは、キャッシュされたファイルを保存するディレクトリを識別します。

>[!NOTE]
>
>Dispatcher と Web サーバーが同じファイルを処理できるよう、この値は Web サーバーのドキュメントルートとまったく同じパスの必要があります。\
>Dispatcher のキャッシュファイルが使用されると、Web サーバーは正しいステータスコードを配信します。そのため、キャッシュファイルを見つけることが重要になります。

複数のファームを使用する場合は、ファームごとに異なるドキュメントルートを使用する必要があります。

### statfile の命名 {#naming-the-statfile}

`/statfile` プロパティは、statfile として使用するファイルを識別します。Dispatcher は、このファイルを使用して、最新のコンテンツ更新の時刻を登録します。 statfile は、Web サーバー上の任意のファイルにすることができます。

statfile にはコンテンツがありません。 コンテンツが更新されると、Dispatcher がタイムスタンプを更新します。 デフォルトの statfile の名前はです。 `.stat` とは docroot に保存されます。 Dispatcher は、statfile へのアクセスをブロックします。

>[!NOTE]
>
>次の場合 `/statfileslevel` が設定されている場合、Dispatcher は `/statfile` プロパティと使用 `.stat` 名前として。

### エラー発生時の古くなったドキュメントの提供 {#serving-stale-documents-when-errors-occur}

`/serveStaleOnError` プロパティは、レンダーサーバーがエラーを返した場合に Dispatcher が無効になったドキュメントを返すかどうかを制御します。デフォルトでは、statfile にアクセスし、キャッシュされたコンテンツが無効になると、Dispatcher は次回要求時にキャッシュされたコンテンツを削除します。

次の場合 `/serveStaleOnError` が `"1"`に設定すると、レンダーサーバーが成功応答を返さない限り、Dispatcher は無効になったコンテンツをキャッシュから削除しません。 AEM からの応答 5xx または接続タイムアウトによって、Dispatcher は期限切れのコンテンツを返し、HTTP ステータス 111（再検証失敗）で応答します。

### 認証使用時のキャッシュ {#caching-when-authentication-is-used}

`/allowAuthorized` プロパティは、以下のいずれかの認証情報を含む要求をキャッシュするかどうかを制御します。

* The `authorization` ヘッダー
* という名前の cookie `authorization`
* という名前の cookie `login-token`

デフォルトでは、この認証情報を含む要求は、キャッシュされたドキュメントがクライアントに返されたときに認証が実行されないので、キャッシュされません。 この設定により、Dispatcher は、必要な権限を持たないユーザーに対して、キャッシュされたドキュメントを提供できなくなります。

ただし、認証済みドキュメントのキャッシュが要件によって許可されている場合は、 `/allowAuthorized` を 1 に変更します。

`/allowAuthorized "1"`

>[!NOTE]
>
>セッション管理（`/sessionmanagement` プロパティを使用）を有効にするには、`/allowAuthorized` プロパティを `"0"` に設定する必要があります。

### キャッシュするドキュメントの指定 {#specifying-the-documents-to-cache}

`/rules` プロパティは、ドキュメントパスに応じてキャッシュされるドキュメントを制御します。`/rules` プロパティにかかわらず、Dispatcher は以下の状況にあるドキュメントをキャッシュしません。

* リクエスト URI に疑問符 (`?`) をクリックします。
   * キャッシュの必要がない、検索結果などの動的ページを示します。
* ファイル拡張子が不明の場合。
   * Web サーバーでドキュメントのタイプ（MIME タイプ）を判別するために、拡張子が必要です。
* 認証ヘッダーは設定（設定可能）されます。
* AEM インスタンスが以下のヘッダーで応答する場合。

   * `no-cache`
   * `no-store`
   * `must-revalidate`

>[!NOTE]
>
>（HTTP ヘッダー用の）GET または HEAD メソッドは、Dispatcher によってキャッシュ可能です。応答ヘッダーのキャッシュについて詳しくは、[HTTP 応答ヘッダーのキャッシュ](#caching-http-response-headers)セクションを参照してください。

各項目 ( `/rules` プロパティにはが含まれます [`glob`](#designing-patterns-for-glob-properties) パターンとタイプ：

* The `glob` パターンは、ドキュメントのパスを照合するために使用されます。
* タイプは、 `glob` パターン。 値は `allow` （ドキュメントをキャッシュする）または `deny` （ドキュメントを常にレンダリングする場合）。

動的ページがない場合（上記のルールで既に除外されたページを超える場合）、すべてをキャッシュするように Dispatcher を設定できます。 「ルール」セクションは次のようになります。

```xml
/rules
  {
    /0000  {  /glob "*"   /type "allow" }
  }
```

glob プロパティについて詳しくは、[glob プロパティのパターンのデザイン](#designing-patterns-for-glob-properties)を参照してください。

ページ内に動的なセクション（ニュースアプリケーションなど）や、非公開のユーザーグループで使用するセクションがある場合は、以下のように例外を定義できます。

>[!NOTE]
>
>キャッシュされたページでユーザー権限がチェックされないので、閉じられたユーザーグループをキャッシュしません。

```xml
/rules
  {
   /0000  { /glob "*" /type "allow" }
   /0001  { /glob "/en/news/*" /type "deny" }
   /0002  { /glob "*/private/*" /type "deny"  }
  }
```

**圧縮**

Apache Web サーバーでは、キャッシュされたドキュメントを圧縮できます。 クライアントから要求された場合は、Apache から圧縮形式のドキュメントを返すことができます。例えば、Apache モジュールの `mod_deflate` を有効にすることで、圧縮は自動的に行われます。

```xml
AddOutputFilterByType DEFLATE text/plain
```

このモジュールは Apache 2.x にデフォルトでインストールされています。

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

`/statfileslevel` プロパティを使用して、キャッシュされたファイルをパスに応じて選択的に無効化します。

* Dispatcher は、ドキュメントルートフォルダーから指定したレベルまでの各フォルダーに `.stat` ファイルを作成します。ドキュメントルートはレベル 0 です。
* `.stat` ファイルが更新されると、ファイルは無効化されます。`.stat` ファイルの最終変更日と、キャッシュされたドキュメントの最終変更日を比較します。`.stat` ファイルのほうが新しい場合は、ドキュメントを再取得します。

* 特定のレベルのファイルが無効化された場合、 **すべて** `.stat` docroot からのファイル **から** 無効化されたファイルのレベル、または設定された `statsfilevel` （どちらか小さい方）がタッチされます。

   * 例えば、 `statfileslevel` プロパティが 6 に設定され、ファイルはレベル 5 で無効化され、 `.stat` docroot から 5 までのファイルが touch されます。 この例では、ファイルがレベル 7 で無効化されると、 `stat` docroot から 6 までのファイルが touch されます ( `/statfileslevel = "6"`) をクリックします。

リソースのみ **道に沿って** 無効化されたファイルに影響を与えます。 次の例を考えて見ましょう。Web サイトで `/content/myWebsite/xx/.` 構造を使用していて、`statfileslevel` を 3 に設定している場合、`.stat` ファイルは次のように作成されます。

* `docroot`
* `/content`
* `/content/myWebsite`
* `/content/myWebsite/*xx*`

ファイルが `/content/myWebsite/xx` が無効化された場合、 `.stat` docroot からまでのファイル `/content/myWebsite/xx`がタッチされます。 このシナリオは、次の場合にのみ該当します。 `/content/myWebsite/xx` 例えば、 `/content/myWebsite/yy` または `/content/anotherWebSite`.

>[!NOTE]
>
>無効化は、余分なヘッダーを送信することで防ぐことができます `CQ-Action-Scope:ResourceOnly`. この方法を使用すると、キャッシュの他の部分を無効化することなく、特定のリソースをフラッシュできます。 詳しくは、 [このページ](https://adobe-consulting-services.github.io/acs-aem-commons/features/dispatcher-flush-rules/index.html) および [手動での Dispatcher キャッシュの無効化](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/page-invalidate.html?lang=en#configuring) を参照してください。

>[!NOTE]
>
>注意：`/statfileslevel` プロパティの値を指定した場合、`/statfile` プロパティは無視されます。

### キャッシュされたファイルの自動無効化 {#automatically-invalidating-cached-files}

`/invalidate` プロパティは、コンテンツが更新されたときに自動的に無効化されるドキュメントを定義します。

自動無効化の場合、Dispatcher はキャッシュされたファイルをコンテンツの更新後に削除しませんが、次に要求されたときにその有効性を確認します。 自動無効化されていないキャッシュ内のドキュメントは、コンテンツの更新によって明示的に削除されるまで、キャッシュに残ります。

自動無効化は、通常、HTMLページで使用されます。 HTMLページには他のページへのリンクが含まれることが多いので、コンテンツの更新がページに影響を与えるかどうかを判断するのが困難です。 コンテンツの更新時にすべての関連ページが無効化されるようにするには、すべての HTML ページを自動的に無効化します。以下の設定は、すべての HTML ページを無効化します。

```xml
  /invalidate
  {
   /0000  { /glob "*" /type "deny" }
   /0001  { /glob "*.html" /type "allow" }
  }
```

glob プロパティについて詳しくは、[glob プロパティのパターンのデザイン](#designing-patterns-for-glob-properties)を参照してください。

この設定により、次のアクティビティが発生します。 `/content/wknd/us/en` 有効化済み：

* 「en」のパターンを持つすべてのファイル。*は `/content/wknd/us` フォルダー。
* The `/content/wknd/us/en./_jcr_content` フォルダーが削除されました。
* その他すべてのファイルで、 `/invalidate` 設定は直ちには削除されません。 これらのファイルは、次のリクエストが発生すると削除されます。 この例では、 `/content/wknd.html` は削除されません。削除されるのは、 `/content/wknd.html` がリクエストされました。

ダウンロード用に自動生成されたPDFと ZIP ファイルを提供する場合は、これらのファイルも自動的に無効にする必要が生じる場合があります。 設定例を次に示します。

```xml
/invalidate
  {
   /0000 { /glob "*" /type "deny" }
   /0001 { /glob "*.html" /type "allow" }
   /0002 { /glob "*.zip" /type "allow" }
   /0003 { /glob "*.pdf" /type "allow" }
  }
```

AEMとAdobe Analyticsの統合により、 `analytics.sitecatalyst.js` ファイルを Web サイト内に配置する必要があります。 例 `dispatcher.any` Dispatcher で提供されるファイルには、このファイルに対して次の無効化ルールが含まれます。

```xml
{
   /glob "*/analytics.sitecatalyst.js"  /type "allow"
}
```

### カスタム無効化スクリプトの使用 {#using-custom-invalidation-scripts}

The `/invalidateHandler` プロパティを使用すると、Dispatcher が受け取った無効化要求ごとに呼び出されるスクリプトを定義できます。

次の引数を指定して呼び出されます。

* ハンドル — 無効化されるコンテンツのパス
* アクション — レプリケーションアクション（アクティブ化、非アクティブ化など）
* Action Scope — レプリケーションアクションの範囲 ( ヘッダーが `CQ-Action-Scope: ResourceOnly` が送信された場合は、 [AEMからのキャッシュされたページの無効化](page-invalidate.md) （詳細）

この方法は、複数の異なる使用例をカバーするために使用できます。 例えば、他のアプリケーション固有のキャッシュを無効にする、またはページの外部化された URL とドキュメントルート内の URL がコンテンツパスと一致しない場合の処理をおこないます。

以下の例では、無効化要求のたびにをファイルに記録します。

```xml
/invalidateHandler "/opt/dispatcher/scripts/invalidate.sh"
```

#### サンプルの無効化ハンドラースクリプト {#sample-invalidation-handler-script}

```shell
#!/bin/bash

printf "%-15s: %s %s" $1 $2 $3>> /opt/dispatcher/logs/invalidate.log
```

### キャッシュをフラッシュできるクライアントの制限 {#limiting-the-clients-that-can-flush-the-cache}

The `/allowedClients` プロパティは、キャッシュのフラッシュを許可する特定のクライアントを定義します。 このグロビングパターンが、IP と照合されます。

以下の例の内容は次のとおりです。

1. 任意のクライアントへのアクセスを拒否
1. localhost へのアクセスを明示的に許可

```xml
/allowedClients
  {
   /0001 { /glob "*.*.*.*"  /type "deny" }
   /0002 { /glob "127.0.0.1" /type "allow" }
  }
```

glob プロパティについて詳しくは、[glob プロパティのパターンのデザイン](#designing-patterns-for-glob-properties)を参照してください。

>[!CAUTION]
>
>次の項目を定義することをお勧めします。 `/allowedClients`.
>
>実行されない場合、任意のクライアントが呼び出しを発行してキャッシュをクリアできます。 繰り返し実行すると、サイトのパフォーマンスに大きな影響を与える可能性があります。

### URL パラメーターを無視する {#ignoring-url-parameters}

`ignoreUrlParams` セクションでは、ページをキャッシュするかキャッシュから提供するかを判断するときにどの URL パラメーターを無視するかを定義します。

* 要求 URL に無視されるパラメーターが含まれている場合、ページはキャッシュされます。
* リクエスト URL に、無視されない 1 つ以上のパラメーターが含まれている場合、ページはキャッシュされません。

あるページのパラメーターが無視された場合、そのページが初めて要求されたときにそのページがキャッシュされます。 ページに対する後続のリクエストは、リクエスト内のパラメーターの値に関係なく、キャッシュされたページに提供されます。

>[!NOTE]
>
>次の設定をお勧めします。 `ignoreUrlParams` 設定を適切な方許可リストに加える法で行う。 そのため、すべてのクエリパラメーターは無視され、既知のクエリパラメーターまたは予期されたクエリパラメーターのみが無視されます（「拒否」）。 その他の詳細と例については、 [このページ](https://github.com/adobe/aem-dispatcher-optimizer-tool/blob/main/docs/Rules.md#dot---the-dispatcher-publish-farm-cache-should-have-its-ignoreurlparams-rules-configured-in-an-allow-list-manner).

無視するパラメーターを指定するには、`ignoreUrlParams` プロパティに glob ルールを追加します。

* 要求に URL パラメーターが含まれているにもかかわらずページをキャッシュするには、そのパラメーター（無視）を許可する glob プロパティを作成します。
* ページがキャッシュされないようにするには、このパラメーターを拒否する glob プロパティを作成します（無視します）。

>[!NOTE]
>
>glob プロパティを設定する際は、クエリパラメーター名と一致する必要があります。 例えば、次の URL の「p1」パラメーターを無視する場合は、 `http://example.com/path/test.html?p1=test&p2=v2`を指定した場合、glob プロパティは次のようになります。
> `/0002 { /glob "p1" /type "allow" }`

次の例では、Dispatcher が、 `nocache` パラメーター。 したがって、 `nocache` パラメーターは、Dispatcher によってキャッシュされることはありません。

```xml
/ignoreUrlParams
{
    # ignore-all-url-parameters-by-dispatcher-and-requests-are-cached
    /0001 { /glob "*" /type "allow" }
    # allow-the-url-parameter-nocache-to-bypass-dispatcher-on-every-request
    /0002 { /glob "nocache" /type "deny" }
}
```

のコンテキストでは、 `ignoreUrlParams` 上記の設定例では、次の HTTP 要求によって、ページがキャッシュされます。これは、 `willbecached` パラメーターは無視されます：

```xml
GET /mypage.html?willbecached=true
```

のコンテキストでは、 `ignoreUrlParams` 設定例では、次の HTTP 要求によってページが **not** キャッシュされるのは、 `nocache` パラメーターは無視されません：

```xml
GET /mypage.html?nocache=true
GET /mypage.html?nocache=true&willbecached=true
```

glob プロパティについて詳しくは、[glob プロパティのパターンのデザイン](#designing-patterns-for-glob-properties)を参照してください。

### HTTP 応答ヘッダーのキャッシュ {#caching-http-response-headers}

>[!NOTE]
>
>この機能は、バージョンで使用できます **4.1.11** Dispatcher の

`/headers` プロパティを使用して、Dispatcher がキャッシュする HTTP ヘッダータイプを定義できます。キャッシュされていないリソースへの最初のリクエストでは、設定済みの値（以下の設定サンプルを参照）のいずれかに一致するすべてのヘッダーが、キャッシュファイルの隣の別ファイルに格納されます。キャッシュされたリソースに対する後続のリクエストでは、保存されたヘッダーが応答に追加されます。

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
>ファイルのグロビング文字は使用できません。 詳しくは、[glob プロパティのパターンのデザイン](#designing-patterns-for-glob-properties)を参照してください。

>[!NOTE]
>
>Dispatcher を使用して AEMから ETag 応答ヘッダーを保存および配信する必要がある場合は、以下の手順を実行します。
>
>* `/cache/headers` セクションにヘッダー名を追加します。
>* 以下を追加します。 [Apache ディレクティブ](https://httpd.apache.org/docs/2.4/mod/core.html#fileetag) Dispatcher 関連のセクションで、以下の操作を実行します。
>
>```xml
>FileETag none
>```

### Dispatcher キャッシュファイルの権限 {#dispatcher-cache-file-permissions}

`mode` プロパティは、新しいディレクトリおよびキャッシュ内のファイルに適用されるファイル権限を指定します。この設定は、呼び出し元のプロセスの `umask` によって制限されます。これは、次の値のうち 1 つまたは複数の合計から構成される 8 進数です。

* `0400` 所有者による読み取りを許可します。
* `0200` 所有者による書き込みを許可します。
* `0100` 所有者がディレクトリ内を検索できるようにします。
* `0040` グループメンバーによる読み取りを許可します。
* `0020` グループメンバーによる書き込みを許可します。
* `0010` グループメンバーがディレクトリ内を検索するのを許可します。
* `0004` 他のユーザーによる読み取りを許可します。
* `0002` 他のユーザーによる書き込みを許可します。
* `0001` 他のユーザーがディレクトリ内を検索することを許可します。

デフォルト値は `0755` これにより、所有者は読み取り、書き込み、または検索を行い、グループとその他のユーザーは読み取りや検索を行うことができます。

### .stat ファイルの更新のスロットリング {#throttling-stat-file-touching}

デフォルトの `/invalidate` のプロパティでは、アクティベーションごとに、すべての `.html` ファイルが無効化されます（パスが `/invalidate` セクションに一致する場合）。トラフィック量が多い Web サイトでは、複数回のアクティベーションを実行すると、バックエンドの CPU 負荷が増加します。 このようなシナリオでは、「スロットル」が望ましい `.stat` web サイトをレスポンシブに保つために、ファイルに touch します。 このアクションは、 `/gracePeriod` プロパティ。

The `/gracePeriod` プロパティは、自動的に無効化された古いリソースを、最後に発生したアクティベーション後にキャッシュから引き続き使用できる秒数を定義します。 このプロパティは、アクティベートのバッチによってキャッシュ全体が繰り返し無効化されるような設定で使用できます。推奨される値は 2 秒です。

詳細については、上記の `/invalidate` および `/statfileslevel` セクションも参照してください。

### 時間に基づくキャッシュの無効化の設定 — /enableTTL {#configuring-time-based-cache-invalidation-enablettl}

時間ベースのキャッシュの無効化は、 `/enableTTL` プロパティに含まれ、HTTP 標準の正規の有効期限ヘッダーが存在することを確認します。 プロパティを 1 に設定した場合 (`/enableTTL "1"`) の場合、バックエンドからの応答ヘッダーを評価します。 ヘッダーに `Cache-Control`, `max-age` または `Expires` 日付：キャッシュされたファイルの横に、有効期限と同じ変更時刻を持つ、補助的な空のファイルが作成されます。 変更時刻を過ぎてキャッシュされたファイルがリクエストされると、バックエンドから自動的に再リクエストされます。

Dispatcher 4.3.5 以前は、TTL 無効化ロジックは設定済みの TTL 値にのみ基づいていました。 Dispatcher 4.3.5 では、両方の TTL が設定されます。 **および** Dispatcher キャッシュの無効化ルールが計上されます。 したがって、キャッシュされたファイルの場合は次のようになります。

1. 次の場合 `/enableTTL` が 1 に設定されている場合、ファイルの有効期限がチェックされます。 設定された TTL に従ってファイルの有効期限が切れた場合、他のチェックは実行されず、キャッシュされたファイルがバックエンドから再リクエストされます。
2. ファイルが期限切れでないか、または `/enableTTL` が設定されていない場合、標準キャッシュの無効化ルールが適用されます ( [/statfileslevel](#invalidating-files-by-folder-level) および [/invalidate](#automatically-invalidating-cached-files). つまり、Dispatcher は、TTL の期限が切れていないファイルを無効にできます。

この新しい実装では、ファイルの TTL が長い（CDN 上などで）場合でも、TTL が期限切れになっていなくても無効にできます。 Dispatcher のキャッシュヒット率よりもコンテンツの鮮度を優先します。

逆に、必要に応じて **のみ** 有効期限ロジックをファイルに適用し、 `/enableTTL` を 1 に設定し、そのファイルを標準のキャッシュ無効化メカニズムから除外します。 例えば、次のことができます。

* このファイルを無視するには、 [無効化ルール](#automatically-invalidating-cached-files) キャッシュセクション内で使用します。 以下のスニペットでは、で終わるすべてのファイルが `.example.html` は無視され、設定された TTL を過ぎた場合にのみ期限切れになります。

```xml
  /invalidate
  {
   /0000  { /glob "*" /type "deny" }
   /0001  { /glob "*.html" /type "allow" }
   /0002  { /glob "*.example.html" /type "deny" }
  }
```

* 高い [/statfilelevel](#invalidating-files-by-folder-level) したがって、ファイルは自動的に無効化されません。

これにより、次のことが可能になります。 `.stat` ファイルの無効化は使用されず、指定したファイルに対して TTL の有効期限のみが有効になります。

>[!NOTE]
>
>次の点に注意してください。 `/enableTTL` を 1 に設定すると、dispatcher 側でのみ TTL キャッシュが有効になります。 したがって、追加ファイルに含まれる TTL 情報（上記を参照）は、Dispatcher からそのようなファイルタイプを要求する他のユーザーエージェントには提供されません。 CDN やブラウザーなど、ダウンストリームシステムにキャッシュヘッダーを提供する場合は、 `/cache/headers` を参照してください。

>[!NOTE]
>
>この機能は、バージョンで使用できます **4.1.11** または Dispatcher の後半で呼び出します。

## ロードバランシングの設定 — /statistics {#configuring-load-balancing-statistics}

`/statistics` セクションは、Dispatcher が各レンダーの応答性のスコアを付けるファイルのカテゴリを定義します。Dispatcher は、スコアを使用して、要求を送信するレンダーを決定します。

作成した各カテゴリが glob パターンを定義します。 Dispatcher は、要求されたコンテンツの URI をこれらのパターンと比較し、要求されたコンテンツのカテゴリを判断します。

* カテゴリの順序によって、URI と比較される順序が決まります。
* URI に一致する最初のカテゴリパターンがファイルのカテゴリになります。 評価されるカテゴリパターンはこれ以上ありません。

Dispatcher は、最大 8 つの統計カテゴリをサポートします。 8 つを超えるカテゴリを定義した場合は、最初の 8 つのカテゴリのみが使用されます。

**レンダリング選択**

Dispatcher がレンダリングされたページを必要とするたびに、次のアルゴリズムを使用してレンダリングを選択します。

1. 要求の `renderid` cookie にレンダー名が格納されている場合、Dispatcher はそのレンダーを使用します。
1. 要求に `renderid` cookie が含まれていない場合、Dispatcher はレンダー統計を比較します。

   1. Dispatcher が要求 URI のカテゴリを決定します。
   1. Dispatcher は、そのカテゴリに対する応答スコアが最も低いレンダーを決定し、そのレンダーを選択します。

1. レンダリングがまだ選択されていない場合は、リストの最初のレンダリングを使用します。

レンダーのカテゴリのスコアは、以前の応答時間と、Dispatcher が試行する、以前の失敗した成功した接続に基づいています。 試行のたびに、要求された URI のカテゴリのスコアが更新されます。

>[!NOTE]
>
>ロードバランシングを使用しない場合は、このセクションを省略できます。

### 統計カテゴリの定義 {#defining-statistics-categories}

レンダリングの選択に関する統計を保持するドキュメントのタイプごとにカテゴリを定義します。 The `/statistics` セクションには `/categories` 」セクションに入力します。 カテゴリを定義するには、 `/categories` セクションに次の形式が含まれる場合。

`/name { /glob "pattern"}`

カテゴリ `name` は、ファームに対して一意である必要があります。`pattern` については、[glob プロパティのパターンのデザイン](#designing-patterns-for-glob-properties)のセクションで説明します。

URI のカテゴリを決定するために、Dispatcher は一致が見つかるまで、URI と各カテゴリパターンを比較します。 Dispatcher はリストの最初のカテゴリから始まり、順番に続きます。 したがって、より具体的なパターンを持つカテゴリを最初に配置します。

例えば、Dispatcher はデフォルト `dispatcher.any` ファイルは、カテゴリと othersHTMLを定義します。 HTML カテゴリのほうが具体的なので、先頭に配置されています。

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

### Dispatcher 統計へのサーバー使用不可の反映 {#reflecting-server-unavailability-in-dispatcher-statistics}

`/unavailablePenalty` プロパティは、レンダーへの接続が失敗した場合にレンダー統計に適用される時間（0.1 秒単位）を設定します。Dispatcher は、要求された URI に一致する統計カテゴリにこの時間を追加します。

例えば、AEM が実行されていない（したがってリッスンしていない）か、ネットワーク関連の問題が発生したので、指定したホスト名およびポートへの TCP/IP 接続を確立できない場合にペナルティが適用されます。

`/unavailablePenalty` プロパティは、`/farm` セクション（`/statistics` セクションの兄弟）の直接の子です。

いいえの場合 `/unavailablePenalty` プロパティが存在する場合、値は `"1"` が使用されます。

```xml
/unavailablePenalty "1"
```

## スティッキー接続フォルダーの識別 — /stickyConnectionsFor {#identifying-a-sticky-connection-folder-stickyconnectionsfor}

The `/stickyConnectionsFor` プロパティは、スティッキードキュメントを格納する 1 つのフォルダーを定義します。 このプロパティには、URL を使用してアクセスします。 Dispatcher は、このフォルダーにある 1 人のユーザーから同じレンダーインスタンスに、すべての要求を送信します。 スティッキー接続を使用すると、セッションデータがすべてのドキュメントに対して一貫性を保ち、存在することを確認できます。 このメカニズムは、`renderid` cookie を利用しています。

次の例は、/products フォルダーへのスティッキー接続を定義しています。

```xml
/stickyConnectionsFor "/products"
```

ページが複数のコンテンツノードのコンテンツで組み立てられている場合は、コンテンツへのパスをリストする `/paths` プロパティを含めます。例えば、`/content/image`、`/content/video` および `/var/files/pdfs` のコンテンツを含むページがあるとします。以下の設定を使用すると、ページ上のすべてのコンテンツに対してスティッキー接続が有効になります。

```xml
/stickyConnections {
  /paths {
    "/content/image"
    "/content/video"
    "/var/files/pdfs"
  }
}
```

### httpOnly {#httponly}

スティッキー接続が有効な場合、Dispatcher モジュールは `renderid` cookie. この cookie には `httponly` フラグ。セキュリティを強化するために追加する必要があります。 次の項目を追加します。 `httponly` フラグを設定する `httpOnly` プロパティを `/stickyConnections` のノード `dispatcher.any` 設定ファイル。 プロパティの値 ( `0` または `1`) は、 `renderid` cookie に `HttpOnly` 属性が追加されました。 デフォルト値は `0`は、属性が追加されていないことを意味します。

詳しくは、 `httponly` フラグ、読み取り [このページ](https://www.owasp.org/index.php/HttpOnly).

### secure {#secure}

スティッキー接続が有効な場合、Dispatcher モジュールは `renderid` cookie. この cookie には `secure` フラグ。セキュリティを強化するために追加する必要があります。 次の項目を追加します。 `secure` フラグ設定 `secure` プロパティを `/stickyConnections` のノード `dispatcher.any` 設定ファイル。 プロパティの値 ( `0` または `1`) は、 `renderid` cookie に `secure` 属性が追加されました。 デフォルト値は `0`は、属性が追加されたことを意味します。 **if** 受信リクエストはセキュリティで保護されています。 値が `1`を指定した場合、受信リクエストがセキュアかどうかに関係なく、secure フラグが追加されます。

## レンダー接続エラーの処理 {#handling-render-connection-errors}

レンダーサーバーが 500 エラー、すなわち使用不可を返した場合の Dispatcher の動作を設定します。

### ヘルスチェックページの指定 {#specifying-a-health-check-page}

`/health_check` プロパティを使用して、ステータスコード 500 が発生したときに確認する URL を指定します。このページもステータスコード 500 を返す場合、インスタンスは使用不可能で、設定可能なタイムペナルティ ( `/unavailablePenalty`) は、再試行の前にレンダーに適用されます。

```xml
/health_check
  {
  # Page gets contacted when an instance returns a 500
  /url "/health_check.html"
  }
```

### ページ再試行遅延の指定 {#specifying-the-page-retry-delay}

The `/retryDelay` プロパティは、Dispatcher が待機する、ファームレンダーへの接続試行周期（秒単位）を設定します。 周期ごとに、Dispatcher が 1 つのレンダーに対して接続を試行する最大回数は、ファーム内のレンダーの数です。

`/retryDelay` が明示的に定義されていない場合、Dispatcher は値 `"1"` を使用します。通常は、デフォルト値が適切です。

```xml
/retryDelay "1"
```

### 再試行回数の設定 {#configuring-the-number-of-retries}

`/numberOfRetries` プロパティは、Dispatcher がレンダーに対して実行する。接続試行周期の最大回数を設定します。この回数の再試行の後、Dispatcher がレンダーに正常に接続できない場合、Dispatcher は失敗した応答を返します。

周期ごとに、Dispatcher が 1 つのレンダーに対して接続を試行する最大回数は、ファーム内のレンダーの数です。したがって、Dispatcher が接続を試みる最大回数は（`/numberOfRetries`）x（レンダー数）になります。

明示的な定義がない場合、デフォルト値は `5`.

```xml
/numberOfRetries "5"
```

### フェイルオーバーメカニズムの使用 {#using-the-failover-mechanism}

元の要求が失敗した場合に、別のレンダーに要求を再送信するには、Dispatcher ファームのフェイルオーバーメカニズムを有効にします。 フェイルオーバーが有効な場合、Dispatcher は次の動作を行います。

* レンダーへの要求が HTTP ステータス 503(UNAVAILABLE) を返した場合、Dispatcher は要求を別のレンダーに送信します。
* レンダーへの要求が HTTP ステータス 50x（503 以外）を返した場合、Dispatcher は `health_check` プロパティに設定されているページへの要求を送信します。
   * ヘルスチェックが 500(INTERNAL_SERVER_ERROR) を返した場合、Dispatcher は元の要求を別のレンダーに送信します。
   * ヘルスチェックが HTTP ステータス 200 を返す場合、Dispatcher は最初の HTTP 500 エラーをクライアントに返します。

フェイルオーバーを有効にするには、次の行をファーム（または Web サイト）に追加します。

```xml
/failover "1"
```

>[!NOTE]
>
>本文を含む HTTP 要求を再試行するには、Dispatcher が `Expect: 100-continue` 要求ヘッダーをレンダーに送信してから、実際のコンテンツをスプールします。CQSE を使用した CQ 5.5 は、100(CONTINUE) またはエラーコードを使用して即座に応答します。 その他のサーブレットコンテナもサポートされています。

## 中断エラーを無視 — /ignoreEINTR {#ignoring-interruption-errors-ignoreeintr}

>[!CAUTION]
>
>このオプションは不要です。 次のログメッセージが表示された場合にのみ使用してください。
>
>`Error while reading response: Interrupted system call`

ファイル・システム指向のシステム・コールが中断される `EINTR` システムコールの対象が、NFS を介してアクセスするリモートシステム上にある場合。 これらのシステムコールがタイムアウトしたり中断されたりする可能性があるかどうかは、基になるファイルシステムがローカルマシンにどのようにマウントされたかに基づきます。

以下を使用します。 `/ignoreEINTR` インスタンスにそのような設定があり、ログに次のメッセージが含まれる場合は、パラメーターを指定します。

`Error while reading response: Interrupted system call`

内部的に、Dispatcher は次のように表現できるループを使用して、リモートサーバー (AEM) から応答を読み取ります。

```text
while (response not finished) {  
read more data  
}
```

このようなメッセージは、「`EINTR`」セクションで `read more data` が発生した場合に生成されることがあります。原因は、データの受信前にシグナルを受信したことです。

このような中断を無視するには、次のパラメーターを `dispatcher.any` ( `/farms`):

`/ignoreEINTR "1"`

`/ignoreEINTR` を `"1"` に設定すると、Dispatcher は応答全体が読み込まれるまでデータの読み込みを続行します。デフォルト値は `0` オプションを無効にします。

## glob プロパティのパターンのデザイン {#designing-patterns-for-glob-properties}

Dispatcher 設定ファイルのいくつかのセクションでは、`glob` プロパティをクライアント要求の選択条件として使用します。の値 `glob` プロパティとは、要求されたリソースのパスやクライアントの IP アドレスなど、Dispatcher が要求の側面と比較するパターンです。 例えば、 `/filter` セクション使用 `glob` Dispatcher が従う、または拒否するページのパスを識別するパターン。

The `glob` の値には、ワイルドカード文字と英数字を含めて、パターンを定義できます。

| ワイルドカード文字 | 説明 | 例 |
|--- |--- |--- |
| `*` | 文字列内の任意の文字の 0 個以上の連続したインスタンスに一致します。 一致の最終文字は、次のいずれかの状況で決定されます。 <br/>文字列内のある文字がパターン内の次の文字に一致し、パターンの文字が次の特性を持つ。<br/><ul><li>*以外</li><li>ではない場合</li><li>リテラル文字（空白を含む）または文字クラス。</li><li>パターンの終わりに達しました。</li></ul>文字クラス内の文字は、リテラルとして解釈されます。 | `*/geo*` `/content/geometrixx`ノードと `/content/geometrixx-outdoors` ノードの下にあるすべてのページに一致します。以下の HTTP 要求は、glob パターンに一致します。<br/><ul><li>`"GET /content/geometrixx/en.html"`</li><li>`"GET /content/geometrixx-outdoors/en.html"` </li></ul><br/> `*outdoors/*`<br/> `/content/geometrixx-outdoors` ノードの下にあるすべてのページに一致します。例えば、次の HTTP 要求は glob パターンに一致します。<br/><ul><li>`"GET /content/geometrixx-outdoors/en.html"`</li></ul> |
| `?` | 任意の 1 文字に一致します。文字クラス外で使用します。文字クラス内のこの文字は、リテラルとして解釈されます。 | `*outdoors/??/*`<br/> geometrixx-outdoors サイトのすべての言語のページに一致します。 例えば、次の HTTP 要求は glob パターンに一致します。<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>次の要求は glob パターンに一致しません。<br/><ul><li>&quot;GET /content/geometrixx-outdoors/en.html&quot;</li></ul> |
| `[ and ]` | 文字クラスの最初と最後を定めます。文字クラスには、1 つまたは複数の文字範囲および単一の文字を含めることができます。<br/>ターゲット文字が文字クラス内または定義されている範囲内のいずれかの文字に一致する場合、一致が発生します。<br/>閉じ角括弧が含まれない場合、パターンによって一致は発生しません。 | `*[o]men.html*`<br/> 次の HTTP 要求に一致します。<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>次の HTTP 要求に一致しません。<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/> `*[o/]men.html*`<br/>次の HTTP 要求に一致します。<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `-` | 文字の範囲を定めます。文字クラスで使用します。 文字クラス外のこの文字は、リテラルとして解釈されます。 | `*[m-p]men.html*`次の HTTP 要求に一致します。<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul>次の HTTP 要求に一致しません。<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `!` | 後続の文字または文字クラスを否定します。 文字クラス内の文字と文字範囲を無効にする場合にのみ使用します。 ワイルドカード文字 `^ wildcard`.<br/>文字クラス外のこの文字は、リテラルとして解釈されます。 | `*[!o]men.html*`<br/>次の HTTP 要求に一致します。<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>次の HTTP 要求に一致しません。<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>`*[!o!/]men.html*`<br/> 次の HTTP 要求に一致しません。<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"` または `"GET /content/geometrixx-outdoors/en/men. html"`</li></ul> |
| `^` | 後続の文字または文字範囲を打ち消します。 文字クラス内の文字と文字範囲のみを否定する場合に使用します。 `!` ワイルドカードに相当します。<br/>文字クラス外のこの文字は、リテラルとして解釈されます。 | ワイルドカード文字 `!` の例と同様、サンプルパターンの文字 `!` が 文字 `^` に置き換えられます。 |


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

Web サーバー設定で、次の属性を設定できます。

* Dispatcher ログファイルの場所.
* ログレベル。

詳しくは、Web サーバーのドキュメント、および使用する Dispatcher インスタンスの readme ファイルを参照してください。

**Apache の交替されるログ／パイプ経由のログ**

を使用する場合、 **Apache** Web サーバーでは、交替されるログ、パイプ経由のログ、またはその両方に対する標準機能を使用できます。 例えば、次のようにパイプ経由のログを使用できます。

`DispatcherLog "| /usr/apache/bin/rotatelogs logs/dispatcher.log%Y%m%d 604800"`

この機能は自動的に回転します。

* Dispatcher ログファイル。拡張子にタイムスタンプ (`logs/dispatcher.log%Y%m%d`) をクリックします。
* 週単位（60 x 60 x 24 x 7 = 604800 秒）で交替されます。

ログの交替やパイプ経由のログについては、Apache Web サーバーのドキュメントを参照してください。 例： [Apache 2.4](https://httpd.apache.org/docs/2.4/logs.html).

>[!NOTE]
>
>インストール後、デフォルトのログレベルは高（レベル 3 =デバッグ）になるので、Dispatcher はすべてのエラーと警告をログに記録します。 このレベルは、最初の段階で役立ちます。
>
>ただし、このレベルでは追加のリソースが必要です。 Dispatcher がスムーズに動作している場合 *要件に従って*&#x200B;を使用すると、ログレベルを下げることができます。

### トレースログ {#trace-logging}

Dispatcher の機能強化の中で、バージョン 4.2.0 ではトレースログも導入されています。

この機能は、ログに追加情報を表示するデバッグログよりも高いレベルです。 次のログが追加されます。

* 転送されたヘッダーの値。
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

ブロックルールに一致するファイルが要求されたときにログに記録されるイベント：

```xml
[Thu Mar 03 14:42:45 2016] [T] [11831] 'GET /content.infinity.json HTTP/1.1' was blocked because of /0082
```

## 基本操作の確認 {#confirming-basic-operation}

Web サーバー、Dispatcher および AEM インスタンスの基本の操作とやり取りを確認するには、次の手順を実行します。

1. `loglevel` を `3`.に設定します。

1. Web サーバーを起動します。 また、Dispatcher も起動します。
1. AEMインスタンスを起動します。
1. Web サーバーと Dispatcher のログファイルとエラーファイルを確認します。
   * Web サーバーに応じて、次のようなメッセージが表示されます。
      * `[Thu May 30 05:16:36 2002] [notice] Apache/2.0.50 (Unix) configured` および
      * `[Fri Jan 19 17:22:16 2001] [I] [19096] Dispatcher initialized (build XXXX)`

1. Web サーバー経由で Web サイトにアクセスします。コンテンツが要求どおりに表示されていることを確認します。\
   例えば、AEM がポート `4502` で、Web サーバーがポート `80` で実行されているローカルインストール上で、以下の両方を使用して Web サイトコンソールにアクセスします。
   * `https://localhost:4502/libs/wcm/core/content/siteadmin.html`
   * `https://localhost:80/libs/wcm/core/content/siteadmin.html`
   * 結果は同じになるはずです。 同じメカニズムで他のページへのアクセスを確認します。

1. キャッシュディレクトリがいっぱいになっていることを確認します。
1. キャッシュが正しくフラッシュされていることを確認するには、ページをアクティブ化します。
1. すべてが正しく動作している場合は、 `loglevel` から `0`.

## 複数の Dispatcher の使用 {#using-multiple-dispatchers}

複雑な設定では、複数の Dispatcher を使用できます。 例えば、次のように使用できます。

* 1 つの Dispatcher でイントラネット上に Web サイトを公開
* 2 つ目の Dispatcher は、異なるアドレスとセキュリティ設定の下で、同じコンテンツをインターネットに公開します。

この場合、各要求が経由する Dispatcher は 1 つだけにしてください。別の Dispatcher から渡された要求は処理されません。したがって、両方の Dispatcher がAEM Web サイトに直接アクセスしていることを確認してください。

## デバッグ {#debugging}

ヘッダーを追加する場合 `X-Dispatcher-Info` 要求に対して、Dispatcher は、ターゲットがキャッシュされたか、キャッシュから返されたか、またはキャッシュ不可能であるかを返します。 応答ヘッダー `X-Cache-Info` には、この情報が読み取り可能な形式で格納されます。これらの応答ヘッダーを使用して、Dispatcher によってキャッシュされた応答に関する問題をデバッグできます。

この機能はデフォルトでは有効になっていないので、応答ヘッダーの場合は有効です `X-Cache-Info` を含めるには、ファームに次のエントリが含まれている必要があります。

```xml
/info "1"
```

例：

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

また、 `X-Dispatcher-Info` ヘッダーに値は必要ありませんが、 `curl` テストの場合は、ヘッダーに送信する値を次のように指定する必要があります。

```xml
curl -v -H "X-Dispatcher-Info: true" https://localhost/content/wknd/us/en.html
```

以下は、の応答ヘッダーの一覧です。 `X-Dispatcher-Info` 戻り値：

* **cached**\
  ターゲットファイルはキャッシュに含まれており、Dispatcher は配信が有効であると判断しました。
* **caching**\
  ターゲットファイルはキャッシュに含まれておらず、Dispatcher は出力をキャッシュして配信することが有効であると判断しました。
* **caching: stat file is more recent** 
ターゲットファイルはキャッシュに含まれていますが、より新しい .stat ファイルによって無効化されます。Dispatcher がターゲットファイルを削除し、出力から再作成して配信します。
* **not cacheable: no document root** 
ファームの設定にドキュメントルート（設定要素 `cache.docroot`）が含まれていません。
* **not cacheable: cache file path too long**\
  ターゲットファイル（ドキュメントルートと URL ファイルが連結されたものが）が、システム上で使用可能な最長ファイル名を超えています。
* **not cacheable: temporary file path too long**\
  一時ファイル名テンプレートが、システムで使用可能な最長ファイル名を超えています。Dispatcher は、キャッシュされたファイルを作成または上書きする前に、まず一時ファイルを作成します。 一時ファイル名は、ターゲットファイル名と文字を含む名前です。 `_YYYYXXXXXX` その後に `Y` および `X` は、一意の名前を作成するために置き換えられます。
* **not cacheable: request URL has no extension**\
  リクエスト URL に拡張子がないか、ファイル拡張子の後にパスがあります（例：`/test.html/a/path`）。
* **not cacheable: request wasn&#39;t aGETまたはHEAD**
HTTP メソッドは、GETやHEADではありません。 Dispatcher は、出力にキャッシュしてはならない動的データが含まれていると想定します。
* **not cacheable: request contained a query string**\
  リクエストにクエリ文字列が含まれていました。Dispatcher は、出力が指定されたクエリ文字列に依存しているので、キャッシュされないと想定します。
* **not cacheable: session manager didn&#39;t authenticate**\
  ファームのキャッシュはセッションマネージャーによって管理され（設定に `sessionmanagement` ノードが含まれている）、リクエストに適切な認証情報が含まれていません。
* **not cacheable: request contains authorization**\
  ファームはキャッシュの出力（`allowAuthorized 0`）が許可されておらず、リクエストに認証情報が含まれている。
* **not cacheable: target is a directory**\
  ターゲットファイルがディレクトリです。この場所は、URL と一部のサブ URL の両方にキャッシュ可能な出力が含まれる、概念的な誤りを示している場合があります。 例えば、 `/test.html/a/file.ext` は最初に受け取り、キャッシュ可能な出力を含みます。Dispatcher は、 `/test.html`.
* **not cacheable: request URL has a trailing slash**\
  リクエスト URL の末尾にスラッシュが使用されています。
* **not cacheable: request URL not in cache rules**\
  このファームのキャッシュルールは、一部のリクエスト URL の出力をキャッシュすることを明示的に拒否しています。
* **not cacheable: authorization checker denied access**\
  ファームの認証チェッカーがキャッシュされたファイルへのアクセスを拒否しました。
* **not cacheable: session not valid** 
ファームのキャッシュがセッションマネージャーによって管理され（設定に `sessionmanagement` ノードが含まれている）、ユーザーセッションが無効であるか、有効でなくなっています。
* **not cacheable: response contains`no_cache`**
リモートサーバーが `Dispatcher: no_cache` Dispatcher による出力のキャッシュが禁止されているヘッダー。
* **not cacheable: response content length zero**
応答の内容の長さが 0 です。Dispatcher は長さ 0 のファイルを作成しません。
