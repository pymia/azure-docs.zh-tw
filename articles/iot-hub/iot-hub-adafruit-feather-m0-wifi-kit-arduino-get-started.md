---
title: "開始使用 Adafruit Feather M0 WiFi Azure IoT 入門套件 | Microsoft Docs"
description: "開始使用 Adafruit Feather M0 WiFi、建立 Azure IoT 中樞，並將 Adafruit Feather M0 WiFi 連線到 IoT 中樞"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot 中樞, 開始使用物聯網, 物聯網教學課程, adafruit 物聯網, 開始使用 arduino"
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 9e8084fe26229ef9ed1676c0a3c34e0ee7be88b1
ms.openlocfilehash: 8689ee35e880b1aa774b09bb194b43f280b2af79


---
# <a name="get-started-with-your-arduino-board-adafruit-feather-m0-wifi"></a>開始使用 Arduino 面板︰Adafruit Feather M0 WiFi

在本教學課程中，您一開始會先了解使用 Arduino 面板的基本知識。 接著會了解如何使用 [Azure IoT 中樞](iot-hub-what-is-iot-hub.md)讓您的裝置順暢地與雲端連線。

## <a name="lesson-1-configure-your-device"></a>第 1 課：設定裝置
![第 1 課端對端圖表][Lesson-1-end-to-end-diagram]

在本課程中，您將以一種作業系統來設定 Arduino 面板、設定開發環境，並將應用程式部署至 Arduino 面板。

### <a name="configure-your-device"></a>設定裝置
設定 Arduino 面板以進行首次使用，方法是組裝面板、接上電源。

*預估完成時間：5 分鐘*

前往[設定裝置][configure-your-device]。

### <a name="get-the-tools"></a>取得工具
下載工具與軟體建置，以建置並部署您第一個適用於 Arduino 面板的應用程式。

*預估完成時間：20 分鐘*

前往[取得工具][get-the-tools]

### <a name="create-and-deploy-the-blink-application"></a>建立並部署閃爍應用程式
從 GitHub 複製範例 Arduino 閃爍應用程式，並使用 gulp 以將此應用程式部署至您的 Arduino 面板。 此範例應用程式會讓 GPIO #13 面板上的 LED 每兩秒閃爍一次。

*預估完成時間：5 分鐘*

前往[建立並部署閃爍應用程式][create-and-deploy-the-blink-application]。

## <a name="lesson-2-create-your-iot-hub"></a>第 2 課：建立 IoT 中樞
![第 2 課端對端圖表][lesson-2-end-to-end-diagram]

在本課程中，您會建立免費的 Azure 帳戶、佈建 Azure IoT 中樞，並在 IoT 中樞建立第一個裝置。

開始本課程前，請先完成第 1 課。

### <a name="get-the-azure-tools"></a>取得 Azure 工具
安裝 Azure 命令列介面 (Azure CLI)。

*預估完成時間：10 分鐘*

前往[取得 Azure 工具][get-azure-tools]。

### <a name="create-your-iot-hub-and-register-your-arduino-board"></a>建立 IoT 中樞並登錄您的 Arduino 面板
建立資源群組、佈建第一個 Azure IoT 中樞，並使用 Azure CLI 將第一個裝置新增至 IoT 中樞。

*預估完成時間：10 分鐘*

前往[建立 IoT 中樞並登錄您的 Arduino 面板][create-your-iot-hub-and-register-your-arduino-board]。

## <a name="lesson-3-send-device-to-cloud-messages"></a>第 3 課：傳送裝置到雲端訊息
![第 3 課端對端圖表][lesson-3-end-to-end-diagram]

在本課中，您會將訊息從 Arduino 面板傳送到 IoT 中樞。 您也會建立 Azure 函式應用程式，此應用程式會從 IoT 中樞取得傳入訊息，並將這些訊息寫入 Azure 表格儲存體。

開始本課程前，請先完成第 1 課和第 2 課。

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>建立 Azure 函數應用程式與 Azure 儲存體帳戶
使用 Azure Resource Manager 範本來建立 Azure 函數應用程式及 Azure 儲存體帳戶。

*預估完成時間：10 分鐘*

前往[建立 Azure 函式應用程式與 Azure 儲存體帳戶][create-an-azure-function-app-and-azure-storage-account]。

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>執行範例應用程式以傳送裝置到雲端訊息
將範例應用程式部署至 Arduino 面板，並執行該應用程式以傳送訊息至 IoT 中樞。

*預估完成時間：10 分鐘*

前往[執行範例應用程式以傳送裝置到雲端訊息][send-device-to-cloud-messages]。

### <a name="read-messages-persisted-in-azure-storage"></a>讀取保存在 Azure 儲存體中的訊息
當裝置到雲端訊息寫入 Azure 儲存體時對其進行監視。

*預估完成時間：5 分鐘*

前往[讀取保存在 Azure 儲存體中的訊息][read-messages-persisted-in-azure-storage]。

## <a name="lesson-4-send-cloud-to-device-messages"></a>第 4 課：傳送雲端到裝置訊息
![第 4 課端對端圖表][lesson-4-end-to-end-diagram]

本課程示範如何將訊息從 Azure IoT 中樞傳送至 Arduino 面板。 這些訊息會針對 GPIO #13 面板上的 LED，控制其開啟與關閉行為。 本課程中已為您準備好範例應用程式以完成這項工作。

開始本課程前，請先完成第 1 課、第 2 課和第 3 課。

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>執行範例應用程式以接收雲端到裝置訊息
＜課程 4＞中的範例應用程式會在 Arduino 面板上執行，並監視來自 IoT 中樞的傳入訊息。 新的 Gulp 工作會從 IoT 中樞將訊息傳送到 Arduino 面板來使 LED 閃爍。

*預估完成時間：10 分鐘*

前往[執行範例應用程式以接收雲端到裝置訊息][receive-cloud-to-device-messages]。

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>選讀區段：變更 LED 的開起與關閉行為
自訂訊息以變更 LED 的開啟與關閉行為。

*預估完成時間：10 分鐘*

前往[選讀區段：變更 LED 的開啟與關閉行為][change-the-on-and-off-behavior-of-the-led]。

## <a name="troubleshooting"></a>疑難排解
如果在課程期間遇到任何問題，您可以在[疑難排解][troubleshooting]一文中尋求解決方案。

<!-- Images and links -->

[Lesson-1-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson1.png
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[create-and-deploy-the-blink-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
[lesson-2-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson2.png
[get-azure-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
[create-your-iot-hub-and-register-your-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[lesson-3-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson3.png
[create-an-azure-function-app-and-azure-storage-account]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[send-device-to-cloud-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md
[read-messages-persisted-in-azure-storage]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-read-table-storage.md
[lesson-4-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson4.png
[receive-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md
[change-the-on-and-off-behavior-of-the-led]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-change-led-behavior.md
[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md


<!--HONumber=Dec16_HO2-->


