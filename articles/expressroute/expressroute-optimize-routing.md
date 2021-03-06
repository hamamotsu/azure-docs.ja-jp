---
title: "ExpressRoute ルーティングの最適化 | Microsoft Docs"
description: "このページでは、Microsoft とユーザー (企業) のネットワークとを接続する ExpressRoute 回線がユーザー側に複数存在する場合のルーティングを最適化する方法について詳しく説明します。"
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
ms.assetid: fca53249-d9c3-4cff-8916-f8749386a4dd
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: charwen
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 26f0992e734f0aae96ac6e8b7040d661d5fb063c


---
# <a name="optimize-expressroute-routing"></a>ExpressRoute ルーティングの最適化
ExpressRoute 回線が複数あるとき、Microsoft への接続経路は複数存在します。 その結果、期待したルーティングが行われない、つまりトラフィックが貴社のネットワークから Microsoft に到達するまでの経路と、Microsoft から貴社のネットワークに到達するまでの経路が、想定よりも長くなってしまう可能性があります。 ネットワーク パスが長くなるほど、遅延は大きくなります。 遅延は、アプリケーションのパフォーマンスとユーザー エクスペリエンスに直接影響します。 この記事では、該当する問題について例示すると共に、標準のルーティング技術を使ってルーティングを最適化する方法を説明します。

## <a name="suboptimal-routing-case-1"></a>最適ではないルーティングの例 (その 1)
では、具体的な例を用いてルーティングの問題を詳しく見ていきましょう。 米国のロサンゼルスとニューヨークにそれぞれ 1 つオフィスがあるとします。 2 つのオフィスは、ワイド エリア ネットワーク (WAN) に接続されています。WAN は、自社のバックボーン ネットワークでも、サービス プロバイダーの IP VPN でもかまいません。 また ExpressRoute 回線が 2 つ存在します。1 つは米国西部に、もう 1 つは米国東部にあり、それらも WAN に接続されています。 オフィスから Microsoft のネットワークには、明らかに 2 とおりの接続経路があります。 このとき米国西部と米国東部の両オフィスで Azure (Azure App Service など) をデプロイしているとします。 ユーザーの接続先として意図した Azure リージョンは当然、ロサンゼルスなら米国西部、ニューヨークなら米国東部です。それぞれのオフィスのユーザーが最適なパフォーマンスを享受できるよう最寄りの Azure サービスがアクセス先となるようにサービス管理者によってアドバタイズされています。 ところが、意図した結果が得られるのは米国東部のオフィスだけで、米国西部のオフィスでは思いどおりの結果が得られません。 問題の原因は、 Azure 米国東部 (23.100.0.0/16) のプレフィックスと Azure 米国西部 (13.100.0.0/16) のプレフィックスの両方が、それぞれの ExpressRoute 回線でアドバタイズされていることにあります。 どのプレフィックスがどのリージョンに属しているかがわからなければ、両者を区別して扱うことはできません。 どちらのプレフィックスも米国西部より米国東部の方が近いと WAN ネットワークで判断される可能性があり、その場合、両オフィスのユーザーが米国東部の ExpressRoute 回線にルーティングされます。 最終的にロサンゼルス オフィスでは、満足なパフォーマンスを享受できないユーザーが続出する結果となります。

