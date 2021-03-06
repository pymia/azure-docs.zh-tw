---
title: "Azure 通知中心"
description: "了解如何在 Azure 中使用推播通知。 程式碼範例是以 C# 撰寫並使用 .NET API。"
author: ysxu
manager: erikre
editor: 
services: notification-hubs
documentationcenter: 
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: hero-article
ms.date: 08/25/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 38735f7c0154388e8698edc5bac931c5a079a865


---
# <a name="azure-notification-hubs"></a>Azure 通知中心
## <a name="overview"></a>Overview
Azure 通知中樞提供一種易用、多平台、向外延展的推播基礎結構，可讓您將行動推播通知從任何後端 (在雲端或內部部署) 傳送到任何行動平台。

使用通知中樞，您可以輕鬆地傳送跨平台個人化推播通知，並擷取不同平台通知系統 (PNS) 的詳細資料。 使用單一 API 呼叫，您可以跨所有裝置將目標設為個別使用者或整個對象區段 (包含數百萬個使用者)。

在企業和消費者案例中，您可以使用通知中樞。 例如：

* 將即時新聞通知傳送給數百萬名使用者 (通知中樞強力支援所有 Windows 和 Windows Phone 裝置上已預先安裝的 Bing 應用程式)，且延遲性低。
* 將位置型折價券傳送至使用者區段。
* 將事件通知傳送給運動/財金/遊戲應用程式使用者或群組。
* 通知使用者有關企業事件 (例如新訊息/電子郵件) 和潛在業務機會。
* 傳送 Multi-Factor Authentication 所需的一次性密碼。

## <a name="what-are-push-notifications"></a>什麼是推播通知？
智慧型手機和平板電腦都可以在事件發生時「通知」使用者。 這些通知可以有許多形式。

在 Windows 市集和 Windows Phone 應用程式中，通知可以是「推播」形式：具有音效的非強制回應視窗出現，以指出有新的通知。 其他支援的通知類型包括「圖格」、「原始」和「徽章」通知。 如需 Windows 裝置上所支援通知類型的詳細資訊，請參閱 [圖格、徽章和通知](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)。

