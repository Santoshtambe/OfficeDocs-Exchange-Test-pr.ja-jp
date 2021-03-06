﻿---
title: 'Managing devices for Outlook for iOS and Android for Exchange Server: Exchange 2013 Help'
TOCTitle: Managing devices for Outlook for iOS and Android for Exchange Server
ms:assetid: 16ce7d24-be74-4466-b126-828a67f69b6e
ms:mtpsurl: https://technet.microsoft.com/ja-jp/library/Mt465748(v=EXCHG.150)
ms:contentKeyID: 70089695
ms.date: 04/24/2018
mtps_version: v=EXCHG.150
ms.translationtype: HT
---

# Managing devices for Outlook for iOS and Android for Exchange Server

 

_**適用先:**Exchange Server 2010, Exchange Server 2013_

_**トピックの最終更新日:**2018-04-01_

**概要**:この記事では、Exchange ActiveSync プロトコルで基本認証を使用する場合に、Exchange Active Sync によって Exchange オンプレミス組織内の iOS および Android 用の Outlook を搭載したモバイル デバイスを管理する方法について説明します。

Microsoft では、オンプレミス環境の Exchange メールボックスへのアクセスに使用するモバイル デバイスを管理するために、Exchange ActiveSync を推奨しています。Exchange ActiveSync は、Microsoft Exchange を実行しているサーバー上の組織の情報に携帯電話がアクセスすることを可能にする Microsoft Exchange の同期プロトコルです。

