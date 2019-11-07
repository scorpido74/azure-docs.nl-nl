---
title: Verbindings problemen oplossen
description: Probleem oplossing voor connectiviteit in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d1139032176b3b44c58471b87cabd10ffeaa3d20
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692422"
---
# <a name="troubleshooting-connectivity-issues"></a>Verbindings problemen oplossen

In dit artikel vindt u algemene technieken voor het oplossen van problemen om verbinding te maken met uw SQL Data Warehouse.
- [Beschik baarheid van service controleren](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Controleren op onderbroken of schaalbewerking](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Uw firewallinstellingen controleren](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [De instellingen van uw VNet/service-eindpunt controleren](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Controleren op de meest recente stuurprogramma's](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Uw verbindingsreeks controleren](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Onregelmatige verbindingsproblemen](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Veelvoorkomende foutberichten](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Beschik baarheid van service controleren

Controleer of de service beschikbaar is. Ga in het Azure Portal naar de SQL Data Warehouse waarmee u verbinding wilt maken. Klik in het deel venster links TOC op **problemen vaststellen en oplossen**.

![Resource status selecteren](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Hier wordt de status van uw SQL Data Warehouse weer gegeven. Als de service niet wordt weer gegeven als **beschikbaar**, raadpleegt u verdere stappen.

![Service beschikbaar](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Als uw resource status laat zien dat uw data warehouse is gepauzeerd of geschaald, volgt u de richt lijnen om uw data warehouse te hervatten.

![-service is onderbroken](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) meer informatie over Resource Health vindt u hier.

## <a name="check-for-paused-or-scaling-operation"></a>Controleren op onderbroken of schaal bewerking

Controleer de portal om te zien of uw SQL Data Warehouse is gepauzeerd of geschaald.

![De service is onderbroken](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Als u ziet dat uw service is gepauzeerd of geschaald, controleert u of deze niet tijdens de onderhouds planning wordt weer geven. Op de portal voor uw SQL Data Warehouse- *overzicht*ziet u de gekozen onderhouds planning.

![Onderhouds planning overzicht](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Neem anders contact op met uw IT-beheerder om te controleren of dit onderhoud geen geplande gebeurtenis is. Volg de stappen die [hier](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute)worden beschreven om de SQL Data Warehouse te hervatten.

## <a name="check-your-firewall-settings"></a>Controleer de firewall instellingen

SQL Database Warehouse communiceert via poort 1433.   Als u verbinding probeert te maken vanuit een bedrijfs netwerk, wordt uitgaand verkeer via poort 1433 mogelijk niet toegestaan door de firewall van uw netwerk. In dat geval kunt u geen verbinding maken met uw Azure SQL Database-server, tenzij de IT-afdeling poort 1433 openstelt. Meer informatie over Firewall configuraties vindt u [hier](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Controleer de instellingen van uw VNet/service-eind punt

Als u fouten 40914 en 40615 ontvangt, raadpleegt u de [fout beschrijving en oplossingen hier](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Controleren op de meest recente Stuur Programma's

### <a name="software"></a>Software

Controleer of u de nieuwste hulpprogram ma's gebruikt om verbinding te maken met uw SQL Data Warehouse:

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Stuurprogramma's

Controleer of u de meest recente versies van het stuur programma gebruikt.  Het gebruik van een oudere versie van de Stuur Programma's kan leiden tot onverwacht gedrag als de oudere Stuur Programma's mogelijk geen ondersteuning bieden voor nieuwe functies.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Controleer uw connection string

Controleer of de verbindingsreeksen juist zijn ingesteld.  Hieronder ziet u enkele voor beelden.  Meer informatie over verbindingstekenreeksen vindt u [hier](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

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

## <a name="intermittent-connection-issues"></a>Onregelmatige verbindings problemen

Controleer of er sprake is van een zware belasting op de server met een groot aantal aanvragen in de wachtrij. Mogelijk moet u uw datawarehouse opschalen voor aanvullende resources.

## <a name="common-error-messages"></a>Veelvoorkomende foutberichten

Fouten 40914 en 40615, Zie [hier de beschrijving en oplossing](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)van de fout.

## <a name="still-having-connectivity-issues"></a>Hebt u nog steeds verbindings problemen?
Maak een [ondersteunings ticket](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) zodat het technische team u kan ondersteunen.
