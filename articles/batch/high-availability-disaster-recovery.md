---
title: Hoge beschikbaarheid en herstel na noodgevallen
description: Meer informatie over het ontwerpen van uw batch-toepassing voor een regionale storing.
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: 1e22cb19aba1dcedc4ece7ddc2d1de0ab3233238
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83725734"
---
# <a name="design-your-application-for-high-availability"></a>Een toepassing voor hoge beschikbaarheid ontwerpen

Azure Batch is een regionale service. Batch is beschikbaar in alle Azure-regio's, maar wanneer een batch-account wordt gemaakt, moet het worden gekoppeld aan een regio. Alle bewerkingen voor het batch-account worden toegepast op die regio. Pools en gekoppelde virtuele machines (Vm's) worden bijvoorbeeld gemaakt in dezelfde regio als het batch-account.

Bij het ontwerpen van een toepassing die gebruikmaakt van batch, moet u rekening houden met de mogelijkheid van een batch die niet beschikbaar is in een regio. Het is mogelijk om een zeldzame situatie te ondervinden waarbij er sprake is van een probleem met de regio als geheel, de volledige batch-service in de regio of een probleem met uw specifieke batch-account.

Als de toepassing of oplossing die gebruikmaakt van batch altijd beschikbaar moet zijn, moet deze zijn ontworpen om een failover naar een andere regio te kunnen uitvoeren of de werk belasting over twee of meer regio's te splitsen. Beide benaderingen vereisen ten minste twee batch-accounts, waarbij elk account zich in een andere regio bevindt.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Meerdere batch-accounts in meerdere regio's

Het gebruik van meerdere batch-accounts in verschillende regio's biedt de mogelijkheid om uw toepassing verder uit te voeren als een batch-account in een andere regio niet meer beschikbaar is. Het gebruik van meerdere accounts is met name belang rijk als uw toepassing Maxi maal beschikbaar moet zijn.

In sommige gevallen kan een toepassing worden ontworpen om altijd twee of meer regio's te gebruiken. Als u bijvoorbeeld een aanzienlijke hoeveelheid capaciteit nodig hebt, moet u mogelijk meerdere regio's gebruiken om een grootschalige toepassing te verwerken of voor toekomstige groei.

## <a name="design-considerations-for-providing-failover"></a>Ontwerp overwegingen voor het leveren van failover

Wanneer u rekening moet houden met de mogelijkheid om een failover naar een andere regio uit te geven, is het belang rijk om te overwegen dat alle onderdelen in een oplossing moeten worden overwogen; het is niet voldoende om simpelweg een tweede batch-account te hebben. In de meeste batch-toepassingen is bijvoorbeeld een Azure-opslag account vereist, waarbij het opslag account en het batch-account moeten worden opgeslagen in dezelfde regio voor aanvaard bare prestaties.

Houd rekening met de volgende punten bij het ontwerpen van een oplossing die failover kan:

- Maak vooraf alle vereiste accounts in elke regio, zoals het batch-account en het opslag account. Vaak worden er geen kosten in rekening gebracht voor het maken van accounts, alleen wanneer er gegevens zijn opgeslagen of als het account wordt gebruikt.
- Zorg ervoor dat quota's vooraf zijn ingesteld op de accounts, zodat u het vereiste aantal kernen kunt toewijzen met behulp van het batch-account.
- Gebruik sjablonen en/of scripts voor het automatiseren van de implementatie van de toepassing in een regio.
- Binaire toepassings bestanden en referentie gegevens up-to-date houden in alle regio's. Als u up-to-date blijft, kunt u ervoor zorgen dat de regio snel online kan worden gebracht zonder te hoeven wachten op het uploaden en implementeren van bestanden. Als een aangepaste toepassing bijvoorbeeld op groeps knooppunten wordt opgeslagen en ernaar wordt verwezen met behulp van batch-toepassings pakketten, moet de nieuwe versie van de toepassing worden geüpload naar elk batch-account en wordt verwezen door de groeps configuratie (of de nieuwe versie de standaard versie maken).
- In de toepassing die batch, opslag en andere services aanroept, kunt u eenvoudig clients of de belasting naar de andere regio overschakelen.
- Een best practice om ervoor te zorgen dat een failover wordt uitgevoerd, is het regel matig overschakelen naar een alternatieve regio als onderdeel van de normale werking. Bijvoorbeeld, met twee implementaties in afzonderlijke regio's, wordt elke maand overschakeld naar de alternatieve regio.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het maken van batch-accounts met de [Azure Portal](batch-account-create-portal.md), de [Azure cli](cli-samples.md), [Power shell](batch-powershell-cmdlets-get-started.md)of de [batch-beheer-API](batch-management-dotnet.md).
- Standaard quota's zijn gekoppeld aan een batch-account. in [dit artikel](batch-quota-limit.md) worden de standaard quotum waarden beschreven en wordt uitgelegd hoe de quota's kunnen worden verhoogd.
