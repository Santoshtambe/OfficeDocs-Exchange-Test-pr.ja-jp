﻿---
title: 'Exchange ハイブリッド展開でのトランスポート ルーティング: Exchange 2013 Help'
TOCTitle: Exchange ハイブリッド展開でのトランスポート ルーティング
ms:assetid: 36c2cea3-2e2f-40ac-88bd-7e1b6bd27828
ms:mtpsurl: https://technet.microsoft.com/ja-jp/library/JJ659050(v=EXCHG.150)
ms:contentKeyID: 49894952
ms.date: 01/11/2018
mtps_version: v=EXCHG.150
ms.translationtype: HT
---

# Exchange ハイブリッド展開でのトランスポート ルーティング

 

_<strong>適用先:</strong>Exchange Server 2013, Exchange Server 2016_

_<strong>トピックの最終更新日:</strong>2016-07-29_

このトピックでは、インターネットからの受信メッセージおよびインターネットへの送信メッセージのルーティング オプションについて説明します。


> [!IMPORTANT]
> SMTP トラフィックを処理または変更するオンプレミスの Exchange サーバーと Office 365 の間には、どんなサーバー、サービス、デバイスも配置しないでください。オンプレミスの Exchange 組織と Office 365 の間の安全なメール フローは、組織間で送信されるメッセージに含まれる情報によって異なります。TCP ポート 25 で変更なしで SMTP トラフィックを許可するファイアウォールはサポートされます。サーバー、サービス、デバイスがオンプレミスの Exchange 組織と Office 365 の間で送信されるメッセージを処理する場合、この情報は削除されます。このような場合、メッセージは組織内部のものとは見なされず、組織内部のメッセージには適用されないスパム対策フィルター、トランスポート ルール、ジャーナル ルール、その他のポリシーの対象となります。




> [!NOTE]
> このトピックの例に、ハイブリッド展開へのエッジ トランスポート サーバーの追加は含まれていません。社内組織、Exchange Online 組織、およびインターネット間でメッセージが通るルートは、エッジ トランスポート サーバーの追加によって変わることはありません。ルーティングは、社内組織内のみで変わります。ハイブリッド展開にエッジ トランスポート サーバーを追加する方法の詳細については、「<A href="edge-transport-servers-with-hybrid-deployments-exchange-2013-help.md">ハイブリッド展開でのエッジ トランスポート サーバー</A>」を参照してください。



## インターネットからの受信メッセージ

ハイブリッド展開の計画と構成の一部として、インターネットの送信者からのすべてのメッセージを Exchange Online 組織経由でルーティングするか、オンプレミスの組織経由でルーティングするかを決定する必要があります。インターネット送信者からのすべてのメッセージは、最初は選択した組織に配信され、次に受信者のメールボックスの場所に従ってルーティングされます。メッセージを Exchange Online 組織経由でルーティングするか、オンプレミスの組織経由でルーティングするかの選択は、両方の組織に送信されるすべてのメッセージに法令遵守ポリシーを適用するかどうかや、それぞれの組織に何個のメールボックスがあるかなど、さまざまな要因に依存します。

オンプレミスおよび Exchange Online の組織で受信者に送信されるメッセージのパスは、ハイブリッド展開で MX レコードを構成する方法によって決まります。推奨される方法は、Office 365 の Exchange Online Protection (EOP) をポイントするように MX レコードを構成することです。この構成により、最も正確なスパム フィルタリングが提供されるからです。ハイブリッド構成ウィザードでは、オンプレミスの組織と Exchange Online 組織の受信インターネット メッセージのルーティングは構成されません。受信インターネット メールの配信方法を変更する場合は、MX レコードを手動で構成する必要があります。

  - **Office 365 の Microsoft Exchange Online Protection (EOP) サービスをポイントするように MX レコードを変更する場合:** これはハイブリッド展開に推奨される構成です。いずれの組織の受信者に送信されるメッセージも、まず Exchange Online 組織でルーティングされます。オンプレミスの組織に存在する受信者に宛てられたメッセージは、最初に Exchange Online 組織経由でルーティングされ、次にオンプレミスの組織の受信者に配信されます。このルートは、オンプレミスの組織よりも Exchange Online 組織により多くの受信者が存在する場合にも推奨されます。この構成オプションは、Exchange Online Protection でスパムのスキャンとブロックを提供するために必要です。

  - **オンプレミス組織を MX レコードでポイントする場合:** いずれの組織の受信者に送信されるメッセージも、まずオンプレミスの組織でルーティングされます。Exchange Online に存在する受信者に宛てられたメッセージは、最初にオンプレミスの組織を経由してルーティングされ、次に Exchange Online の受信者に配信されます。このルートは、組織で送受信されたメッセージをジャーナリング ソリューションで検査することを義務付けるコンプライアンス ポリシーがある組織に役立ちます。このオプションを選択すると、Exchange Online Protection は効果的にスパム メッセージをスキャンできません。