在 Apple iOS 裝置上，推播同樣會以對話方塊的形式通知使用者，而要求使用者檢視或關閉通知。 按一下 [檢視]  即會開啟接收訊息的應用程式。 如需 iOS 通知的詳細資訊，請參閱 [iOS 通知](http://go.microsoft.com/fwlink/?LinkId=615245)。

推播通知有助於行動裝置在保有足夠資源的情況下顯示最新資訊。 後端系統可以將通知傳送給行動裝置，即使裝置上的對應應用程式未作用也是一樣。 推播通知是消費者應用程式的重要元件，可用來增加應用程式的應用面和使用性。 通知對企業也有幫助，因為掌握最新資訊的員工對於商務活動更能處理得當。

行動應用案例的特定範例包括：

1. 以目前的財務資訊更新 Windows 8 或 Windows Phone 上的圖格。
2. 在以工作流程為基礎的企業應用程式中，透過已將某個工作項目指派給使用者的快顯通知，對該名使用者發出警示。
3. 在 CRM 應用程式 (例如 Microsoft Dynamics CRM) 中，顯示具有目前潛在客戶數目的徽章。

## <a name="how-push-notifications-work"></a>推播通知的運作方式
推播通知可透過名為「平台通知系統 (PNS)」的平台特定基礎結構來傳遞。 PNS 可提供準系統功能 (即不支援廣播和個人化的系統)，不具共同介面。 例如，若要將通知傳送至 Windows 市集應用程式，開發人員必須連絡 WNS (Windows 通知服務)。 若要將通知傳送至 iOS 裝置，同一位開發人員必須連絡 APNS (Apple Push Notification Service)，並再次傳送訊息。 Azure 通知中樞提供通用介面以及其他功能，以協助支援跨每個平台的推播通知。

但綜觀而言，所有平台通知系統都依循相同的模式：

1. 用戶端應用程式會連絡 PNS 以擷取其「控制代碼」。 控制代碼類型視系統而定。 WNS 的控制代碼類型為 URI 或「通知通道」。 APNS 的控制代碼則為權杖。
2. 用戶端應用程式會將此控制代碼儲存在應用程式「後端」，以供後續使用。 WNS 的後端通常是雲端服務。 就 Apple 而言，則是名為「供應商」的系統。
3. 若要傳送推播通知，應用程式後端必須使用控制代碼連絡 PNS，以將特定的用戶端應用程式執行個體訂為目標。
4. PNS 會將通知轉送至控制代碼所指定的裝置。

![][0]

## <a name="the-challenges-of-push-notifications"></a>推播通知的挑戰
儘管這些系統功能非常強大，但即便 App 開發人員想要實作一般的推播通知工作 (例如廣播或傳送推播通知給分段式使用者)，都有許多工作有待處理。

就行動應用程式而言，推播通知是雲端服務中最常被要求的功能之一。 原因是，其運作所需的基礎結構頗為複雜，且大部分與應用程式的主要商業邏輯沒有關聯。 建置隨選推播基礎結構的挑戰包括：

* **平台相依性。**  若要將通知傳送至不同平台上的裝置，必須在後端為多個介面進行編碼。 除了低階細節有所差異外，通知的呈現方式 (圖格、快顯通知或徽章) 也會隨平台而不同。 這些差異可能會致使後端程式碼複雜且難以維護。
* **調整。**  此基礎結構的調整可分為兩個層面：
  
  * 根據 PNS 準則，在每次啟動應用程式時，都必須重新整理裝置權杖。 在此情況下，光是為了保有最新的裝置權杖，就會產生大量的流量 (和隨之而來的資料庫存取)。 當裝置數目增加時 (可能達百萬以上)，建立及維護此基礎結構的成本將會很可觀。
  * 大部分的 PNS 並不支援廣播至多個裝置。 因此，若要廣播至數百萬個裝置，就必須對 PNS 呼叫數百萬次。 要能夠調整這些要求可不是容易的事，因為應用程式開發人員通常都會想要維持較低的整體延遲性。 例如，最後一個接收訊息的裝置，不應在通知送出的 30 分鐘後才收到通知，因為這樣很可能就失去推播通知的意義了。
* **路由。**  PNS 提供將訊息傳送至裝置的途徑。 但大部分的應用程式通知都會以使用者和 (或) 相關群組 (例如，所有指派至特定客戶帳戶的員工) 為對象。 因此，若要將通知遞送至正確的裝置，應用程式後端必須保有為相關群組與裝置權杖建立關聯的登錄。 這項額外工作將導致應用程式的上市時程延宕和維護成本提高。

## <a name="why-use-notification-hubs"></a>為何要使用通知中心？
通知中樞可消除複雜性：您不需要管理推播通知的挑戰。 而是，您可以使用通知中樞。 通知中樞採用完整的多平台、向外延展的推播通知基礎結構，可大幅減少應用程式後端所執行的推播專用程式碼。 通知中樞可實作推播基礎結構的所有功能。 裝置只需註冊其 PNS 控制代碼即可，後端會負責將平台共用格式訊息傳送給使用者或相關群組 (如下圖所示)：

![][1]

通知中樞提供具有下列優點且容易使用的推播通知基礎結構：

* **多個平台。**
  
  * 支援所有主要行動平台。 通知中心可傳送推播通知至 Windows 市集、iOS、Android 和 Windows Phone 應用程式。
  * 通知中心提供可將通知傳送至所有支援平台的共同介面。 不需要平台特定通訊協定。 應用程式後端可傳送平台專用格式的通知，或平台共用格式的通知。 應用程式只會與通知中樞通訊。
  * 裝置控制代碼管理。 通知中樞會維護控制代碼登錄以及來自 PNS 的意見。
* **使用任何後端**：雲端或內部部署、.NET、PHP、Java、Node 等。
* **調整。**  通知中心可因應數百萬個裝置的需求，而無需重新架構或分區。
* **豐富的傳遞模式集**：
  
  * *廣播*：允許使用單一 API 呼叫幾乎同時廣播到數百萬個裝置。
  * *單點傳播/多點傳播*：推播至標記代表個別使用者，包括其所有裝置；或更大的群組；例如，個別表單係數 (平板電腦與電話)。
  * *分割*：標記運算式所定義的推播至複雜區段 (例如，紐約追蹤洋基的裝置)。
    
    每個裝置在將其控制代碼傳送至通知中樞時，都可指定一或多個「標記」。 如需 [標記]的詳細資訊。 標籤不一定要預先佈建或處置。 透過標籤，通知將可輕易傳送給使用者或相關群組。 由於標籤可包含任何應用程式專用的識別碼 (例如使用者或群組 ID)，因此使用標籤後，應用程式後端將可省卻儲存及管理裝置控制代碼的工作負擔。
* **個人化**：每個裝置都可以有一或多個範本，以達成每個裝置的當地語系化和個人化，而不影響後端程式碼。
* **安全性**：共用存取密碼 (SAS) 或同盟驗證。
* **豐富遙測**：可在入口網站並透過程式設計方式使用。

## <a name="integration-with-app-service-mobile-apps"></a>與 App Service Mobile Apps 整合
為了在 Azure 服務間促進完美且統一的體驗， [App Service Mobile Apps] 具備使用通知中樞提供推播通知的內建支援。 [App Service Mobile Apps] 具有高擴充性且可供全球使用，是專為企業開發人員與系統整合人員設計的行動應用程式開發平台，能提供一組豐富的功能給行動應用程式開發人員。

Mobile Apps 開發人員可以使用下列流程來利用通知中樞：

1. 擷取裝置 PNS 控制代碼
2. 透過便利的 Mobile Apps Client SDK 註冊 API，使用通知中樞註冊裝置和 [範本]
   * 請注意，Mobile Apps 會基於安全性考量，去除註冊上的所有標籤。 直接從後端使用通知中樞，將標籤關聯至裝置。
3. 從 App 後端使用通知中樞傳送通知

以下是透過此註冊為開發人員帶來的一些便利性：

* **Mobile Apps Client SDK。**  這些多平台 SDK 提供簡單的 API 來進行註冊，然後會自動與連結到行動 App 的通知中樞聯繫。 開發人員不需要透過通知中樞認證進行挖掘，以及使用其他服務。
  
  * SDK 會使用 Mobile Apps 驗證的使用者識別碼自動標記指定的裝置，以啟用推送至使用者案例。
  * SDK 會自動使用 Mobile Apps 安裝識別碼做為 GUID 來向通知中樞註冊，省去開發人員維護多個服務 GUID 的麻煩。
* **安裝模型。**  Mobile Apps 會使用通知中樞的最新推送模型，來呈現 JSON 安裝中所有與裝置相關聯的推送屬性，其會與推播通知密切合作且易於使用。
* **彈性。**  即使已就地整合，開發人員一律還是可以選擇直接使用通知中樞。
* **[Azure 入口網站]中的整合體驗。**  Mobile Apps 中會以視覺化方式呈現以功能形式出現的推送，而開發人員可以透過 Mobile Apps 輕鬆使用相關聯的通知中樞。

## <a name="next-steps"></a>後續步驟
您可以在以下主題找到「通知中樞」的詳細資訊：

* **[客戶如何使用通知中樞]**
* **[通知中樞教學課程和指南]**
* **通知中樞快速入門教學課程** ([iOS]、[Android]、[Windows Universal]、[Windows Phone]、[Kindle]、[Xamarin.iOS]、[Xamarin.Android])

針對推播通知，相關的 .NET 受管理 API 參考位於此處：

* [Microsoft.WindowsAzure.Messaging.NotificationHub]
* [Microsoft.ServiceBus.Notifications]

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png
[客戶如何使用通知中樞]: http://azure.microsoft.com/services/notification-hubs
[通知中樞教學課程和指南]: http://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[App Service Mobile Apps]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
[範本]: notification-hubs-範本-cross-platform-push-messages.md
[Azure 入口網站]: https://portal.azure.com
[標記]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)



<!--HONumber=Nov16_HO2-->


