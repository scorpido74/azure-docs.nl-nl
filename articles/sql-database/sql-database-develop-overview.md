---
title: Overzicht van toepassingsontwikkeling
description: Kom meer te weten over beschikbare verbindingsbibliotheken en aanbevolen procedures voor toepassingen die verbinding maken met SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.openlocfilehash: 741906bbe9de68459b2e4a704a243fde4771b3a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067333"
---
# <a name="sql-database-application-development-overview"></a>Overzicht van SQL Database-toepassingen

In dit artikel leest u meer over de algemene zaken waar ontwikkelaars van op de hoogte moeten zijn voor het schrijven van code om verbinding te maken met Azure SQL Database. Dit artikel is van toepassing op alle implementatiemodellen van Azure SQL Database (Single database, Elastic pools, Managed instance).

> [!TIP]
> Bekijk de handleidingen voor het starten van [afzonderlijke databases](sql-database-single-database-quickstart-guide.md) en [beheerde instanties](sql-database-managed-instance-quickstart-guide.md) als u uw Azure SQL-database moet instellen.
>

## <a name="language-and-platform"></a>Taal en platform

U verschillende [programmeertalen en -platforms](sql-database-connect-query.md) gebruiken om Verbinding te maken en Azure SQL Database op te vragen. U [voorbeeldtoepassingen](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) vinden die u gebruiken om verbinding te maken met de Azure SQL-database.

U gebruikmaken van open-source tools zoals [cheetah,](https://github.com/wunderlist/cheetah) [sql-cli](https://www.npmjs.com/package/sql-cli), [VS Code](https://code.visualstudio.com/). Daarnaast werkt Azure SQL Database met Microsoft-hulpprogramma's zoals [Visual Studio](https://www.visualstudio.com/downloads/) en [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). U ook de Azure-portal, PowerShell en REST API's gebruiken om extra productiviteit te behalen.

## <a name="authentication"></a>Authentication

Toegang tot Azure SQL Database wordt beveiligd met aanmeldingen en firewalls. Azure SQL Database ondersteunt zowel SQL Server- als [Azure Active Directory -verificatiegebruikers](sql-database-aad-authentication.md) en aanmeldingen. AAD-aanmeldingen zijn alleen beschikbaar in Managed Instance. 

Meer informatie over [het beheren van databasetoegang en aanmelding](sql-database-manage-logins.md).

## <a name="connections"></a>Verbindingen

In de verbindingslogica van de client overschrijft u de standaardtime-out zodat deze 30 seconden bedraagt. De standaardwaarde van 15 seconden is te kort voor verbindingen die afhankelijk zijn van internet.

Als u een [verbindingsgroep](https://msdn.microsoft.com/library/8xx3tyca.aspx) gebruikt, zorgt u ervoor dat de verbinding wordt verbroken zodra uw programma er niet actief meer gebruik van maakt en wanneer uw programma niet wordt voorbereid om opnieuw verbinding te maken.

Vermijd langlopende transacties omdat een infrastructuur- of verbindingsfout de transactie kan terugdraaien. Splits de transactie zo mogelijk in de meerdere kleinere transacties en gebruik [batching om de prestaties te verbeteren](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Flexibiliteit

Azure SQL Database is een cloudservice waarbij u tijdelijke fouten verwachten die zich voordoen in de onderliggende infrastructuur of in de communicatie tussen cloudentiteiten. Hoewel Azure SQL Database veerkrachtig is voor de tijdelijke infrastructuurfouten, kunnen deze fouten van invloed zijn op uw connectiviteit. Wanneer er een tijdelijke fout optreedt tijdens het maken van verbinding met SQL Database, moet uw code [de aanroep opnieuw proberen.](sql-database-connectivity-issues.md) Het wordt aanbevolen om voor logica voor opnieuw proberen uitstellogica te gebruiken zodat de SQL Database niet overbelast raakt door meerdere clients die tegelijk opnieuw proberen. Logica opnieuw proberen is afhankelijk van de [foutberichten voor SQL Database-clientprogramma's.](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md)

Zie [Planning voor Azure-onderhoudsgebeurtenissen in Azure SQL Database voor](sql-database-planned-maintenance.md)meer informatie over het voorbereiden van geplande onderhoudsgebeurtenissen in uw Azure SQL-database.

## <a name="network-considerations"></a>Overwegingen voor het netwerk

- Zorg er op de computer die als host fungeert voor uw clientprogramma voor dat de firewall uitgaande TCP-communicatie toestaat via poort 1433.  Meer informatie: [Configureer een Azure SQL Database firewall](sql-database-configure-firewall-settings.md).
- Als uw clientprogramma verbinding maakt met SQL Database terwijl uw client wordt uitgevoerd op een virtuele Azure-machine (VM), moet u bepaalde poortbereiken op de VM openen. Meer informatie: [Poorten na 1433 voor ADO.NET 4.5 en SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Clientverbindingen met Azure SQL Database omzeilen soms de proxy en werken rechtstreeks samen met de database. Andere poorten dan poort 1433 worden belangrijk. Voor meer informatie, [Azure SQL Database-connectiviteitsarchitectuur](sql-database-connectivity-architecture.md) en [-poorten na 1433 voor ADO.NET 4.5 en SQL Database.](sql-database-develop-direct-route-ports-adonet-v12.md)
- Zie [netwerkconfiguratie voor beheerde exemplaren voor](sql-database-howto-managed-instance.md#network-configuration)netwerkconfiguratie voor een beheerde instantie .

## <a name="next-steps"></a>Volgende stappen

Ontdek alle [mogelijkheden van SQL Database.](sql-database-technical-overview.md)
