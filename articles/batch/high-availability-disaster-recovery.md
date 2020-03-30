---
title: Hoge beschikbaarheid en herstel na noodgevallen - Azure Batch
description: Meer informatie over het ontwerpen van uw Batch-toepassing voor een regionale storing. Workloads moeten mislukken naar een andere regio of worden verdeeld over twee of meer regio's.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2019
ms.author: labrenne
ms.openlocfilehash: 84b0cce9557b4ae05586579f175cd0f5db14fdfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77026078"
---
# <a name="design-your-application-for-high-availability"></a>Een toepassing voor hoge beschikbaarheid ontwerpen

Azure Batch is een regionale service. Batch is beschikbaar in alle Azure-regio's, maar wanneer een Batch-account wordt gemaakt, moet deze aan een regio zijn gekoppeld. Alle bewerkingen voor het batchaccount zijn vervolgens van toepassing op die regio. Zwembaden en bijbehorende virtuele machines (VM's) worden bijvoorbeeld gemaakt in dezelfde regio als het Batch-account.

Bij het ontwerpen van een toepassing die Batch gebruikt, moet u rekening houden met de mogelijkheid dat Batch niet beschikbaar is in een regio. Het is mogelijk om een zeldzame situatie tegen te komen waarin er een probleem is met de regio als geheel, de hele Batch-service in de regio of een probleem met uw specifieke Batch-account.

Als de toepassing of oplossing met Batch altijd beschikbaar moet zijn, moet deze zijn ontworpen om naar een andere regio te mislukken of de werkbelasting altijd te verdelen over twee of meer regio's. Beide benaderingen vereisen ten minste twee Batch-accounts, waarbij elk account zich in een andere regio bevindt.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Meerdere Batch-accounts in meerdere regio's

Als u meerdere Batch-accounts in verschillende regio's gebruikt, uw toepassing blijven draaien als een Batch-account in een andere regio niet beschikbaar is. Het gebruik van meerdere accounts is vooral belangrijk als uw toepassing zeer beschikbaar moet zijn.

In sommige gevallen kan een toepassing worden ontworpen om altijd twee of meer regio's te gebruiken. Wanneer u bijvoorbeeld een aanzienlijke hoeveelheid capaciteit nodig hebt, kan het nodig zijn om meerdere regio's te gebruiken om een grootschalige toepassing te verwerken of om tegemoet te komen aan toekomstige groei.

## <a name="design-considerations-for-providing-failover"></a>Ontwerpoverwegingen voor het verstrekken van failover

Een belangrijk punt om te overwegen bij het verstrekken van de mogelijkheid om failover naar een alternatieve regio is dat alle componenten in een oplossing moeten worden overwogen; het volstaat niet om gewoon een tweede Batch-account te hebben. In de meeste Batch-toepassingen is bijvoorbeeld een Azure-opslagaccount vereist, waarbij het opslagaccount en batch-account zich in dezelfde regio moeten bevinden voor acceptabele prestaties.

Houd rekening met de volgende punten bij het ontwerpen van een oplossing die kan mislukken:

- Maak vooraf alle vereiste accounts in elke regio, zoals het Batch-account en het opslagaccount. Er zijn vaak geen kosten voor het maken van accounts, alleen wanneer er gegevens zijn opgeslagen of het account wordt gebruikt.
- Zorg ervoor dat quota van tevoren op de accounts zijn ingesteld, zodat u het vereiste aantal cores toewijzen met behulp van het Batch-account.
- Gebruik sjablonen en/of scripts om de implementatie van de toepassing in een regio te automatiseren.
- Houd toepassingsbinaries en referentiegegevens up-to-date in alle regio's. Als u up-to-date blijft, zorgt u ervoor dat de regio snel online kan worden gebracht zonder te hoeven wachten op het uploaden en implementeren van bestanden. Als bijvoorbeeld een aangepaste toepassing die moet worden geïnstalleerd op poolknooppunten wordt opgeslagen en waarnaar wordt verwezen met batchtoepassingspakketten, moet deze worden geüpload naar elk Batch-account en wordt verwezen naar de poolconfiguratie (of maken van de nieuwe versie de standaardversie).
- In de toepassing die Batch, opslag en andere services aanroept, schakelt u eenvoudig clients of de belasting over naar de verschillende regio.
- Een beste praktijk om ervoor te zorgen dat een failover succesvol zal zijn, is om vaak over te schakelen naar een alternatieve regio als onderdeel van de normale werking. Met twee implementaties in afzonderlijke regio's schakelt u bijvoorbeeld elke maand over naar de alternatieve regio.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het maken van Batch-accounts met de [Azure-portal,](batch-account-create-portal.md)de [Azure CLI,](cli-samples.md) [PowerShell](batch-powershell-cmdlets-get-started.md)of de [Api voor batchbeheer](batch-management-dotnet.md).
- Standaardquota zijn gekoppeld aan een Batch-account; [in dit artikel](batch-quota-limit.md) worden de standaardquotumwaarden beschreven en wordt beschreven hoe de quota kunnen worden verhoogd.
