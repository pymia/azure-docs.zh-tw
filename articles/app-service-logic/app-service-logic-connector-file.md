---
title: "在 Logic Apps 中使用檔案連接器 | Microsoft Docs"
description: "如何建立並設定檔案連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
author: rajeshramabathiran
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 07ceb81a-f8f6-4901-a7a2-06a9ac47efd5
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: rajram
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 0fd71a72040ecf4e51fc118ce881b6c8ceb51b61


---
# <a name="get-started-with-the-file-connector-and-add-it-to-your-logic-app"></a>開始使用檔案連接器並將它加入您的邏輯應用程式
> [!NOTE]
> 這一版文章適用於 Logic Apps 2014-12-01-preview 結構描述版本。
>
>

連線至檔案系統以對主機電腦上的檔案執行上傳、下載等其他操作。 邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理資料。 您可以將檔案連接器加入您的商務工作流程，就能在邏輯應用程式的該工作流程中處理資料。

檔案連接器使用混合式連線管理員對主機檔案系統建立混合式連線。

## <a name="creating-a-file-connector-for-your-logic-app"></a>建立邏輯應用程式的檔案連接器
若要使用檔案連接器，您必須先建立檔案連接器 API 應用程式的執行個體。 以下步驟可以達到此目的：

1. 使用 Azure 入口網站左邊的 [+新增] 選項開啟 Azure Marketplace。
2. 搜尋「檔案連接器」。
3. 從搜尋結果選取 [檔案連接器 (預覽版)]  。
4. 選取 [建立]  按鈕
5. 設定檔案連接器，如下所示：  
   ![][1]

   * **名稱** - 提供檔案連接器的名稱
   * **封裝設定**
     * **根資料夾** - 指定主機電腦上的根資料夾路徑。 例如 D:\FileConnectorTest
     * **服務匯流排連接字串** - 提供服務匯流排連接字串。 請確認服務匯流排命名空間是「標準」類型而「不是」「基本」，如此才能使用服務匯流排轉送。  服務匯流排轉送用來連接至混合式連線管理員。
   * **App Service 方案** - 選取或建立 App Service 方案
   * **定價層** - 選擇連接器的定價層
   * **資源群組** - 選取或建立連接器所在的資源群組
   * **訂用帳戶** - 選擇您要建立此連接器的訂用帳戶
   * **位置** - 選擇您要部署連接器的地理位置
6. 按一下 [建立]。 將建立新的檔案連接器

## <a name="configure-hybrid-connection-manager"></a>設定混合式連線管理員
API 應用程式執行個體建立之後，瀏覽至其儀表板。  作法是按一下 [瀏覽] > [API Apps] > 選取您的檔案連接器 API Apps。  需要在這裡設定混合式連線管理員。
如需有關設定和疑難排解混合式連線管理員的詳細資訊，請參閱 [使用混合式連線管理員]。

## <a name="using-the-file-connector-in-your-logic-app"></a>在邏輯應用程式中使用檔案連接器
建立 API 應用程式之後，您現在可以使用檔案連接器做為邏輯應用程式的動作。 若要這樣做，您需要：

1. 建立新的邏輯應用程式，並選擇具有檔案連接器的相同資源群組。 依照指示 [建立新的邏輯應用程式]。
2. 在建立的 Logic Apps 中開啟 [觸發程序和動作] 以開啟 Logic Apps 設計工具，並設定您的流程。
3. 檔案連接器就會出現在右側資源庫中的 [此資源群組中的 API Apps] 區段。
4. 您可以在 [檔案連接器] 上按一下來將檔案連接器 API 應用程式置入編輯器。 檔案連接器公開一個觸發程序和 4 個動作：  
   ![][5]
5. 其中每個都公開特定的屬性。 下圖列出觸發程序和「取得檔案」動作的屬性︰  
   ![][6]
6. 設定這些之後，就可以在流程中使用觸發程序和動作。 同樣地，您也可以設定其他動作。

> [!NOTE]
> 成功從資料夾讀取檔案之後，檔案觸發程序會刪除檔案。
>
>

## <a name="file-connector-rest-apis"></a>檔案連接器 REST API
若要在邏輯應用程式外使用連接器，可以利用連接器公開的 REST API。 您可以使用 [瀏覽] -> [API 應用程式] -> [檔案連接器] 檢視這個「API 定義」。 在 [摘要] 區段下的 [API 定義] 透鏡上按一下，來檢視此連接器公開的 API︰  
![][7]

如需 API 的詳細資訊，請參閱 [檔案連接器 API 定義]。

## <a name="do-more-with-your-connector"></a>進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。 請參閱 [什麼是 Logic Apps？](app-service-logic-what-are-logic-apps.md)。

> [!NOTE]
> 如果您想在註冊 Azure 帳戶前開始使用 Azure Logic Apps，請移至 [試用 Logic App](https://tryappservice.azure.com/?appservice=logic)，即可在 App Service 中立即建立短期入門 Logic Apps。 不需要信用卡；沒有承諾。
>
>

檢視位於 [連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)的 Swagger REST API 參考。

您也可以檢閱連接器的效能統計資料及控制安全性。 請參閱[監視 Logic Apps](app-service-logic-monitor-your-logic-apps.md)。

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[建立新的邏輯應用程式]: app-service-logic-create-a-logic-app.md
[File connector API definition]: https://msdn.microsoft.com/library/dn936296.aspx
[使用混合式連線管理員]: ../app-service-web/web-sites-hybrid-connection-get-started.md



<!--HONumber=Dec16_HO2-->