詳細については、「[Exchange Online および Office 365 でのメール フローのベスト プラクティス (概要)](https://technet.microsoft.com/ja-jp/library/jj937232\(v=exchg.150\))」を参照してください。

インターネットの受信者からオンプレミスの受信者および Exchange Online の受信者に対して送信されたメッセージのルーティングの計画方法については、以下のセクションで一致するものをご覧ください。各セクションで「オンプレミスの Exchange サーバー」は、Exchange 2013 クライアント アクセス サーバーか Exchange 2016 メールボックス サーバーのいずれかです。

## 受信インターネット メッセージを Exchange Online 組織経由でルーティングする

次の手順と図は、Office 365 組織の EOP サービスを MX レコードでポイントする場合にハイブリッド展開で発生する受信メッセージのパスを示しています。メッセージのパスは、メール トランスポートの集中管理を有効にするかどうかによって異なります。


> [!IMPORTANT]
> 最初に EOP に配信されてから Exchange Online 組織を通ってルーティングされるメッセージを受信する社内メールボックスそれぞれに EOP ライセンスを購入しなければならない場合があります。詳細については、マイクロソフト製品販売店にお問い合わせください。



メール トランスポートの集中管理が**無効**である場合 (既定の構成)、受信インターネット メッセージはハイブリッド展開で次のようにルーティングされます。

1.  受信メッセージは、インターネットの送信者から受信者 julie@contoso.com および david@contoso.com に送信されます。Julie のメールボックスは、オンプレミスの Exchange メールボックス サーバーにあります。David のメールボックスは Exchange Online にあります。

2.  両方の受信者の電子メール アドレスに contoso.com が含まれており、contoso.com の MX レコードが EOP をポイントしているため、メッセージは EOP に配信されます。

3.  EOP は両方の受信者のメッセージを Exchange Online にルーティングします。

4.  Exchange Online はメッセージをウイルス スキャンして、それぞれの受信者を参照します。参照により、Julie のメールボックスはオンプレミス組織にあり、David のメールボックスは Exchange Online 組織内にあることがわかります。

5.  Exchange Online はメッセージを 2 つのコピーに分割します。メッセージの 1 つのコピーは David のメールボックスに配信されます。

6.  2 番目のコピーは Exchange Online から EOP に送り返されます。

7.  EOP は、オンプレミス組織のオンプレミスの Exchange サーバーにメッセージを送信します。

8.  Exchange は Exchange メールボックス サーバーにメッセージを送信し、メッセージはそこから Julie のメールボックスに配信されます。

**メール トランスポートの集中管理を無効にした状態で (既定の構成) 社内組織と Exchange Online 組織の両方に Exchange Online 組織でメールをルーティングする**

![EXO への集中管理受信](images/JJ659050.f98341e0-088d-49f0-bf8e-09f35255bf9e(EXCHG.150).png "EXO への集中管理受信")

メール トランスポートの集中管理が**有効**である場合、受信インターネット メッセージはハイブリッド展開で次のようにルーティングされます。

1.  受信メッセージは、インターネットの送信者から受信者 julie@contoso.com および david@contoso.com に送信されます。Julie のメールボックスは、オンプレミスの Exchange メールボックス サーバーにあります。David のメールボックスは Exchange Online にあります。

2.  両方の受信者の電子メール アドレスに contoso.com が含まれており、contoso.com の MX レコードが EOP をポイントしているため、メッセージは EOP に配信され、ウイルス スキャンされます。

3.  メール トランスポートの集中管理が有効であるため、EOP は両方の受信者のメッセージをオンプレミスの Exchange サーバーにルーティングします。

4.  オンプレミスの Exchange サーバーは、受信者ごとの参照を実行します。参照により、Julie のメールボックスはオンプレミス組織にあり、David のメールボックスは Exchange Online 組織内にあることがわかります。

5.  オンプレミスの Exchange サーバーがメッセージを 2 つのコピーに分割します。メッセージのコピーのうちの 1 つはオンプレミスの Exchange メールボックス サーバー上にある Julie のメールボックスに配信されます。

6.  もう一方のコピーは、オンプレミスの Exchange サーバーから EOP に送り返されます。

7.  EOP は Exchange Online にメッセージを送信します。

8.  Exchange はメッセージを David のメールボックスに配信します。

**メール トランスポートの集中管理を有効にした状態で社内組織と Exchange Online 組織の両方に Exchange Online 組織でメールをルーティングする**

![集中管理が有効にされた受信 EXO](images/JJ659050.062422d5-9cb6-42c2-9ec0-31962cd7ada6(EXCHG.150).png "集中管理が有効にされた受信 EXO")

## 社内組織で受信インターネット メッセージをルーティングする

次の手順と図は、MX レコードで社内組織をポイントする場合にハイブリッド展開で発生する受信インターネット メッセージのパスを示しています。

1.  受信メッセージは、インターネットの送信者から受信者 julie@contoso.com および david@contoso.com に送信されます。Julie のメールボックスは、オンプレミス組織の Exchange メールボックス サーバーにあります。David のメールボックスは Exchange Online にあります。

2.  両方の受信者のメール アドレスに contoso.com が含まれており、contoso.com の MX レコードがオンプレミス組織をポイントしているため、メッセージはオンプレミスの Exchange サーバーに配信されます。

3.  オンプレミスの Exchange サーバーは、オンプレミスのグローバル カタログ サーバーを使用して各受信者の参照を実行します。グローバル カタログを参照すると、Julie のメールボックスはオンプレミスの Exchange メールボックス サーバー上にあり、David のメールボックスは Exchange Online 組織内にあり、そのハイブリッド ルーティング アドレスは david@contoso.mail.onmicrosoft.com であると判断できます。

4.  オンプレミスの Exchange サーバーがメッセージを 2 つのコピーに分割します。メッセージのコピーのうちの 1 つはオンプレミスの Exchange メールボックス サーバーに送信された後、このメールボックス サーバーで Julie のメールボックスに配信されます。

5.  メッセージの 2 番目のコピーは、オンプレミスの Exchange サーバーによって EOP に送信されます。これは、Exchange Online 組織に送信されたメッセージを、TLS を使用するように構成された送信コネクタを使用して受信します。

6.  EOP はメッセージを Exchange Online 組織に送信します。そこでメッセージはウイルス スキャンされて David のメールボックスに配信されます。

**オンプレミスの組織と Exchange Online 組織の両方に対し、オンプレミスの組織を通してメールをルーティングする**

![オンプレミスの集中管理された受信メール](images/JJ659050.a246a1e2-7593-4d13-9426-73622a545c9a(EXCHG.150).png "オンプレミスの集中管理された受信メール")

## インターネットへの送信メッセージ

組織の受信者宛ての受信メッセージのルーティング方法を選択するだけでなく、Exchange Online の受信者から送信される送信メッセージのルーティング方法も選択できます。ハイブリッド構成ウィザードを実行する際に、以下の 2 つのオプションのうちいずれかを選択できます。

  - **メール トランスポートの集中管理を有効にしない**   このオプションはハイブリッド構成ウィザードで既定で選択されており、Exchange Online 組織から送信された送信メッセージはインターネットに直接ルーティングされます。このオプションは、社内の法令遵守ポリシーやその他の処理ルールを、Exchange Online 組織の受信者から送信されるメッセージに適用する必要がない場合に使用します。

  - **メール トランスポートの集中管理を有効にする**   このオプションを選択すると、Exchange Online 組織から送信された送信メッセージは社内組織でルーティングされます。同じ Exchange Online 組織内の他の受信者に送信されるメッセージを除き、Exchange Online 組織の受信者から送信されたすべてのメッセージは、社内組織経由で送信されます。これにより、法令遵守ルールをこれらのメッセージに適用したり、受信者が Exchange Online 組織にいるか、社内組織にいるかにかかわらず、適用する必要がある他のプロセスや要件をすべての受信者に適用したりできるようになります。
    

    > [!NOTE]
    > メール トランスポートの集中管理は、コンプライアンスに関連する特定のトランスポート ニーズがある組織のみに推奨されます。一般的な Exchange 組織では、メール トランスポートの集中管理を有効にしないことをお勧めします。



社内の受信者から送信されたメッセージは、上記のハイブリッド構成ウィザードで選択した選択肢に関係なく、常に DNS を使用してインターネットに直接送信されます。

次の手順と図は、社内の受信者から送信されたメッセージの送信メッセージの経路を説明しています。

1.  オンプレミスの Exchange メールボックス サーバーにメールボックスがある Julie は、外部インターネット受信者 erin@cpandl.com にメッセージを送信します。

2.  Exchange サーバーは cpandl.com の MX レコードを検索し、メッセージをインターネット上にある cpandl.com メール サーバーに送信します。

**社内の送信者からインターネットの受信者へのメッセージ**

![オンプレミスからの送信メール](images/JJ659050.71f287d6-b814-4820-a0dc-575f17d13894(EXCHG.150).png "オンプレミスからの送信メール")

Exchange Online 組織内の受信者からインターネットの受信者に対して送信されたメッセージのルーティングの計画方法については、以下のセクションで一致するものを参照してください。

## DNS を使用してインターネット向けのメッセージを Exchange Online から配信する (メール トランスポートの集中管理は無効)

次の手順と図は、**\[メール トランスポートの集中管理を有効にする\]** をハイブリッド構成ウィザードで選択していない場合 (既定の構成) に発生する、Exchange Online の受信者からインターネット受信者に送信されたメッセージの送信メッセージ パスを示しています。

1.  社内の Exchange Online 組織にメールボックスがある David が、外部のインターネット受信者 erin@cpandl.com にメッセージを送信します。

2.  Exchange Online はメッセージをスキャンしてウイルスの有無を確認し、Exchange Online EOP 会社に送信します。

3.  EOP は cpandl.com の MX レコードを検索し、メッセージをインターネット上にある cpandl.com メール サーバーに送信します。

**メール トランスポートの集中管理を無効にした状態 (既定の構成) で Exchange Online の送信者からのメールをインターネットに直接ルーティングする**

![EXO の直接送信メール](images/JJ659050.fe1c4241-0d6e-47bf-b61d-5af732d2dbbc(EXCHG.150).png "EXO の直接送信メール")

## オンプレミス組織で Exchange Online からのインターネット向けのメッセージをルーティングする (メール トランスポートの集中管理が有効)

次の手順と図は、**\[メール トランスポートの集中管理を有効にする\]** をハイブリッド構成ウィザードで選択したときに発生する、Exchange Online の受信者からインターネットの受信者に送信されたメッセージの送信メッセージ パスを示しています。

1.  社内の Exchange Online 組織にメールボックスがある David が、外部のインターネット受信者 erin@cpandl.com にメッセージを送信します。

2.  Exchange Online はメッセージをウイルス スキャンして EOP に送信します。

3.  EOP はインターネット向けのすべてのメッセージをオンプレミスのサーバーに送信するように構成されているため、メッセージはオンプレミスの Exchange サーバーにルーティングされます。メッセージは TLS を使用して送信されます。

4.  オンプレミスの Exchange サーバーは、David のメッセージに対して、法令遵守、ウイルス対策、および管理者によって構成されたその他のプロセスを実行します。

5.  オンプレミスの Exchange サーバーは cpandl.com の MX レコードを検索し、メッセージをインターネット上にある cpandl.com メール サーバーに送信します。

**メール トランスポートの集中管理を有効にした状態で Exchange Online の送信者からのメールを社内組織でルーティングする**

![オンプレミス経由の出力 EXO](images/JJ659050.7ea9ffee-944b-45ae-ba4d-c3484100399b(EXCHG.150).png "オンプレミス経由の出力 EXO")
