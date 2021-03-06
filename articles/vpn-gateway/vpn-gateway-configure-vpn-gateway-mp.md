---
title: "Azure クラシック ポータルで VPN ゲートウェイを構成する | Microsoft Docs"
description: "この記事では、仮想ネットワーク VPN ゲートウェイの構成手順、およびゲートウェイの VPN ルーティングの種類を変更する手順について説明します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: fbe59ba8-b11f-4d21-9bb1-225ec6c6d351
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/11/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 799a7ebd9df049bc3f8f4452f33ac40fa414c390


---
# <a name="configure-a-vpn-gateway-for-the-classic-deployment-model"></a>クラシック デプロイ モデル用の VPN ゲートウェイの構成
セキュリティで保護されたクロスプレミス接続を Azure と、オンプレミスの場所の間で作成する場合は、VPN ゲートウェイ接続を構成する必要があります。 クラシック デプロイメント モデルでは、ゲートウェイは 2 種類の VPN ルーティング (静的または動的) のいずれかにできます。 どちらの種類を選択するかは、ネットワーク設計計画、および使用するオンプレミスの VPN デバイスの両方によって決まります。 

たとえば、ポイント対サイト接続などの一部の接続オプションでは、動的ルーティング ゲートウェイが必要です。 サイト間接続であればどちらの種類のゲートウェイ VPN ルーティングでも構成可能ですが、ポイント対サイト (P2S) 接続とサイト間 (S2S) 接続の両方をサポートする構成にする場合は動的ルーティング ゲートウェイを構成する必要があります。 

さらに、接続に使用するデバイスが、作成する種類の VPN ルーティングをサポートしていることを確認する必要があります。 「 [VPN デバイスについて](vpn-gateway-about-vpn-devices.md)」を参照してください。

**この記事の内容** 