この記事では、基本認証で認証するときの Android および iOS 用の Outlook を実行するモバイル デバイスを対象とした特定の Exchange ActiveSync の機能とシナリオについて説明します。Microsoft Exchange の同期プロトコルの詳細については、[Exchange ActiveSync](exchange-activesync-exchange-2013-help.md) でご確認いただけます。さらに、「[Office ブログ](https://go.microsoft.com/fwlink/p/?linkid=623922)」では、iOS および Android 用の Outlook を実行するデバイスで Exchange ActiveSync を使用した場合のパスワードの適用とその他のメリットを詳細に説明しています。

## PIN ロックとデバイスの暗号化

組織の Exchange ActiveSync ポリシーが、ユーザーが電子メールを同期するためにモバイル デバイスのパスワードを必要とする場合は、Outlook はこのポリシーをデバイス レベルで適用します。この動作は、Apple と Google で提供される使用可能な制御に基づき、iOS デバイスと Android デバイスでは異なります。

IOS デバイスでは、Outlook はパスコードまたは PIN が適切に設定されていることを確認します。パスコードが設定されていない場合、Outlook は、iOS 設定でパスコードを作成するようユーザーにメッセージを表示します。パスコードがセットアップされるまで、ユーザーは iOS 用 Outlook にアクセスできません。

iOS 用 Outlook は、iOS 10.0 以降でのみ動作します。これらのデバイスには、組み込み暗号化が装備されています。この暗号化は、Outlook が iOS デバイスにローカルに格納するすべてのデータを暗号化するためにパスコードが有効にされると、Outlook が使用する機能です。そのため、PIN のある iOS デバイスは、これが ActiveSync ポリシーで必要かどうかに関係なく、暗号化されます。

Android デバイスでは、Outlook は画面ロックの規則を適用します。また、Google は、パスワードの長さと複雑さ、および電話をワイプする前に許容される画面ロック解除の試行回数に関する Exchange ポリシーに Android 用 Outlook が従うようにする制御を提供します。Android 用 Outlook はストレージの暗号化も推奨し (有効になっていない場合)、ステップ バイ ステップ チュートリアルを使用して、ユーザーにこのプロセスの手順をガイドします。

これらのパスワード セキュリティ設定をサポートしていない iOS と Android デバイスは、Exchange メールボックスに接続できません。

## Exchange ActiveSync でのリモート ワイプ

Exchange ActiveSync を使用すると、管理者はデバイスが侵害されている場合などに、リモートでデバイスをワイプできます。iOS および Android 用の Outlook では、完全なデバイス ワイプではなく、リモート ワイプが Outlook アプリ自体で行われます。

管理者によってリモート ワイプ コマンドが要求されると、ワイプは Outlook アプリによる Exchange への次の接続の数秒以内に実行されます。Outlook アプリはリセットされ、すべての Outlook の電子メール、予定表、連絡先、およびファイルのデータがデバイスと Outlook サービスから削除されます。ワイプは、ユーザーの個人用アプリや Outlook 以外の情報には影響しません。

iOS および Android 用の Outlook は、Exchange のユーザーのモバイル デバイスの下に単一のモバイル デバイス関連付けとして表示されるため、リモート ワイプ コマンドはデータを削除し、そのユーザーに関連付けられている Outlook (iPhone、iPad、Android) を実行しているすべてのデバイスから同期関係を削除します。


> [!NOTE]
> iOS および Android 用の Outlook の背後にはクラウド アーキテクチャがあるため、リモート デバイス ワイプの結果は Exchange に報告されません。ワイプが成功した場合でも、状態は<STRONG>保留</STRONG>として表示されます。これは既知の問題であり、解決策を開発中です。



## デバイス識別子とアクセスの制御

iOS および Android 用の Outlook のクラウドベースのアーキテクチャにより、Outlook の接続は Exchange のユーザーごとに単一のモバイル デバイス識別子 (ID) として表示されます。つまり、各ユーザーのモバイル デバイスのアクセス制御は、このデバイス ID に関連付けられているすべてのデバイスに適用されます。この実装では、従来の Exchange ActiveSync デバイスのアクセス制御の動作とは異なる 2 つの条件を作成します。

  - **ブロック:** ブロック規則は、すべてのデバイスとサポートされているオペレーティング システム上の Outlook をブロックします。個々のデバイスまたはオペレーティング システムをブロックすることはできません。

  - **検疫:** 検疫プロセスはデバイス単位ではなく、ユーザー単位で機能します。ユーザーのデバイスが検疫から解放されると、必要な数の追加のデバイスに Outlook をインストールして構成できます。ユーザーは検疫から解放されているため、そのユーザーに関連付けられているすべての新しいデバイスは検疫されません。

モバイル デバイス メールボックス ポリシーが設定されている場合は、関連付けられているすべてのデバイスに適用されます。そのため、特定のメールボックスに対して PIN ロックを適用する場合は、そのメールボックスに接続するすべてのデバイスに PIN が必要です。


> [!NOTE]
> デバイス ID は<EM>物理デバイス</EM> ID によって管理されないため、予告なしに変更することができます。これが行われると、ユーザー デバイスを管理するためにデバイス ID が使用された場合に、予期しない結果が発生する可能性があります。これは、許可されている既存のデバイスが Exchange によって予期せずブロックまたは隔離されることがあるためです。したがって、管理者はデバイスの種類またはデバイス モデルに基づいて、デバイスを許可/ブロックするモバイル デバイスのポリシーのみを設定することをお勧めします。



## iOS および Android 用の Outlook のブロック

このセクションでは、Exchange のオンプレミス組織に属するユーザーが iOS および Android 用の Outlook の基本認証を使ってデータにアクセスしないようにするために、アプリをブロックする方法について説明します。

どの Exchange 組織にも、セキュリティやデバイスの管理に関するそれぞれのポリシーがあります。iOS および Android 用の Outlook が組織のニーズに合わない、あるいは何らかの理由で組織にとって最適なソリューションではないと判断される場合、管理者は Exchange ActiveSync のデバイス管理ポリシーを使ってアプリをブロックすることができます。Exchange ActiveSync の構成に関する詳細な説明については、「[デバイス アクセスの制御](https://go.microsoft.com/fwlink/p/?linkid=6240)」を参照してください。Exchange ActiveSync の管理画面で、Outlook アプリは **\[DeviceModel:'Outlook for iOS and Android'\]** または **\[DeviceType:'Outook'\]** のように識別されています。


> [!NOTE]
> デバイス ID は<EM>物理デバイス</EM> ID によって管理されないため、予告なしに変更することができます。これが行われると、ユーザー デバイスを管理するためにデバイス ID が使用された場合に、予期しない結果が発生する可能性があります。これは、許可されている既存のデバイスが Exchange によって予期せずブロックまたは隔離されることがあるためです。したがって、管理者はデバイスの種類またはデバイス モデルに基づいて、デバイスを許可/ブロックするモバイル デバイスのポリシーのみを設定することをお勧めします。



組織内の Exchange ユーザーは、Outlook on the web for iPhone/iPad/Android アプリや iOS や Android の組み込みメール アプリを使って、引き続き Exchange のメールボックスにアクセスすることができます。

次の例では、Exchange 管理センターでデバイス ルールを作成した後に、そのデバイス ルールが PowerShell でどのように表示されるかを示します。

![PowerShell におけるデバイス ルールの例。](images/Mt465748.d1418996-ff8b-4ddb-a3d9-7a80d808e677(EXCHG.150).png "PowerShell におけるデバイス ルールの例。")

## Exchange ActiveSync でのデバイス管理に関するよくあるご質問

以下は、iOS および Android 用の Outlook を実行するデバイスのパスワード、PIN、および暗号化ポリシーに関してよく寄せられる質問です。

## iOS 上のネイティブ メール アプリケーションでは、パスワードの長さと複雑さの要件に対して Exchange ActiveSync ポリシーが適用されます。なぜ Outlook では適用されないのですか?

Outlook は、Microsoft が利用可能なサード パーティ アプリケーション開発者制御を利用しています。Apple はさらに多くのコントロールを開発者が利用できるようにしており、Microsoft も引き続きこの機能を改善していきます。

## なぜ、iOS および Android 用の Outlook では、アプリ レベルでなく、デバイス レベルで PIN が適用されるのですか?

IOS と Android で利用できる機能を評価した結果、Microsoft は、利便性とセキュリティの両方の観点から、デバイス レベルの PIN がお客様にとって最適なエクスペリエンスを提供すると考えています。アプリ レベルの PIN では、ユーザーは電子メールにアクセスするために 2 つの異なる PIN を入力する必要があることを意味しますが、これは望ましくない場合があります。さらに、デバイス レベルの PIN は、ネイティブ デバイスの暗号化、iOS での TouchID、Android での Smart Lock などの機能を利用できることを意味します。リモート ワイプはまだアプリ レベルで実装されています。これは、Outlook のワイプ時にユーザーの個人用アプリケーションとデータには影響がないことを意味しています。

## Outlook for Android はデバイスの暗号化をサポートしますか?

はい。Outlook for Android は Exchange のモバイル デバイス メールボックス ポリシー経由でデバイスの暗号化をサポートします。しかし、Android 7.0 より前では、このプロセスの可用性と実装は、Android OS バージョンとデバイスの製造元によって異なり、ユーザーは暗号化プロセス中にキャンセルできるようになっています。Google から Android 7.0 に導入された変更により、Outlook for Android は Android 7.0 以降を実行しているデバイスで暗号化を強制できるようになりました。これらのオペレーティング システムを実行しているデバイスのユーザーは暗号化プロセスをキャンセルできません。

Android デバイスが暗号化されておらず、攻撃者がデバイスを所持している場合でも、デバイスの PIN が有効になっている限り、Outlook データベースにはアクセスできません。これは、USB デバッグが有効になっていて、Android SDK がインストールされている場合にも該当します。攻撃者がこの情報にアクセスするために PIN をバイパスするようにデバイスをルート化しようとすると、ルート化プロセスはすべてのデバイス ストレージをワイプして、すべての Outlook データを削除します。デバイスが盗まれる前に、ユーザーによってデバイスの暗号化の解除とルート化が行われている場合、攻撃者はデバイスの USB デバッグを有効にし、Android SDK がインストールされているコンピューターにデバイスを接続することによって、Outlook データベースにアクセス可能になります。

