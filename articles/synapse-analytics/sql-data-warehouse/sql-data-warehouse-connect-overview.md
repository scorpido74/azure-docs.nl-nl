---
title: Verbinding maken met Synapse SQL-groep
description: Maak verbinding met SQL-pool.
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
ms.openlocfilehash: 70dd1ae883ee1f44672dccb802c0e770d7676440
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619092"
---
# <a name="connect-to-synapse-sql-pool"></a>Verbinding maken met Synapse SQL-groep
Maak verbinding met SQL-pool.

## <a name="find-your-server-name"></a>Uw servernaam vinden
De servernaam in het volgende voorbeeld is sqlpoolservername.database.windows.net. Ga als volgt te werk om de volledig gekwalificeerde servernaam te vinden:

1. Ga naar de [Azure-portal.](https://portal.azure.com)
2. Klik op **Azure Synapse Analytics**.
3. Klik op de SQL-groep waarmee u verbinding wilt maken.
4. Zoek de volledige servernaam.
   
    ![Volledige servernaam](media/sql-data-warehouse-connect-overview/server-connect.PNG)

## <a name="supported-drivers-and-connection-strings"></a>Ondersteunde stuurprogramma's en verbindingsreeksen
SQL-groep ondersteunt [ADO.NET,](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx) [ODBC,](https://msdn.microsoft.com/library/jj730314.aspx) [PHP](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396)en [JDBC](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx). Als u de nieuwste versie en documentatie wilt vinden, klikt u op een van de voorgaande stuurprogramma's. 

Als u de verbindingstekenreeks automatisch wilt genereren voor het stuurprogramma dat u gebruikt vanuit de Azure-portal, klikt u op de **tekenreeksen van de databaseverbinding weergeven** van het voorgaande voorbeeld. Hier volgen ook enkele voorbeelden van hoe een verbindingsreeks er voor elk stuurprogramma uitziet.

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
SQL-groep standaardiseert een aantal instellingen tijdens het maken van verbinding en objecten. Deze instellingen kunnen niet worden overschreven, en omvatten:

| Database-instelling | Waarde |
|:--- |:--- |
| [ANSI_NULLS](https://msdn.microsoft.com/library/ms188048.aspx) |AAN |
| [QUOTED_IDENTIFIERS](https://msdn.microsoft.com/library/ms174393.aspx) |AAN |
| [DATEFORMAT](https://msdn.microsoft.com/library/ms189491.aspx) |mdy |
| [DATEFIRST](https://msdn.microsoft.com/library/ms181598.aspx) |7 |

## <a name="next-steps"></a>Volgende stappen
Zie [Query’s uitvoeren met Visual Studio](sql-data-warehouse-query-visual-studio.md) als u verbinding wilt maken en een query wilt uitvoeren met Visual Studio. Zie Verificatie naar Azure [Synapse Analytics voor](sql-data-warehouse-authentication.md)meer informatie over verificatieopties.

