---
title: "Linux 上的 App Service 簡介 | Microsoft Docs"
description: "了解 Linux 上的 App Service。"
keywords: azure app service, linux, oss
services: app-service
documentationcenter: 
author: naziml
manager: wpickett
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: naziml
translationtype: Human Translation
ms.sourcegitcommit: a3df293a056f4b06281bf8b80717529286d528b8
ms.openlocfilehash: 48f26113d129ebfa613f1b8f2a372d0e7286aea1


---
# <a name="introduction-to-app-service-on-linux"></a>Linux 上的 App Service 簡介
Linux 上的 Azure App Service 目前處於公開預覽狀態，支援在 Linux 上以原生方式執行 Web 應用程式。

## <a name="overview"></a>Overview
針對支援的應用程式堆疊，客戶可以使用 Linux 上的 App Service，以原生方式將 Web 應用程式裝載於 Linux。 下節列出目前支援的應用程式堆疊。 

## <a name="features"></a>特性
Linux 上的 App Service 目前支援下列應用程式堆疊︰

* Node.js
* PHP
* .Net Core

客戶可以使用下列工具來部署應用程式︰

* FTP
* 本機 Git
* GitHub 或 Bitbucket

調整應用程式大小︰

* 客戶可以在 App Service 方案中變更階層，即可相應增加和減少 Web 應用程式的規模。
* 客戶可以將應用程式相應放大，在其 SKU 的範圍內跨多個執行個體來執行應用程式。

針對 Kudu，有一些基本功能可用於下列幾項︰

* 環境
* 部署
* 基本主控台

## <a name="limitations"></a>限制
Azure 入口網站只會顯示 Linux 上的 App Service 目前可用的功能，並隱藏其餘部分。 隨著我們啟用更多功能，它們將會反映在入口網站上。

某些功能目前沒有作用，例如虛擬網路整合、Azure Active Directory/協力廠商驗證或 Kudu 網站擴充。 但隨著我們啟用這些功能，我們將會在文件和部落格中提供變更消息。

目前只在下列區域提供此公開預覽版本：

* 美國西部
* 西歐 
* 東南亞

Linux 上的 Web Apps 只在「專用」App Service 方案中才支援，而且沒有「免費」或「共用」層。 此外，一般和 Linux Web 應用程式的 App Service 方案互斥，因此，您無法在非 Linux App Service 方案中建立 Linux Web 應用程式。

只有同一區域沒有非 Linux Web Apps 的資源群組中，才能建立 Linux 上的 Web Apps。

## <a name="next-steps"></a>後續步驟
請參閱下列連結以開始使用 Linux 上的 App Service。 您可以在[我們的論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)張貼問題和疑難。

* [在 Linux 上的 App Service 中建立 Web Apps](app-service-linux-how-to-create-a-web-app.md)
* [如何針對 Linux 上的 App Service 使用自訂 Docker 映像](app-service-linux-using-custom-docker-image.md)
* [在 Linux 上的 Web Apps 中使用適用於 Node.js 的 PM2 組態](app-service-linux-using-nodejs-pm2.md)
* [在 Linux 上的 Azure App Service Web Apps 中使用 .NET Core](app-service-linux-using-dotnetcore.md)



<!--HONumber=Jan17_HO1-->


