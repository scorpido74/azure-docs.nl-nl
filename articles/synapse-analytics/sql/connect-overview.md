---
title: Verbinding maken met Synapse SQL
description: Maak verbinding met Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 9748b0354ce09752296fb7d736e09af716f19351
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424606"
---
# <a name="connect-to-synapse-sql"></a>Verbinding maken met Synapse SQL
Maak verbinding met de Synapse-SQL-mogelijkheid in azure Synapse Analytics.

## <a name="supported-tools-for-sql-on-demand-preview"></a>Ondersteunde hulpprogram ma's voor SQL on-demand (preview)

Het volledig ondersteunde hulp programma is Azure Data Studio (preview).

SQL Server Management Studio wordt gedeeltelijk ondersteund vanuit versie 18,4. Er zijn beperkte functies, zoals verbinding maken en query's uitvoeren.

## <a name="find-your-server-name"></a>Uw servernaam vinden

De server naam voor SQL-groep in het volgende voor beeld is: showdemoweu.sql.azuresynapse.net.
De server naam voor SQL on-demand in het volgende voor beeld is: showdemoweu-ondemand.sql.azuresynapse.net.

Ga als volgt te werk om de volledig gekwalificeerde servernaam te vinden:

1. Ga naar de [Azure Portal](https://portal.azure.com).
2. Klik op **Synapse-werk ruimten**.
3. Klik op de werk ruimte waarmee u verbinding wilt maken.
4. Ga naar overzicht.
5. Zoek de volledige servernaam.

## <a name="sql-pool"></a>**SQL-pool**

![Volledige servernaam](./media/connect-overview/server-connect-example.png)

## <a name="sql-on-demand"></a>**SQL on-demand**

![Volledige server naam SQL on-demand](./media/connect-overview/server-connect-example-sqlod.png)

## <a name="supported-drivers-and-connection-strings"></a>Ondersteunde stuurprogramma's en verbindingsreeksen
Synapse SQL ondersteunt [ADO.net](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx), [ODBC](https://msdn.microsoft.com/library/jj730314.aspx), [php](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396)en [JDBC](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx). Klik op een van de voor gaande Stuur Programma's om de meest recente versie en documentatie te vinden. Als u automatisch de connection string wilt genereren voor het stuur programma dat u gebruikt vanuit de Azure Portal, klikt u op de **verbindings reeksen Data Base weer geven** in het vorige voor beeld. Hier volgen ook enkele voorbeelden van hoe een verbindingsreeks er voor elk stuurprogramma uitziet.

> [!NOTE]
> Overweeg de verbindingstime-out in te stellen op 300 seconden. De verbinding blijft dan in stand tijdens korte perioden van niet-beschikbaarheid.

### <a name="adonet-connection-string-example"></a>Voorbeeld van ADO.NET-verbindingsreeks

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Voorbeeld van ODBC-verbindingsreeks

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Voorbeeld van PHP-verbindingsreeks

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Voorbeeld van JDBC-verbindingsreeks

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Verbindingsinstellingen
Synapse SQL standaardisert enkele instellingen tijdens het maken van de verbinding en het aanmaken van het object. Deze instellingen kunnen niet worden overschreven, en omvatten:

| Database-instelling | Waarde |
|:--- |:--- |
| [ANSI_NULLS](/sql/t-sql/statements/set-ansi-nulls-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |AAN |
| [QUOTED_IDENTIFIERS](/sql/t-sql/statements/set-quoted-identifier-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |AAN |
| [DATEFORMAT](/sql/t-sql/statements/set-dateformat-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |mdy |
| [DATEFIRST](/sql/t-sql/statements/set-datefirst-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |7 |

## <a name="recommendations"></a>Aanbevelingen

Voor het uitvoeren van **SQL op aanvraag** -query's zijn de aanbevolen hulpprogram ma's [Azure Data Studio](get-started-azure-data-studio.md) en Azure Synapse Studio.

## <a name="next-steps"></a>Volgende stappen
Zie [Query’s uitvoeren met Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) als u verbinding wilt maken en een query wilt uitvoeren met Visual Studio. Zie [verificatie voor Synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie over verificatie opties.