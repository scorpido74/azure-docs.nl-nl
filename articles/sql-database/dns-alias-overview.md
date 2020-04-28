---
title: DNS-alias
description: Uw toepassingen kunnen verbinding maken met een alias voor de naam van uw Azure SQL Database-Server. Ondertussen kunt u de SQL Database de alias punten naar elke wille keurige manier wijzigen, om het testen te vergemakkelijken.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73820617"
---
# <a name="dns-alias-for-azure-sql-database"></a>DNS-alias voor Azure SQL Database

Azure SQL Database heeft een Domain Name System (DNS)-server. Power shell-en REST [-api's accepteren oproepen voor het maken en beheren van DNS-aliassen](#anchor-powershell-code-62x) voor uw SQL database-server naam.

Een *DNS-alias* kan worden gebruikt in plaats van de naam van de Azure SQL database-server. Client Programma's kunnen de alias gebruiken in hun verbindings reeksen. De DNS-alias biedt een Vertaal laag die uw client Programma's kan omleiden naar verschillende servers. Deze laag reserveert u de problemen bij het zoeken en bewerken van alle clients en de bijbehorende verbindings reeksen.

Veelvoorkomende toepassingen voor een DNS-alias bevatten de volgende gevallen:

- Maak een gemakkelijk te onthouden naam voor een Azure-SQL Server.
- Tijdens de eerste ontwikkeling kan uw alias verwijzen naar een test SQL Database-Server. Wanneer de toepassing live gaat, kunt u de alias wijzigen zodat deze naar de productie server verwijst. Voor de overgang van testen naar productie zijn geen wijzigingen aangebracht in de configuraties van verschillende clients die verbinding maken met de database server.
- Stel dat de enige data base in uw toepassing wordt verplaatst naar een andere SQL Database-Server. Hier kunt u de alias wijzigen zonder de configuraties van verschillende clients te wijzigen.
- Tijdens een regionale onderbreking gebruikt u geo-Restore om uw data base te herstellen in een andere server en regio. U kunt uw bestaande alias zodanig aanpassen dat deze naar de nieuwe server verwijst, zodat de bestaande client toepassing er opnieuw verbinding mee kan maken. 

## <a name="domain-name-system-dns-of-the-internet"></a>Domain Name System (DNS) van Internet

Internet is afhankelijk van de DNS-server. De DNS vertaalt uw beschrijvende namen in de naam van uw Azure SQL Database-Server.

## <a name="scenarios-with-one-dns-alias"></a>Scenario's met één DNS-alias

Stel dat u uw systeem moet overschakelen naar een nieuwe Azure SQL Database-Server. In het verleden moest u elke connection string in elk client programma zoeken en bijwerken. Maar nu, als de verbindings reeksen een DNS-alias gebruiken, moet alleen een alias eigenschap worden bijgewerkt.

De functie voor de DNS-alias van Azure SQL Database kan helpen bij de volgende scenario's:

### <a name="test-to-production"></a>Testen naar productie

Wanneer u begint met het ontwikkelen van de client Programma's, hebben ze een DNS-alias in hun verbindings reeksen. U maakt de eigenschappen van het alias punt naar een test versie van uw Azure SQL Database-Server.

Later wanneer het nieuwe systeem Live in productie gaat, kunt u de eigenschappen van de alias bijwerken zodat deze naar de productie SQL Database Server verwijzen. U hoeft geen wijzigingen aan te brengen in de client Programma's.

### <a name="cross-region-support"></a>Ondersteuning voor meerdere regio's

Een herstel na nood geval kan uw SQL Database-Server naar een andere geografische regio verplaatsen. Voor een systeem dat een DNS-alias gebruikt, is het nood zakelijk om alle verbindings reeksen voor alle clients te zoeken en bij te werken. In plaats daarvan kunt u een alias bijwerken om te verwijzen naar de nieuwe SQL Database-Server die nu als host fungeert voor uw data base.

## <a name="properties-of-a-dns-alias"></a>Eigenschappen van een DNS-alias

De volgende eigenschappen zijn van toepassing op elke DNS-alias voor uw SQL Database-Server:

- *Unieke naam:* Elke alias naam die u maakt, is uniek op alle Azure SQL Database servers, net zoals de server namen.
- *Server is vereist:* Er kan geen DNS-alias worden gemaakt tenzij deze verwijst naar exact één server, en de server moet al bestaan. Een bijgewerkte alias moet altijd verwijzen naar precies één bestaande server.
  - Wanneer u een SQL Database-Server neerzet, worden ook alle DNS-aliassen die naar de server verwijzen, door het Azure-systeem verwijderd.
- *Niet gebonden aan een regio:* DNS-aliassen zijn niet gebonden aan een regio. DNS-aliassen kunnen worden bijgewerkt om te verwijzen naar een Azure SQL Database-Server die zich in een geografische regio bevindt.
  - Wanneer u echter een alias bijwerkt om te verwijzen naar een andere server, moeten beide servers aanwezig zijn in hetzelfde Azure- *abonnement*.
- *Machtigingen:* Voor het beheren van een DNS-alias moet de gebruiker machtigingen voor *Server Inzender* of hoger hebben. Zie [aan de slag met Access Control op basis van rollen in de Azure Portal](../role-based-access-control/overview.md)voor meer informatie.

## <a name="manage-your-dns-aliases"></a>Uw DNS-aliassen beheren

U kunt ook de REST Api's en Power shell-cmdlets gebruiken om uw DNS-aliassen programmatisch te beheren.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST-Api's voor het beheren van uw DNS-aliassen

De documentatie voor de REST-Api's is beschikbaar in de buurt van de volgende weblocatie:

- [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/)

Ook kunnen de REST Api's worden weer gegeven in GitHub op:

- [Azure SQL Database-Server, REST Api's voor DNS-aliassen](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>Power shell voor het beheren van uw DNS-aliassen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek.

Power shell-cmdlets zijn beschikbaar die de REST-Api's aanroepen.

Een code voorbeeld van Power shell-cmdlets die wordt gebruikt voor het beheren van DNS-aliassen wordt beschreven in:

- [Power shell voor het Azure SQL Database van DNS-alias](dns-alias-powershell.md)

De cmdlets die in het code voorbeeld worden gebruikt, zijn de volgende:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): maakt een nieuwe DNS-alias in het Azure SQL database-service systeem. De alias verwijst naar Azure SQL Database Server 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): alle DNS-aliassen ophalen en weer geven die zijn toegewezen aan SQL DB-server 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): wijzigt de server naam waarmee de alias is geconfigureerd, van Server 1 naar SQL DB Server 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Verwijder de DNS-alias uit SQL DB-Server 2, met behulp van de naam van de alias.

