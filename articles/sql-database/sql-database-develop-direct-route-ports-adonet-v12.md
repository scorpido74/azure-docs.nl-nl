---
title: Havens na 1433
description: Clientverbindingen van ADO.NET naar Azure SQL Database kunnen de proxy omzeilen en rechtstreeks met de database communiceren met andere poorten dan 1433.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 04/03/2019
ms.openlocfilehash: c0012b61cf43d01afd5e7f5f52948310b5eb8420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73828063"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Poorten boven 1433 voor ADO.NET 4.5

In dit onderwerp wordt het verbindingsgedrag van Azure SQL Database beschreven voor clients die ADO.NET 4.5 of een latere versie gebruiken.

> [!IMPORTANT]
> Zie [Azure SQL Database-connectiviteitsarchitectuur](sql-database-connectivity-architecture.md)voor informatie over connectiviteitsarchitectuur.
>

## <a name="outside-vs-inside"></a>Buiten vs binnen

Voor verbindingen met Azure SQL Database moeten we eerst vragen of uw clientprogramma *buiten* of *binnen* de Azure-cloudgrens wordt uitgevoerd. De onderafdelingen bespreken twee veelvoorkomende scenario's.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*Buiten:* Client wordt uitgevoerd op uw desktopcomputer

Poort 1433 is de enige poort die moet worden geopend op uw desktopcomputer en die uw SQL Database-clienttoepassing host.

### <a name="inside-client-runs-on-azure"></a>*Binnen:* Client wordt uitgevoerd op Azure

Wanneer uw client binnen de Azure-cloudgrens wordt uitgevoerd, wordt een *directe route* gebruikt om te communiceren met de SQL Database-server. Nadat een verbinding tot stand is gebracht, hebben verdere interacties tussen de client en de database geen Azure SQL Database Gateway nodig.

De volgorde is als volgt:

1. ADO.NET 4.5 (of hoger) een korte interactie met de Azure-cloud initieert en een dynamisch geïdentificeerd poortnummer ontvangt.

   * Het dynamisch geïdentificeerde poortnummer ligt tussen 11000 en 11999.
2. ADO.NET maakt dan rechtstreeks verbinding met de SQL Database-server, zonder middleware ertussen.
3. Query's worden rechtstreeks naar de database verzonden en de resultaten worden rechtstreeks naar de client geretourneerd.

Controleer of de poortbereiken van 11000-11999 op uw Azure-clientmachine beschikbaar blijven voor ADO.NET 4,5 clientinteracties met SQL Database.

* Met name poorten in het assortiment moeten vrij zijn van andere uitgaande blokkers.
* Op uw Azure VM bepaalt de **Windows Firewall met geavanceerde beveiliging** de poortinstellingen.
  
  * U de [gebruikersinterface van](https://msdn.microsoft.com/library/cc646023.aspx) de firewall gebruiken om een regel toe te voegen waarvoor u het **TCP-protocol** opgeeft, samen met een poortbereik met de syntaxis zoals **11000-11999**.

## <a name="version-clarifications"></a>Versie verduidelijkingen

In dit gedeelte worden de bijnaams verduidelijkt die verwijzen naar productversies. Het bevat ook een aantal combinaties van versies tussen producten.

### <a name="adonet"></a>ADO.NET

* ADO.NET 4.0 ondersteunt het TDS 7.3 protocol, maar niet 7.4.
* ADO.NET 4.5 en later ondersteunt het TDS 7.4 protocol.

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 of hoger

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4.2 of hoger (JDBC 4.0 ondersteunt eigenlijk TDS 7.4, maar implementeert geen "omleiding")

## <a name="related-links"></a>Verwante koppelingen

* ADO.NET 4.6 werd uitgebracht op 20 juli 2015. Een blog aankondiging van de .NET team is [hier](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-net-framework-4-6.aspx)beschikbaar .
* ADO.NET 4.5 werd uitgebracht op 15 augustus 2012. Een blog aankondiging van de .NET team is [hier](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx)beschikbaar .
  * Een blogpost over ADO.NET 4.5.1 is [hier](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-net-framework-4-5-1-preview.aspx)beschikbaar.

* Microsoft® ODBC Driver 17 voor SQL Server® - Windows, Linux, & macOShttps://www.microsoft.com/download/details.aspx?id=56567

* Verbinding maken met Azure SQL Database V12 via Omleidinghttps://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [Lijst met tds-protocolversies](https://www.freetds.org/userguide/tdshistory.htm)
* [Overzicht van SQL-databaseontwikkeling](sql-database-develop-overview.md)
* [Azure SQL Database-firewall](sql-database-firewall-configure.md)
* [Procedure: Firewallinstellingen voor SQL Database configureren](sql-database-configure-firewall-settings.md)


