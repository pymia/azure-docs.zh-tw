---
title: "開始使用跨資料庫查詢 (垂直資料分割) | Microsoft Docs"
description: "如何使用具有垂直分割資料庫的彈性資料庫查詢"
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
ms.assetid: e5b44b10-c432-4f96-b20e-08615ff4d5dd
ms.service: sql-database
ms.custom: multiple databases
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: torsteng
translationtype: Human Translation
ms.sourcegitcommit: a877c17a503e58c49ae781aed61ed120d069c737
ms.openlocfilehash: 41c26ebb4737eaf0d198e920892b19da7c389a66


---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>開始使用跨資料庫查詢 (垂直資料分割) (預覽)
Azure SQL Database 彈性資料庫查詢 (預覽) 可讓您執行使用單一連接點跨越多個資料庫的 T-SQL 查詢。 本主題適用於 [垂直分割的資料庫](sql-database-elastic-query-vertical-partitioning.md)。  

完成時，您將：了解如何設定和使用 Azure SQL Database 以執行跨越多個相關資料庫的查詢。 

如需有關彈性資料庫查詢功能的詳細資訊，請參閱 [Azure SQL Database 彈性資料庫查詢概觀](sql-database-elastic-query-overview.md)。 

## <a name="create-the-sample-databases"></a>建立範例資料庫
開始使用前，我們需要在相同或不同的邏輯伺服器中建立兩個資料庫 **Customers** 和 **Orders**。   

在 **Orders** 資料庫上執行下列查詢，以建立 **OrderInformation** 資料表及輸入範例資料。 

    CREATE TABLE [dbo].[OrderInformation]( 
        [OrderID] [int] NOT NULL, 
        [CustomerID] [int] NOT NULL 
        ) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

現在，在 **Customers** 資料庫上執行下列查詢，以建立 **CustomerInformation** 資料表及輸入範例資料。 

    CREATE TABLE [dbo].[CustomerInformation]( 
        [CustomerID] [int] NOT NULL, 
        [CustomerName] [varchar](50) NULL, 
        [Company] [varchar](50) NULL 
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
    ) 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## <a name="create-database-objects"></a>建立資料庫物件
### <a name="database-scoped-master-key-and-credentials"></a>資料庫範圍的主要金鑰和認證
1. 在 Visual Studio 中開啟 SQL Server Management Studio 或 SQL Server Data Tools
2. 連接至 Orders 資料庫，並執行下列 T-SQL 命令：
   
        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
        WITH IDENTITY = '<username>', 
        SECRET = '<password>';  
   
    "username" 和 "password" 應該是用來登入 Customers 資料庫的使用者名稱和密碼。
    目前不支援使用 Azure Active Directory 與彈性查詢進行驗證。

### <a name="external-data-sources"></a>外部資料來源
若要建立外部資料來源，請在 Orders 資料庫上執行下列命令： 

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
        (TYPE = RDBMS, 
        LOCATION = '<server_name>.database.windows.net', 
        DATABASE_NAME = 'Customers', 
        CREDENTIAL = ElasticDBQueryCred, 
    ) ;

### <a name="external-tables"></a>外部資料表
在符合 CustomerInformation 資料表定義的 Orders 資料庫上，建立外部資料表：

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
    ( [CustomerID] [int] NOT NULL, 
      [CustomerName] [varchar](50) NOT NULL, 
      [Company] [varchar](50) NOT NULL) 
    WITH 
    ( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>執行範例彈性資料庫 T-SQL 查詢
一旦您已定義外部資料來源和外部資料表，您現在即可使用 T-SQL 來查詢外部資料表。 在 Orders 資料庫上執行此查詢： 

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
    FROM OrderInformation 
    INNER JOIN CustomerInformation 
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## <a name="cost"></a>成本
目前，彈性資料庫查詢功能算在 Azure SQL Database 的成本內。  

如需價格資訊，請參閱 [SQL Database 價格](https://azure.microsoft.com/pricing/details/sql-database)。 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->

<!--anchors-->



<!--HONumber=Dec16_HO2-->


