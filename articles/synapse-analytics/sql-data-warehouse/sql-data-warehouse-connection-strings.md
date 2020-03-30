---
title: Verbindingsreeksen
description: Verbindingstekenreeksen voor SQL Data Warehouse
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f64c584ea5a32f371f84facc195cd02ef6ffdff0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350624"
---
# <a name="connection-strings-for-azure-sql-data-warehouse"></a>Verbindingstekenreeksen voor Azure SQL Data Warehouse
U verbinding maken met SQL Data Warehouse met verschillende toepassingsprotocollen zoals [ADO.NET,][ADO.NET] [ODBC,][ODBC] [PHP][PHP] en [JDBC.][JDBC] Hieronder vindt u enkele voorbeelden van verbindingstekenreeksen voor elk protocol.  U de Azure-portal ook gebruiken om uw verbindingstekenreeks te maken.  Als u uw verbindingstekenreeks wilt maken met de Azure-portal, navigeert u naar uw databaseblad onder *Essentials* klik op *Tekenreeksen voor databaseverbindingen weergeven.*

## <a name="sample-adonet-connection-string"></a>Voorbeeld ADO.NET verbindingstekenreeks
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Voorbeeld van ODBC-verbindingstekenreeks
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Voorbeeld van PHP-verbindingstekenreeks
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Voorbeeld van JDBC-verbindingstekenreeks
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

> [!NOTE]
> Overweeg de time-out van de verbinding in te stellen op 300 seconden, zodat de verbinding korte perioden van onbeschikbaarheid kan overleven.
> 
> 

## <a name="next-steps"></a>Volgende stappen
Zie [Query’s uitvoeren bij Visual Studio][Query with Visual Studio] als u wilt beginnen met het uitvoeren van query’s bij uw datawarehouse met Visual Studio en andere toepassingen.

<!--Image references-->

<!--Azure.com references-->
[Query with Visual Studio]:sql-data-warehouse-query-visual-studio.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx

<!--Other references-->
