---
title: 針對 Windows VM 擴充功能的失敗進行疑難排解 | Microsoft Docs
description: 了解如何針對 Azure Windows VM 擴充功能的失敗進行疑難排解
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: timlt
editor: ''
tags: top-support-issue,azure-resource-manager

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap

---
# 針對 Azure Windows VM 擴充功能的失敗進行疑難排解
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## 檢視擴充功能狀態
Azure Resource Manager 範本可以從 Azure PowerShell 執行。一旦執行範本之後，即可以從 Azure 資源總管或命令列工具檢視延伸模組狀態。

下列是一個範例：

Azure PowerShell：

      Get-AzureRmVM -ResourceGroupName $RGName -Name $vmName -Status

以下是範例輸出：

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## 針對擴充功能失敗進行疑難排解
### 在 VM 上重新執行擴充功能
如果您使用自訂指令碼擴充功能在 VM 上執行指令碼，有時候可能會遇到雖然成功建立了 VM 但指令碼卻失敗的錯誤。在這樣的情況下，若要從錯誤中復原，建議您移除延伸模組並再次重新執行範本。請注意：未來將增強這項功能，以移除對解除安裝延伸模組的需求。

#### 從 Azure PowerShell 移除擴充功能
    Remove-AzureRmVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

一旦移除了延伸模組，範本就可以重新執行並在 VM 上執行指令碼。

<!---HONumber=AcomDC_0608_2016-->