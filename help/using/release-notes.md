---
title: AEM Dispatcher リリースノート
seo-title: AEM Dispatcher Release Notes
description: Adobe Experience Manager Dispatcher 固有のリリースノート
seo-description: Release notes specific to Adobe Experience Manager Dispatcher
uuid: ae3ccf62-0514-4c03-a3b9-71799a482cbd
topic-tags: release-notes
content-type: reference
products: SG_EXPERIENCEMANAGER/6.4
discoiquuid: ff3d38e0-71c9-4b41-85f9-fa896393aac5
exl-id: b55c7a34-d57b-4d45-bd83-29890f1524de
source-git-commit: f2036e8f73d5f9f58ff713df8f04caad89d660fc
workflow-type: tm+mt
source-wordcount: '1063'
ht-degree: 50%

---

# AEM Dispatcher リリースノート{#aem-dispatcher-release-notes}

## リリース情報 {#release-information}

|  |  |
|--- |--- |
| 商品 | Adobe Experience Manager (AEM) Dispatcher |
| バージョン | 4.3.6 |
| タイプ | マイナーリリース |
| 日付 | 2023年7月25日（PT） |
| ダウンロード URL | <ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Microsoft Internet Information Services（IIS）](release-notes.md#iis)</li></ul> |
| 互換性 | AEM 6.1 以降 |

## システム要件および使用条件 {#system-requirements-and-prerequisites}

詳しくは、 [サポートされるプラットフォーム](https://helpx.adobe.com/jp/experience-manager/6-4/sites/deploying/using/technical-requirements.html)要件と前提条件に関する詳細は、こちらのページを参照してください。

Adobeでは、最新の機能、最新のバグ修正、そして最高のパフォーマンスを得るために、最新バージョンのAEM Dispatcher を使用することを強くお勧めします。

## インストール手順 {#installation-instructions}

詳しい手順については、[Dispatcher のインストール](dispatcher-install.md)を参照してください。

## リリース履歴 {#release-history}

### リリース 4.3.6（2023 年 7 月 25 日） {#jyly}

**機能強化**：

* DISP-911 AEM-05 - X-Edge-Key は disp_apache2.c でリークされる可能性があります
* DISP-937 すべてのセレクターのログ記録
* DISP-998 起動時のバニティー URL の読み込みを設定可能にする

### リリース 4.3.5（2022 年 4 月） {#apr}

**機能強化**：

* DISP-954 — 有効期限が過ぎない場合でもサポートの無効化
* DISP-949 — フィルターによってPOST要求がブロックされている場合でも、Dispatcher が 404 ではなく 200 を返す

### リリース 4.3.4（2021 年 11 月 29 日） {#nov}

**バグ修正**:

* DISP-833 - X-Forwarded-Host ヘッダーには、コンマで区切られたホスト名のリストが含まれている場合があります
* DISP-835 - DispatcherUseForwardedHost が最後に来た場合にホストヘッダーをスローする

**機能強化**：

* DISP-874 - DISP-818 の実装をフラグを介してオンまたはオフにする Dispatcher 設定を作成します。 `DispatcherRestrictUncacheableContent`. デフォルト値は Off です。オンにすると、mod によって設定されたキャッシュヘッダーがキャッシュ不能なコンテンツの有効期限切れになり、すべて削除されます。 これは、バージョン 4.3.3（デフォルトはオン）で見つかった動作とは異なりますが、4.3.3 より前のバージョン（デフォルトはオフ）と同じです。 保持 `DispatcherRestrictUncacheableContent`ブラウザーキャッシュの柔軟性を高めるため、のデフォルトの「オフ」が推奨されるアプローチです。 バージョン 4.3.3 から 4.3.4 にアップグレードする際に、バージョン 4.3.3 と同じ動作を維持する場合は、明示的に `DispatcherRestrictUncacheableContent` をオンにします。
* DISP-841 - Dispatcher は、504 応答コードに対して/serverStaleOnError を考慮しません
* DISP-874 - DISP-818 の実装をオンまたはオフにする Dispatcher 設定を作成する
* DISP-883 - Dispatcher での URL リクエストの分解を示すトレース
* DISP-944 — バニティー URL のプリロード

### リリース 4.3.3（2019 年 10 月 18 日） {#october}

**バグ修正**:

* DISP-739 - LogLevel Dispatcher: **レベル** 動作しない
* DISP-749 - Alpine Linux Dispatcher がトレースログレベルでクラッシュする

**機能強化**：

* DISP-813 - Dispatcher での OpenSSL 1.1.x のサポート
* DISP-814 — キャッシュフラッシュ中の Apache 40x エラー
* DISP-818 - mod_expires は、キャッシュ不可のコンテンツ用に Cache-Control ヘッダーを追加します。
* DISP-821 — ログコンテキストをソケットに格納しない
* DISP-822 - Dispatcher は pselect の代わりに ppoll を使用する必要がある
* DISP-824 — セキュアな DispatcherUseForwardedHost
* DISP-825 — ディスクに空き容量がなくなった場合の特殊メッセージをログに記録する
* DISP-826 — クエリ文字列を使用した URI の再取得のサポート

**新機能**:

* DISP-703 — ファーム固有のキャッシュヒット率
* DISP-827 — テスト用のローカルサーバー
* DISP-828 - Dispatcher 用のテスト用 Docker イメージの作成

### リリース 4.3.2（2019 年 1月 31 日）  {#jan}

**バグ修正**:

* DISP-734 - ハンドラーとして設定されていない場合、Dispatcher が insert_output_filter でクラッシュする
* DISP-735 - Alpine Linux で RE が機能しない
* DISP-740 - MacOS Mojave での Dispatcher の読み込みがデフォルトで無効になっている
* DISP-742 - ブロックされたリクエストによって Auth Checker で保護されたリソースに情報が漏洩する場合がある

**機能強化**：

* DISP-746 - dispatcher にラベルのない文字列が存在する場合は警告が表示される

**新機能**:

* DISP-747 - Apache 環境で要求の情報を提供

### リリース 4.3.1（2018 年 10 月 16 日）  {#oct}

**バグ修正**:

* DISP-656 - Dispatcher により誤った ETag ヘッダーが提供される
* DISP-694 - キープアライブ接続が古くなった場合に警告が表示されない
* DISP-714 - Cookie ベースのセッション管理は IIS では機能しない
* DISP-715 - RenderID cookie の Secure フラグ
* DISP-720 - 一時ファイルが閉じられていないため、システムに極端な負荷がかかる可能性がある（開いているファイルが多すぎる）
* DISP-721 - Apache が正常に子プロセスを再起動したとき、Dispatcher が poll() を中断する
* DESP-722 - キャッシュファイルが 8 進数モード 0600 で作成される
* DISP-723 - レンダリングタイムアウトが 0 に設定されている場合の暗黙的な 10 分のタイムアウト（および再試行）
* DISP-725 - 文字列後の末尾の文字が名称未設定の値に変換される
* DISP-726 - ファームが受信ホストと一致していない場合に警告が記録される
* DESP-727 - Dispatcher が空のキャッシュファイルのコンテンツの長さのリクエストをチェックする
* DISP-730 - Dispatcher を介してヘッダーファイルにアクセスしようとしたときの 404
* DISSP-731 - Dispatcher がログインジェクションに対して脆弱性を持つ
* DISSP-732 - Dispatcher は、URL内の連続した「/」を削除する必要がある
* DISP-733 - Dispatcher は、Age ヘッダーを設定（計算）する必要がある

**機能強化**：

* DISP-656 - Dispatcher により誤った ETag ヘッダーが提供される
* DISP-694 - キープアライブ接続が古くなった場合に警告が表示されない
* DISP-715 - RenderID cookie の Secure フラグ
* DESP-722 - キャッシュファイルが 8 進数モード 0600 で作成される
* DISP-726 - ファームが受信ホストと一致していない場合に警告が記録される

### リリース 4.3.0（2018 年 6 月 13 日）  {#jun}

**バグ修正**:

* DISP-682 - 数値ログレベルが誤って適用される
* DISSP-685 - 32 ビット Solaris SPARC バイナリでは __ divdi3 への未定義の参照がある
* DISSP-688 - Dispatcher が404応答で「X- Cache- Info」ヘッダーを返さない
* DISP-690 - Last- Modified ヘッダーがキャッシュできない
* DISP-691 - w3wp. exe でのアクセス違反
* DISP-693 - Dispatcher ダウンロードページ上の Solaris サーバーのアーキテクチャ詳細を更新する必要がある
* DISP-695 - Dispatcher モジュール 4.2.3 の DispatcherLog レベルに関する問題
* DISP-698 - Dispatcher TTLは、s- maxageおよび private ディレクティブをサポートする必要がある
* DISP-700 - Alpine Linux ではモジュールが正しく機能しない
* DISP-704 - %2b を含むブラウザーリクエストが、エンコードされていない状態でパブリッシャーに送信される
* DISP-705 - 二重の空きまたは破損（fasttop）により Dispatcher がクラッシュする。
* DISP-706 - 無効化中、Dispatcher が逆参照シンボリックリンクをたどるため無限ループが発生する可能性がある
* DISP-709 - 一部のバニティー URL 拡張のブロック
* Disp-710- Cent OS 6 では使用できない Linux 用ビルド

**機能強化**：

* DISP-652 - Dispatcher により誤った Data ヘッダーが提供される

## 役立つリソース {#helpful-resources}

* [AEM Dispatcher の概要](dispatcher.md)

## ダウンロード {#downloads}

### Apache 2.4 {#apache}

| プラットフォーム | アーキテクチャ | OpenSSL のサポート | ダウンロード |
|---|---|---|---|
| Linux | i686（32 ビット） | なし | [dispatcher-apache2.4-linux-i686-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.6.tar.gz) |
| Linux | i686（32 ビット） | 1.0 | [dispatcher-apache2.4-linux-i686-ssl1.0-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.0-4.3.6.tar.gz) |
| Linux | i686（32 ビット） | 1.1 | [dispatcher-apache2.4-linux-i686-ssl1.1-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.1-4.3.6.tar.gz) |
| Linux | x86_64（64 ビット） | なし | [dispatcher-apache2.4-linux-x86_64-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.6.tar.gz) |
| Linux | x86_64（64 ビット） | 1.0 | [dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.6.tar.gz) |
| Linux | x86_64（64 ビット） | 1.1 | [dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.6.tar.gz) |
| Linux | aarch64（64 ビット） | なし | [dispatcher-apache2.4-linux-aarch64-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-4.3.6.tar.gz) |
| Linux | aarch64（64 ビット） | 1.0 | [dispatcher-apache2.4-linux-aarch64-ssl1.0-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl1.0-4.3.6.tar.gz) |
| Linux | aarch64（64 ビット） | 1.1 | [dispatcher-apache2.4-linux-aarch64-ssl1.1-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl1.1-4.3.6.tar.gz) |
| macOS | arm64（64 ビット） | なし | [dispatcher-apache2.4-darwin-arm64-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-arm64-4.3.6.tar.gz) |
| macOS | x86_64（64 ビット） | なし | [dispatcher-apache2.4-darwin-x86_64-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.6.tar.gz) |

### IIS {#iis}

| プラットフォーム | アーキテクチャ | OpenSSL のサポート | ダウンロード |
|---|---|---|---|
| Windows | x86（32ビット） | なし | [dispatcher-iis-windows-x86-4.3.6.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.6.zip) |
| Windows | x86（32ビット） | 1.0 | [dispatcher-iis-windows-x86-ssl1.0-4.3.6.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.0-4.3.6.zip) |
| Windows | x86（32ビット） | 1.1 | [dispatcher-iis-windows-x86-ssl1.1-4.3.6.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.1-4.3.6.zip) |
| Windows | x64（64 ビット） | なし | [dispatcher-iis-windows-x64-4.3.6.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.6.zip) |
| Windows | x64（64 ビット） | 1.0 | [dispatcher-iis-windows-x64-ssl1.0-4.3.6.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.0-4.3.6.zip) |
| Windows | x64（64 ビット） | 1.1 | [dispatcher-iis-windows-x64-ssl1.1-4.3.6.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.1-4.3.6.zip) |