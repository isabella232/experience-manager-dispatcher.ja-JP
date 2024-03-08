---
title: Web サイトのキャッシュパフォーマンスの最適化
seo-title: Optimizing a Website for Cache Performance
description: キャッシュのメリットを最大化するように Web サイトをデザインする方法について説明します。
seo-description: Dispatcher offers a number of built-in mechanisms that you can use to optimize performance. Learn how to design your web site to maximize the benefits of caching.
uuid: 2d4114d1-f464-4e10-b25c-a1b9a9c715d1
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: ba323503-1494-4048-941d-c1d14f2e85b2
redirecttarget: https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/configuring-performance.html
index: y
internal: n
snippet: y
source-git-commit: 762f575a58f53d25565fb9f67537e372c760674f
workflow-type: ht
source-wordcount: '1134'
ht-degree: 100%

---


# Web サイトのキャッシュパフォーマンスの最適化 {#optimizing-a-website-for-cache-performance}

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-10-25T04:13:34.919-0400

<p>This is a redirect to /experience-manager/6-2/sites/deploying/using/configuring-performance.html</p>

 -->

>[!NOTE]
>
>Dispatcher のバージョンは AEM とは無関係です。以前のバージョンの AEM のドキュメントに組み込まれている Dispatcher ドキュメントへのリンクをたどると、このページにリダイレクトされる可能性があります。

Dispatcher は、パフォーマンスの最適化に利用できる多数の組み込みメカニズムを提供します。この節では、キャッシュのメリットを最大化するように web サイトをデザインする方法について説明します。

>[!NOTE]
>
>まず覚えておいてほしいのは、Dispatcher は標準の web サーバーにキャッシュを格納するという点です。これは、次のことを意味します。
>
>* URL を使用してページおよび要求として格納できるすべてのデータをキャッシュできます。
>* その他のデータ（HTTP ヘッダー、cookie、セッションデータ、フォームデータなど）は格納できません。
>
>通常、多くのキャッシュ戦略は適切な URL の選択を含んでおり、この追加データには依存しないことです。

## 一貫したページエンコーディングの使用 {#using-consistent-page-encoding}

HTTP 要求ヘッダーはキャッシュされないので、ページエンコーディング情報をヘッダーに格納すると、問題が発生する可能性があります。この場合、Dispatcher がキャッシュからページを提供すると、ページで web サーバーのデフォルトのエンコーディングが使用されます。この問題を回避する方法は 2 つあります。

* 1 つのエンコーディングのみを使用する場合は、web サーバーで使用されるエンコーディングが、AEM web サイトのデフォルトのエンコーディングと同じであることを確認します。
* `<META>` タグを HTML の `head` セクションで使用して、エンコーディングを設定します。次に例を示します。

```xml
        <META http-equiv="Content-Type" content="text/html; charset=EUC-JP">
```

## URL パラメーターの使用回避 {#avoid-url-parameters}

