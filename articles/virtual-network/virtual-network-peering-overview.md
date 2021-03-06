---
title: "Azure 虛擬網路對等互連 | Microsoft Docs"
description: "了解 Azure 中的 VNet 對等互連。"
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: narayan
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 0d4d13d44581f98ead7d65f3bb819e54b93a76b6


---
# <a name="vnet-peering"></a>VNet 對等互連
VNet 對等互連是透過 Azure 骨幹網路來連接同一區域中兩個虛擬網路 (VNet) 的機制。 一旦對等互連，針對用作連線而言兩個虛擬網路看起來就像一個。 它們仍做為不同的資源進行管理，但這些虛擬網路中的虛擬機器可以使用私人 IP 位址彼此直接通訊。

在已對等互連虛擬網路中虛擬機器之間的流量會透過 Azure 基礎結構路由傳送，如同在同一虛擬網路中 VM 之間路由傳送的流量。 使用 VNet 對等互連的一些優點包括︰

* 不同虛擬網路的資源之間具有低延遲、高頻寬連線。
* 可以在已對等互連的 VNet 中將網路應用裝置和 VPN 閘道等資源當做傳輸點。
* 可以將使用 Azure Resource Manager 模型的虛擬網路連接到使用傳統部署模型的虛擬網路，並在這些虛擬網路的資源之間啟用完整連線能力。

VNet 對等互連的需求和重要層面︰

* 已對等互連的兩個虛擬網路應位於相同的 Azure 區域。
* 已對等互連的虛擬網路應該要有非重疊的 IP 位址空間。
* VNet 對等互連的目標是兩個虛擬網路，且之間沒有任何衍生的可轉移關聯性。 例如，如果虛擬網路 A 與虛擬網路 B 對等互連，同時虛擬網路 B 與虛擬網路 C 對等互連，不代表虛擬網路 A 與虛擬網路 C 對等互連。
* 只要兩個訂用帳戶之間有一個具有權限的使用者授權對等互連，就可以建立兩個不同訂用帳戶虛擬網路之間的對等互連，而訂用帳戶會與相同的 Azure Active Directory 租用戶產生關聯。 
* 資源管理員模型中的虛擬網路和傳統部署模型之間的對等互連需要 VNet 應在相同的訂用帳戶中。
* 使用 Resource Manager 部署模型的虛擬網路可以與另一個使用此模型的虛擬網路對等互連，或與使用傳統部署模型的虛擬網路對等互連。 然而，使用傳統部署模型的虛擬網路無法彼此對等互連。
* 雖然在已對等互連虛擬網路中虛擬機器之間的通訊沒有其他頻寬限制，但仍然會有基於 VM 大小的頻寬上限。

