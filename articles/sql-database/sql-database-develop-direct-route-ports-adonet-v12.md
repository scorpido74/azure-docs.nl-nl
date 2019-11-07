---
title: Poorten boven 1433 voor SQL Database
description: Client verbindingen van ADO.NET naar Azure SQL Database kunnen de proxy overs Laan en rechtstreeks communiceren met de data base via andere poorten dan 1433.
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
ms.openlocfilehash: 38ab3b6b0c64ad17311d15fd12e7625e8aa728ce
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690684"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Poorten boven 1433 voor ADO.NET 4,5

In dit onderwerp wordt het Azure SQL Database verbindings gedrag voor clients beschreven die gebruikmaken van ADO.NET 4,5 of een latere versie.

> [!IMPORTANT]
> Zie voor meer informatie over connectiviteits architectuur [Azure SQL database connectiviteits architectuur](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Buiten zijde

Voor verbindingen met Azure SQL Database moet u eerst vragen of uw client programma *buiten* of *binnen* de Azure-Cloud grens wordt uitgevoerd. In de subsecties worden twee veelvoorkomende scenario's besproken.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*Buiten:* Client wordt uitgevoerd op uw desktop computer

Poort 1433 is de enige poort die moet worden geopend op de desktop computer die als host fungeert voor uw SQL Database-client toepassing.

### <a name="inside-client-runs-on-azure"></a>*Binnen:* Client wordt uitgevoerd op Azure

Wanneer de-client wordt uitgevoerd binnen de grens van de Azure-Cloud, wordt gebruikgemaakt van wat we een *directe route* kunnen aanroepen om te communiceren met de SQL database-server. Nadat een verbinding tot stand is gebracht, omvatten verdere interacties tussen de client en data base geen Azure SQL Database gateway.

De volg orde is als volgt:

1. ADO.NET 4,5 (of hoger) initieert een korte interactie met de Azure-Cloud en ontvangt een dynamisch geïdentificeerd poort nummer.

   * Het dynamisch geïdentificeerde poort nummer bevindt zich in het bereik van 11000-11999.
2. ADO.NET maakt vervolgens rechtstreeks verbinding met de SQL Database Server, zonder middleware in tussen.
3. Query's worden rechtstreeks naar de-data base verzonden en de resultaten worden direct naar de client geretourneerd.

Zorg ervoor dat de poortbereiken van 11000-11999 op uw Azure-client machine beschikbaar blijven voor ADO.NET 4,5-client interacties met SQL Database.

* In het bijzonder moeten poorten in het bereik vrij van andere uitgaande blok keringen zijn.
* Op uw virtuele Azure-machine beheert de **Windows Firewall met geavanceerde beveiliging** de poort instellingen.
  
  * U kunt de [gebruikers interface van de firewall](https://msdn.microsoft.com/library/cc646023.aspx) gebruiken om een regel toe te voegen waarvoor u het **TCP** -protocol en een poort bereik opgeeft met de syntaxis zoals **11000-11999**.

## <a name="version-clarifications"></a>Versie-uitleg

Deze sectie verduidelijkt de monikers die verwijzen naar de product versies. Er wordt ook een aantal versies tussen producten weer gegeven.

### <a name="adonet"></a>ADO.NET

* ADO.NET 4,0 ondersteunt het TDS 7,3-protocol, maar niet 7,4.
* ADO.NET 4,5 en hoger ondersteunt het TDS 7,4-protocol.

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 of hoger

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4,2 of hoger (JDBC 4,0 ondersteunt werkelijk TDS 7,4, maar implementeert de omleiding niet)

## <a name="related-links"></a>Verwante koppelingen

* ADO.NET 4,6 is uitgebracht op 20 juli 2015. [Hier](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-net-framework-4-6.aspx)vindt u een blog aankondiging van het .net-team.
* ADO.NET 4,5 is uitgebracht op 15 augustus 2012. [Hier](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx)vindt u een blog aankondiging van het .net-team.
  * Een blog bericht over ADO.NET 4.5.1 is [hier](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-net-framework-4-5-1-preview.aspx)beschikbaar.

* Micro soft® ODBC-stuur programma 17 voor SQL Server®-Windows, Linux, & macOS https://www.microsoft.com/download/details.aspx?id=56567

* Verbinding maken met Azure SQL Database V12 via omleiding https://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [Lijst met TDS-protocol versies](https://www.freetds.org/userguide/tdshistory.htm)
* [Overzicht van SQL Database ontwikkeling](sql-database-develop-overview.md)
* [Azure SQL Database firewall](sql-database-firewall-configure.md)
* [Procedure: Firewall-instellingen configureren op SQL Database](sql-database-configure-firewall-settings.md)


