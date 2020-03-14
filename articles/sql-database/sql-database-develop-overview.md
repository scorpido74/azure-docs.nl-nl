---
title: Overzicht van toepassings ontwikkeling
description: Kom meer te weten over beschikbare verbindingsbibliotheken en aanbevolen procedures voor toepassingen die verbinding maken met SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.openlocfilehash: 26aa9948a44727ff4c8092eb5131b1c054bf5442
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257002"
---
# <a name="sql-database-application-development-overview"></a>Overzicht van het ontwikkelen van SQL Database toepassingen

In dit artikel leest u meer over de algemene zaken waar ontwikkelaars van op de hoogte moeten zijn voor het schrijven van code om verbinding te maken met Azure SQL Database. Dit artikel is van toepassing op alle implementatie modellen van Azure SQL Database (één data base, elastische Pools, beheerd exemplaar).

> [!TIP]
> Bekijk de aan de slag-hand leidingen voor [afzonderlijke data bases](sql-database-single-database-quickstart-guide.md) en [beheerde instanties](sql-database-managed-instance-quickstart-guide.md) als u uw Azure SQL database moet instellen.
>

## <a name="language-and-platform"></a>Taal en platform

U kunt verschillende [Programmeer talen en platformen](sql-database-connect-query.md) gebruiken om verbinding te maken en Azure SQL database op te vragen. U kunt [voorbeeld toepassingen](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) vinden die u kunt gebruiken om verbinding te maken met de Azure SQL database.

U kunt gebruikmaken van open-source hulpprogram ma's zoals [Cheetah](https://github.com/wunderlist/cheetah), [SQL-CLI](https://www.npmjs.com/package/sql-cli)en [VS code](https://code.visualstudio.com/). Daarnaast werkt Azure SQL Database met Microsoft-hulpprogramma's zoals [Visual Studio](https://www.visualstudio.com/downloads/) en [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). U kunt ook de Azure Portal, Power shell en REST Api's gebruiken om extra productiviteit te verkrijgen.

## <a name="authentication"></a>Verificatie

Toegang tot Azure SQL Database is beveiligd met aanmeldingen en firewalls. Azure SQL Database ondersteunt gebruikers en aanmeldingen voor zowel SQL Server als [Azure Active Directory (Aad)-verificatie](sql-database-aad-authentication.md) . AAD-aanmeldingen zijn alleen beschikbaar in een beheerd exemplaar. 

Meer informatie over het [beheren van database toegang en-aanmelding](sql-database-manage-logins.md).

## <a name="connections"></a>Verbindingen

In de verbindingslogica van de client overschrijft u de standaardtime-out zodat deze 30 seconden bedraagt. De standaardwaarde van 15 seconden is te kort voor verbindingen die afhankelijk zijn van internet.

Als u een [verbindingsgroep](https://msdn.microsoft.com/library/8xx3tyca.aspx) gebruikt, zorgt u ervoor dat de verbinding wordt verbroken zodra uw programma er niet actief meer gebruik van maakt en wanneer uw programma niet wordt voorbereid om opnieuw verbinding te maken.

Vermijd langlopende trans acties omdat een infra structuur of verbindings fout de trans actie kan terugdraaien. Als dat mogelijk is, splitst u de trans actie in de meerdere kleinere trans acties en gebruikt [u batch verwerking om de prestaties te verbeteren](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Flexibiliteit

Azure SQL Database is een Cloud service waar u mogelijk tijdelijke fouten verwacht die zich voordoen in de onderliggende infra structuur of in de communicatie tussen Cloud entiteiten. Hoewel Azure SQL Database bestendig is op het mislukken van de transitieve infra structuur, kunnen deze storingen invloed hebben op uw verbinding. Wanneer er een tijdelijke fout optreedt tijdens het verbinden met SQL Database, moet uw code [de aanroep opnieuw proberen](sql-database-connectivity-issues.md). Het wordt aanbevolen om voor logica voor opnieuw proberen uitstellogica te gebruiken zodat de SQL Database niet overbelast raakt door meerdere clients die tegelijk opnieuw proberen. Pogings logica is afhankelijk van de [fout berichten voor SQL database-client Programma's](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md).

Voor meer informatie over het voorbereiden van geplande onderhouds gebeurtenissen in uw Azure SQL database raadpleegt u [Azure-onderhouds gebeurtenissen plannen in Azure SQL database](sql-database-planned-maintenance.md).

## <a name="network-considerations"></a>Aandachtspunten voor netwerken

- Zorg er op de computer die als host fungeert voor uw clientprogramma voor dat de firewall uitgaande TCP-communicatie toestaat via poort 1433.  Meer informatie: [een Azure SQL database firewall configureren](sql-database-configure-firewall-settings.md).
- Als uw client programma verbinding maakt met SQL Database terwijl uw client wordt uitgevoerd op een virtuele machine (VM) van Azure, moet u bepaalde poortbereiken openen op de VM. Meer informatie: [poorten boven 1433 voor ADO.NET 4,5 en SQL database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Client verbindingen met Azure SQL Database soms de proxy omzeilen en rechtstreeks communiceren met de data base. Andere poorten dan poort 1433 worden belangrijk. [Azure SQL database connectiviteits architectuur](sql-database-connectivity-architecture.md) en [poorten hoger dan 1433 voor ADO.NET 4,5 en SQL database](sql-database-develop-direct-route-ports-adonet-v12.md)voor meer informatie.
- Zie [netwerk configuratie voor beheerde instanties](sql-database-howto-managed-instance.md#network-configuration)voor netwerk configuratie voor een beheerd exemplaar.

## <a name="next-steps"></a>Volgende stappen

Bekijk alle [mogelijkheden van SQL Database](sql-database-technical-overview.md).
