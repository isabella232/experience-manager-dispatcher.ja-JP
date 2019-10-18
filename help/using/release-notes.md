---
title: AEM Dispatcher リリースノート
seo-title: AEM Dispatcher リリースノート
description: Adobe Experience Manager Dispatcher 固有のリリースノート
seo-description: Adobe Experience Manager Dispatcher 固有のリリースノート
uuid: ae3ccf62-0514-4c03-a3b9-71799a482cbd
topic-tags: release-notes
content-type: reference
products: SG_EXPERIENCEMANAGER/6.4
discoiquuid: ff3d38e0-71c9-4b41-85f9-fa896393aac5
translation-type: tm+mt
source-git-commit: cc838c5df8c0b65c86f21f45f1ca73cf14c0d545

---


# AEM Dispatcher リリースノート{#aem-dispatcher-release-notes}

## リリース情報 {#release-information}

|  |  |
|--- |--- |
| 商品 | Adobe Experience Manager (AEM) Dispatcher |
| バージョン | 4.3.3 |
| 種類 | マイナーリリース |
| 日付 | 2019年10月19日 |
| ダウンロード URL | <ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Microsoft Internet Information Services（IIS）](release-notes.md#iis)</li></ul> |
| 互換性 | AEM 6.1 以降 |

## システム要件および使用条件 {#system-requirements-and-prerequisites}

要件と前提条件の詳細につ [いては、「サポートされるプラットフォーム](https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/technical-requirements.html) 」ページを参照してください。

最新の機能、最新のバグフィックス、そして最高のパフォーマンスを利用するため、最新バージョンの AEM Dispatcher を使用することを強くお勧めします。

## インストール手順 {#installation-instructions}

詳しい手順については、[Dispatcher のインストール](dispatcher-install.md)を参照してください。

## リリース履歴 {#release-history}

### Release 4.3.3 (2019-Oct-18) {#october}

**バグ修正**:

* DISP-739 - logLevelディスパッチャー：レ **ベル** が機能しない
* DISP-749:Alpine linuxディスパッチャーがトレースログレベルでクラッシュします。

**機能強化**：

* DISP-813 - openssl 1.1.x用のディスパッチャーのサポート
* DISP-814：キャッシュフラッシュ中にApache 40xエラーが発生する
* DISP-818 - mod_expiresは、キャッシュ不能なコンテンツのCache-Controlヘッダを追加します。
* DISP-821 — ログコンテキストをソケットに格納しない
* DISP-822 — ディスパッチャーは、pselectの代わりにppollを使用する必要があります。
* DISP-824 — セキュリティで保護されたDispatcherUseForwardedHost
* DISP-825 — ディスクに空き領域がなくなった場合のログスペシャルメッセージ
* DISP-826 — クエリ文字列を使用したURIの再取得をサポートします。

**新機能**:

* DISP-703 — ファーム固有のキャッシュヒット率
* DISP-827 — テスト用のローカルサーバー
* DISP-828 — ディスパッチャー用のテスト用ドッカーイメージを作成します。

### リリース 4.3.2（2019 年 1月 31 日） {#jan}

**バグ修正**:

* DISP-734 - ハンドラーとして設定されていない場合、Dispatcher が insert_output_filter でクラッシュする
* DISP-735 - Alpine Linux で RE が機能しない
* DISP-740 - MacOS Mojave での Dispatcher の読み込みがデフォルトで無効になっている
* DISP-742 - ブロックされたリクエストによって Auth Checker で保護されたリソースに情報が漏洩する場合がある

**機能強化**：

* DISP-746 - dispatcher にラベルのない文字列が存在する場合は警告が表示される

**新機能**:

* DISP-747 - Apache 環境で要求の情報を提供

### リリース 4.3.1（2018 年 10 月 16 日） {#oct}

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

### リリース 4.3.0（2018 年 6 月 13 日） {#jun}

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

| プラットフォーム | アーキテクチャ | OpenSSLのサポート | ダウンロード |
|---|---|---|---|
| Linux | i686（32 ビット） | なし | [dispatcher-apache2.4-linux-i686-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.3.tar.gz) |
| Linux | i686（32 ビット） | 1.0 | [dispatcher-apache2.4-linux-i686-ssl1.0-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.0-4.3.3.tar.gz) |
| Linux | i686（32 ビット） | 1.1 | [dispatcher-apache2.4-linux-i686-ssl1.1-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.1-4.3.3.tar.gz) |
| Linux | x86_64（64 ビット） | なし | [dispatcher-apache2.4-linux-x86_64-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.3.tar.gz) |
| Linux | x86_64（64 ビット） | 1.0 | [dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.3.tar.gz) |
| Linux | x86_64（64 ビット） | 1.1 | [dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.3.tar.gz) |
| macOS | x86_64（64 ビット） | なし | [dispatcher-apache2.4-darwin-x86_64-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.3.tar.gz) |

### IIS {#iis}

| プラットフォーム | アーキテクチャ | OpenSSLのサポート | ダウンロード |
|---|---|---|---|
| Windows | x86（32ビット） | なし | [dispatcher-iis-windows-x86-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.3.zip) |
| Windows | x86（32ビット） | 1.0 | [dispatcher-iis-windows-x86-ssl1.0-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.0-4.3.3.zip) |
| Windows | x86（32ビット） | 1.1 | [dispatcher-iis-windows-x86-ssl1.1-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.1-4.3.3.zip) |
| Windows | x64（64 ビット） | なし | [dispatcher-iis-windows-x64-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.3.zip) |
| Windows | x64（64 ビット） | 1.0 | [dispatcher-iis-windows-x64-ssl1.0-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.0-4.3.3.zip) |
| Windows | x64（64 ビット） | 1.1 | [dispatcher-iis-windows-x64-ssl1.1-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.1-4.3.3.zip) |