この記事は、(Azure ポータルではなく) [クラシック ポータル](https://manage.windowsazure.com) を使用したクラシック デプロイメント モデルを対象としたものです。 

**Azure のデプロイ モデルについて**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-overview"></a>構成の概要
次の手順は、Azure クラシック ポータルでの VPN ゲートウェイの構成方法です。 次の手順は、クラシック デプロイメント モデルを使用して作成された仮想ネットワークのゲートウェイに適用されます。 現時点では、ゲートウェイの一部の構成設定のみを Azure ポータルで利用できます。 利用可能になった時点で、Azure ポータルに新しい一連の手順が作成される予定です。

1. [VNet 用の VPN ゲートウェイの作成](#create-a-vpn-gateway)
2. [VPN デバイスの構成に関する情報を収集します。](#gather-information-for-your-vpn-device-configuration)
3. [VPN デバイスの構成](#configure-your-vpn-device)
4. [ローカル ネットワークの範囲と VPN ゲートウェイの IP アドレスを確認します。](#verify-your-local-network-ranges-and-vpn-gateway-ip-address)

### <a name="before-you-begin"></a>開始する前に
ゲートウェイを構成する前に、仮想ネットワークを作成する必要があります。 クロスプレミス接続用の仮想ネットワークを作成する手順については、「[サイト間 VPN 接続を使用したVirtual Network の構成](vpn-gateway-site-to-site-create.md)」または「[Azure Virtual Network へのポイント対サイト VPN 接続の構成](vpn-gateway-point-to-site-create.md)」をご覧ください。 次に、下記の手順で VPN ゲートウェイを構成し、VPN デバイスの構成に必要な情報を収集します。 

既に構成されている VPN ゲートウェイの VPN ルーティングの種類を変更する場合は、「 [ゲートウェイの VPN ルーティングの種類を変更する方法](#how-to-change-the-vpn-routing-type-for-your-gateway)」をご覧ください。

## <a name="create-a-vpn-gateway"></a>VPN ゲートウェイの作成
1. [Azure クラシック ポータル](https://manage.windowsazure.com)での **[ネットワーク]** ページで、仮想ネットワークの状態列が **[作成済み]** であることを確認します。
2. **[名前]** 列で、仮想ネットワークの名前をクリックします。
3. **[ダッシュボード]** ページを見ると、この VNet に対してゲートウェイが構成されていないことがわかります。 このステータスは、ゲートウェイの構成手順を進めると変化します。

![ゲートウェイが未作成](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)

次に、ページ下部にある **[ゲートウェイの作成]**をクリックします。 *静的ルーティング*と*動的ルーティング*のいずれかを選択できます。 VPN ルーティングの種類は、いくつかの要因を考慮して選択する必要があります。 たとえば、どの VPN デバイスがポイント対サイト接続をサポートしているか、およびポイント対サイト接続をサポートする必要があるかなどを検討します。 必要な VPN ルーティングの種類については、「 [Virtual Network 接続の VPN デバイスについて](vpn-gateway-about-vpn-devices.md) 」を参照してください。 ゲートウェイを作成した後は、ゲートウェイを削除して再作成しないかぎりゲートウェイ VPN ルーティングの種類を変更することはできません。 ゲートウェイを作成するかどうかを確認するメッセージが表示されたら、 **[はい]**をクリックします。

![ゲートウェイ VPN ルーティングの種類](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

ゲートウェイの作成中は、ページに表示されているゲートウェイの図が黄色に変わり、「 *ゲートウェイを作成しています*」と表示されます。 ゲートウェイの作成には最大で 45 分かかる場合があります。 ゲートウェイの作成が完了するまで待ってから他の構成設定に進みます。

![ゲートウェイの作成](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

ゲートウェイのステータスが *接続中*になると 、VPN デバイスで必要な情報を収集できます。

![ゲートウェイの接続](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## <a name="gather-information-for-your-vpn-device-configuration"></a>VPN デバイスの構成に関する情報を収集します。
ゲートウェイを作成したら、VPN デバイスの構成情報を収集します。 この情報は、仮想ネットワークのページ の **[ダッシュ ボード]** ページで確認できます。

1. **ゲートウェイの IP アドレス -** IP アドレスは**[ダッシュ ボード]** ページで確認できます。 これは、ゲートウェイの作成が完了するまでは確認できません。
2. **共有キー -** 画面下部の **[キーの管理]** をクリックします。 次に、キーの隣のアイコンをクリックしてクリップボードにコピーし、キーを貼り付けてから保存します。 このボタンは、単一の S2S VPN トンネルに対してのみ有効です。 複数の S2S VPN トンネルが存在する場合は、 *Get Virtual Network Gateway Shared Key* という API または PowerShell コマンドレットを使用します。

![[キーの管理]](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)

## <a name="configure-your-vpn-device"></a>VPN デバイスの構成
ここまでの手順が完了したら、ネットワーク管理者は VPN デバイスを構成して接続を作成する必要があります。 VPN デバイスの詳細については、「 [Virtual Network 接続の VPN デバイスについて](vpn-gateway-about-vpn-devices.md) 」を参照してください。

VPN デバイスの構成完了後は、更新された接続情報を VNet のダッシュ ボード ページで表示できます。

接続のテストは、次のコマンドのいずれかで実行することもできます。

|  | Cisco ASA | Cisco ISR/ASR | Juniper SSG/ISG | Juniper SRX/J |
| --- | --- | --- | --- | --- |
| **メイン モード SA の確認** |show crypto isakmp sa |show crypto isakmp sa |get ike cookie |show security ike security-association |
| **クイック モード SA の確認** |show crypto ipsec sa |show crypto ipsec sa |get sa |show security ipsec security-association |

## <a name="verify-your-local-network-ranges-and-vpn-gateway-ip-address"></a>ローカル ネットワークの範囲と VPN ゲートウェイの IP アドレスを確認します。
### <a name="verify-your-vpn-gateway-ip-address"></a>VPN ゲートウェイ の IP アドレスの確認
ゲートウェイを正常に接続するには、クロスプレミス構成で使用するように指定したローカル ネットワークで VPN デバイスの IP アドレスを正しく構成する必要があります。 通常、この構成はサイト間構成プロセスの実行中に行われます。 ただし、以前このローカル ネットワークで別のデバイスを使用していた場合、またはこのローカル ネットワークで使用していた IP アドレスを変更した場合は、ゲートウェイの IP アドレスを編集して正しい値を指定してください。

1. ゲートウェイ IP アドレスを確認するには、左側のポータル ウィンドウで **[ネットワーク]** をクリックして、ページ上部の **[ローカル ネットワーク]** を選択します。 各ローカル ネットワークに存在する作成済みの VPN Gateway のアドレスが表示されます。 IP アドレスを編集するには、VNet を選択してページ下部の **[編集]** をクリックします。
2. **[ローカル ネットワークの詳細を指定]** ページで IP アドレスを編集し、ページ下部にある矢印をクリックして次に進みます。
3. **[アドレス空間を指定]** ページで、右下のチェックマークをクリックして設定を保存します。

### <a name="verify-the-address-ranges-for-your-local-networks"></a>ローカル ネットワークのアドレス範囲の確認
トラフィックがゲートウェイを通過してオンプレミスの場所へと正しく流れるように、それぞれの IP アドレス範囲が指定されていることを確認する必要があります。 各範囲が Azure **ローカル ネットワーク** 構成のリストに含まれている必要があります。 オンプレミスの場所のネットワーク構成によっては、この作業にはかなりの手間がかかる場合があります。 リストされた範囲内に含まれる IP アドレスに向けたトラフィックは、仮想ネットワークの VPN ゲートウェイを通過して送信されます。 オンプレミスの構成で受信トラフィックを受信できることを確認するテストを実施する場合がありますが、リストに含まれる範囲は必ずしもプライベート アドレスの範囲である必要はありません。

ローカル ネットワークの範囲を追加または編集するには、次の手順を実行します。

1. ローカル ネットワークの IP アドレス範囲を編集するには、左側のポータル ウィンドウで **[ネットワーク]** をクリックして、ページ上部の **[ローカル ネットワーク]** を選択します。 ポータルの **[編集]** ページでリストを表示すると、簡単にアドレス範囲を確認できます。 アドレス範囲を表示するには、VNet を選択し、ページ下部の **[編集]** をクリックします。
2. **[ローカル ネットワークの詳細を指定する]** ページでは何も変更しません。 ページ下部にある矢印をクリックして次に進みます。
3. **[アドレス空間を指定します]** ページでネットワーク アドレス空間を変更します。 その後、チェック マークをクリックして構成を保存します。

## <a name="how-to-view-gateway-traffic"></a>ゲートウェイ トラフィックを表示する方法
ゲートウェイおよびゲートウェイ トラフィックは、Virtual Network の **[ダッシュ ボード]** ページで表示できます。

**[ダッシュ ボード]** ページでは次の内容を確認できます。

* ゲートウェイを通過するデータ量。送受信の両方向の合計量が表示されます。
* 仮想ネットワークで指定されている DNS サーバーの名前。
* ゲートウェイと VPN デバイスの間の接続。
* VPN デバイスへのゲートウェイ接続の構成に使用されている共有キー。

## <a name="how-to-change-the-vpn-routing-type-for-your-gateway"></a>ゲートウェイの VPN ルーティングの種類を変更する方法
接続の構成によっては特定の種類のゲートウェイ ルーティングのみが使用可能であるため、既存の VPN ゲートウェイの VPN ルーティングの種類を変更することが必要になる場合があります。 これには、静的ゲートウェイが存在する既存のサイト間接続に、ポイント対サイト接続を追加する場合などがあります。 ポイント対サイト接続では、動的ゲートウェイが必要です。 これは、P2S 接続を構成することを意味し、ゲートウェイの VPN ルーティングの種類を静的から動的に変更する必要があります。

ゲートウェイの VPN ルーティングの種類を変更する必要がある場合、既存のゲートウェイを削除してから、新しいルーティングの種類で新しいゲートウェイを作成します。 ゲートウェイのルーティングの種類を変更するときには、仮想ネットワーク全体を削除する必要はありません。

ゲートウェイ VPN ルーティングの種類を変更する前に、使用する VPN デバイスが変更後のルーティングの種類をサポートしていることを確認します。 「 [Virtual Network 接続の VPN デバイスについて](vpn-gateway-about-vpn-devices.md)」のページを参照すると、新しいルーティング構成のサンプルのダウンロード、および VPN デバイスの要件の確認ができます。

> [!IMPORTANT]
> 仮想ネットワークの VPN ゲートウェイを削除すると、ゲートウェイに割り当てられた VIP は解放されます。 ゲートウェイを再作成するときには新しい VIP が割り当てられます。
> 
> 

1. **既存の VPN ゲートウェイを削除します。**
   
    仮想ネットワークの **[ダッシュ ボード]** ページ下部にある **[ゲートウェイの削除]** をクリックします。 ゲートウェイの削除が完了したことを示す通知を受信するまで待機します。 ゲートウェイが削除されたことを示す通知が表示されたら、新しいゲートウェイを作成できます。
2. **新しい VPN ゲートウェイを作成します。**
   
    このページ上部の「 [VPN ゲートウェイの作成](#create-a-vpn-gateway)」の手順に従って新しいゲートウェイを作成します。

## <a name="next-steps"></a>次のステップ
仮想ネットワークに仮想マシンを追加できます。 「 [カスタム仮想マシンを作成する方法](../virtual-machines/virtual-machines-windows-classic-createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。

ポイント対サイト VPN 接続を構成する場合は、 [ポイント対サイト VPN 接続の構成](vpn-gateway-point-to-site-create.md)に関するページを参照してください。




<!--HONumber=Nov16_HO3-->


