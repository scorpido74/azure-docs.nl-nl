---
title: DNS-alias
description: Uw toepassingen kunnen verbinding maken met een alias voor de naam van uw Azure SQL Database-server. Ondertussen u de SQL Database de alias punten op elk gewenst moment, om het testen te vergemakkelijken en ga zo maar door.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 05fa542a0ad1c72f73148eefd304a9771798598d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820617"
---
# <a name="dns-alias-for-azure-sql-database"></a>DNS-alias voor Azure SQL Database

Azure SQL Database heeft een DNS-server (Domain Name System). PowerShell- en REST-API's accepteren [oproepen om DNS-aliassen voor](#anchor-powershell-code-62x) uw SQL Database-servernaam te maken en te beheren.

Een *DNS-alias* kan worden gebruikt in plaats van de naam van de Azure SQL Database-server. Clientprogramma's kunnen de alias gebruiken in hun verbindingstekenreeksen. De DNS-alias biedt een vertaallaag die uw clientprogramma's naar verschillende servers kan doorleiden. Deze laag bespaart u de moeilijkheden van het moeten vinden en bewerken van alle clients en hun verbinding strings.

Veelvoorkomende toepassingen voor een DNS-alias zijn de volgende gevallen:

- Maak een eenvoudig te onthouden naam voor een Azure SQL Server.
- Tijdens de eerste ontwikkeling kan uw alias verwijzen naar een test SQL Database-server. Wanneer de toepassing live gaat, u de alias wijzigen om naar de productieserver te verwijzen. De overgang van test naar productie vereist geen wijziging in de configuraties van verschillende clients die verbinding maken met de databaseserver.
- Stel dat de enige database in uw toepassing wordt verplaatst naar een andere SQL Database-server. Hier u de alias wijzigen zonder de configuraties van verschillende clients te hoeven wijzigen.
- Tijdens een regionale storing gebruikt u geo-restore om uw database in een andere server en regio te herstellen. U uw bestaande alias wijzigen om naar de nieuwe server te wijzen, zodat de bestaande clienttoepassing er opnieuw verbinding mee kan maken. 

## <a name="domain-name-system-dns-of-the-internet"></a>Domain Name System (DNS) van het internet

Het internet is afhankelijk van de DNS. De DNS vertaalt uw vriendelijke namen naar de naam van uw Azure SQL Database-server.

## <a name="scenarios-with-one-dns-alias"></a>Scenario's met één DNS-alias

Stel dat u uw systeem moet overschakelen naar een nieuwe Azure SQL Database-server. In het verleden moest u elke verbindingstekenreeks in elk clientprogramma vinden en bijwerken. Maar nu, als de verbindingstekenreeksen een DNS-alias gebruiken, moet alleen een alias-eigenschap worden bijgewerkt.

De functie DNS-alias van Azure SQL Database kan helpen in de volgende scenario's:

### <a name="test-to-production"></a>Test naar productie

Wanneer u begint met het ontwikkelen van de clientprogramma's, laat ze dan een DNS-alias gebruiken in hun verbindingstekenreeksen. U maakt de eigenschappen van de alias aan te wijzen op een testversie van uw Azure SQL Database-server.

Later wanneer het nieuwe systeem live in productie gaat, u de eigenschappen van de alias bijwerken om naar de productie SQL Database-server te wijzen. Er is geen wijziging in de clientprogramma's nodig.

### <a name="cross-region-support"></a>Ondersteuning voor verschillende regio's

Een disaster recovery kan uw SQL Database-server verschuiven naar een andere geografische regio. Voor een systeem dat een DNS-alias gebruikte, kan de noodzaak worden vermeden om alle verbindingstekenreeksen voor alle clients te vinden en bij te werken. In plaats daarvan u een alias bijwerken om te verwijzen naar de nieuwe SQL Database-server die nu uw database host.

## <a name="properties-of-a-dns-alias"></a>Eigenschappen van een DNS-alias

De volgende eigenschappen zijn van toepassing op elke DNS-alias voor uw SQL Database-server:

