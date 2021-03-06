---
title: "規劃 Service Fabric 叢集容量 | Microsoft Docs"
description: "Service Fabric 叢集容量規劃考量。 Nodetypes、持久性和可靠性層級"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: a7b05db6af721a62b7cbb795c329b383bd391e0a
ms.openlocfilehash: fbb3933ef2dff882467fb2db34f9aa14dec13b1f


---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Service Fabric 叢集容量規劃考量
對於任何生產部署而言，容量規劃都是一個很重要的步驟。 以下是一些您在該程序中必須考量的項目。

* 您的叢集一開始所需的節點類型的數目
* 每個節點類型 (大小、主要、網際網路對向、VM 數目等) 的屬性
* 叢集的可靠性和持久性的特性

讓我們簡短地檢閱各個項目。

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>您的叢集一開始所需的節點類型的數目
首先，您必須了解您要建立的叢集將用於什麼用途，以及您要規劃哪些要部署到此叢集中的應用程式種類。 如果您不清楚叢集的用途，您很可能還未準備好進入容量規劃程序。

建立您的叢集一開始所需的節點類型的數目。  每個節點類型都會對應到虛擬機器調整集。 然後每個節點類型可以獨立相應增加或相應減少，可以開啟不同組的連接埠，並可以有不同的容量度量。 因此，節點類型數目的決定基本上可歸結為下列考量︰

* 您的應用程式是否有多個服務，而且其中是否有任何服務必須是公開或網際網路對向的服務？ 一般應用程式包含可接收用戶端輸入的前端閘道服務，以及一或多個與前端服務溝通的後端服務。 因此，在此情況下，您最終會有至少兩個節點類型。
* 您 (構成應用程式) 的服務是否有不同的基礎結構需求，例如，更多的 RAM 或更高的 CPU 週期？ 例如，讓我們假設您想要部署的應用程式包含前端服務和後端服務。 前端服務可以在容量較小 (D2 之類的 VM 大小)，且擁有可連線至網際網路之連接埠的 VM 上執行。  不過，需要大量計算的後端服務必須在容量較大 (D4、D6、D15 的 VM 大小)，且不連線至網際網路的 VM 上執行。
  
  在此範例中，您可以決定將所有服務都放在一個節點類型上，但我們建議您將它們放在包含兩個節點類型的叢集上。  這可讓每個節點類型都有不同的屬性，例如，網際網路連線或 VM 大小。 VM 的數目也可以單獨調整。  
* 您無法預測未來，因此請利用您所知道的事實，決定您的應用程式一開始所需的節點類型的數目。 您之後都可以新增或移除節點類型。 Service Fabric 叢集必須至少有一個節點類型。

## <a name="the-properties-of-each-node-type"></a>每個節點類型的屬性
**節點類型**就像是雲端服務中的角色。 可用來定義定義 VM 的大小、VM 的數目，以及 VM 的屬性。 在 Service Fabric 叢集中定義的每個節點類型都會安裝為不同的虛擬機器擴展集。 VM 調整集是一種 Azure 計算資源，可以用來將一組虛擬機器當做一個集合加以部署和管理。 如果定義為不同的 VM 調整集，則每個節點類型都可以獨立相應增加或相應減少、可以開放不同組的連接埠，而且可以有不同的容量度量。

您的叢集可以多個節點類型，但主要節點類型 (您在入口網站定義的第一個節點類型) 必須至少有 5 個 VM 供叢集用於生產工作負載 (或至少有 3 個 VM 供測試叢集使用)。 如果您要使用 Resource Manager 範本建立叢集，則會在節點類型定義下找到 **is Primary** 屬性。 主要節點類型就是放置 Service Fabric 系統服務所在的節點類型。  

### <a name="primary-node-type"></a>主要節點類型
若是包含多個節點類型的叢集，您必須選擇其中一個做為主要節點類型。 以下是主要節點類型的特性︰

* 主要節點類型的 VM 大小下限取決於您選擇的持久性層級。 持久性層級的預設值為 Bronze。 如需有關持久性層級的定義以及可採用的值，請向下捲動。  
* 主要節點類型的 VM 數目下限取決於您選擇的可靠性層級。 可靠性層級的預設值為 Silver。 如需有關可靠性層級的定義以及可採用的值，請向下捲動。
* Service Fabric 系統服務 (例如，叢集管理員服務或映像存放區服務) 會放在主要節點類型上，因此，叢集的可靠性和持久性取決於您為主要節點類型所選取的可靠性層級值與持久性層級值。

![有兩個節點類型的叢集螢幕擷取畫面 ][SystemServices]

### <a name="non-primary-node-type"></a>非主要節點類型
如果是包含多個節點類型的叢集，則會有一個主要節點類型，其餘則是非主要節點類型。 以下是非主要節點類型的特性︰

* 此節點類型的 VM 大小下限取決於您選擇的持久性層級。 持久性層級的預設值為 Bronze。 如需有關持久性層級的定義以及可採用的值，請向下捲動。  
* 此節點類型的 VM 數目下限可以是 1。 不過，您應該根據您想要在這個節點類型中執行的應用程式/服務的複本數目，選擇這個數目。 部署叢集之後，節點類型中的 VM 數目可能會增加。

## <a name="the-durability-characteristics-of-the-cluster"></a>叢集的持久性特性
持久性層級用來向系統指示您的 VM 對於基本 Azure 基礎結構所擁有的權限。 在主要節點類型中，此權限可讓 Service Fabric 暫停會影響系統服務及具狀態服務的仲裁需求的任何 VM 層級基礎結構要求 (例如，VM 重新開機、VM 重新安裝映像，或 VM 移轉)。 在非主要節點類型中，此權限可讓 Service Fabric 暫停會影響其中所執行之具狀態服務的仲裁需求的任何 VM 層級基礎結構要求，例如，VM 重新開機、VM 重新安裝映像、VM 移轉等等。

此權限會以下列值表示︰

* Gold - 每個 UD 可持續暫停基礎結構工作 2 小時
* Silver - 每個 UD 可持續暫停基礎結構工作 30 分鐘 (此值目前未啟用。 啟用之後，就可在單一核心以上的所有標準 VM 上使用)。
* Bronze - 無權限。 這是預設值。

## <a name="the-reliability-characteristics-of-the-cluster"></a>叢集的可靠性特性
可靠性層級用來設定您想要在此叢集中的主要節點類型上執行的系統服務複本數目。 複本數目越多，叢集中的系統服務越可靠。  

可靠性層級可以採用以下的值。

* Platinum - 執行包含 9 個目標複本集的系統服務
* Gold - 執行包含 7 個目標複本集的系統服務
* Silver - 執行包含 5 個目標複本集的系統服務
* Bronze - 執行包含 3 個目標複本集的系統服務

> [!NOTE]
> 您選擇的可靠性層級會決定您的主要節點類型必須具備的節點數目下限。 可靠性層級與叢集大小上限沒有關係。 因此，您可以有 20 個節點叢集在 Bronze 可靠性層級執行。
> 
> 

 您可以選擇將叢集的可靠性從一個層級更新為另一個層級。 如此一來就會觸發變更系統服務複本集計數所需的叢集升級。 請先等候升級完成，再對叢集進行任何其他變更，例如新增節點等等。您可以在 Service Fabric Explorer 上或執行 [Get-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt126012.aspx) 監視升級的進度

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟
一旦您完成容量規劃並設定叢集之後，請閱讀︰

* [Service Fabric 叢集安全性](service-fabric-cluster-security.md)
* [Service Fabric 健康情況模型簡介](service-fabric-health-introduction.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png



<!--HONumber=Jan17_HO2-->


