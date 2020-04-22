---
title: Tijdelijke verbindingsfouten verwerken - Azure Database voor PostgreSQL - Single Server
description: Meer informatie over het verwerken van tijdelijke verbindingsfouten voor Azure Database voor PostgreSQL - Single Server.
keywords: postgresql-verbinding, verbindingstekenreeks, verbindingsproblemen, tijdelijke fout, verbindingsfout
author: jasonwhowell
ms.author: jasonh
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 8138512dfc893f3523c5ad78a93aef1bcdedfe70
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768033"
---
# <a name="handling-transient-connectivity-errors-for-azure-database-for-postgresql---single-server"></a>Tijdelijke connectiviteitsfouten voor Azure Database for PostgreSQL verwerken: één server

In dit artikel wordt beschreven hoe u tijdelijke fouten verwerken die verbinding maken met Azure Database voor PostgreSQL.

## <a name="transient-errors"></a>Tijdelijke fouten

Een tijdelijke fout, ook wel een tijdelijke fout genoemd, is een fout die zichzelf zal oplossen. Meestal manifesteren deze fouten zich als een verbinding met de databaseserver die wordt verwijderd. Ook kunnen nieuwe verbindingen met een server niet worden geopend. Tijdelijke fouten kunnen bijvoorbeeld optreden wanneer er hardware- of netwerkfouten optreden. Een andere reden zou kunnen zijn een nieuwe versie van een PaaS-service die wordt uitgerold. De meeste van deze gebeurtenissen worden automatisch beperkt door het systeem in minder dan 60 seconden. Een aanbevolen manier voor het ontwerpen en ontwikkelen van applicaties in de cloud is het verwachten van tijdelijke fouten. Stel dat ze kunnen gebeuren in elk onderdeel op elk gewenst moment en om de juiste logica op zijn plaats om deze situaties te behandelen.

## <a name="handling-transient-errors"></a>Tijdelijke fouten verwerken

Tijdelijke fouten moeten worden verwerkt met behulp van logica voor opnieuw proberen. Situaties die in aanmerking moeten worden genomen:

* Er treedt een fout op wanneer u een verbinding probeert te openen
* Een niet-actieve verbinding wordt aan de serverzijde verbroken. Wanneer u een opdracht probeert uit te geven, kan deze niet worden uitgevoerd
* Een actieve verbinding die momenteel een opdracht uitvoert, wordt verbroken.

De eerste en tweede zaak zijn vrij rechttoe rechtaan te behandelen. Probeer de verbinding opnieuw te openen. Wanneer u slaagt, is de tijdelijke fout door het systeem beperkt. U uw Azure Database opnieuw gebruiken voor PostgreSQL. We raden u aan te wachten voordat u de verbinding opnieuw probeert. Terug uit als de eerste pogingen mislukken. Op deze manier kan het systeem alle beschikbare middelen gebruiken om de foutsituatie te overwinnen. Een goed patroon om te volgen is:

* Wacht 5 seconden voordat je eerste nieuwe poging doet.
* Voor elke volgende opnieuw proberen, de verhoging van het wachten exponentieel, tot 60 seconden.
* Stel een maximum aantal nieuwe pogingen in op welk punt de bewerking is mislukt.

Wanneer een verbinding met een actieve transactie mislukt, is het moeilijker om het herstel correct te verwerken. Er zijn twee gevallen: Als de transactie alleen-lezen van aard was, is het veilig om de verbinding te heropenen en de transactie opnieuw te proberen. Als de transactie echter ook naar de database is geschreven, moet u bepalen of de transactie is teruggedraaid of dat deze is geslaagd voordat de tijdelijke fout is uitgevoerd. In dat geval heeft u misschien de commit-bevestiging van de databaseserver niet ontvangen.

Een manier om dit te doen, is het genereren van een unieke ID op de client die wordt gebruikt voor alle pogingen. U geeft deze unieke ID als onderdeel van de transactie door aan de server en slaat deze op in een kolom met een unieke beperking. Op deze manier u de transactie veilig opnieuw proberen. Het zal lukken als de vorige transactie is teruggedraaid en de client gegenereerde unieke ID nog niet bestaat in het systeem. Het wordt niet weergegeven als de schending van de dubbele sleutel wordt aangegeven als de unieke id eerder is opgeslagen omdat de vorige transactie is voltooid.

Wanneer uw programma communiceert met Azure Database voor PostgreSQL via middleware van derden, vraagt u de leverancier of de middleware opnieuw proberenlogica bevat voor tijdelijke fouten.

Zorg ervoor dat u de logica opnieuw probeert. Probeer bijvoorbeeld uw code uit te voeren terwijl u de rekenbronnen van uw Azure Database voor PostgreSQL-server opschaalt of omlaag. Uw toepassing moet de korte downtime die tijdens deze bewerking wordt ondervonden, zonder problemen verwerken.

## <a name="next-steps"></a>Volgende stappen

* [Verbindingsproblemen met Azure Databases for PostgreSQL oplossen](howto-troubleshoot-common-connection-issues.md)
