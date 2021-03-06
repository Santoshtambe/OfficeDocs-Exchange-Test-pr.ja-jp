﻿---
title: 'Exchange ハイブリッド展開でのトランスポート オプション: Exchange 2013 Help'
TOCTitle: Exchange ハイブリッド展開でのトランスポート オプション
ms:assetid: da605a78-5429-4de8-8b04-bc4c45a41ba1
ms:mtpsurl: https://technet.microsoft.com/ja-jp/library/JJ659055(v=EXCHG.150)
ms:contentKeyID: 49894957
ms.date: 01/11/2018
mtps_version: v=EXCHG.150
ms.translationtype: HT
---

# Exchange ハイブリッド展開でのトランスポート オプション

 

_<strong>適用先:</strong>Exchange Server 2013, Exchange Server 2016_

_<strong>トピックの最終更新日:</strong>2016-12-09_

ハイブリッド環境では、メールボックスを社内 Exchange 組織に持つことも、Exchange Online 組織に持つこともできます。これら 2 つの別々の組織をユーザーに対して 1 つの結合した組織として表示し、組織間でメッセージの交換を行う重要なコンポーネントは、ハイブリッド トランスポートです。ハイブリッド トランスポートでは、いずれかの組織の受信者間で送信されたメッセージは、認証、暗号化され、トランスポート層セキュリティ (TLS) を使用して転送されます。これらのメッセージは、トランスポート ルール、ジャーナリング、およびスパム対策ポリシーといった Exchange コンポーネントに対して「内部」として表示されます。ハイブリッド トランスポートは、ハイブリッド構成ウィザードによって自動的に構成されます。

ハイブリッド トランスポート構成をハイブリッド構成ウィザードで動作させるには、Exchange Online からの接続を受け入れるオンプレミス SMTP エンドポイントを メールボックス サーバー (Exchange 2016 以降)、クライアント アクセス サーバー (Exchange 2013)、ハブ トランスポート サーバー (Exchange 2010 以前)、またはエッジ トランスポート サーバー (Exchange 2010 以降) にする必要があります。


> [!IMPORTANT]
> SMTP トラフィックを処理または変更するオンプレミスの Exchange サーバーと Office 365 の間には、どんなサーバー、サービス、デバイスも配置しないでください。オンプレミスの Exchange 組織と Office 365 の間の安全なメール フローは、組織間で送信されるメッセージに含まれる情報によって異なります。TCP ポート 25 で変更なしで SMTP トラフィックを許可するファイアウォールはサポートされます。サーバー、サービス、デバイスがオンプレミスの Exchange 組織と Office 365 の間で送信されるメッセージを処理する場合、この情報は削除されます。このような場合、メッセージは組織内部のものとは見なされず、組織内部のメッセージには適用されないスパム対策フィルター、トランスポート ルール、ジャーナル ルール、その他のポリシーの対象となります。



外部のインターネット送信者から、両方の組織の受信者に送信された受信メッセージは、共通の受信ルートを通ります。組織から外部のインターネット受信者に送信された送信メッセージは、共通の送信ルートまたは独立ルート経由で送信されます。

ハイブリッド展開を計画して構成するときに、送信メールおよび受信メールのルーティング方法を選択する必要があります。社内組織および Exchange Online 組織内の受信者の受信および送信メッセージがたどるルートは、次の要因によって決まります。

  - Exchange Online またはオンプレミス組織を経由して、オンプレミス メールボックスと Exchange Online メールボックスの両方の受信インターネット メールをルーティングしたいですか。
    
    両方の組織の受信メッセージが辿るルートは、大部分のメールボックスの設置場所、Office 365 のマルウェア対策およびスパム対策スキャンを使用してオンプレミス組織を保護するかどうか、コンプライアンス インフラストラクチャの構成場所などの、さまざまな要因によって異なります。

  - Exchange Online 組織から外部受信者への送信メールを社内組織でルーティングしますか (メール トランスポートの集中管理)、それとも直接インターネットにルーティングしますか。
    
    メール トランスポートの集中管理を使用して、Exchange Online 組織内のメールボックスから送信されるすべてのメールがインターネットに配信される前に、社内組織を経由させることができます。この方法は、インターネットを介して送信および受信するすべてのメールを社内サーバーによって処理する必要のある、法令遵守のシナリオにおいて役立ちます。代わりに、Exchange Online が外部受信者向けのメッセージを直接インターネットに配信するように構成できます。
    

    > [!NOTE]
    > メール トランスポートの集中管理は、コンプライアンスに関連する特定のトランスポート ニーズがある組織のみに推奨されます。標準的な Exchange 組織に対する提案は、集中型メール トランスポートを有効にしないことです。これを有効にすると、オンプレミス サーバーで処理されるメッセージの量が大幅に増加し、使用される帯域幅が増大し、オンプレミス サーバー上で不要な依存関係が構築されます。



  - 社内組織にエッジ トランスポート サーバーを展開しますか
    
    ドメインに参加している内部 Exchange サーバーを直接インターネットに公開したくない場合は、エッジ トランスポート サーバーを境界ネットワークに展開できます。エッジ トランスポート サーバーをハイブリッド展開に追加する方法の詳細については、「[ハイブリッド展開でのエッジ トランスポート サーバー](edge-transport-servers-with-hybrid-deployments-exchange-2013-help.md)」を参照してください。

