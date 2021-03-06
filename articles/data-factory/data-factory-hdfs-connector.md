---
title: "從內部部署的 HDFS 移動資料 | Microsoft Docs"
description: "深入了解如何使用 Azure Data Factory 從內部部署的 HDFS 移動資料"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: 0f0eaaa927ea73cec845dbb369dc2c4a7a8466ba


---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>使用 Azure Data Factory 從內部部署的 HDFS 移動資料
本文將概述如何使用 Azure Data Factory 中的複製活動將資料從內部部署 HDFS 移動到另一個資料存放區。 本文是根據 [資料移動活動](data-factory-data-movement-activities.md) 一文，該文呈現使用複製活動移動資料的一般概觀以及支援的資料存放區組合。

Data Factory 目前只支援將資料從內部部署 HDFS 移動到其他資料存放區，而不支援將資料從其他資料存放區移動到內部部署 HDFS。

## <a name="enabling-connectivity"></a>啟用連線
Data Factory 服務支援使用資料管理閘道器連接至內部部署 HDFS。 請參閱 [在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 一文來了解資料管理閘道和設定閘道的逐步指示。 即使 HDFS 裝載於 Azure IaaS VM 中，仍請使用閘道器與其連接。

雖然您可以在與 HDFS 相同的內部部署機器或 Azure VM 上安裝閘道器，仍建議您在個別的機器/Azure IaaS VM 上安裝。 在個別機器上安裝閘道器可減少資源爭用並改善效能。 當您在不同機器上安裝閘道器時，機器應該能夠存取具有 HDFS 的機器。

## <a name="copy-data-wizard"></a>複製資料精靈
要建立從內部部署 HDFS 複製資料的管線，最簡單的方法是使用複製資料精靈。 如需使用複製資料精靈建立管線的快速逐步解說，請參閱 [教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md) 。

以下範例提供可用來使用 [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 建立管線的範例 JSON 定義。 這些範例示範如何將資料從內部部署 HDFS 複製到 Azure Blob 儲存體。 不過，您可以在 Azure Data Factory 中使用複製活動，將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 所說的任何接收器。

## <a name="sample-copy-data-from-on-premises-hdfs-to-azure-blob"></a>範例：將資料從內部部署 HDFS 複製到 Azure Blob
此範例示範如何將資料從內部部署 HDFS 複製到 Azure Blob 儲存體。 不過，您可以在 Azure Data Factory 中使用複製活動， **直接** 將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 所說的任何接收器。  

此範例具有下列 Data Factory 實體：

1. [OnPremisesHdfs](#hdfs-linked-service-properties)類型的連結服務。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)類型的連結服務。
3. [FileShare](#hdfs-dataset-type-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
5. 具有使用 [FileSystemSource](#hdfs-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

此範例會每個小時將資料從內部部署 HDFS 複製到 Azure Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

第一步是設定資料管理閘道。 如需相關指示，請參閱 [在內部部署位置和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 。

**HDFS 連結服務** 此範例會使用 Windows 驗證。 請參閱 [HDFS 連結服務](#hdfs-linked-service-properties) 章節以了解您可以使用的各種不同類型的驗證。

    {
        "name": "HDFSLinkedService",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Windows",
                "userName": "Administrator",
                "password": "password",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }

**Azure 儲存體連結服務**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**HDFS 輸入資料集** 此資料集是指 HDFS 資料夾 DataTransfer/UnitTest/。 管線會將此資料夾中的所有檔案複製到目的地。

設定 “external”: ”true” 會通知 Data Factory 服務：這是 Data Factory 外部的資料集而且不是由 Data Factory 中的活動所產生。

    {
        "name": "InputDataset",
        "properties": {
            "type": "FileShare",
            "linkedServiceName": "HDFSLinkedService",
            "typeProperties": {
                "folderPath": "DataTransfer/UnitTest/"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval":  1
            }
        }
    }




**Azure Blob 輸出資料集**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。 資料夾路徑會使用開始時間的年、月、日和小時部分。

    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**具有複製活動的管線**

此管線包含「複製活動」，該活動已設定為使用這些輸入和輸出資料集，並且排定為每小時執行。 在管線 JSON 定義中，**source** 類型設為 **FileSystemSource**，而 **sink** 類型設為 **BlobSink**。 針對 **query** 屬性指定的 SQL 查詢會選取過去一小時內要複製的資料。

    {
        "name": "pipeline",
        "properties":
        {
            "activities":
            [
                {
                    "name": "HdfsToBlobCopy",
                    "inputs": [ {"name": "InputDataset"} ],
                    "outputs": [ {"name": "OutputDataset"} ],
                    "type": "Copy",
                    "typeProperties":
                    {
                        "source":
                        {
                            "type": "FileSystemSource"
                        },
                        "sink":
                        {
                            "type": "BlobSink"
                        }
                    },
                    "policy":
                    {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1,
                        "timeout": "00:05:00"
                    }
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="hdfs-linked-service-properties"></a>HDFS 連結服務屬性
下表提供 HDFS 連結服務專屬 JSON 元素的描述。

| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| 類型 |類型屬性必須設為： **Hdfs** |是 |
| Url |到 HDFS 的 URL |是 |
| encryptedCredential |[New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) 輸出。 |否 |
| userName |Windows 驗證的使用者名稱。 |是 (適用於 Windows 驗證) |
| password |Windows 驗證的密碼。 |是 (適用於 Windows 驗證) |
| authenticationType |Windows 或匿名。 |是 |
| gatewayName |Data Factory 服務應該用來連接到 HDFS 的閘道器名稱。 |是 |

如需設定內部部署 HDFS 認證的詳細資訊，請參閱[利用資料管理閘道在內部部署來源和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)。

### <a name="using-anonymous-authentication"></a>使用匿名驗證
    {
        "name": "hdfs",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Anonymous",
                "userName": "hadoop",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }


### <a name="using-windows-authentication"></a>使用 Windows 驗證
    {
        "name": "hdfs",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Windows",
                "userName": "Administrator",
                "password": "password",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }



## <a name="hdfs-dataset-type-properties"></a>HDFS 資料集類型屬性
如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的 **typeProperties** 區段都不同，可提供資料存放區中的資料位置資訊。 **FileShare** 資料集類型 (包含 HDFS 資料集) 的 typeProperties 區段具有下列屬性。

| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| folderPath |資料夾的路徑。 範例：`myfolder`<br/><br/>使用逸出字元 ‘ \ ’ 當做字串中的特殊字元。 例如︰若為 folder\subfolder，請指定 folder\\\\subfolder；若為 d:\samplefolder，請指定 d:\\\\samplefolder。<br/><br/>您可以結合此屬性與 **partitionBy**，讓資料夾路徑以配量開始/結束日期時間為基礎。 |是 |
| fileName |如果您想要資料表參考資料夾中的特定檔案，請指定 **folderPath** 中的檔案名稱。 如果沒有為此屬性指定任何值，資料表會指向資料夾中的所有檔案。<br/><br/>若未指定輸出資料集的 fileName，所產生檔案的名稱是下列格式︰ <br/><br/>Data.<Guid>.txt (例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |否 |
| partitionedBy |partitionedBy 可以用來指定時間序列資料的動態 folderPath 和 filename。 範例：folderPath 可針對每小時的資料進行參數化。 |否 |
| fileFilter |指定要用來在 folderPath (而不是所有檔案) 中選取檔案子集的篩選器。 <br/><br/>允許的值為︰`*` (多個字元) 和 `?` (單一字元)。<br/><br/>範例 1：`"fileFilter": "*.log"`<br/>範例 2：`"fileFilter": 2014-1-?.txt"`<br/><br/>**注意**：fileFilter 適用於輸入 FileShare 資料集 |否 |
| format |支援下列格式類型：**TextFormat**、**AvroFormat**、**JsonFormat**、**OrcFormat**、**ParquetFormat**。 將格式下的 **type** 屬性設定為這些值其中之一。 如需詳細資料，請參閱[指定 TextFormat](#specifying-textformat)、[指定 AvroFormat](#specifying-avroformat)、[指定 JsonFormat](#specifying-jsonformat)、[指定 OrcFormat](#specifying-orcformat)、[指定 ParquetFormat](#specifying-parquetformat) 各節。 如果您想要在以檔案為基礎的存放區之間依原樣複製檔案 (二進位複本)，您可以在輸入和輸出資料集定義中略過格式區段。 |否 |
| compression |指定此資料的壓縮類型和層級。 支援的類型為：**GZip**、**Deflate** 和 **BZip2**，而支援的層級為：**最佳**和**最快**。 **AvroFormat** 或 **OrcFormat** 格式的資料目前不支援壓縮設定。 如需詳細資訊，請參閱 [壓縮支援](#compression-support) 一節。 |否 |

> [!NOTE]
> 無法同時使用檔名和 fileFilter。
>
>

### <a name="using-partionedby-property"></a>使用 partionedBy 屬性
如上一節所述，您可以利用 partitionedBy 指定時間序列資料的動態 folderPath 和 filename。 您可以使用 Data Factory 巨集和系統變數以及可指出給定資料配量的邏輯時間週期的 SliceStart、SliceEnd 來這麼做。

若要進一步了解時間序列資料集、排程和配量，請參閱[建立資料集](data-factory-create-datasets.md)、[排程和執行](data-factory-scheduling-and-execution.md)以及[建立管線](data-factory-create-pipelines.md)等文章。

#### <a name="sample-1"></a>範例 1：
    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy":
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

在此範例中，{Slice} 會取代成 Data Factory 系統變數 SliceStart 的值 (使用指定的格式 (YYYYMMDDHH))。 SliceStart 是指配量的開始時間。 每個配量的 folderPath 都不同。 例如：wikidatagateway/wikisampledataout/2014100103 或 wikidatagateway/wikisampledataout/2014100104。

#### <a name="sample-2"></a>範例 2：
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],

在此範例中，SliceStart 的年、月、日和時間會擷取到 folderPath 和 fileName 屬性所使用的個別變數。

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="hdfs-copy-activity-type-properties"></a>HDFS 複製活動類型屬性
如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。 屬性 (例如名稱、描述、輸入和輸出資料表，以及原則) 適用於所有類型的活動。

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同。 就「複製活動」而言，這些屬性會根據來源和接收器的類型而有所不同。

在複製活動中，如果來源的類型為 **FileSystemSource** ，則 typeProperties 區段會有下列可用屬性：

**FileSystemSource** 支援下列屬性：

| 屬性 | 說明 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| 遞迴 |表示是否從子資料夾，或只有從指定的資料夾，以遞迴方式讀取資料。 |True/False (預設值為 False) |否 |

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>效能和微調
請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)一文，以了解在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法。



<!--HONumber=Nov16_HO3-->