![基本 VNet 對等互連](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>連線能力
兩個虛擬網路對等互連後，虛擬網路中的虛擬機器 (Web 角色/背景工作角色) 可以直接與已對等互連虛擬網路中的其他虛擬機器連線。 這兩個網路具有完整的 IP 層級連線能力。

在對等互連虛擬網路中，兩部虛擬機器之間的來回網路延遲與在本機虛擬網路中的來回網路延遲相同。 網路輸送量為依照虛擬機器大小，按比例允許的頻寬。 對於頻寬則沒有其他額外限制。

已對等互連虛擬網路中虛擬機器之間的流量會透過 Azure 後端基礎結構直接路由傳送，而不是透過閘道。

在已對等互連的虛擬網路中，虛擬網路中的虛擬機器可存取內部負載平衡 (ILB) 端點。 如有需要，可以將網路安全性群組 (NSG) 套用在任一個虛擬網路，以封鎖其他虛擬網路或子網路的存取權限。

當使用者設定對等互連時，他們可以開啟或關閉虛擬網路之間的 NSG 規則。 如果使用者選擇開啟對等互連虛擬網路 (預設選項) 之間完整的連線，則使用者接著可以在特定子網路或虛擬機器上使用 NSG 以封鎖或拒絕特定的存取。

Azure 提供的虛擬機器內部 DNS 名稱解析無法在對等互連虛擬網路中運作。 虛擬機器必須具有只在本機虛擬網路內可解析的內部 DNS 名稱。 不過，使用者可以將在對等互連虛擬網路中執行的虛擬機器設定為虛擬網路的 DNS 伺服器。

## <a name="service-chaining"></a>服務鏈結
使用者可以設定使用者定義的路由表，將指向對等互連虛擬網路中的虛擬機器當做「下一個躍點」IP 位址，如本文稍後的圖表所示。 這可讓使用者達到服務鏈結，他們可以透過使用者定義路徑資料表將流量從一個虛擬網路導向在對等互連虛擬網路中執行的虛擬應用裝置。

使用者也可以有效地建立中樞和輪輻類型的環境，讓中樞得以裝載網路虛擬應用裝置等基礎結構元件。 所有輪輻虛擬網路都能與其對等互連，也能與在中樞虛擬網路中執行之應用裝置的流量子集對等互量。 簡單來說，VNet 對等互連可讓在「使用者定義路由表」上的下一個躍點 IP 位址等於對等互連虛擬網路中虛擬機器的位址。

## <a name="gateways-and-on-premises-connectivity"></a>閘道及內部部署連線能力
不論每個虛擬網路是否與其他虛擬網路對等互連，它們仍可以擁有自己的閘道並使用它來連線至內部部署。 即使虛擬網路已對等互連，使用者也可以使用閘道來設定 [VNet 對 VNet 連線](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) 。

當針對虛擬網路內部連線的兩個選項已設定時，虛擬網路之間的流量將會透過對等互連設定流動 (也就是透過 Azure 骨幹)。

當虛擬網路已對等互連時，使用者也可以將對等互連虛擬網路中的閘道設定為內部部署傳輸點。 在此情況下，使用遠端閘道的虛擬網路不能擁有專屬閘道。 每個虛擬網路只能擁有一個閘道。 它可以是本機閘道或遠端閘道 (在已對等互連的虛擬網路中)，如下圖所示。

使用 Resource Manager 模式之虛擬網路與使用傳統部署模型之虛擬網路間的對等互連關聯性不支援閘道傳輸。 對等互連關聯性中的這兩個虛擬網路都需要使用 Resource Manager 部署模型，閘道傳輸才能正常運作。

當共用單一 Azure ExpressRoute 連線的虛擬網路已對等互連時，它們之間的流量會經過對等互連關聯性 (也就是透過 Azure 骨幹網路)。 使用者依然可以在每個虛擬網路中使用本機閘道來連接內部部署線路。 他們也可以使用共用閘道，並設定內部部署連線的傳輸。

![VNet 對等互連傳輸](./media/virtual-networks-peering-overview/figure02.png)

## <a name="provisioning"></a>佈建
VNet 對等互連是需要權限的作業。 它是 VirtualNetworks 命名空間下的個別功能。 使用者可以取得特定權限以授權對等互連。 具有虛擬網路讀寫權限的使用者會自動繼承這些權限。

身為系統管理員或具有對等互連功能權限的使用者可以啟動對另一個 VNet 的對等互連作業。 如果另一端有對等互連的相符要求而且其他需求也符合，就會建立對等互連。

請參閱「後續步驟」一節中的文章，進一步了解如何建立兩個虛擬網路之間的 VNet 對等互連。

## <a name="limits"></a>限制
單一虛擬網路允許的對等互連數目有其限制。 如需詳細資訊，請參閱 [Azure 網路限制](../azure-subscription-service-limits.md#networking-limits) 。

## <a name="pricing"></a>價格
VNet 對等互連在審查期間不需支付費用。 發行後，我們會針對使用對等互連的輸入和輸出流量會收取少許費用。 如需詳細資訊，請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/virtual-network)。

## <a name="next-steps"></a>後續步驟
* [設定虛擬網路之間的對等互連](virtual-networks-create-vnetpeering-arm-portal.md)。
* 了解 [NSG](virtual-networks-nsg.md)。
* 了解 [使用者定義的路由和 IP 轉送](virtual-networks-udr-overview.md)。




<!--HONumber=Dec16_HO2-->