可能な限り、キャッシュするページの URL パラメーターは使用しないでください。例えば、ピクチャーギャラリーがある場合、次の URL はキャッシュされません（Dispatcher が [適切に設定](dispatcher-configuration.md#main-pars_title_24)されている場合を除く）。

```xml
www.myCompany.com/pictures/gallery.html?event=christmas&amp;page=1
```

ただし、次のように、これらのパラメーターをページ URL に配置できます。

```xml
www.myCompany.com/pictures/gallery.christmas.1.html
```

>[!NOTE]
>
>この URL は、gallery.html と同じページと同じテンプレートを呼び出します。テンプレートの定義では、ページをレンダリングするスクリプトを指定できます。または、すべてのページに同じスクリプトを使用できます。

## URL でカスタマイズ {#customize-by-url}

ユーザーがフォントサイズ（またはその他のレイアウトのカスタマイズ）を変更できるようにする場合は、様々なカスタマイズが URL に反映されていることを確認します。

例えば、Cookie はキャッシュされないので、フォントサイズを Cookie（または同様のメカニズム）に格納した場合、キャッシュされたページのフォントサイズは保持されません。その結果、Dispatcher は任意のフォントサイズのドキュメントをランダムに返します。

URL にフォントサイズをセレクターとして含めると、次の問題を回避できます。

```xml
www.myCompany.com/news/main.large.html
```

>[!NOTE]
>
>ほとんどのレイアウトアスペクトでは、スタイルシートやクライアントサイドスクリプトを使用することもできます。通常、これらはキャッシュと非常にうまく連携します。
>
>これは印刷版でも役立ちます。次のような URL を使用できます。
>
>`www.myCompany.com/news/main.print.html`
>
>テンプレート定義のスクリプトグロビングを使用して、印刷ページをレンダリングする個別のスクリプトを指定できます。

## タイトルとして使用されている画像ファイルの無効化 {#invalidating-image-files-used-as-titles}

ページのタイトルや他のテキストを画像としてレンダリングする場合は、ページ上のコンテンツの更新時にファイルが削除されるように、ファイルを保存することをお勧めします。

1. 画像ファイルを、ページと同じフォルダーに配置します。
1. 画像ファイルに次の命名形式を使用します。



   `<page file name>.<image file name>`

例えば、myPage.title.gif ファイルにページ myPage.html のタイトルを格納できます。ページが更新されると、このファイルは自動的に削除されるので、ページタイトルに対する変更はキャッシュに自動的に反映されます。

>[!NOTE]
>
>画像ファイルは、必ずしも AEM インスタンス上に物理的に存在するわけではありません。画像ファイルを動的に作成するスクリプトを使用できます。次に、Dispatcher がファイルを web サーバーに保存します。

## ナビゲーションに使用された画像ファイルの無効化 {#invalidating-image-files-used-for-navigation}

ナビゲーションエントリに画像を使用する場合、この方法はタイトルと同じですが、少し複雑です。すべてのナビゲーション画像をターゲットページと共に保存します。通常とアクティブ用に 2 つの画像を使用する場合は、次のスクリプトを使用できます。

* 通常どおりページを表示するスクリプト。
* 「.normal」リクエストを処理し、通常の画像を返すスクリプト。
* 「.active」リクエストを処理し、アクティベートされた画像を返すスクリプト。

ページと同じ命名ハンドルを使用してこれらの写真を作成し、コンテンツの更新によって写真とページが削除されるようにしてください。

変更されないページについては、ページ自体は通常、自動的に無効化されますが、写真はキャッシュに残ります。

## パーソナライズ機能 {#personalization}

Dispatcher はパーソナライズされたデータをキャッシュできないので、パーソナライズ機能は必要な場所にのみ適用することをお勧めします。その理由を次に示します。

* 自由にカスタマイズ可能な開始ページを使用する場合は、ユーザーが要求するたびにそのページを構成する必要があります。
* 一方、10 個の異なる開始ページを選択できるようにする場合は、各ページをキャッシュしてパフォーマンスを強化できます。

>[!NOTE]
>
>各ページをパーソナライズする（例えば、ユーザー名をタイトルバーに挿入する）場合は、パフォーマンスに重大な影響を及ぼす可能性があるので、ページをキャッシュできません。
>
>ただし、どうしても必要な場合は、次の方法があります。
>
>* iFrame を使用して、全ユーザーに共通する部分と、特定ユーザーのすべてのページに共通する部分とにページを分割します。こうすれば、それぞれの部分をキャッシュできます。
>* クライアントサイド JavaScript を利用して、パーソナライズされた情報を表示します。ただし、ユーザーが JavaScript を無効にした場合、ページが正しく表示されるようにする必要があります。
>

## スティッキー接続 {#sticky-connections}

[スティッキー接続](dispatcher.md#TheBenefitsofLoadBalancing)を使用すると、1 人のユーザー用のドキュメントがすべて同じサーバーで作成されるようになります。ユーザーがそのフォルダーを離れて後から戻ってきた場合も、この接続は維持されます。Web サイトのスティッキー接続に必要なすべてのドキュメントを保持するフォルダーを 1 つ定義します。他のドキュメントを中に含めないようにしてください。パーソナライズされたページとセッションデータを使用する場合、それがロードバランシングに影響を及ぼすためです。

## MIME タイプ {#mime-types}

ブラウザーがファイルのタイプを判別する方法は 2 とおりあります。

1. 拡張子（.html、.gif、.jpg など）
1. サーバーがファイルと共に送信する MIME タイプ。

ほとんどのファイルでは、MIME タイプがファイル拡張子で暗黙に指定されます。次のような例があります。

1. 拡張子（.html、.gif、.jpg など）
1. サーバーがファイルと共に送信する MIME タイプ。

ファイル名に拡張子がない場合は、プレーンテキストとして表示されます。

MIME タイプは HTTP ヘッダーに含まれており、Dispatcher は HTTP ヘッダーをキャッシュしません。AEM アプリケーションが、認識されたファイル末尾を持たず、代わりに MIME タイプに依存するファイルを返す場合、これらのファイルは正しく表示されない可能性があります。

ファイルが適切にキャッシュされるようにするには、次のガイドラインに従います。

* ファイルの拡張子が常に適切であることを確認します。
* download.jsp?file=2214 などの URL を含む汎用的なファイル提供スクリプトの使用を避けます。代わりに、ファイル仕様を含む URL を使用するようにスクリプトを書き直します（前述の例ならば download.2214.pdf となります）。

