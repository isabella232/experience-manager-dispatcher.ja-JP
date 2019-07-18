---
title: Dispatcher のセキュリティチェックリスト
seo-title: Dispatcher のセキュリティチェックリスト
description: 本番環境に進む前に、セキュリティチェックリストを完了する必要があります。
seo-description: 本番環境に進む前に、セキュリティチェックリストを完了する必要があります。
uuid: 7bfa3202-03f6-48e9-8d2e-2a40e137ecbe
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: fbfafa55-c029-4ed7-ab3e-1bebfde18248
jcr-lastmodifiedby: remove-legacypath-6-1
index: y
internal: n
snippet: y
translation-type: ht
source-git-commit: 6d3ff696780ce55c077a1d14d01efeaebcb8db28

---


# Dispatcher のセキュリティチェックリスト{#the-dispatcher-security-checklist}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-05T05:14:35.365-0400

<p>Food for thought listed on <a href="https://jira.corp.adobe.com/browse/DOC-5649">DOC-5649</a>. To be considered while proof-reading.</p> 
<p> </p>

 -->

Dispatcher はフロントエンドシステムとして機能し、Adobe Experience Manager インフラストラクチャに追加のセキュリティレイヤーをもたらします。実稼動させる前に、以下のチェックリストを確認することを強くお勧めします。

