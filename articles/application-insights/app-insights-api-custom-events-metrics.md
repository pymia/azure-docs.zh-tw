---
title: "自訂事件和度量的 Application Insights API | Microsoft Docs"
description: "在您的裝置或桌面應用程式、網頁或服務中插入幾行程式碼，來追蹤使用狀況及診斷問題。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 7bd26ffdec185a1ebd71fb88383c2ae4cd6d504f
ms.openlocfilehash: 460b9e3cf2b4c7a85895c2db872344a1a5bc0ff9


---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>自訂事件和度量的 Application Insights API


在您的應用程式中插入幾行程式碼，以了解使用者對它進行的動作或協助診斷問題。 您可以從裝置和桌面應用程式、Web 用戶端以及 Web 伺服器傳送遙測。 [Azure Application Insights](app-insights-overview.md) 核心遙測 API 可讓您傳送自訂的事件和度量，以及您自己的標準遙測版本。 這個 API 與標準 Application Insights 資料收集器所使用的 API 相同。

## <a name="api-summary"></a>API summary
API 是跨所有平台統一的，除了一些小變化形式。

| 方法 | 用於 |
| --- | --- |
| [`TrackPageView`](#page-views) |頁面、畫面、刀鋒視窗或表單 |
| [`TrackEvent`](#track-event) |使用者動作和其他事件。 用來追蹤使用者行為，或監視效能。 |
| [`TrackMetric`](#track-metric) |效能度量，例如與特定事件不相關的佇列長度 |
| [`TrackException`](#track-exception) |記錄診斷的例外狀況。 追蹤與其他事件的發生相對位置，並且檢查堆疊追蹤。 |
| [`TrackRequest`](#track-request) |記錄伺服器要求的頻率和持續時間以進行效能分析。 |
| [`TrackTrace`](#track-trace) |診斷記錄訊息。 您也可以擷取第三方記錄檔。 |
| [`TrackDependency`](#track-dependency) |記錄對您的應用程式所依賴的外部元件的呼叫持續時間及頻率。 |

您可以 [附加屬性和度量](#properties) 至這裡大部分的遙測呼叫。

## <a name="a-nameprepabefore-you-start"></a><a name="prep"></a>開始之前
如果您尚未完成這些動作：

* 將 Application Insights SDK 加入至專案：

  * [ASP.NET 專案][greenbrown]
  * [Java 專案][java]
  * [每個網頁中的 JavaScript][client]   
* 在裝置或 Web 伺服器程式碼中，加入：

    *C#:* `using Microsoft.ApplicationInsights;`

    *VB:* `Imports Microsoft.ApplicationInsights`

    *Java：* `import com.microsoft.applicationinsights.TelemetryClient;`

## <a name="construct-a-telemetryclient"></a>建構 TelemetryClient
建構 TelemetryClient 的執行個體 (除了在網頁中的 JavaScript)：

*C#:*

    private TelemetryClient telemetry = new TelemetryClient();

*VB:*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

TelemetryClient 具備執行緒安全。

我們建議針對您每個應用程式的模組使用 `TelemetryClient` 執行個體。 比方說，您可能在 Web 服務中有一個 `TelemetryClient` 報告傳入的 http 要求，另一個中介類別告報商業邏輯事件。 您可以設定如 `TelemetryClient.Context.User.Id` 的屬性以追蹤使用者和工作階段，或 `TelemetryClient.Context.Device.Id` 來識別電腦。 這項資訊會附加至執行個體所傳送的所有事件。

## <a name="track-event"></a>追蹤事件
在 Application Insights 中，「自訂事件」是您可以在[計量瀏覽器][metrics]中顯示為彙總計數，以及在[診斷搜尋][diagnostic]中顯示為個別發生點的資料點。 (它與 MVC 或其他架構的「事件」不相關。)

在您的程式碼中插入 TrackEvent 呼叫，以計算使用者選擇特定功能的頻率、達成特定目標的頻率，或可能製造特定類型的錯誤。

例如，在遊戲應用程式中，每當使用者贏得遊戲時傳送事件：

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*

    telemetry.TrackEvent("WinGame");

*VB*

    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");


### <a name="view-your-events-in-the-azure-portal"></a>在 Azure 入口網站中檢視您的事件
若要查看事件計數，請開啟 [ [計量瀏覽器](app-insights-metrics-explorer.md) ] 刀鋒視窗、新增圖表，然後再選取 [事件]。  

![](./media/app-insights-api-custom-events-metrics/01-custom.png)

若要比較不同事件的計數，請將圖表類型設為 [方格]，並依事件名稱進行分組：

![](./media/app-insights-api-custom-events-metrics/07-grid.png)

在方格中逐一點選事件名稱，以查看該事件的個別發生次數。

![鑽研事件](./media/app-insights-api-custom-events-metrics/03-instances.png)

按一下任何發生以查看詳細資料。

若要在「搜尋」或「計量瀏覽器」中專注查看特定事件，請將刀鋒視窗篩選器設為您感興趣的事件名稱：

![開啟 [篩選器]，展開 [事件名稱]，然後選取一或多個值](./media/app-insights-api-custom-events-metrics/06-filter.png)

## <a name="track-metric"></a>追蹤度量
使用 TrackMetric 傳送未附加至特定事件的度量。 例如，您可以定期監視佇列長度。

度量會在計量瀏覽器中顯示為統計圖表，但不同於事件，您不能搜尋診斷搜尋中的個別發生次數。

度量值應該 > = 0，才能正確顯示。

*JavaScript*

    appInsights.trackMetric("Queue", queue.Length);

*C#*

    telemetry.TrackMetric("Queue", queue.Length);

*VB*

    telemetry.TrackMetric("Queue", queue.Length)

*Java*

    telemetry.trackMetric("Queue", queue.Length);

事實上，您可能會在背景執行緒中執行這個動作：

*C#*

    private void Run() {
     var appInsights = new TelemetryClient();
     while (true) {
      Thread.Sleep(60000);
      appInsights.TrackMetric("Queue", queue.Length);
     }
    }


若要查看結果，開啟 [計量瀏覽器] 並加入新的圖表。 將它設定為顯示您的度量。

![加入新的圖表或選取圖表，並在 [自訂] 底下選取您的度量](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

有一些 [度量的數目限制](#limits) 可供您使用。

## <a name="page-views"></a>頁面檢視
在裝置或網頁應用程式中，每個畫面或頁面載入時預設會傳送頁面檢視遙測。 但是，您可以變更為在其他或不同的時間追蹤頁面檢視。 例如，在顯示索引標籤或刀鋒視窗的應用程式中，您可能想要在使用者每次開啟新的刀鋒視窗時追蹤「頁面」。

![[概觀] 刀鋒視窗上的使用方式透鏡](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

使用者和工作階段資料會與頁面檢視一起傳送為屬性，當有頁面檢視遙測時，讓使用者與工作階段圖表顯現。

#### <a name="custom-page-views"></a>自訂頁面檢視
*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*VB*

    telemetry.TrackPageView("GameReviewPage")


如果您在不同的 HTML 網頁內有數個索引標籤，您也可以指定 URL：

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

#### <a name="timing-page-views"></a>計時頁面檢視
根據預設，回報為「頁面檢視載入時間」的時間是測量從瀏覽器傳送要求開始，直到呼叫瀏覽器的頁面載入事件為止的時間。

相反地，您可以：

* 在 [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) 呼叫中設定明確的持續時間。
  * `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`
* 使用頁面檢視計時呼叫 `startTrackPage` 和 `stopTrackPage`。

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

...

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

做為第一個參數的名稱會將開始和停止呼叫相關聯。 預設為目前的頁面名稱。

產生後顯示在計量瀏覽器中的頁面載入持續時間是衍生自開始和停止呼叫之間的間隔。 取決於您實際計時的間隔。

## <a name="track-request"></a>追蹤要求
由伺服器 SDK 用來記錄 HTTP 要求。

如果您想要在沒有 Web 服務模組執行的內容中模擬要求，您也可以自行呼叫。

*C#*

    // At start of processing this request:

    // Operation Id and Name are attached to all telemetry and help you identify
    // telemetry associated with one request:
    telemetry.Context.Operation.Id = Guid.NewGuid().ToString();
    telemetry.Context.Operation.Name = requestName;

    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... process the request ...

    stopwatch.Stop();
    telemetry.TrackRequest(requestName, DateTime.Now,
       stopwatch.Elapsed,
       "200", true);  // Response code, success



## <a name="operation-context"></a>作業內容
您可藉由為遙測項目附加通用的作業識別碼，讓它們能夠關聯在一起。 標準的要求追蹤模組會針對在處理 HTTP 要求時傳送的例外狀況和其他事件執行此動作。 在[搜尋](app-insights-diagnostic-search.md)和[分析](app-insights-analytics.md)中，您可以使用此識別碼，輕易地找出任何與要求相關聯的事件。

設定此識別碼的最簡單方式是使用下列模式來設定作業內容：

    // Establish an operation context and associated telemetry item:
    using (var operation = telemetry.StartOperation<RequestTelemetry>("operationName"))
    {
        // Telemetry sent in here will use the same operation ID.
        ...
        telemetry.TrackEvent(...); // or other Track* calls
        ...
        // Set properties of containing telemetry item - for example:
        operation.Telemetry.ResponseCode = "200";

        // Optional: explicitly send telemetry item:
        telemetry.StopOperation(operation);

    } // When operation is disposed, telemetry item is sent.

除了設定作業內容以外，`StartOperation` 會建立採用您指定類型的遙測項目，並在處置作業或在您明確地呼叫 `StopOperation` 時傳送它。 如果您使用 `RequestTelemetry` 為遙測類型，則其 [持續時間] 會設定為開始與停止之間的時間間隔。

作業內容不可為巢狀。 如果已經有作業內容，則其識別碼會與所有內含項目 (包括使用 StartOperation 建立的項目) 相關聯。

在搜尋中，會使用作業內容來建立 [相關項目] 清單：

![相關項目](./media/app-insights-api-custom-events-metrics/21.png)

## <a name="track-exception"></a>追蹤例外狀況
將例外狀況傳送至 Application Insights：以[計算它們][metrics]，做為問題頻率的指示，以及[檢查個別發生次數][diagnostic]。 報告包含堆疊追蹤。

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*JavaScript*

    try
    {
       ...
    }
    catch (ex)
    {
       appInsights.trackException(ex);
    }

SDK 將自動攔截許多例外狀況，所以您不一定需要明確呼叫 TrackException。

* ASP.NET： [撰寫程式碼以攔截例外狀況](app-insights-asp-net-exceptions.md)
* J2EE： [自動攔截例外狀況](app-insights-java-get-started.md#exceptions-and-request-failures)
* JavaScript：自動攔截。 如果您想要停用自動收集，請在您插入網頁的程式碼片段中加入一行：

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```

## <a name="track-trace"></a>追蹤
使用此選項可協助您藉由將 'breadcrumb trail' 傳送至 Application Insights 來診斷問題。 您可以傳送診斷資料區塊，並且在[診斷搜尋][diagnostic]中檢查。

[記錄配接器][trace]會使用此 API 將第三方記錄傳送至入口網站。

*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);


您可以搜尋訊息內容，但是 (不同於屬性值) 您無法在其中進行篩選。

`message` 上的大小限制比屬性上的限制高得多。
TrackTrace 的優點在於您可以將較長的資料放在訊息中。 例如，您可以在該處編碼 POST 資料。  

此外，您可以在訊息中新增嚴重性層級。 就像其他遙測一樣，您可以新增屬性值以供協助篩選或搜尋不同的追蹤集。 例如：

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

在[搜尋][diagnostic]中，這可讓您輕鬆地篩選出與特定資料庫相關且具有特定嚴重性層級的所有訊息。

## <a name="track-dependency"></a>追蹤相依性
您可以使用這個呼叫來追蹤回應時間以及呼叫外部程式碼片段的成功率。 結果會出現在入口網站中的相依性圖表中。

```C#

            var success = false;
            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

請記住，伺服器 SDK 包含 [相依性模組](app-insights-asp-net-dependencies.md) ，可用來自動探索和追蹤特定相依性呼叫 (例如資料庫和 REST API)。 您必須在伺服器上安裝代理程式才能讓模組正常運作。 如果您想要追蹤不會由自動化追蹤攔截的呼叫，或不想安裝代理程式，您可以使用這個呼叫。

若要關閉標準的相依性追蹤模組，請編輯 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 並刪除 `DependencyCollector.DependencyTrackingTelemetryModule` 的參考。

## <a name="flushing-data"></a>排清資料
通常 SDK 會在選擇的時間傳送資料以將對使用者的影響降到最低。 不過，在某些情況下您可能想要排清緩衝區，例如，如果您在會關閉的應用程式中使用 SDK。

*C#*

    telemetry.Flush();

    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(1000);

請注意，此函式對[伺服器遙測通道](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/)而言是非同步的。

## <a name="authenticated-users"></a>驗證的使用者
在 Web app 中，預設是透過 Cookie 來識別使用者。 如果使用者從不同的電腦或瀏覽器存取您的 app 或刪除 Cookie，就可能多次計算他們。

但是，如果使用者登入您的 app，您可以藉由在瀏覽器程式碼中設定經過驗證的使用者識別碼，來取得更正確的計數。

*JavaScript*

```JS
    // Called when my app has identified the user.
    function Authenticated(signInId) {
      var validatedId = signInId.replace(/[,;=| ]+/g, "_");
      appInsights.setAuthenticatedUserContext(validatedId);
      ...
    }
```

在 ASP.NET Web MVC 應用程式，例如：

*Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name
                   .Replace("\\", "\\\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

這不需要用到使用者的實際登入名稱。 只需是使用者的唯一識別碼。 其中不能包含空格或任何 `,;=|`字元。

使用者識別碼也會設定於工作階段 Cookie 中，並傳送到伺服器。 如果安裝了伺服器 SDK，則會傳送經過驗證的使用者識別碼以做為用戶端和伺服器遙測的內容屬性一部分，讓您能夠對它進行篩選和搜尋。

如果您的 app 會將使用者群組為帳戶，您也可以傳遞該帳戶的識別碼 (具有相同的字元限制)。

      appInsights.setAuthenticatedUserContext(validatedId, accountId);

在[計量瀏覽器](app-insights-metrics-explorer.md)中，您可建立可計算**已驗證的使用者**和**使用者帳戶**的圖表。

您亦可[搜尋][diagnostic]具特定使用者名稱和帳戶的用戶端資料點。

## <a name="a-namepropertiesafilter-search-and-segment-your-data-with-properties"></a><a name="properties"></a>使用屬性來篩選、搜尋和分割您的資料
您可以將屬性和測量結果附加至您的事件 (同時還有度量，頁面檢視、例外狀況和其他的遙測資料)。

**屬性** 是可在使用情況報告中用來篩選遙測的字串值。 例如，如果您的應用程式提供數個遊戲，則您可以將遊戲的名稱附加至每個事件，以了解哪些遊戲較受歡迎。

字串長度有 1k 的限制。 (如果您想要傳送大量的資料區塊，請使用訊息參數 [TrackTrace](#track-trace)。)

**度量** 是可以用圖表方式呈現的數值。 例如，您可能想要查看玩家達到的分數是否逐漸增加。 圖表可以依據隨事件傳送的屬性分割，讓您可以針對不同遊戲取得個別或堆疊圖表。

度量值應該 > = 0，才能正確顯示。

有一些 [屬性、屬性值和度量的數目限制](#limits) 可供您使用。

*JavaScript*

    appInsights.trackEvent
      ("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

    appInsights.trackPageView
        ("page name", "http://fabrikam.com/pageurl.html",
          // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );


*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string>
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);


*VB*

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)


*Java*

    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());

    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());

    telemetry.trackEvent("WinGame", properties, metrics);


> [!NOTE]
> 切勿在屬性中記錄個人識別資訊。
>
>

**如果您使用度量**，請開啟 [計量瀏覽器]，然後從自訂群組中選取度量：

![開啟計量瀏覽器，選取圖表，並選取度量](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

*若未顯示您的計量或 [自訂] 標題不存在，請關閉選取的刀鋒視窗並於稍後重試。*有時候度量經由管線彙總時可能需要一個小時。

**如果您使用屬性和度量**，依據屬性分割度量：

![設定群組，然後在 [群組依據] 底下選取屬性](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)

**在「診斷搜尋」中**，您可以檢視事件個別發生次數的屬性和度量。

![選取執行個體，然後選取 [...]](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)

使用 [搜尋] 欄位來查看具有特定屬性值的事件出現次數。

![將詞彙輸入 [搜尋] 中](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[深入了解搜尋運算式][diagnostic]。

#### <a name="alternative-way-to-set-properties-and-metrics"></a>設定屬性和度量的替代方式
如果更加方便，您可以收集個別物件中事件的參數：

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [!WARNING]
> 不要重複使用相同的遙測項目執行個體 (此範例中的 `event`) 來呼叫 Track*() 多次。 這可能會讓遙測隨著不正確的組態傳送。
>
>

## <a name="a-nametimeda-timing-events"></a><a name="timed"></a> 計時事件
有時候您想要繪製執行某些動作耗費多少時間的圖表。 例如，您可能想要知道使用者在遊戲中思考選項時花費多少時間。 這是使用測量參數的實用範例。

*C#*

    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... perform the timed action ...

    stopwatch.Stop();

    var metrics = new Dictionary <string, double>
       {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

    // Set up some properties:
    var properties = new Dictionary <string, string>
       {{"signalSource", currentSignalSource.Name}};

    // Send the event:
    telemetry.TrackEvent("SignalProcessed", properties, metrics);



## <a name="a-namedefaultsadefault-properties-for-custom-telemetry"></a><a name="defaults"></a>自訂遙測資料的預設屬性
如果您想為您撰寫的一些自訂事件設定預設屬性值，您可以在 TelemetryClient 中設定它們。 它們會附加至從該用戶端傳送的每個遙測項目。

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");

*VB*

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...


    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);

    gameTelemetry.TrackEvent("WinGame");



個別遙測呼叫可以覆寫其屬性字典中的預設值。

**針對 JavaScript Web 用戶端**， [請使用 JavaScript 遙測初始設定式](#js-initializer)。

**若要將屬性新增至所有遙測**，並包括來自標準集合模組的資料，請[實作 `ITelemetryInitializer`](app-insights-api-filtering-sampling.md#add-properties)。

## <a name="sampling-filtering-and-processing-telemetry"></a>取樣、篩選及處理遙測資料
您可以撰寫程式碼，在從 SDK 傳送遙測資料前加以處理。 處理包括從標準遙測模組 (如 HTTP 要求收集和相依性收集) 的資料。

* 實作 `ITelemetryInitializer` 以在遙測中[新增屬性](app-insights-api-filtering-sampling.md#add-properties)；例如，新增版本號碼或從其他屬性計算得出的值。
* [篩選](app-insights-api-filtering-sampling.md#filtering)可以先修改或捨棄遙測，再藉由實作 `ITelemetryProcesor` 從 SDK 傳送遙測。 您可控制要傳送或捨棄的項目，但是您必須考量這對您的度量的影響。 視您捨棄項目的方式而定，您可能會喪失在相關項目之間瀏覽的能力。
* [取樣](app-insights-api-filtering-sampling.md) 是減少從應用程式傳送至入口網站的資料量的套件方案。 但不會影響顯示的計量，而且藉由在相關項目 (如例外狀況、要求和頁面檢視) 之間瀏覽，並不會影響您診斷問題的能力。

[深入了解](app-insights-api-filtering-sampling.md)

## <a name="disabling-telemetry"></a>停用遙測
**動態停止和開始** 收集及傳輸遙測資料：

*C#*

```C#

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

若要**停用選取的標準收集器** (例如效能計數器、HTTP 要求或相依性)，請刪除或註解化 [ApplicationInsights.config][config] 中的相關行。 例如，如果您想要傳送自己的 TrackRequest 資料，可以這麼做。

## <a name="a-namedebugadeveloper-mode"></a><a name="debug"></a>開發人員模式
偵錯期間，讓您的遙測透過管線加速很有用，如此您就可以立即看到結果。 您也會取得額外的訊息，協助您追蹤任何遙測的問題。 在生產環境中將它關閉，因為它可能會拖慢您的應用程式。

*C#*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*VB*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="a-nameikeya-set-the-instrumentation-key-for-selected-custom-telemetry"></a><a name="ikey"></a> 設定已選取自訂遙測的檢測金鑰
*C#*

    var telemetry = new TelemetryClient();
    telemetry.InstrumentationKey = "---my key---";
    // ...


## <a name="a-namedynamic-ikeya-dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> 動態檢測金鑰
若要避免混合來自開發、測試和實際執行環境的遙測，您可以[建立個別 Application Insights 資源][create]，並且依據環境變更其金鑰。

而不是從組態檔取得檢測金鑰，您可以在程式碼中設定。 在初始化方法中設定金鑰，例如 ASP.NET 服務中的 global.aspx.cs：

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey;



在網頁中，您可能想要從 Web 伺服器的狀態設定，而不是按其原義編碼至指令碼。 例如，在 ASP.NET 應用程式中產生的網頁：

*Razor 中的 JavaScript*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="telemetrycontext"></a>TelemetryContext
TelemetryClient 具有內容屬性，其中包含與所有遙測資料一起傳送的值數目。 它們通常由標準遙測模組設定，但是您也可以自行設定它們。 例如：

    telemetry.Context.Operation.Name = "MyOperationName";

如果您自行設定這些值，請考慮從 [ApplicationInsights.config][config] 的相關程式碼行移除，如此您的值和標準值才不致混淆。

* **元件** 識別應用程式及其版本
* **裝置** 應用程式執行所在的裝置的相關資料 (在 Web 應用程式中，這是傳送遙測的伺服器或用戶端裝置)
* **InstrumentationKey** 識別 Azure 中遙測顯示之位置的 Application Insights 資源。 通常會從 ApplicationInsights.config 揀選
* **位置** 識別裝置的地理位置。
* **作業** 在 Web 應用程式中，目前的 HTTP 要求。 在其他應用程式類型中，您可以設定以將事件群組在一起。
  * **識別碼**：產生的值，與不同事件相互關聯，如此當您在 [診斷搜尋] 中檢查任何事件時，您可以發現「相關項目」
  * **名稱**：識別碼，通常是 HTTP 要求的 URL。
  * **SyntheticSource**：如果不為 null 或空白，這個字串表示要求的來源已被識別為傀儡程式或 Web 測試。 根據預設，會從計量瀏覽器的計算中排除。
* **屬性** 與所有遙測資料一起傳送的屬性。 可以在個別 Track* 呼叫中覆寫。
* **工作階段** 識別使用者的工作階段。 識別碼會設為產生的值，當使用者一段時間沒有作用時會變更。
* **使用者** 使用者資訊。

## <a name="limits"></a>限制
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

*如何避免達到資料速率限制？*

* 使用 [取樣](app-insights-sampling.md)。

*資料保留多久？*

* 請參閱[資料保留和隱私權][資料]。

## <a name="reference-docs"></a>參考文件
* [ASP.NET 參考](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java 參考](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [JavaScript 參考](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>SDK 程式碼
* [ASP.NET 核心 SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Windows Server 套件](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [所有平台](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>問題
* *Track_() 呼叫會擲回什麼例外狀況？*

    無。 您不需要將它們包裝在 try-catch 子句中。 如果 SDK 發生問題，它會記錄訊息，您可以在偵錯主控台輸出中查看。若訊息得以留存，也可在診斷搜尋中查看。
* *是否有 REST API 可供從入口網站中取得資料？*

    是，[資料存取 API](https://dev.applicationinsights.io/)。 其他擷取資料的方法包括[從分析匯出至 Power BI](app-insights-export-power-bi.md) 和[連續匯出](app-insights-export-telemetry.md)。

## <a name="a-namenextanext-steps"></a><a name="next"></a>接續步驟
[搜尋事件和記錄檔][diagnostic]

[範例和逐步解說](app-insights-code-samples.md)

[疑難排解][qna]

<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[資料]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md



<!--HONumber=Nov16_HO3-->


