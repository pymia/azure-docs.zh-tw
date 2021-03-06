---
title: "Logic Apps 內部部署資料閘道連接 | Microsoft Docs"
description: "如何從邏輯應用程式建立內部部署資料閘道連接的相關資訊。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: fc61aa855fbcdb8f3ee90f64cbfc73f070f4bbfe
ms.openlocfilehash: f0d079fd13be7fb2b10d1a3556046f93d59b640c


---
# <a name="connect-to-the-on-premises-data-gateway-for-logic-apps"></a>連接至 Logic Apps 的內部部署資料閘道
支援的邏輯應用程式連接器可讓您設定連接，透過內部部署資料閘道存取內部部署資料。  下列步驟將引導您了解如何安裝和設定內部部署資料閘道，以搭配邏輯應用程式使用。

## <a name="prerequisites"></a>必要條件
  * 必須在 Azure 中使用工作或學校電子郵件地址將內部部署資料閘道與您的帳戶 (Azure Active Directory 帳戶) 相關聯
  * 如果您使用 Microsoft 帳戶 (例如 @outlook.com, @live.com))，可以[依照這裡的步驟](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)，使用您的 Azure 帳戶建立一個工作或學校的電子郵件地址 
  * 必須 [在本機電腦上安裝](app-service-logic-gateway-install.md)內部部署資料閘道。
  * 閘道必須未被另一個 Azure 內部部署資料閘道宣告 ([宣告會在下面建立步驟 2 時進行](#2-create-an-azure-on-premises-data-gateway-resource)) - 一個安裝只能與一個閘道資源相關聯。

## <a name="installing-and-configuring-the-connection"></a>安裝和設定連接
### <a name="1-install-the-on-premises-data-gateway"></a>1.安裝內部部署資料閘道
安裝內部部署資料閘道的資訊可參閱 [本文](app-service-logic-gateway-install.md)。  必須先在內部部署機器上安裝閘道，才能繼續執行其餘的步驟。

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2.建立 Azure 內部部署資料閘道資源
安裝之後，您必須將您的 Azure 訂用帳戶與內部部署資料閘道相關聯。

1. 使用安裝閘道期間所使用的工作或學校電子郵件地址登入 Azure
2. 按一下 [新增]  資源按鈕
3. 搜尋並選取 [內部部署資料閘道] 
4. 完成資訊，將閘道與您的帳戶相關聯 - 包括選取適當的 [安裝名稱] 
   
    ![內部部署資料閘道連接][1]
5. 按一下 [建立]  按鈕以建立資源

### <a name="3-create-a-logic-app-connection-in-the-designer"></a>3.在設計工具中建立邏輯應用程式連接
現在，您的 Azure 訂用帳戶已經與內部部署資料閘道的執行個體相關聯，您可以從邏輯應用程式內建立連接。

1. 開啟邏輯應用程式，然後選擇支援內部部署連接的連接器 (撰寫本文時為 SQL Server)
2. 選取 [透過內部部署資料閘道連接] 
   
    ![邏輯應用程式設計工具閘道建立][2]
3. 選取 [閘道]  來連接並完成所需的任何其他連接資訊
4. 按一下 [建立]  來建立連接

連接現在應該已成功設定，可在邏輯應用程式中使用。  

## <a name="next-steps"></a>後續步驟
* [Logic Apps 範例和常見案例](app-service-logic-examples-and-scenarios.md)
* [企業整合功能](app-service-logic-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/app-service-logic-gateway-connection/createblade.PNG
[2]: ./media/app-service-logic-gateway-connection/blankconnection.PNG
[3]: ./media/app-service-logic-gateway-connection/checkbox.PNG



<!--HONumber=Jan17_HO1-->


