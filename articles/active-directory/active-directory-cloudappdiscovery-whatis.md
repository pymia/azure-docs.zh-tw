---
title: "使用 Cloud App Discovery 尋找未受管理的雲端應用程式 | Microsoft Docs"
description: "提供有關使用 Cloud App Discovery 尋找和管理應用程式、它的優勢和運作方式的資訊。"
services: active-directory
keywords: "雲端應用程式探索, 管理應用程式"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 011cdf5f1e1b78832a8e4f18f4eef0f376860c45


---
# <a name="finding-unmanaged-cloud-applications-with-cloud-app-discovery"></a>使用 Cloud App Discovery 尋找未受管理的雲端應用程式
## <a name="overview"></a>概觀
在現代企業中，IT 部門通常不會知道組織的成員執行其工作所使用的所有雲端應用程式。 很容易知道為什麼系統管理員必須對未經授權存取公司資料、可能的資料外洩和其他安全性風險有所顧慮。 缺乏認知可能使得要建立一個可應付這些安全性風險的計劃讓人卻步。

Cloud App Discovery (AD) Premium 是 Azure Active Directory 的一個功能，可讓您探索您組織中的人員所使用的雲端應用程式。

**使用 Cloud App Discovery，您可以：**

* 尋找使用的雲端應用程式，並依使用者數目、流量或應用程式的 Web 要求數目測量使用量。
* 識別正在使用應用程式的使用者。
* 匯出資料以進行離線分析。
* 讓這些應用程式在 IT 的控制下並為使用者管理啟用單一登入。

## <a name="how-it-works"></a>運作方式
1. 應用程式使用代理程式會安裝在使用者的電腦上。
2. 代理程式所擷取的應用程式使用情況資訊會透過安全的加密通道，傳送到 Cloud App Discovery 服務。
3. Cloud App Discovery 服務會評估資料並產生報告。

![Cloud App Discovery 圖表](./media/active-directory-cloudappdiscovery/cad01.png)

若要開始使用 Cloud App Discovery，請參閱 [開始使用 Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)

## <a name="related-articles"></a>相關文章
* [Cloud App Discovery 的安全性和隱私權考量](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Cloud App Discovery 群組原則部署指南](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)
* [Cloud App Discovery System Center 部署指南](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)
* [具有自訂連接埠的 Proxy 伺服器的 Cloud App Discovery 登錄設定](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Cloud App Discovery 代理程式變更記錄 ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Cloud App Discovery 常見問題集](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx)
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)




<!--HONumber=Dec16_HO5-->


