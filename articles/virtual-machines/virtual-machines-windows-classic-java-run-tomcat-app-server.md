---
title: "虛擬機器上的 Tomcat | Microsoft Docs"
description: "本教學課程使用以傳統部署模型建立的資源，示範如何建立 Windows 虛擬機器，並將其設定為執行 Apache Tomcat 應用程式伺服器。"
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: d627aa09-f7d6-4239-8110-f8fc5111b939
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: 75388bba3084099fcea8d9ec1f3a2c1e909e914f


---
# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>如何在以傳統部署模型建立的虛擬機器上執行 Java 應用程式伺服器
> [!IMPORTANT] 
> Azure 建立和處理資源的部署模型有二種： [資源管理員和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用資源管理員模式。 如需 Resource Manager 範本使用 Java 8 與 Tomcat 部署 webapp 的詳細資訊，請參閱[這裡](https://azure.microsoft.com/documentation/templates/201-web-app-java-tomcat/)。

Azure 可讓您利用虛擬機器來提供伺服器功能。 例如，於 Azure 上執行的虛擬機器在經過設定後可代管 Apache Tomcat 等 Java 應用程式伺服器。 完成本指南的內容後，您將了解如何建立於 Azure 上執行的虛擬機器，以及該如何設定才能執行 Java 應用程式伺服器。

您將了解：

* 如何建立已安裝 Java Development Kit (JDK) 的虛擬機器。
* 如何從遠端登入虛擬機器。
* 如何將 Java 應用程式伺服器安裝在虛擬機器上。
* 如何為虛擬機器建立端點。
* 如何在防火牆中為應用程式伺服器開啟連接埠。

本教學課程的目的是在虛擬機器上安裝 Apache Tomcat 應用程式伺服器。 完成安裝作業會產生如下的 Tomcat 安裝。

![執行 Apache Tomcat 的虛擬機器][virtual_machine_tomcat]

[!INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>建立虛擬機器
1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 依序按一下 [新增]、[計算]、[虛擬機器] 及 [從組件庫]。
3. 在 [虛擬機器映像選取] 對話方塊中，選取 [JDK 7 Windows Server 2012]。
   請注意，如果您的舊版應用程式尚未準備好在 JDK 7 中運作，可以使用 [JDK 6 Windows Server 2012  ]。
4. 按 [下一步] 。
5. 在 [虛擬機器組態]  對話方塊中：
   1. 指定虛擬機器的名稱。
   2. 指定要用於虛擬機器的大小。
   3. 在 [使用者名稱]  欄位中輸入系統管理員的名稱。 請記住您即將輸入的名稱和密碼，因為當從遠端登入此虛擬機器時將會需要用到。
   4. 在 [新增密碼] 欄位中輸入密碼，然後在 [確認] 欄位中再輸入一次。 此為系統管理員帳戶的密碼。
   5. 按 [下一步] 。
6. 在下一個 [虛擬機器組態]  對話方塊中：
   1. 針對 [雲端服務]，使用預設值 [建立新的雲端服務]。
   2. [Cloud service DNS name]  的值在整個 cloudapp.net 中必須是唯一的。 必要時請修改此值，使 Azure 指出該值是唯一的。
   3. 指定區域、同質群組或虛擬網路。 為達成本教學課程的目的，請指定 **美國西部**之類的區域。
   4. 針對 [儲存體帳戶]，選取 [使用自動產生的儲存體帳戶]。
   5. 針對 [可用性設定組]，選取 [(無)]。
   6. 按 [下一步] 。
7. 在最終的 [虛擬機器組態]  對話方塊中：
   1. 接受預設的端點項目。
   2. 按一下 [完成] 。

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>從遠端登入虛擬機器
1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 按一下 [虛擬機器] 。
3. 按一下要登入的虛擬機器名稱。
4. 在啟動虛擬機器後，頁面底部的快顯功能表可允許連接。
5. 按一下 [連接] 。
6. 視需要回應提示以連接虛擬機器。 這應會需要儲存或開啟包含連線詳細資料的 .rdp 檔案。 您可能必須將 url:port 複製為 .rdp 檔案中第一行的最後一個部分，並將其貼入遠端登入應用程式中。

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>將 Java 應用程式伺服器安裝在虛擬機器上
您可以將 Java 應用程式伺服器複製到虛擬機器，或您也可以透過安裝程式安裝 Java 應用程式伺服器。

基於本教學課程的目的，我們將安裝 Tomcat。

1. 登入虛擬機器後，開啟瀏覽器工作階段並前往 [Apache Tomcat](http://tomcat.apache.org/download-70.cgi)。
2. 按兩下 [32-bit/64-bit Windows Service Installer] 的連結。 在使用本技術的前提下，Tomcat 將以 Windows 服務的形式安裝。
3. 出現提示時，選擇執行安裝程式。
4. 在 [Apache Tomcat Setup]  精靈中，按照提示安裝 Tomcat。 基於本教學課程的目的，接受預設值即可。 待 [完成 Apache Tomcat 設定精靈] 對話方塊出現時，您可以選擇性勾選 [執行 Apache Tomcat]，以立即啟動 Tomcat。 按一下 [完成]  ，完成 Tomcat 安裝程序。

## <a name="to-start-tomcat"></a>啟動 Tomcat
如果您未在 [完成 Apache Tomcat 設定精靈] 對話方塊中選擇執行 Tomcat，請在虛擬機器上開啟命令提示字元，然後執行 **net start Tomcat7** 予以啟動。

如果您現在執行虛擬機器的瀏覽器並開啟 <http://localhost:8080>，應該可以看見執行中的 Tomcat。

若要從外部機器看見執行中的 Tomcat，您需要建立端點並開啟連接埠。

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>為虛擬機器建立端點
1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 按一下 [虛擬機器] 。
3. 按一下執行 Java 應用程式伺服器之虛擬機器的名稱。
4. 按一下 [端點] 。
5. 按一下 [新增] 。
6. 在 [新增端點] 對話方塊中，確認已選取 [新增獨立端點]，然後按 [下一步]。
7. 在 [新端點詳細資訊  ] 對話方塊中：
   1. 指定端點的名稱，如 **HttpIn**。
   2. 指定 [TCP]  通訊協定。
   3. 指定公用連接埠 [80]  。
   4. 指定私人連接埠 [8080]  。
   5. 按一下 [ **完成** ] 按鈕以關閉對話方塊。 系統隨即會建立端點。

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>在防火牆中為虛擬機器開啟連接埠
1. 登入虛擬機器。
2. 按一下 Windows [開始] 。
3. 按一下 [控制台] 。
4. 依序按一下 [系統及安全性]、[Windows 防火牆] 及 [進階設定]。
5. 按一下 [輸入規則]，然後按一下 [新增規則]。
   ![新增輸入規則][NewIBRule]
6. 針對 [規則類型]，選取 [連接埠]，然後按 [下一步]。
   ![新增輸入規則連接埠][NewRulePort]
7. 在 [通訊協定和連接埠] 畫面上，選取 [TCP]，在 [特定本機連接埠] 中指定 **8080**，然後按 [下一步]。
   ![新增輸入規則][NewRuleProtocol]
8. 在 [動作] 畫面上，選取 [允許連接]，然後按 [下一步]。
   ![新增輸入規則動作][NewRuleAction]
9. 在 [設定檔] 畫面上，確定已選取 [網域]、[私人] 和 [公用]，然後按 [下一步]。
   ![新增輸入規則設定檔][NewRuleProfile]
10. 在 [名稱] 畫面上，指定規則的名稱，例如 **HttpIn** (不過規則名稱不一定要與端點名稱相符)，然後按一下 [完成]。  
    ![新增輸入規則名稱][NewRuleName]

此時，您應該可以使用 **http://*your\_DNS\_name*.cloudapp.net** 格式的 URL (其中 ***your\_DNS\_name*** 代表在建立虛擬機器時指定的 DNS 名稱)，從外部瀏覽器檢視 Tomcat 網站。

## <a name="application-lifecycle-considerations"></a>應用程式生命週期考量
* 您可以自行建立 Web 應用程式封存檔 (WAR)，然後再將其新增至 **webapps** 資料夾。 例如，建立基本 Java Service Page (JSP) 動態 Web 專案並以 WAR 檔案形式將其匯入，將 WAR 複製到虛擬機器上的 Apache Tomcat **webapps** 資料夾，然後在瀏覽器中予以執行。
* 依預設，當您安裝 Tomcat 服務時，系統會將其設定為手動啟動。 您可以使用 [服務] 嵌入式管理單元將其切換為自動啟動。 依序按一下 Windows [開始]、[系統管理工具] 及 [服務] 以啟動 [服務] 嵌入式管理單元。 按兩下 **Apache Tomcat** 服務，並將 [啟動類型] 設為 [自動]。
  
    ![將服務設定為自動啟動][service_automatic_startup]
  
    使 Tomcat 自動啟動的優點在於，當虛擬機器重新開機時 (例如安裝需要重新開機的軟體更新後) 其將會啟動。

## <a name="next-steps"></a>後續步驟
檢視 [Java 開發人員中心](https://azure.microsoft.com/develop/java/)中的資訊，了解您可能會想要納入 Java 應用程式的其他服務 (例如 Azure 儲存體、服務匯流排和 SQL Database)。

[virtual_machine_tomcat]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProfile.png



<!--HONumber=Dec16_HO1-->