- *Unieke naam:* Elke aliasnaam die u maakt, is uniek voor alle Azure SQL Database-servers, net zoals servernamen dat zijn.
- *Server is vereist:* Er kan geen DNS-alias worden gemaakt, tenzij deze precies naar één server verwijst en de server al bestaat. Een bijgewerkte alias moet altijd precies naar één bestaande server verwijzen.
  - Wanneer u een SQL Database-server neerzet, worden in het Azure-systeem ook alle DNS-aliassen verwijderd die naar de server verwijzen.
- *Niet gebonden aan een regio:* DNS-aliassen zijn niet gebonden aan een regio. Alle DNS-aliassen kunnen worden bijgewerkt om te verwijzen naar een Azure SQL Database-server die zich in een geografisch gebied bevindt.
  - Wanneer u echter een alias bijwerkt om naar een andere server te verwijzen, moeten beide servers in hetzelfde *Azure-abonnement*bestaan.
- *Machtigingen:* Als u een DNS-alias wilt beheren, moet de gebruiker machtigingen *voor serverbijdragen* of hoger hebben. Zie [Aan de slag met op rollen gebaseerd toegangsbeheer in de Azure-portal](../role-based-access-control/overview.md)voor meer informatie.

## <a name="manage-your-dns-aliases"></a>Uw DNS-aliassen beheren

Zowel REST API's als PowerShell-cmdlets zijn beschikbaar om u in staat te stellen uw DNS-aliassen programmatisch te beheren.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST API's voor het beheren van uw DNS-aliassen

De documentatie voor de REST API's is beschikbaar in de buurt van de volgende weblocatie:

- [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/)

Ook zijn de REST API's te zien in GitHub op:

- [Azure SQL Database-server, DNS-alias REST API's](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell voor het beheren van uw DNS-aliassen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek.

PowerShell-cmdlets zijn beschikbaar die de REST API's noemen.

Een codevoorbeeld van PowerShell-cmdlets die worden gebruikt voor het beheren van DNS-aliassen, wordt gedocumenteerd op:

- [PowerShell voor DNS-alias naar Azure SQL-database](dns-alias-powershell.md)

De cmdlets die in het codevoorbeeld worden gebruikt, zijn de volgende:

- [Nieuw-AzSqlServerDNSAlias:](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias)Hiermee maakt u een nieuwe DNS-alias in het Azure SQL Database-servicesysteem. De alias verwijst naar Azure SQL Database server 1.
- [Get-AzSqlServerDNSAlias:](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias)alle DNS-aliassen die zijn toegewezen aan SQL DB server 1 oppakken en weergeven.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): wijzigt de servernaam waarnaar de alias is geconfigureerd, van server 1 naar SQL DB-server 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Verwijder de DNS-alias uit SQL DB server 2 met de naam van de alias.

## <a name="limitations-during-preview"></a>Beperkingen tijdens preview

Momenteel heeft een DNS-alias de volgende beperkingen:

- *Vertraging van maximaal 2 minuten:* Het duurt maximaal 2 minuten voordat een DNS-alias wordt bijgewerkt of verwijderd.
  - Ongeacht een korte vertraging, stopt de alias onmiddellijk met het verwijzen van clientverbindingen naar de verouderde server.
- *DNS-lookup:* Voor nu is de enige gezaghebbende manier om te controleren naar welke server een bepaalde DNS-alias verwijst door een [DNS-lookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup)uit te voeren.
- _Tabelcontrole wordt niet ondersteund:_ U geen DNS-alias gebruiken op een Azure SQL Database-server waarop *tabelcontrole* is ingeschakeld in een database.
  - Tabelcontrole wordt afgeschaft.
  - We raden u aan over te stappen op [Blob Auditing.](sql-database-auditing.md)

## <a name="related-resources"></a>Gerelateerde resources

- [Overzicht van de bedrijfscontinuïteit met Azure SQL Database](sql-database-business-continuity.md), inclusief disaster recovery.

## <a name="next-steps"></a>Volgende stappen

- [PowerShell voor DNS-alias naar Azure SQL-database](dns-alias-powershell.md)
