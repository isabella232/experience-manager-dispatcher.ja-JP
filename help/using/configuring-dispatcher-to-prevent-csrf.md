---
title: CSRF 攻撃を防止するための Dispatcher の設定
seo-title: CSRF 攻撃を防止するための Adobe AEM Dispatcher の設定
description: クロスサイトリウクエストフォージェリー攻撃を防ぐための AEM Dispatcher の設定方法について説明します。
seo-description: クロスサイトリウクエストフォージェリー攻撃を防ぐための Adobe AEM Dispatcher の設定方法について説明します。
uuid: f290bdeb-54e2-4649-b0fc-6257b422af2d
topic-tags: dispatcher
content-type: reference
discoiquuid: d61d021e-b338-4a1d-91ee-55427557e931
exl-id: bcd38878-f977-46a6-b01a-03e4d90aef01
source-git-commit: 3a0e237278079a3885e527d7f86989f8ac91e09d
workflow-type: tm+mt
source-wordcount: '246'
ht-degree: 100%

---

# CSRF 攻撃を防止するための Dispatcher の設定{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM には、クロスサイトリクエストフォージェリ攻撃を防ぐことを目的としたフレームワークがあります。このフレームワークを適切に利用するには、Dispatcher 設定を次のように変更する必要があります。

>[!NOTE]
>
>既存の設定に基づいて、以下の例のルールの数字を必ず変更してください。Dispatcher は、最後に一致したルールを使用して許可または拒否するので、既存リストの下部にルールを配置してください。

1. author-farm.any ファイルと publish-farm.any ファイルの `/clientheaders` セクションで、リストの下部に次のエントリを追加します。\
   `CSRF-Token`
1. `author-farm.any` ファイル、`publish-farm.any` ファイル、または `publish-filters.any`ファイルの /filters セクションに次の行を追加して、Dispatcher 経由での `/libs/granite/csrf/token.json` に対する要求を許可します。\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. `publish-farm.any`ファイルの `/cache /rules` セクションに、Dispatcher が `token.json` ファイルをキャッシュできないようにするルールを追加します。一般的に、オーサーインスタンスはキャッシュをバイパスするので、`author-farm.any` ファイルにルールを追加する必要はありません。\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

設定が機能していることを検証するには、DEBUG モードの dispatcher.log を見て、token.json ファイルがキャッシュされておらず、フィルターによってブロックされていることを検証します。次のようなメッセージが表示されるはずです。\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

Apache の `access_log` で、要求が引き継がれていることを検証することもできます。/libs/granite/csrf/token.json に対する要求には、HTTP 200 のステータスコードが返されます。
