---
title: "如何建立 App Service 環境"
description: "App Service 的建立流程說明"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2016
ms.author: ccompy
translationtype: Human Translation
ms.sourcegitcommit: af98ae3b425e2e0584da53721249dbcd605a17d1
ms.openlocfilehash: 8d8e1ce7758691a392fdb6d6459e04c6b4f7f5f6


---
# <a name="how-to-create-an-app-service-environment"></a>如何建立 App Service Environment
### <a name="overview"></a>Overview
App Service 環境 (ASE) 是 Azure App Service 的進階服務選項，可提供多租用戶戳記中不提供的增強式設定功能。  ASE 功能基本上會將 Azure App Service 部署到客戶的虛擬網路中。  若要更深入了解 App Service Environment 所提供的功能，請閱讀[什麼是 App Service Environment][WhatisASE] 文件。

### <a name="before-you-create-your-ase"></a>建立 ASE 之前
務必注意您無法變更的項目。  建立後，您無法變更 ASE 的相關層面是：

* 位置
* 訂閱
* 資源群組
* 使用的 VNet
* 使用的子網路 
* 子網路大小

挑選 VNet 然後指定子網路時，請確定它足夠大以容納未來的成長。  

### <a name="creating-an-app-service-environment"></a>建立 App Service 環境
存取 ASE 建立 UI 的方法有兩種。  在 Azure Marketplace 中搜尋 ***App Service 環境***或經由 [新增] -> [Web + 行動] -> [App Service 環境]，即可找到。  若要建立 ASE：

1. 提供 ASE 的名稱。  針對 ASE 指定的名稱將用於在 ASE 中建立的應用程式。  如果 ASE 的名稱是 appsvcenvdemo，則子網域名稱會是 .appsvcenvdemo.p.azurewebsites.net。  如果您因此建立名為 *mytestapp* 的應用程式，則可定址於 *mytestapp.appsvcenvdemo.p.azurewebsites.net*。  您無法在 ASE 的名稱中使用空白字元。  如果您在名稱中使用大寫字元，則網域名稱會是該名稱的全小寫版本。  如果您使用 ILB，則 ASE 名稱不會用於您的子網域中，但是會在 ASE 建立期間明確指定
   
    ![][1]
2. 選取您的訂用帳戶。  用於 ASE 的訂用帳戶也是將用來在該 ASE 中建立所有應用程式的帳戶。  您無法將 ASE 放在另一個訂用帳戶中的 VNet
3. 選取或指定新的資源群組。  用於 ASE 的資源群組必須是與用於您的 VNet 的相同。  如果您選取既有的 VNet，則您的 ASE 資源群組選取項目將會更新，以反映 VNet 的資源群組。
   
    ![][2]
4. 請選取您的虛擬網路及位置選項。  您可以選擇建立新的 VNet，或選取既有的 VNet。  如果您選取新的 VNet，則可以指定名稱和位置。 新的 VNet 會有位址範圍 192.168.250.0/23，和定義為 192.168.250.0/24 名為 **default** 的子網路。  您也可以僅選取既有的傳統或 Resource Manager VNet。  [VIP 類型] 選項會決定您的 ASE 是否可以從網際網路 (外部) 直接存取，或者它使用內部負載平衡器 (ILB)。  若要深入了解，請參閱[在 App Service 環境中使用內部負載平衡器][ILBASE]。  如果您選取 [外部] VIP 類型，則可以選取系統針對 IPSSL 用途會建立幾個外部 IP 位址。  如果您選取 [內部]，則需要指定 ASE 將使用的子網域。  ASE 可以部署到使用公用位址範圍「或」RFC1918 位址空間 (也就是 私人位址) 的虛擬網路。  若要搭配使用虛擬網路與公用位址範圍，您必須事先建立 VNet。  選取既有的 VNet 時，您必須在 ASE 建立期間建立新的子網路。  **您無法在入口網站中使用預先建立的子網路。如果您使用 Resource Manager 範本建立 ASE，則可以使用既有的子網路建立 ASE。**  若要使用此處的資訊透過範本建立 ASE，[從範本建立 App Service 環境][ILBAseTemplate]，以及這裡[從範本建立 ILB App Service 環境][ASEfromTemplate]。

### <a name="details"></a>詳細資料
一個 ASE 是使用 2 個前端和 2 個背景工作角色建立。  前端可做為 HTTP/HTTPS 端點，將流量傳送到背景工作角色 (也就是裝載您的應用程式的角色)。   建立 ASE 之後，您可以調整數量，且甚至可以在這些資源集區上設定自動調整規則。  如需更多關於手動調整、管理及監視 App Service 環境的詳細資料，請前往此處：[如何設定 App Service 環境][ASEConfig] 

只有一個 ASE 可以存在於 ASE 所使用的子網路中。  子網路無法用於 ASE 以外的任何項目

### <a name="after-app-service-environment-creation"></a>在 App Service 環境建立之後
建立 ASE 之後，您可以調整：

* 前端的數量 (最小值：2)
* 背景工作的數量 (最小值：2)
* IP SSL 可用的 IP 位址數目
* 前端或背景工作所使用的計算資源大小 (前端大小下限為 P2)

以下有更多關於手動調整、管理及監視 App Service 環境的詳細資料：[如何設定 App Service 環境][ASEConfig] 

如需自動調整的相關資訊，請參閱：[如何設定 App Service 環境的自動調整][ASEAutoscale]

有其他無法自訂的相依性，例如資料庫和儲存體。  這些都是由 Azure 處理並由系統隨附。  系統儲存體對於整個 App Service 環境最多可支援 500 GB，且 Azure 會根據系統規模的需要來調整資料庫。

## <a name="getting-started"></a>開始使用
您可以在 [應用程式服務環境的讀我檔案](../app-service/app-service-app-service-environments-readme.md)中取得 App Service 環境的所有相關文章與做法。

若要開始使用 App Service Environment，請參閱 [App Service Environment 簡介][WhatisASE]

如需有關 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service][AzureAppService]。

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
[ILBAseTemplate]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-create-ilb-ase-resourcemanager/



<!--HONumber=Dec16_HO3-->


