---
title: Dispatcher に関する問題のトラブルシューティング
seo-title: Troubleshooting AEM Dispatcher Problems
description: Dispatcher に関する問題のトラブルシューティングについて説明します。
seo-description: Learn to troubleshoot AEM Dispatcher issues.
uuid: 9c109a48-d921-4b6e-9626-1158cebc41e7
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: a612e745-f1e6-43de-b25a-9adcaadab5cf
exl-id: 29f338ab-5d25-48a4-9309-058e0cc94cff
source-git-commit: 26c8edbb142297830c7c8bd068502263c9f0e7eb
workflow-type: tm+mt
source-wordcount: '560'
ht-degree: 43%

---

# Dispatcher に関する問題のトラブルシューティング {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Dispatcher のバージョンは AEM とは無関係ですが、Dispatcher のドキュメントは AEM のドキュメントに組み込まれています。最新バージョンの AEM のドキュメントに組み込まれている Dispatcher のドキュメントを必ず使用してください。
>
>以前のバージョンの AEM のドキュメントに組み込まれている Dispatcher のドキュメントへのリンクをたどると、このページにリダイレクトされる可能性があります。

>[!NOTE]
>
>次を確認します。 [Dispatcher ナレッジベース](https://helpx.adobe.com/experience-manager/kb/index/dispatcher.html), [Dispatcher のフラッシュ問題のトラブルシューティング](https://experienceleague.adobe.com/search.html?lang=en#q=troubleshooting%20dispatcher%20flushing%20issues&amp;sort=relevancy&amp;f:el_product=[Experience%20Manager]) そして [Dispatcher に関する主な問題とよくある質問](dispatcher-faq.md) を参照してください。

## 基本設定の確認 {#check-the-basic-configuration}

最初の手順は常に、基本事項を確認することです。

* [基本操作の確認](/help/using/dispatcher-configuration.md#confirming-basic-operation)
* Web サーバーおよび Dispatcher のすべてのログファイルを確認します。 必要に応じて、 `loglevel` Dispatcher に使用されます。 [ログ](/help/using/dispatcher-configuration.md#logging).

* [設定の確認](/help/using/dispatcher-configuration.md)：

   * 複数の Dispatcher があるか。

      * 調査中の Web サイトまたはページを処理している Dispatcher が特定されているか。
   * フィルターが実装されているか。

      * これらのフィルターは、調査中の問題に影響を与えていますか？


## IIS 診断ツール {#iis-diagnostic-tools}

IIS には、実際のバージョンに応じて様々なトレースツールがあります。

* IIS 6 - IIS 診断ツールをダウンロードし設定できます。
* IIS 7 - トレース機能が完全に組み込まれています。

これらのツールは、アクティビティを監視するのに役立ちます。

## IIS と 404 Not Found {#iis-and-not-found}

IIS を使用する場合、次のことが考えられます `404 Not Found` 様々なシナリオで返されます。 このエラーが返された場合は、次のナレッジベース記事を参照してください。

* [IIS 6/7: POST method returns 404](https://helpx.adobe.com/experience-manager/kb/IIS6IsapiFilters.html)
* [IIS 6:ベースパスを含む URL へのリクエスト `/bin` 戻る `404 Not Found`](https://helpx.adobe.com/experience-manager/kb/RequestsToBinDirectoryFailInIIS6.html)

また、Dispatcher のキャッシュルートと IIS のドキュメントルートが同じディレクトリに設定されていることを確認します。

## ワークフローモデルの削除に関する問題 {#problems-deleting-workflow-models}

**エラー内容**

Dispatcher 経由で AEM のオーサーインスタンスにアクセスしていて、ワークフローモデルを削除しようとしたときの問題。

**再現の手順：**

1. オーサーインスタンスにログインします（要求が Dispatcher を通じてルーティングされていることを確認します）。
1. ワークフローの作成例えば、「タイトル」が「workflowToDelete」に設定されているとします。
1. ワークフローが正しく作成されたことを確認します。
1. ワークフローを選択して右クリックし、「 **削除**.

1. 「**はい**」をクリックして確認します。
1. 次の内容を示すエラーメッセージボックスが表示されます。\
   「`ERROR 'Could not delete workflow model!!`」。

**解決方法**

`dispatcher.any` ファイルの `/clientheaders` セクションに、次のヘッダーを追加します。

* `x-http-method-override`
* `x-requested-with`

```
{  
{  
/clientheaders  
{  
...  
"x-http-method-override"  
"x-requested-with"  
}
```

## mod_dir（Apache）との干渉 {#interference-with-mod-dir-apache}

このプロセスでは、Dispatcher とのやり取りを説明します `mod_dir` Apache Web サーバー内で、予期しない様々な問題が発生する可能性があるので、

### Apache 1.3 {#apache}

Apache 1.3 では、 `mod_dir` は、URL がファイルシステム内のディレクトリにマッピングされるすべての要求を処理します。

次のいずれかを実行します。

* 要求を既存の `index.html` ファイルへリダイレクト
* ディレクトリのリストを生成

Dispatcher が有効な場合、Dispatcher をコンテンツタイプのハンドラーとして登録することで、このような要求を処理します `httpd/unix-directory`.

### Apache 2.x {#apache-x}

Apache 2.x では、内容が異なります。 モジュールで、URL の修正など様々なステージの要求を処理できます。この `mod_dir` は、要求（URL がディレクトリにマッピングされている場合）を `/` 追加されました。

Dispatcher は `mod_dir` 修正しましたが、リダイレクトされた URL( `/` ) を追加しました。 このプロセスでは、リモートサーバー (AEMなど ) がに対する要求を処理する場合に問題が発生する可能性があります。 `/a_path` ～への要求とは異なる `/a_path/` ( `/a_path` は既存のディレクトリにマッピングされます )。

この状況が発生した場合は、次のいずれかを実行する必要があります。

* 無効 `mod_dir` の `Directory` または `Location` Dispatcher が処理するサブツリー

* `DirectorySlash Off` を使用して、`mod_dir` によって「`/`」が付加されないように設定します。
