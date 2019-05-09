---
title: Dispatcher に関する問題のトラブルシューティング
seo-title: AEMディスパッチャーの問題のトラブルシューティング
description: ディスパッチャーの問題のトラブルシューティングについて説明します。
seo-description: AEMディスパッチャーの問題のトラブルシューティングについて説明します。
uuid: 9c109a48- d921-4b6e-9626-1158cebc41e7
cmgrlastmodified: 01.11.2007082229[ahtmoz]
pageversionid: '1193211344162'
template: /apps/docs/templates/contentpage
contentOwner: ユーザーは、
products: SG_ PERANDATEMENTMANAGER/CLACKER
topic-tags: dispatcher
content-type: リファレンス
discoiquuid: a612e745- f1e6-43de- b25a-9adcadab5cf
translation-type: tm+mt
source-git-commit: 76cffbfb616cd5601aed36b7076f67a2faf3ed3b

---


# Dispatcher に関する問題のトラブルシューティング {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Dispatcher のバージョンは AEM とは無関係ですが、Dispatcher のドキュメントは AEM のドキュメントに組み込まれています。最新バージョンの AEM のドキュメントに組み込まれている Dispatcher のドキュメントを必ず使用してください。
>
>以前のバージョンの AEM のドキュメントに組み込まれている Dispatcher のドキュメントへのリンクをたどると、このページにリダイレクトされる可能性があります。

>[!NOTE]
>
>詳しくは [、ディスパッチャーナレッジベース](https://helpx.adobe.com/cq/kb/index/dispatcher.html)、 [ディスパッチャーのフラッシュの問題のトラブルシューティング](https://helpx.adobe.com/adobe-cq/kb/troubleshooting-dispatcher-flushing-issues.html) および [ディスパッチャーのトップの問題に関するFAQ](dispatcher-faq.md) も参照してください。

## 基本設定の確認 {#check-the-basic-configuration}

最初の手順は常に、基本事項を確認することです。

* [基本操作の確認](#ConfirmBasicOperation)
* Webサーバーおよびディスパッチャーのすべてのログファイルをチェックします。必要に応じてディスパッチャー `loglevel`[ログに使用するようにし](#Logging)ます。

* [設定の確認](#ConfiguringtheDispatcher)：

   * 複数のディスパッチャーがあるか。

      * 調査しているWebサイト/ページを処理するディスパッチャーを決定しているか。
   * フィルターを実装しているか。

      * これらのフィルターが調査中の問題に影響しているか。


## IIS 診断ツール {#iis-diagnostic-tools}

IISには、実際のバージョンに応じて様々なトレースツールが用意されています。

* IIS6- IIS6の診断ツールをダウンロードして設定できます
* IIS7-トレースは完全に統合されています

これらは、アクティビティの監視に役立ちます。

## IIS と 404 Not Found {#iis-and-not-found}

IISを使用する場合、様々なシナリオで返さ `404 Not Found` れる可能性があります。その場合は、次のナレッジベース記事を参照してください。

* [IIS 6/7: POST method returns 404](https://helpx.adobe.com/dispatcher/kb/IIS6IsapiFilters.html)
* [IIS6:ベースパス `/bin` を返すURLへのリクエスト `404 Not Found`](https://helpx.adobe.com/dispatcher/kb/RequestsToBinDirectoryFailInIIS6.html)

Dispatcher のキャッシュルートと IIS のドキュメントルートが同じディレクトリに設定されていることも確認してください。

## ワークフローモデルの削除に関する問題 {#problems-deleting-workflow-models}

**エラー内容**

Dispatcher 経由で AEM のオーサーインスタンスにアクセスしていて、ワークフローモデルを削除しようとしたときの問題。

**再現の手順：**

1. オーサーインスタンスにログインします（要求が Dispatcher を経由することを確認してください）。
1. 新しいワークフローを作成します。例えば、タイトルがWorkflowToDeleteに設定されているとします。
1. ワークフローが正常に作成されたことを確認します。
1. ワークフローを選択して右クリックし、 **「削除**」をクリックします。

1. 「**はい**」をクリックして実行を確認します。
1. エラーメッセージボックスが表示されます。\
   &quot; `ERROR 'Could not delete workflow model!!`&quot;.

**解決方法**

ファイルのセクションに `/clientheaders` 次のヘッダーを追加 `dispatcher.any` します。

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

## mod_ dir（Apache）の干渉 {#interference-with-mod-dir-apache}

Dispatcher と Apache Web サーバー内の `mod_dir` がどのようにやり取りするかを説明します。このやり取りは、予期しない様々な問題につながる可能性があるので、よく理解しておく必要があります。

### Apache 1.3 {#apache}

Apache1.3 `mod_dir` では、URLがファイルシステム内のディレクトリにマッピングされるすべてのリクエストを処理します。

次のいずれかになります。

* リクエストを既存 `index.html` のファイルにリダイレクト
* ディレクトリリストの生成

ディスパッチャーが有効になっている場合、そのようなリクエストは、コンテンツタイプのハンドラーとして登録することで処理さ `httpd/unix-directory`れます。

### Apache 2.x {#apache-x}

Apache2. xでは異なります。モジュールでは、URLフィックスアップなど、リクエストの様々なステージを処理できます。`mod_dir` このステージを処理するには、（URLがディレクトリにマッピングされている場合）、 `/` 追加されたURLにリクエストをリダイレクトします。

ディスパッチャーは `mod_dir` フィックスアップを中断しませんが、リダイレクトされたURL（つまり追加さ `/` れたURL）へのリクエストを完全に処理します。リモートサーバー（例えば、AEM）がリクエストに対して異なる要求（ `/a_path` 既存のディレクトリにマッピングする場合 `/a_path/``/a_path` ）を処理すると、問題が発生する可能性があります。

これが発生する場合は、次のいずれかを実行する必要があります。

* ディスパッチャー `mod_dir` によって処理さ `Directory` れるまた `Location` はサブツリーに対して無効にする

* を使用し `DirectorySlash Off` て追加し `mod_dir` ないように設定する `/`
