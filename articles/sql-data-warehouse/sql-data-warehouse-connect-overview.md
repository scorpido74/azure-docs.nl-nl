---
title: Verbinding maken met Azure SQL Data Warehouse
description: Maak verbinding met Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5a14b99753c9f06f2e0cf32dd8b5c7776cfdad89
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685872"
---
# <a name="connect-to-azure-sql-data-warehouse"></a>Verbinding maken met Azure SQL Data Warehouse
Maak verbinding met Azure SQL Data Warehouse.

## <a name="find-your-server-name"></a>Uw servernaam vinden
De naam van de server in het volgende voor beeld is samplesvr.database.windows.net. Ga als volgt te werk om de volledig gekwalificeerde servernaam te vinden:

1. Ga naar de [Azure Portal][Azure portal].
2. Klik op **SQL data Warehouses**.
3. Klik op de datawarehouse waarmee u verbinding wilt maken.
4. Zoek de volledige servernaam.
   
    ![Volledige servernaam][1]

## <a name="supported-drivers-and-connection-strings"></a>Ondersteunde stuurprogramma's en verbindingsreeksen
Azure SQL Data Warehouse biedt ondersteuning voor [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP][PHP] en [JDBC][JDBC]. Klik op een van de voor gaande Stuur Programma's om de meest recente versie en documentatie te vinden. Als u automatisch de connection string wilt genereren voor het stuur programma dat u gebruikt vanuit de Azure Portal, klikt u op de **verbindings reeksen Data Base weer geven** in het vorige voor beeld. Hier volgen ook enkele voorbeelden van hoe een verbindingsreeks er voor elk stuurprogramma uitziet.

> [!NOTE]
> Overweeg de verbindingstime-out in te stellen op 300 seconden. De verbinding blijft dan in stand tijdens korte perioden van niet-beschikbaarheid.
> 
> 

### <a name="adonet-connection-string-example"></a>Voorbeeld van ADO.NET-verbindingsreeks
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Voorbeeld van ODBC-verbindingsreeks
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Voorbeeld van PHP-verbindingsreeks
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Voorbeeld van JDBC-verbindingsreeks
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Verbindingsinstellingen
SQL Data Warehouse standaardiseert enkele instellingen tijdens het maken van de verbinding en het maken van objecten. Deze instellingen kunnen niet worden overschreven, en omvatten:

| Database-instelling | Waarde |
|:--- |:--- |
| [ANSI_NULLS][ANSI_NULLS] |AAN |
| [QUOTED_IDENTIFIERS][QUOTED_IDENTIFIERS] |AAN |
| [DATEFORMAT][DATEFORMAT] |mdy |
| [DATEFIRST][DATEFIRST] |7 |

## <a name="next-steps"></a>Volgende stappen
Zie [Query’s uitvoeren bij Visual Studio][Query with Visual Studio] als u verbinding wilt maken en een query wilt uitvoeren met Visual Studio. Zie [Verificatie met Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse] voor meer informatie over verificatieopties.

<!--Articles-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/server-connect.PNG