![](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>解決策: BGP コミュニティの使用
両オフィスのユーザーに使用されるルーティングを最適化するには、Azure の米国西部リージョンに属しているプレフィックスと米国東部リージョンに属しているプレフィックスとを区別できなければなりません。 この情報をコード化するためには、[BGP コミュニティ値](expressroute-routing.md)を使用します。 そこで各 Azure リージョンに一意の BGP コミュニティ値を割り当てました (例: 米国東部は "12076:51004"、米国西部は "12076:51006")。 これでどのプレフィックスがどの Azure リージョンに属しているかという情報が得られるので、優先する ExpressRoute 回線を構成することができます。 ルーティング情報の交換に BGP を使用するため、BGP の Local Preference を使用してルーティングを制御できます。 この例では、13.100.0.0/16 に割り当てる Local Preference 値を米国西部では米国東部よりも大きくすることが考えられます。同様に、23.100.0.0/16 に割り当てる Local Preference 値は、米国西部より米国東部の方が大きくなるようにします。 このように構成することで、Microsoft への経路が 2 つあっても、ロサンゼルスのユーザーは米国西部の ExpressRoute 回線を使用して米国西部リージョンに接続し、ニューヨークのユーザーは米国東部の ExpressRoute を使用して米国東部リージョンに接続することができます。 両側でルーティングが最適化されます。 

![](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

## <a name="suboptimal-routing-case-2"></a>最適ではないルーティングの例 (その 2)
もう 1 つ例を紹介します。Microsoft から貴社ネットワークへの接続経路が長くなってしまうケースです。 このケースでは、オンプレミスの Exchange サーバーと Exchange Online を[ハイブリッド環境](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx)で使用しています。 オフィスはいずれも WAN に接続されています。 マイクロソフトには、両オフィスのオンプレミス サーバーのプレフィックスを 2 つの ExpressRoute 回線を介してアドバタイズします。 Exchange Online はメールボックスを移行する場合などに、オンプレミス サーバーへの接続を開始します。 ここで問題が発生します。ロサンゼルス オフィスへの接続が、わざわざ米国東部の ExpressRoute 回線にルーティングされてから、大陸を横断して米国西部のオフィスに戻ってくるという事態が発生しています。 問題の原因は 1 つ目の例と同様です。 手掛かりがなければ、Microsoft のネットワークが、米国東部に近い利用者のプレフィックスと、米国西部に近い利用者のプレフィックスを判別できないのです。 ロサンゼルスのオフィスへの経路が正しく選択されないという事態が起こります。

![](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>解決策: AS PATH プリペンドの使用
この問題には解決策が 2 つあります。 1 つ目は単純です。米国西部の ExpressRoute 回線にはロサンゼルス オフィスのオンプレミス プレフィックス (177.2.0.0/31) を、米国東部の ExpressRoute 回線にはニューヨーク オフィスのオンプレミス プレフィックス (177.2.0.2/31) をアドバタイズします。 結果的に、Microsoft からそれぞれのオフィスに接続するための経路は 1 つしか存在しないことになります。 あいまいさが排除され、ルーティングが最適化されます。 この設計を採用した場合、フェールオーバーの方針を考慮する必要があります。 ExpressRoute を介した Microsoft への経路に障害が発生しても、Exchange Online が貴社のオンプレミス サーバーに引き続き接続できるように対策を講じなければなりません。 

もう 1 つの解決策は、引き続き両方の ExpressRoute 回線で 2 つのプレフィックスをアドバタイズしたうえで、どのプレフィックスがどちらのオフィスに近いか、という手掛かりを Microsoft に知らせる方法です。 BGP の AS Path プリペンドがサポートされているため、プレフィックスの AS Path を構成することでルーティングを制御することができます。 この例では、172.2.0.0/31 の AS PATH を、米国東部では意図的に長くすることが考えられます。そうすることで、このプレフィックスに向かうトラフィックでは、米国西部の ExpressRoute 回線が優先されます (このプレフィックスに対する経路は米国西部の方が短いと Microsoft のネットワークが判断します)。 米国西部でも同様に、172.2.0.2/31 の AS PATH を意図的に長くし、米国東部の ExpressRoute 回線が優先されるようにします。 これで両方のオフィスのルーティングが最適化されます。 このように設計すれば、いずれかの ExpressRoute 回線で障害が発生しても、Exchange Online は、もう 1 つの ExpressRoute 回線および WAN を介して引き続き貴社オフィスに到達することができます。 

> [!IMPORTANT]
> Microsoft ピア設定では、受信したプレフィックスの AS PATH からプライベート AS 番号が削除されます。 Microsoft ピア設定のルーティングを制御するには、AS PATH にパブリック AS 番号を付加する必要があります。
> 
> 

![](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!IMPORTANT]
> この記事の例は Microsoft ピアリングとパブリック ピアリングのものですが、プライベート ピアリングでも同じ機能がサポートされます。 また、AS PATH プリペンドは単一の ExpressRoute 回線内で機能し、プライマリ パスとセカンダリ パスの選択に影響します。
> 
> 




<!--HONumber=Nov16_HO2-->


