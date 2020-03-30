---
title: Connectiviteit oplossen van problemen
description: Problemen met connectiviteit oplossen in SQL Analytics.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 689a2e549c2627c607b6549f164e55a73318f63e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350050"
---
# <a name="troubleshooting-connectivity-issues"></a>Connectiviteitsproblemen oplossen

In dit artikel worden veelvoorkomende probleemoplossingstechnieken weergegeven rond verbinding maken met uw SQL Analytics-database.
- [Beschikbaarheid van de service controleren](sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Controleren op onderbroken of schaalbewerking](sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Uw firewallinstellingen controleren](sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [De instellingen van uw VNet/service-eindpunt controleren](sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Controleren op de meest recente stuurprogramma's](sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Uw verbindingsreeks controleren](sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Onregelmatige verbindingsproblemen](sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Veelvoorkomende foutberichten](sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Beschikbaarheid van de service controleren

Controleer of de service beschikbaar is. Ga in de Azure-portal naar de SQL Analytics-database die u probeert te verbinden. Klik in het linker-TOC-paneel op **Diagnosticeren en los problemen op.**

![Resourcestatus selecteren](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

De status van uw SQL Analytics wordt hier weergegeven. Als de service niet wordt weergegeven als **beschikbaar,** controleert u verdere stappen.

![Beschikbare service](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Als uit de resourcestatus blijkt dat uw SQL Analytics-exemplaar is onderbroken of geschaald, volgt u de richtlijnen om uw instantie te hervatten.

![Service onderbroken](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) Aanvullende informatie over resourcestatus vindt u hier.

## <a name="check-for-paused-or-scaling-operation"></a>Controleren op onderbroken of schaalbewerking

Controleer de portal om te zien of uw SQL Analytics-exemplaar is onderbroken of geschaald.

![Service onderbroken](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Als u ziet dat uw service is onderbroken of geschaald, controleert u of deze niet tijdens uw onderhoudsschema is. Op de portal voor uw SQL *Analytics-overzicht*ziet u het gekozen onderhoudsschema.

![Overzicht Onderhoudsschema](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Neem anders contact op met uw IT-beheerder om te controleren of dit onderhoud geen geplande gebeurtenis is. Volg de [hier](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute)beschreven stappen om de SQL Analytics-instantie te hervatten.

## <a name="check-your-firewall-settings"></a>Uw firewallinstellingen controleren

SQL Analytics-database communiceert via poort 1433.Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, is uitgaand verkeer via poort 1433 mogelijk niet toegestaan vanwege de firewall van het netwerk. In dat geval kunt u geen verbinding maken met uw Azure SQL Database-server, tenzij de IT-afdeling poort 1433 openstelt. Aanvullende informatie over firewallconfiguraties vindt u [hier.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

## <a name="check-your-vnetservice-endpoint-settings"></a>De instellingen van uw VNet/service-eindpunt controleren

Zie [foutbeschrijving en resolutie hier](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)als u fouten 40914 en 40615 ontvangt.

## <a name="check-for-the-latest-drivers"></a>Controleren op de meest recente stuurprogramma's

### <a name="software"></a>Software

Controleer of u de nieuwste tools gebruikt om verbinding te maken met uw SQL Analytics-database:

* SSMS
* Azure Data Studio
* SQL Server-gegevenshulpprogramma's (Visual Studio)

### <a name="drivers"></a>Stuurprogramma's

Controleer of u de nieuwste stuurprogrammaversies gebruikt.Het gebruik van een oudere versie van de stuurprogramma's kan leiden tot onverwacht gedrag, omdat de oudere stuurprogramma's mogelijk geen nieuwe functies ondersteunen.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [Php](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Uw verbindingsreeks controleren

Controleer of de verbindingsreeksen juist zijn ingesteld.  Hieronder staan enkele voorbeelden.  Meer informatie over verbindingstekenreeksen vindt u [hier](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

ADO.NET-verbindingsreeks

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC-verbindingstekenreeks

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

TEKENREEKS PHP-verbinding

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

JDBC-verbindingsreeks

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Onregelmatige verbindingsproblemen

Controleer of er sprake is van een zware belasting op de server met een groot aantal aanvragen in de wachtrij. Mogelijk moet u uw SQL Analytics-exemplaar opschalen voor extra bronnen.

## <a name="common-error-messages"></a>Veelvoorkomende foutberichten

Fouten 40914 en 40615, zie de [foutbeschrijving en resolutie hier](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Heb je nog steeds verbindingsproblemen?
Maak een [ondersteuningsticket](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) zodat het engineeringteam u kan ondersteunen.