>[!CAUTION]
>
>稼動させる前に、使用するバージョンの AEM のセキュリティチェックリストも確認する必要があります。対応する [Adobe Experience Manager のドキュメント](https://helpx.adobe.com/jp/experience-manager/6-3/sites/administering/using/security-checklist.html)を参照してください。

## 最新バージョンの Dispatcher の使用 {#use-the-latest-version-of-dispatcher}

使用するプラットフォームに適した、使用可能な最新バージョンをインストールしてください。製品とセキュリティの機能強化を利用するには、最新バージョンを使用するように Dispatcher インスタンスをアップグレードする必要があります。[Dispatcher のインストール](dispatcher-install.md)を参照してください。

>[!NOTE]
>
>Dispatcher のログファイルを見て、インストールされている Dispatcher の現在のバージョンを確認できます。
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>ログファイルを見つけるには、`httpd.conf` の Dispatcher 設定を調べます。

## キャッシュをフラッシュできるクライアントの制限 {#restrict-clients-that-can-flush-your-cache}

[キャッシュをフラッシュできるクライアントを制限する](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache)ことをお勧めします。

## トランスポート層のセキュリティのための HTTPS の有効化 {#enable-https-for-transport-layer-security}

オーサーインスタンスとパブリッシュインスタンスの両方で HTTPS トランスポート層を有効にすることをお勧めします。

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:41:28.841-0400

<p>Recommended to have SSL termination, front end SSL.</p> 
<p>Question is do we want to have SSL communication between dispatcher and AEM instances (publish and/or author).</p> 
<p>We might want to have two items:</p> 
<ul> 
 <li>MUST HTTPS clients -&gt; dispatcher / load balancer</li> 
 <li>NICE load balancer -&gt; dispatcher<br /> </li> 
 <li>NICE dispatcher -&gt; instances if sensitive information such as credit cards / or infrastructure requirements such as DMZ</li> 
</ul>

 -->

## アクセスの制限 {#restrict-access}

Dispatcher の設定時に、できる限り外部アクセスを制限してください。Dispatcher に関するドキュメントの[サンプルの /filter セクション](dispatcher-configuration.md#main-pars_184_1_title)を参照してください。

## 管理 URL へのアクセスの拒否{#make-sure-access-to-administrative-urls-is-denied}

フィルターを使用して、Web コンソールなどすべての管理 URL への外部アクセスを確実にブロックします。

ブロックする必要がある URL のリストについては、[Dispatcher のセキュリティのテスト](dispatcher-configuration.md#testing-dispatcher-security)を参照してください。

## ブラックリストの代わりにホワイトリストを使用 {#use-whitelists-instead-of-blacklists}

アクセスを制御する方法としては、ホワイトリストのほうが優れています。ホワイトリストは、明示的にホワイトリストに含まれているもの以外はすべてのアクセス要求を拒否するからです。このモデルでは、未確認だったり、特定の設定ステージで考慮されていなかった可能性がある新しい要求に対して、より厳しい制御を適用できます。

## 専用システムユーザーでの Dispatcher の実行 {#run-dispatcher-with-a-dedicated-system-user}

Dispatcher の設定時に、最低限の権限を持つ専用ユーザーによって Web サーバーが実行されていることを確認してください。Dispatcher のキャッシュフォルダーへの書き込みアクセス権のみを付与することをお勧めします。

さらに、IIS ユーザーは、Web サイトを以下のように設定する必要があります。

1. Web サイトの物理パス設定で、「**特定のユーザーとして接続**」を選択します。
1. ユーザーを設定します。

## サービス拒否（DoS）攻撃の防止{#prevent-denial-of-service-dos-attacks}

サービス拒否（DoS）攻撃は、対象となるユーザーがコンピューターリソースを使用できない状態にするものです。

Dispatcher レベルでは、DoS 攻撃を防御するように設定する方法は 2 つあります。[](https://docs.adobe.com/content/docs/jp/dispatcher.html#/filter (Filters))

* mod_rewrite モジュール（[Apache 2.4](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html) など）を使用し、URL 検証を実行します（URL パターン規則がそれほど複雑ではない場合）。

* [フィルター](dispatcher-configuration.md#configuring-access-to-conten-tfilter)を使用することで、疑わしい拡張子を持つ URL を Dispatcher がキャッシングするのを阻止します。\
   例えば、キャッシング規則を変更して、以下のような mime タイプのみをキャッシングするよう制限します。

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`
   [外部アクセスを制限する](#restrict-access)ための設定ファイルのサンプルを参照できます。このファイルには、mine タイプの制限も含まれます。

パブリッシュインスタンス上ですべての機能を安全に有効にするには、以下のノードへのアクセスを防ぐようにフィルターを設定します。

* `/etc/`
* `/libs/`

次に、以下のノードパスへのアクセスを許可するようにフィルターを設定します。

* `/etc/designs/*`
* `/etc/clientlibs/*`
* `/etc/segmentation.segment.js`
* `/libs/cq/personalization/components/clickstreamcloud/content/config.json`
* `/libs/wcm/stats/tracker.js`
* `/libs/cq/personalization/*`（JS、CSS および JSON）
* `/libs/cq/security/userinfo.json` （CQ ユーザー情報）
* `/libs/granite/security/currentuser.json`（**データをキャッシュしてはいけません**）

* `/libs/cq/i18n/*` （内部化）

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:38:17.016-0400

<p>We need to highlight whether a path applies to all versions or specific ones.<br /> </p>

 -->

## CSRF 攻撃を防止するための Dispatcher の設定 {#configure-dispatcher-to-prevent-csrf-attacks}

AEM には、クロスサイトリクエストフォージェリ攻撃を防ぐことを目的とした[フレームワーク](https://helpx.adobe.com/jp/experience-manager/6-3/sites/administering/using/security-checklist.html#verification-steps)があります。このフレームワークを適切に利用するには、Dispatcher で CSRF トークンサポートをホワイトリストに登録する必要があります。この情報を出力するには、次の操作をおこないます。

1. `/libs/granite/csrf/token.json` パスを許可するフィルターを作成する。
1. Dispatcher 設定の `clientheaders` セクションに `CSRF-Token` ヘッダーを追加する。

## クリックジャッキングの防止 {#prevent-clickjacking}

クリックジャッキングを防ぐには、`SAMEORIGIN` に設定した HTTP ヘッダー `X-FRAME-OPTIONS` を指定するように Web サーバーを設定することをお勧めします。

クリックジャッキングについて詳しくは、[OWASP のサイト](https://www.owasp.org/index.php/Clickjacking)を参照してください。

## 侵入テストの実施 {#perform-a-penetration-test}

実稼動に移行する前に、AEM インフラストラクチャの侵入テストを実施することを強くお勧めします。

