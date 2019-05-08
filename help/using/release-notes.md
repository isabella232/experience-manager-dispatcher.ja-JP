---
title: AEMディスパッチャーリリースノート
seo-title: AEMディスパッチャーリリースノート
description: Adobe Experience Managerディスパッチャーに固有のリリースノート
seo-description: Adobe Experience Managerディスパッチャーに固有のリリースノート
uuid: ae3cf62-0514-4c03- a3b9-71799a482cbd
topic-tags: release-notes
content-type: リファレンス
products: SG_ EXPERNEMENTMANAGER/6.4
discoiquuid: ff3d38e0-71c9-4b41-85f9- fa896393aac5
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# AEMディスパッチャーリリースノート{#aem-dispatcher-release-notes}

## リリース情報 {#release-information}

|  |
|--- |--- |
| 商品 | Adobe Experience Manager（AEM）ディスパッチャー |
| バージョン | 4.3.2 |
| タイプ | マイナーリリース |
| 日付 | 2019年1月1日 |
| ダウンロード URL | <ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Microsoft Internet Information Services（IIS）](release-notes.md#iis)</li></ul> |
| 互換性 | AEM6.1以降 |

## システム要件および使用条件 {#system-requirements-and-prerequisites}

要件と前提条件について詳しくは、 [サポートされるプラットフォーム](https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/technical-requirements.html) ページを参照してください。

最新バージョンのAEMディスパッチャーを使用して最新の機能、最新のバグ修正、最高のパフォーマンスを実現することを強くお勧めします。

## インストール手順 {#installation-instructions}

詳しい手順については、「ディスパッチャー [のインストール」を参照](dispatcher-install.md)してください。

## リリース履歴 {#release-history}

### リリース4.3.2（2019年1月31日） {#jan}

**バグ修正**:

* DESP-734:ハンドラーとして設定されていない場合、ディスパッチャーが挿入_ output_ filterでクラッシュする原因となります
* Disp-735: RIAは、Albelian Linuxでは機能しません
* Disp-740: MacOS Mojaveでのディスパッチャーの読み込みは、デフォルトで無効になっています
* DESP-742:ブロックされたリクエストによって情報が認証チェッカーによって保護されたリソースに漏洩する場合があります

**改善点**:

* DESP-746:ディスパッチャー内の未署名の文字列が表示されます。警告が表示されます

**新機能**:

* DISSP-747- Apache環境でリクエスト情報を提供します

### リリース4.3.1（2018年10月16日） {#oct}

**バグ修正**:

* DISSP-656:ディスパッチャーにより、ETagヘッダーが正しく提供されません
* DISP-694:ライブ接続を維持し続けるときに警告を抑制します
* DESP-714: cookieベースのセッション管理がIISで機能しません
* DESP-715: renderid cookieのセキュアフラグ
* DISP-720:一時ファイルが閉じていない場合、浪費する可能性があります（開いているファイルが多すぎる）
* DESP-721: Apacheが子を完全に再起動したときに、ディスパッチャーがポーリングを中断します
* DESP-722:キャッシュファイルは8進数モード0600で作成されます
* Disp-723:レンダリングタイムアウトが0に設定されている場合は、暗黙的な10分のタイムアウト（および再試行）
* DESP-725:文字列後の末尾の文字が名称未設定の値に変換されます
* DISSP-726:ファームが入力ホストと一致していない場合に警告を記録します
* DESP-727:ディスパッチャーは、空のキャッシュファイルのコンテンツの長さをリクエストします
* ディスパッチャーを介してヘッダーファイルにアクセスしようとしたときのDISP-730~404
* DISSP-731:ディスパッチャーはログ挿入に脆弱な脆弱性を持つ
* DISSP-732:ディスパッチャーは、URL内の連続した&quot;/&quot;を削除する必要があります
* DISP-733:ディスパッチャーは、年齢ヘッダーを設定（計算）する必要があります

**改善点**:

* DISSP-656:ディスパッチャーにより、ETagヘッダーが正しく提供されません
* DISP-694:ライブ接続を維持し続けるときに警告を抑制します
* DESP-715: renderid cookieのセキュアフラグ
* DESP-722:キャッシュファイルは8進数モード0600で作成されます
* DISSP-726:ファームが入力ホストと一致していない場合に警告を記録します

### リリース4.3.0（2018年6月13日） {#jun}

**バグ修正**:

* DISP-682:数値ログレベルが誤って適用されました
* DISSP-685-32ビットSolaris SPARCバイナリには、__ didf3への未定義の参照があります
* DISSP-688:ディスパッチャーが404応答で&quot;X- Cache- Info&quot;ヘッダーを返しません
* DISP-690: Last- Modifiedヘッダーがキャッシュできません
* DESP-691- w3wp. exeのアクセス違反
* DESP-693:ディスパッチャーダウンロードページ上のSolarisサーバーのアーキテクチャ詳細を更新する必要があります
* ディスパッチャー:ディスパッチャーモジュール4.2.3のDispatcherLogレベルの問題-695
* DISSP-698:ディスパッチャーTTLは、s- maxageおよびprivateディレクティブをサポートする必要があります
* DESP-700: Android Linuxではモジュールが正しく機能しません
* DESP-704:%2bを含むブラウザーリクエストが、発行者にエンコードされていない状態に送信されます
* DESP-705-二重の空きまたは破損によりディスパッチャーがクラッシュします（fasttop）。
* DESP-706:無効化中、ディスパッチャーは、無限ループの原因となる可能性のある後方参照コードリンクです
* DESP-709-一部のバニティURL拡張のブロック
* Disp-710- Linux用のビルドがセントOS6で使用できない

**改善点**:

* DISSP-652:ディスパッチャーは、日付ヘッダーに誤りがあります

## 参考リソース {#helpful-resources}

* [AEMディスパッチャーの概要](dispatcher.md)

## ダウンロード {#downloads}

### Apache 2.4 {#apache}

| プラットフォーム | アーキテクチャ | SSLサポート | ダウンロード |
|---|---|---|---|
| AIX | PowerPC（32- bit） | いいえ | [dispatcher-apache2.4-aix-powerpc-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc-4.3.2.tar.gz) |
| AIX | PowerPC（32- bit） | 可 | [dispatcher-apache2.4-aix-powerpc-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc-ssl-4.3.2.tar.gz) |
| AIX | PowerPC（64- bit） | いいえ | [dispatcher-apache2.4-aix-powerpc64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc64-4.3.2.tar.gz) |
| AIX | PowerPC（64- bit） | 可 | [dispatcher-apache2.4-aix-powerpc64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc64-ssl-4.3.2.tar.gz) |
| Linux | i686（32- bit） | いいえ | [dispatcher-apache2.4-linux-i686-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.2.tar.gz) |
| Linux | i686（32- bit） | 可 | [dispatcher-apache2.4-linux-i686-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl-4.3.2.tar.gz) |
| Linux | x86_64（64- bit） | いいえ | [dispatcher-apache2.4-linux-x86_64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.2.tar.gz) |
| Linux | x86_64（64- bit） | 可 | [dispatcher-apache2.4-linux-x86_64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl-4.3.2.tar.gz) |
| macOS | x86_64（64- bit） | いいえ | [dispatcher-apache2.4-darwin-x86_64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.2.tar.gz) |
| Solaris | AMD（32ビット） | いいえ | [dispatcher-apache2.4-solaris-i386-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-i386-4.3.2.tar.gz) |
| Solaris | AMD（32ビット） | 可 | [dispatcher-apache2.4-solaris-i386-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-i386-ssl-4.3.2.tar.gz) |
| Solaris | AMD（64- bit） | いいえ | [dispatcher-apache2.4-solaris-amd64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-amd64-4.3.2.tar.gz) |
| Solaris | AMD（64- bit） | 可 | [dispatcher-apache2.4-solaris-amd64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-amd64-ssl-4.3.2.tar.gz) |
| Solaris | SPARC（32ビット） | いいえ | [dispatcher-apache2.4-solaris-sparc-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparc-4.3.2.tar.gz) |
| Solaris | SPARC（32ビット） | 可 | [dispatcher-apache2.4-solaris-sparc-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparc-ssl-4.3.2.tar.gz) |
| Solaris | SPARC（64- bit） | いいえ | [dispatcher-apache2.4-solaris-sparcv9-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparcv9-4.3.2.tar.gz) |
| Solaris | SPARC（64- bit） | 可 | [dispatcher-apache2.4-solaris-sparcv9-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparcv9-ssl-4.3.2.tar.gz) |

### IIS {#iis}

| プラットフォーム | アーキテクチャ | SSLサポート | ダウンロード |
|---|---|---|---|
| Windows | x86（32ビット） | いいえ | [dispatcher-iis-windows-x86-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.2.zip) |
| Windows | x86（32ビット） | 可 | [dispatcher-iis-windows-x86-ssl-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl-4.3.2.zip) |
| Windows | x64（64- bit） | いいえ | [dispatcher-iis-windows-x64-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.2.zip) |
| Windows | x64（64- bit） | 可 | [dispatcher-iis-windows-x64-ssl-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl-4.3.2.zip) |
