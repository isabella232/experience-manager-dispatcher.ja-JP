---
title: CSRF 攻撃を防止するための Dispatcher の設定
seo-title: CSRF攻撃を防止するためのAdobe AEMディスパッチャーの設定
description: クロスサイト要求偽造攻撃を防ぐためにAEMディスパッチャーを設定する方法について説明します。
seo-description: クロスサイト要求偽造攻撃を防ぐためにAdobe AEMディスパッチャーを設定する方法について説明します。
uuid: f290bdeb-54e2-4649- b0fc-6257b422af2d
topic-tags: dispatcher
content-type: リファレンス
discoiquuid: d61d021e- b338-4a1d-91ee-55427557e931
translation-type: tm+mt
source-git-commit: 69edbe7608b46c93d238515e4223606eadad0ac4

---


# CSRF 攻撃を防止するための Dispatcher の設定{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM には、クロスサイトリクエストフォージェリ攻撃を防ぐことを目的としたフレームワークがあります。このフレームワークを適切に利用するには、Dispatcher 設定を次のように変更する必要があります。

>[!NOTE]
>
>既存の設定に基づいて、以下の例のルールの数字を必ず変更してください。Dispatcher は、最後に一致したルールを使用して許可または拒否するので、既存リストの下部にルールを配置してください。

1. 作成者- farm. anyおよびpublish- farm. any `/clientheaders` のセクションで、リストの下部に次のエントリを追加します。\
   `CSRF-Token`
1. &quot;/content/help/jp/aem- forms/6-1/ `author-farm.any` mobile- workspace `publish-farm.any` /in- `publish-filters.any` aem- forms. html&quot;セクションで、ディスパッチャー `/libs/granite/csrf/token.json` を介してリクエストを許可するための次の行を追加します。\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. この `/cache /rules``publish-farm.any`セクションのセクションの下で、ディスパッチャーがファイルを `token.json` キャッシュすることをブロックするルールを追加します。通常、作成者はキャッシュをバイパスするので、ルールを追加する必要はありません `author-farm.any`。\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

設定が機能していることを検証するには、DEBUG モードの dispatcher.log を見て、token.json ファイルがキャッシュされておらず、フィルターによってブロックされていることを検証します。次のようなメッセージが表示されます。\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

また、Apacheでリクエストが続行することを検証 `access_log`することもできます。&quot;/libs/granite/csrf/token. json&quot;に対するリクエストは、HTTP200ステータスコードを返します。
