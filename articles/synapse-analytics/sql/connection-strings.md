---
title: Verbindings reeksen voor Synapse SQL (preview-versie)
description: Verbindings reeksen voor Synapse SQL (preview-versie)
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 8c1f13449c8ee5152e4e5b48af87f41004e599dc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424557"
---
# <a name="connection-strings-for-synapse-sql-preview"></a>Verbindings reeksen voor Synapse SQL (preview-versie)

U kunt verbinding maken met Synapse SQL (preview) met verschillende toepassings protocollen, zoals [ADO.net](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx), [ODBC](https://msdn.microsoft.com/library/jj730314.aspx), [php](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396)en [JDBC](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx). Hieronder ziet u enkele voor beelden van verbindings reeksen voor elk protocol. 

U kunt ook de Azure Portal gebruiken om uw connection string te bouwen.  Als u uw connection string wilt bouwen met behulp van de Azure Portal, gaat u naar de Blade data base en klikt u onder *Essentials* op *database verbindings reeksen weer geven*.

## <a name="sample-adonet-connection-string"></a>Voor beeld van ADO.NET connection string

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Voor beeld van ODBC-connection string

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Voor beeld PHP connection string

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Voor beeld van JDBC connection string

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

> [!NOTE]
> U kunt de time-out voor de verbinding instellen op 300 seconden om te zorgen dat de verbinding de korte tijd niet beschikbaar is.

## <a name="recommendations"></a>Aanbevelingen

Voor het uitvoeren van **SQL op aanvraag** -query's zijn de aanbevolen hulpprogram ma's [Azure Data Studio](get-started-azure-data-studio.md) en Azure Synapse Studio.

## <a name="next-steps"></a>Volgende stappen

Zie [query's uitvoeren met Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)om te beginnen met het uitvoeren van query's op uw analyses met Visual Studio en andere toepassingen.
