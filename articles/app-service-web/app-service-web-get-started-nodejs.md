---
title: "在 5 分鐘內將您的 Node.js Web 應用程式部署到 Azure (CLI 2.0 預覽) | Microsoft Docs"
description: "藉由部署範例 Node.js 應用程式，了解在 App Service 中執行 Web 應用程式有多麼簡單。 快速開始進行真正的開發，並立即查看結果。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 412cc786-5bf3-4e1b-b696-6a08cf46501e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 06a8dfbac31024cb44fd38bcf9c4a4ea79dbc968


---
# <a name="deploy-your-first-nodejs-web-app-to-azure-in-five-minutes-cli-20-preview"></a>在 5 分鐘內，將您的第一個 Node.js Web 應用程式部署到 Azure (CLI 2.0 預覽)

> [!div class="op_single_selector"]
> * [第一個 HTML 網站](app-service-web-get-started-html.md)
> * [第一個 .NET 應用程式](app-service-web-get-started-dotnet.md)
> * [第一個 PHP 應用程式](app-service-web-get-started-php.md)
> * [第一個 Node.js 應用程式](app-service-web-get-started-nodejs.md)
> * [第一個 Python 應用程式](app-service-web-get-started-python.md)
> * [第一個 Java 應用程式](app-service-web-get-started-java.md)
> 
> 

本教學課程將協助您部署您的第一個 Node.js Web 應用程式至 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。
您可以使用 App Service 來建立 Web 應用程式、[行動應用程式後端](/documentation/learning-paths/appservice-mobileapps/)和 [Web 應用程式](../app-service-api/app-service-api-apps-why-best-platform.md)。

您將： 

* 在 Azure App Service 中建立 Web 應用程式。
* 部署範例 Node.js 程式碼。
* 看見您的程式碼在生產環境中即時執行。
* 以您 [推送 Git 認可](https://git-scm.com/docs/git-push)的相同方式，更新 Web 應用程式。

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本

您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](app-service-web-get-started-nodejs-cli-nodejs.md) – 適用於傳統和資源管理部署模型的 CLI
- [Azure CLI 2.0 (預覽)](app-service-web-get-started-nodejs.md) - 適用於資源管理部署模型的新一代 CLI

## <a name="prerequisites"></a>必要條件
* [Git](http://www.git-scm.com/downloads)。
* [Azure CLI 2.0 Preview](/cli/azure/install-az-cli2)。
* Microsoft Azure 帳戶。 如果您沒有這類帳戶，可以[註冊免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或是[啟用自己的 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

> [!NOTE]
> 您可以[試用 App Service](https://azure.microsoft.com/try/app-service/)，而不需要 Azure 帳戶。 建立入門 App，並試用長達一小時。不需要信用卡，也不需簽定合約。
> 
> 

## <a name="deploy-a-nodejs-web-app"></a>部署 Node.js Web 應用程式
1. 開啟新的 Windows 命令提示字元、PowerShell 視窗、Linux Shell 或 OS X 終端機。 執行 `git --version` 和 `azure --version`，確認電腦上已安裝 Git 和 Azure CLI。
   
    ![測試已在 Azure 中針對您的第一個 Web 應用程式安裝 CLI 工具](./media/app-service-web-get-started-languages/1-test-tools-2.0.png)
   
    如果您尚未安裝工具，請參閱 [必要條件](#Prerequisites) 以取得下載連結。
2. 如下所示，登入 Azure：
   
        az login
   
    依照說明訊息進行來繼續登入程序。
   
    ![登入 Azure 以建立第一個 Web 應用程式](./media/app-service-web-get-started-languages/3-azure-login-2.0.png)

3. 設定 App Service 的部署使用者。 稍後您將使用這些認證來部署程式碼。
   
        az appservice web deployment user set --user-name <username> --password <password>

3. 建立新的[資源群組](../azure-resource-manager/resource-group-overview.md)。 在這個第一個 App Service 教學課程中，您真的不需要知道它是什麼。

        az group create --location "<location>" --name my-first-app-group

    若要查看您可用於 `<location>` 的可能值，請使用 `az appservice list-locations`CLI 命令。

3. 建立新的「免費」[App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 在這個第一個 App Service 教學課程中，只需知道，您可以免費使用本方案中 Web 應用程式。

        az appservice plan create --name my-free-appservice-plan --resource-group my-first-app-group --sku FREE

4. 在 `<app_name>` 中使用唯一名稱建立新的 Web 應用程式。

        az appservice web create --name <app_name> --resource-group my-first-app-group --plan my-free-appservice-plan

4. 接下來，您會取得想要部署的 Node.js 範例程式碼。 切換至工作目錄 (`CD`)，並如下所示複製範例應用程式︰
   
        cd <working_directory>
        git clone https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git

5. 切換至範例 App 的儲存機制。
   
        cd app-service-web-nodejs-get-started
5. 使用下列命令，設定 App Service Web 應用程式的本機 Git 部署︰

        az appservice web source-control config-local-git --name <app_name> --resource-group my-first-app-group

    您會取得如下所示的 JSON 輸出，這表示已設定遠端 Git 儲存機制︰

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. 將 JSON 中的 URL 新增為本機儲存機制的 Git 遠端 (為了簡單起見，稱為 `azure`)。

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. 將範例程式碼部署至 Azure App，如同使用 Git 推送任何程式碼一樣。 出現提示時，使用您稍早設定的密碼。
   
        git push azure master
   
    ![將程式碼推送至您在 Azure 中的第一個 Web 應用程式](./media/app-service-web-get-started-languages/node-git-push.png)
   
    `git push` 不只將程式碼放在 Azure 中，也會在部署引擎中觸發部署工作。 
    如果您的專案 (儲存機制) 根目錄中有 package.json，部署指令碼會為您還原必要的套件。 

恭喜，您的應用程式已部署至 Azure App Service。

## <a name="see-your-app-running-live"></a>看見您的應用程式即時執行
若要查看 Azure 中即時執行的應用程式，請從儲存機制中的任何目錄執行此命令︰

    azure site browse

## <a name="make-updates-to-your-app"></a>更新您的應用程式
您現在可以使用 Git 隨時從您的專案 (儲存機制) 根目錄進行推送，以更新即時網站。 您可以使用第一次部署程式碼時的相同方式來執行這項作業。 例如，每次您想要推送已在本機測試的新變更時，只需從專案 (儲存機制) 根目錄執行下列命令︰

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>後續步驟
[建立、設定及部署 Node.js Express Web 應用程式到 Azure](app-service-web-nodejs-get-started.md)。 遵循本教學課程，您將學習在 Azure 中執行任何 Node.js Web 應用程式的基本技巧，例如：

* 從 PowerShell/Bash 在 Azure 中建立及設定 App。
* 設定 Node.js 版本。
* 使用不在應用程式根目錄中的啟動檔案。
* 使用 NPM 進行自動化。
* 取得錯誤和輸出記錄檔。

或者，進一步運用您的第一個 Web 應用程式。 例如：

* 嘗試 [將程式碼部署至 Azure 的其他方法](web-sites-deploy.md)。 例如，若要從您的其中一個 GitHub 儲存機制中部署，只需在 [部署選項] 中改為選取 [GitHub] 而不是 [本機 Git 儲存機制] 即可。
* 加強您 Azure App 的功能。 驗證您的使用者。 根據需求加以調整。 設定一些效能警示。 都只要點幾下滑鼠就能完成。 請參閱 [在您的第一個 Web 應用程式中新增功能](app-service-web-get-started-2.md)。




<!--HONumber=Jan17_HO3-->


