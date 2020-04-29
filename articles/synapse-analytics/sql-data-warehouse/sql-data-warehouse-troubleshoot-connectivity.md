---
title: Verbindings problemen oplossen
description: Probleem oplossing voor connectiviteit in Synapse SQL-pool.
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
ms.openlocfilehash: d69c8dd28b946df3fff500c31c7cdefa4767c0c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408122"
---
# <a name="troubleshooting-connectivity-issues"></a>Connectiviteitsproblemen oplossen

Dit artikel bevat algemene probleemoplossings technieken om verbinding te maken met uw SQL Analytics-Data Base.

## <a name="check-service-availability"></a>Beschik baarheid van service controleren

Controleer of de service beschikbaar is. Ga in het Azure Portal naar de Synapse-SQL-groep waarmee u verbinding wilt maken. Klik in het deel venster links TOC op **problemen vaststellen en oplossen**.

![Resource status selecteren](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Hier wordt de status van uw Synapse SQL-pool weer gegeven. Als de service niet wordt weer gegeven als **beschikbaar**, raadpleegt u verdere stappen.

![Service beschikbaar](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Als in uw resource status blijkt dat uw Synapse-exemplaar van de SQL-groep is gepauzeerd of geschaald, volgt u de richt lijnen om uw exemplaar te hervatten.

![Service heeft](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) aanvullende informatie over resource Health gepauzeerd.

## <a name="check-for-paused-or-scaling-operation"></a>Controleren op onderbroken of schaalbewerking

Controleer de portal om te zien of uw Synapse-exemplaar van de SQL-groep is gepauzeerd of geschaald.

![De service is onderbroken](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Als u ziet dat uw service is gepauzeerd of geschaald, controleert u of deze niet tijdens de onderhouds planning wordt weer geven. Op de portal voor het *overzicht*van uw Synapse-SQL-groep ziet u de gekozen onderhouds planning.

![Onderhouds planning overzicht](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Neem anders contact op met uw IT-beheerder om te controleren of dit onderhoud geen geplande gebeurtenis is. Volg [deze stappen](pause-and-resume-compute-portal.md)om het exemplaar van SQL Analytics te hervatten.

## <a name="check-your-firewall-settings"></a>Uw firewallinstellingen controleren

SQL Analytics-Data Base communiceert via poort 1433.Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, is uitgaand verkeer via poort 1433 mogelijk niet toegestaan vanwege de firewall van het netwerk. In dat geval kunt u geen verbinding maken met uw Azure SQL Database-server, tenzij de IT-afdeling poort 1433 openstelt. Meer informatie over Firewall configuraties vindt u [hier](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>De instellingen van uw VNet/service-eindpunt controleren

Als u fouten 40914 en 40615 ontvangt, raadpleegt u de [fout beschrijving en oplossingen hier](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Controleren op de meest recente stuurprogramma's

### <a name="software"></a>Software

Controleer of u de nieuwste hulpprogram ma's gebruikt om verbinding te maken met uw Synapse-SQL-groep:

- SSMS
- Azure Data Studio
- SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Stuurprogramma's

Controleer of u de meest recente versies van het stuur programma gebruikt.Het gebruik van een oudere versie van de Stuur Programma's kan leiden tot onverwacht gedrag als de oudere Stuur Programma's mogelijk geen ondersteuning bieden voor nieuwe functies.

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="check-your-connection-string"></a>Uw verbindingsreeks controleren

Controleer of de verbindingsreeksen juist zijn ingesteld.  Hieronder ziet u enkele voor beelden.  Meer informatie over verbindingstekenreeksen vindt u [hier](sql-data-warehouse-connection-strings.md).

ADO.NET-verbindingsreeks

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC-verbindings reeks

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

PHP-verbindings reeks

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

JDBC-verbindingsreeks

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Onregelmatige verbindingsproblemen

Controleer of er sprake is van een zware belasting op de server met een groot aantal aanvragen in de wachtrij. Mogelijk moet u uw Synapse SQL-groep opschalen voor meer bronnen.

## <a name="common-error-messages"></a>Veelvoorkomende foutberichten

Fouten 40914 en 40615, Zie [hier de beschrijving en oplossing](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)van de fout.

## <a name="still-having-connectivity-issues"></a>Hebt u nog steeds verbindings problemen?

Maak een [ondersteunings ticket](sql-data-warehouse-get-started-create-support-ticket.md) zodat het technische team u kan ondersteunen.