## <a name="limitations-during-preview"></a>Beperkingen tijdens de preview

Momenteel heeft een DNS-alias de volgende beperkingen:

- *Vertraging van Maxi maal twee minuten:* Het duurt Maxi maal twee minuten voordat een DNS-alias wordt bijgewerkt of verwijderd.
  - Ongeacht de korte vertraging stopt de alias direct met het verwijzen naar client verbindingen met de verouderde server.
- *DNS-zoek opdracht:* De enige gezaghebbende manier om te controleren naar welke server een gegeven DNS-alias verwijst, is door een [DNS-zoek opdracht](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup)uit te voeren.
- Het _controleren van tabellen wordt niet ondersteund:_ U kunt geen DNS-alias gebruiken op een Azure SQL Database Server waarvoor *tabel controle* is ingeschakeld voor een Data Base.
  - De tabel controle is afgeschaft.
  - We raden u aan om over te stappen op de [controle van blobs](sql-database-auditing.md).

## <a name="related-resources"></a>Gerelateerde resources

- [Overzicht van bedrijfs continuïteit met Azure SQL database](sql-database-business-continuity.md), met inbegrip van herstel na nood gevallen.

## <a name="next-steps"></a>Volgende stappen

- [Power shell voor het Azure SQL Database van DNS-alias](dns-alias-powershell.md)
