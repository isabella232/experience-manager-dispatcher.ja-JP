---
title: Dispatcher に関する問題のトラブルシューティング
seo-title: AEM Dispatcher に関する問題のトラブルシューティング
description: Dispatcher に関する問題のトラブルシューティングについて説明します。
seo-description: AEM Dispatcher に関する問題のトラブルシューティングについて説明します。
uuid: 9c109a48-d921-4b6e-9626-1158cebc41e7
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: '1193211344162'
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: a612e745-f1e6-43de-b25a-9adcaadab5cf
translation-type: ht
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Dispatcher に関する問題のトラブルシューティング {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Dispatcher のバージョンは AEM とは無関係ですが、Dispatcher のドキュメントは AEM のドキュメントに組み込まれています。最新バージョンの AEM のドキュメントに組み込まれている Dispatcher のドキュメントを必ず使用してください。
>
>以前のバージョンの AEM のドキュメントに組み込まれている Dispatcher のドキュメントへのリンクをたどると、このページにリダイレクトされる可能性があります。

>[!NOTE]
>
>詳しくは、[Dispatcher ナレッジベース](https://helpx.adobe.com/jp/experience-manager/kb/index/dispatcher.html)、[Dispatcher のフラッシュ問題のトラブルシューティング](https://helpx.adobe.com/jp/adobe-cq/kb/troubleshooting-dispatcher-flushing-issues.html)、および [Dispatcher に関する主な問題とよくある質問](dispatcher-faq.md)を参照してください。

## 基本設定の確認 {#check-the-basic-configuration}

最初の手順は常に、基本事項を確認することです。

* [基本操作の確認](#ConfirmBasicOperation)
* Web サーバーおよび Dispatcher のログファイルを確認します。必要に応じて、Dispatcher の[ログ](#Logging)に使用する `loglevel` を増やします。

* [設定の確認](#ConfiguringtheDispatcher)：

   * 複数の Dispatcher があるか。

      * 調査中の Web サイトまたはページを処理している Dispatcher が特定されているか。
   * フィルターが実装されているか。

      * これらのフィルターが調査中の問題に影響しているか。


## IIS 診断ツール{#iis-diagnostic-tools}

IIS には、実際のバージョンに応じて様々なトレースツールがあります。

* IIS 6 - IIS 診断ツールをダウンロードし設定できます。
* IIS 7 - トレース機能が完全に組み込まれています。

これらのツールを使用して、アクティビティを監視できます。

## IIS と 404 Not Found{#iis-and-not-found}

IIS の使用時、様々な場面で「`404 Not Found`」が返されることがあります。このエラーが返された場合は、次のナレッジベース記事を参照してください。

* [IIS 6/7 POST メソッドが404を返す](https://helpx.adobe.com/jp/experience-manager/kb/IIS6IsapiFilters.html)
* [IIS 6：基本 `/bin` を含む URLへのリクエストで`404 Not Found`が返される ](https://helpx.adobe.com/jp/dispatcher/kb/RequestsToBinDirectoryFailInIIS6.html)

Dispatcher のキャッシュルートと IIS のドキュメントルートが同じディレクトリに設定されていることも確認してください。

## ワークフローモデルの削除に関する問題 {#problems-deleting-workflow-models}

**エラー内容**

Dispatcher 経由で AEM のオーサーインスタンスにアクセスしていて、ワークフローモデルを削除しようとしたときの問題。

**再現の手順：**

1. オーサーインスタンスにログインします（要求が Dispatcher を経由することを確認してください）。
1. 新しいワークフローを作成します。タイトルは、例えば workflowToDelete と設定します。
1. ワークフローが正しく作成されたことを確認します。
1. ワークフローを選択して右クリックし、「**削除**」をクリックします。

1. 「**はい**」をクリックして実行を確認します。
1. 次のエラーメッセージボックスが表示されます。\
   「`ERROR 'Could not delete workflow model!!`」。

**解決方法**

`dispatcher.any` ファイルの `/clientheaders` セクションに、次のヘッダーを追加します。

* `x-http-method-override`
* `x-requested-with`

`{  
{  
/clientheaders  
{  
...  
"x-http-method-override"  
"x-requested-with"  
}`

## mod_dir（Apache）との干渉{#interference-with-mod-dir-apache}

Dispatcher と Apache Web サーバー内の `mod_dir` がどのようにやり取りするかを説明します。このやり取りは、予期しない様々な問題につながる可能性があるので、よく理解しておく必要があります。

### Apache 1.3 {#apache}

Apache 1.3 では、`mod_dir` は URL がファイルシステムのディレクトリにマッピングされたすべての要求を処理します。

次のいずれかを実行します。

* 要求を既存の `index.html` ファイルへリダイレクト
* ディレクトリのリストを生成

Dispatcher が有効な場合、Dispatcher をコンテンツタイプ `httpd/unix-directory` のハンドラーとして登録することで、このような要求を処理します。

### Apache 2.x {#apache-x}

Apache 2.x では、内容が異なります。モジュールで、URL の修正など様々なステージの要求を処理できます。`mod_dir` でこのステージを処理するには、要求（URL がディレクトリにマッピングされている場合）を「`/`」付きの URL にリダイレクトします。

Dispatcher は `mod_dir` による修正の処理に影響しませんが、リダイレクトされた（「`/`」付きの）URL への要求を完全に処理します。したがって、リモートサーバー（AEM など）で `/a_path` への要求の処理が `/a_path/` への要求の処理と異なる場合（`/a_path` が既存のディレクトリにマッピングされる場合）に、問題となることがあります。

問題が発生した場合は、次のいずれかを実行します。

* Dispatcher が操作する`Directory`サブツリーまたは`Location`サブツリーでの `mod_dir` を無効にします

* `DirectorySlash Off` を使用して、`mod_dir` によって「`/`」が付加されないように設定します。