インターネットで送受信されるメッセージのルーティング方法に関係なく、社内組織と Exchange Online 組織間で送信されるすべてのメッセージはセキュリティ保護されたトランスポートで送信されます。詳細については、後の「信頼された通信」を参照してください。

これらのオプションが組織内でのメッセージのルーティングにどのように影響するかについては、「[Exchange ハイブリッド展開でのトランスポート ルーティング](transport-routing-in-exchange-hybrid-deployments-exchange-2013-help.md)」を参照してください。

## ハイブリッド展開での Exchange Online Protection

EOP は Microsoft によって提供されているオンライン サービスで、社内組織をウイルス、スパム、フィッシング詐欺、およびポリシー違反から保護するために多くの企業で使用されています。Office 365 では、Exchange Online 組織を上記の脅威から保護するのに EOP を使用します。Office 365 にサインアップすると、Exchange Online 組織に関連付けられた EOP の会社が自動的に作成されます。

EOP の会社には、Exchange Online 組織用に構成できるメール トランスポート設定がいくつか含まれています。どの SMTP ドメインが特定の IP アドレスから発せられる必要があるか、TLS および Secure Sockets Layer (SSL) 証明書を必要とするかどうか、スパム対策フィルターまたは法令遵守ポリシーをバイパスするかどうかを指定できます。EOP は Exchange Online 組織の正面玄関です。すべてのメッセージは発信元に関係なく、Exchange Online 組織のメールボックスに到達する前に EOP を通る必要があります。そして、Exchange Online 組織から送信されたすべてのメッセージは、インターネットに到達する前に EOP を通る必要があります。

ハイブリッド展開をハイブリッド構成ウィザードで構成すると、社内組織と Exchange Online 組織に含まれる EOP の会社で、すべてのトランスポート設定が自動的に構成されます。ハイブリッドの構成ウィザードは、この EOP の会社のすべての受信コネクタと送信コネクタおよびその他の設定を構成して、社内組織と Exchange Online 組織間で送信されるメッセージをセキュリティ保護し、メッセージを正しい送信先にルーティングします。Exchange Online 組織にカスタムのトランスポート設定を構成する場合、この EOP の会社で構成することもできます。

## 信頼された通信

オンプレミス組織と Exchange Online 組織の両方の受信者を保護するため、および組織間で送信されるメッセージが傍受されて読まれないことを保証するために、社内組織と EOP の間のトランスポートは強制的に TLS を使用するように構成されます。セキュリティで保護されたメール トランスポートは、信頼できる第三者の証明機関 (CA) から提供される TLS/SSL 証明書を使用します。EOP と Exchange Online 組織間のメッセージにも TLS が使用されます。

強制 TLS トランスポートを使用する場合、送信側サーバーと受信側サーバーは、互いのサーバー上で構成された証明書を調べます。証明書で構成されているサブジェクト名またはいずれかのサブジェクトの別名 (SAN) は、管理者が相手側のサーバー上で明示的に指定した FQDN と一致する必要があります。たとえば、EOP が mail.contoso.com FQDN から送信されたメッセージを受け入れ、セキュリティで保護するように構成されている場合、送信側の社内クライアント アクセス サーバーまたはエッジ トランスポート サーバーには、サブジェクト名または SAN の形で mail.contoso.com が指定された SSL 証明書が必要です。この要件を満たしていない場合、接続は EOP によって拒否されます。


> [!NOTE]
> 使用している FQDN は、受信者の電子メール ドメイン名に一致する必要はありません。唯一の要件は、証明書のサブジェクト名または SAN の FQDN が、受信側または送信側のサーバーで受け入れるように構成されている FQDN と一致する必要があるということです。



TLS を使用するのに加えて、組織間のメッセージは「内部」として処理されます。このアプローチにより、メッセージはスパム対策設定やその他のサービスをバイパスできるようになります。

SSL 証明書とドメイン セキュリティの詳細については、「[ハイブリッド展開の証明書要件](certificate-requirements-for-hybrid-deployments-exchange-2013-help.md)」および「[TLS 証明書について](http://go.microsoft.com/fwlink/p/?linkid=187237)」を参照してください。

