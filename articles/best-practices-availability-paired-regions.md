---
title: Zorg voor bedrijfscontinuïteit & noodherstel met Azure Paired Regions
description: Toepassingstolerantie garanderen met azure regional pairing
author: jpconnock
manager: angrobe
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jeconnoc
ms.openlocfilehash: 778943dad9a04632797d5d9165b6f1f9a3eb9850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248253"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Bedrijfscontinuïteit en herstel na noodgevallen (BCDR: gekoppelde Azure-regio’s

## <a name="what-are-paired-regions"></a>Wat zijn gekoppelde regio's?

Een Azure-regio bestaat uit een set datacenters die binnen een latentiegedefinieerde perimeter zijn geïmplementeerd en zijn verbonden via een specifiek netwerk met lage latentie.  Dit zorgt ervoor dat Azure-services binnen een Azure-regio de best mogelijke prestaties en beveiliging bieden.  

Een Azure-geografie definieert een gebied van de wereld dat ten minste één Azure-regio bevat. Regio's definiëren een discrete markt, meestal met twee of meer regio's, die de grenzen van gegevensresidentie en naleving behouden.  Meer informatie over de wereldwijde infrastructuur van Azure [vindt u hier](https://azure.microsoft.com/global-infrastructure/regions/)

Een regionaal paar bestaat uit twee regio's binnen dezelfde geografie. Azure serialiseert platformupdates (gepland onderhoud) voor regionale paren, zodat slechts één regio in elk paar tegelijk wordt bijgewerkt. Als een storing meerdere regio's treft, wordt ten minste één regio in elk paar geprioriteerd voor herstel.

![AzureGeografie](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Sommige Azure-services maken verder gebruik van gekoppelde regio's om de bedrijfscontinuïteit te waarborgen en te beschermen tegen gegevensverlies.  Azure biedt verschillende [opslagoplossingen](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) die profiteren van gekoppelde regio's om de beschikbaarheid van gegevens te garanderen. [Azure Geo-redundant Storage](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) repliceert bijvoorbeeld automatisch gegevens naar een secundaire regio, zodat gegevens duurzaam zijn, zelfs in het geval dat de primaire regio niet kan worden hersteld. 

Houd er rekening mee dat niet alle Azure-services automatisch gegevens repliceren en dat niet alle Azure-services automatisch van een mislukte regio naar het paar worden teruggevallen.  In dergelijke gevallen moeten herstel en replicatie door de klant worden geconfigureerd.

## <a name="can-i-select-my-regional-pairs"></a>Kan ik mijn regionale paren selecteren?

Nee. Sommige Azure-services zijn afhankelijk van regionale paren, zoals de [redundante opslag](./storage/common/storage-redundancy.md)van Azure. Met deze services u geen nieuwe regionale koppelingen maken.  Omdat Azure de geplande onderhouds- en herstelprioritering voor regionale paren regelt, u uw eigen regionale paren niet definiëren om van deze services te profiteren. U echter uw eigen oplossing voor noodherstel maken door services in een aantal regio's te bouwen en Azure-services te gebruiken om deze te koppelen. 

U azure-services zoals [AzCopy](./storage/common/storage-use-azcopy-v10.md) bijvoorbeeld gebruiken om back-ups van gegevens te plannen naar een opslagaccount in een andere regio.  Met [Azure DNS en Azure Traffic Manager](./networking/disaster-recovery-dns-traffic-manager.md)kunnen klanten een veerkrachtige architectuur voor hun toepassingen ontwerpen die het verlies van de primaire regio zal overleven.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>Ben ik beperkt tot het gebruik van diensten binnen mijn regionale paren?

Nee. Hoewel een bepaalde Azure-service kan vertrouwen op een regionaal paar, u uw andere services hosten in elke regio die voldoet aan uw bedrijfsbehoeften.  Een Azure GRS-opslagoplossing kan gegevens in Canada Central koppelen aan een peer in Canada East terwijl u Compute-resources in Oost-VS gebruikt.  

## <a name="must-i-use-azure-regional-pairs"></a>Moet ik regionale azureparen gebruiken?

Nee. Klanten kunnen Azure-services gebruiken om een veerkrachtige service te ontwerpen zonder te vertrouwen op de regionale paren van Azure.  We raden u echter aan om het herstel van bedrijfscontinuïteitsrampen (BCDR) in regionale paren te configureren om te profiteren van [isolatie](./security/fundamentals/isolation-choices.md) en [de beschikbaarheid](./availability-zones/az-overview.md)te verbeteren. We adviseren voor toepassingen die meerdere actieve regio’s ondersteunen om, waar mogelijk, beide regio’s in een regiopaar te gebruiken. Dit zorgt voor een optimale beschikbaarheid voor toepassingen en een minimale hersteltijd in geval van een ramp. Ontwerp waar mogelijk uw toepassing voor [maximale veerkracht](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview) en het gemak van [disaster recovery.](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)

## <a name="azure-regional-pairs"></a>Azure-regioparen

| Geografie | Regionaal paar A | Regionaal paar B  |
|:--- |:--- |:--- |
| Azië-Pacific |Oost-Azië (Hongkong) | Zuidoost-Azië (Singapore) |
| Australië |Australië - oost |Australië - zuidoost |
| Australië |Australië - centraal |Australië - centraal 2 |
| Brazilië |Brazilië - zuid |VS - zuid-centraal |
| Canada |Canada - midden |Canada - oost |
| China |China - noord |China East|
| China |China Noord 2 |China Oost 2|
| Europa |Noord-Europa (Ierland) |West-Europa (Nederland) |
| Frankrijk |Frankrijk - centraal|Frankrijk - zuid|
| Duitsland |Duitsland - centraal |Duitsland - noordoost |
| India |India - centraal |India - zuid |
| India |India - west |India - zuid |
| Japan |Japan - oost |Japan - west |
| Korea |Korea - centraal |Korea - zuid |
| Noord-Amerika |VS - oost |VS - west |
| Noord-Amerika |VS - oost 2 |VS - centraal |
| Noord-Amerika |VS - noord-centraal |VS - zuid-centraal |
| Noord-Amerika |VS - west 2 |VS - west-centraal |
| Noorwegen | Noorwegen-Oost | Noorwegen West |
| Zuid-Afrika | Zuid-Afrika Noord |Zuid-Afrika West |
| Zwitserland | Zwitserland Noord |Zwitserland West |
| VK |Verenigd Koninkrijk West |Verenigd Koninkrijk Zuid |
| Verenigde Arabische Emiraten | VAE Noord | VAE Centraal
| Ministerie van Defensie van de V.S. |US DoD East |US DoD Central |
| Amerikaanse overheid |VS (overheid) - Arizona |VS (overheid) - Texas |
| Amerikaanse overheid |US Gov - Iowa |VS (overheid) - Virginia |
| Amerikaanse overheid |VS (overheid) - Virginia |VS (overheid) - Texas |

> [!Important]
> - West-India is gekoppeld in een richting alleen. De secundaire regio van West-India is Zuid-India, maar de secundaire regio van Zuid-India is Centraal-India.
> - Brazilië Zuid is uniek omdat het is gekoppeld aan een regio buiten zijn geografie. Brazilië Zuid secundaire regio is South Central VS. De secundaire regio van Zuid-Centraal VS is niet Brazilië Zuid.


## <a name="an-example-of-paired-regions"></a>Een voorbeeld van gekoppelde regio’s
De afbeelding hieronder illustreert een hypothetische toepassing die het regionale paar gebruikt voor disaster recovery. De groene getallen markeren de activiteiten in verschillende regio's van drie Azure-services (Azure compute, storage en database) en de manier waarop ze zijn geconfigureerd om te repliceren in verschillende regio's. De unieke voordelen van het implementeren in gekoppelde regio's worden benadrukt door de oranje getallen.

![Overzicht van voordelen in gepaarde regio's](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figuur 2 – Hypothetisch Azure-regiopaar

## <a name="cross-region-activities"></a>Activiteiten tussen de regio's
Zoals bedoeld in figuur 2.

1. **Azure Compute (IaaS)** - U moet vooraf aanvullende rekenbronnen inrichten om ervoor te zorgen dat resources beschikbaar zijn in een andere regio tijdens een ramp. Zie [technische richtlijnen voor azure-tolerantie](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md)voor meer informatie . 

2. **Azure Storage** - Als u beheerde schijven gebruikt, leest u over [back-ups met meerdere regio's](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) met Azure Backup en [replicaren vm's](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) van de ene regio naar het andere met Azure Site Recovery. Als u opslagaccounts gebruikt, wordt er standaard een GEO-redundante opslag (GRS) geconfigureerd wanneer een Azure Storage-account wordt gemaakt. Met GRS worden uw gegevens automatisch drie keer gerepliceerd binnen de primaire regio en drie keer in het gekoppelde gebied. Zie [Azure Storage Redundancy Options](storage/common/storage-redundancy.md)voor meer informatie.

3. **Azure SQL Database** – Met Azure SQL Database Geo-Replicatie u asynchrone replicatie van transacties configureren naar elke regio in de wereld; We raden u echter aan deze resources in een gekoppelde regio te implementeren voor de meeste scenario's voor noodherstel. Zie [Geo-replicatie in Azure SQL Database](sql-database/sql-database-geo-replication-overview.md)voor meer informatie.

4. **Azure Resource Manager**: Resource Manager biedt inherent logische isolatie van componenten binnen regio’s. Dit betekent dat logische fouten in de ene regio minder snel van invloed zijn op een andere.

## <a name="benefits-of-paired-regions"></a>Voordelen van gekoppelde regio's

5. **Fysieke isolatie** - Indien mogelijk geeft Azure de voorkeur aan ten minste 300 mijl scheiding tussen datacenters in een regionaal paar, hoewel dit niet praktisch of mogelijk is in alle regio's. Fysieke datacenterscheiding vermindert de kans op natuurrampen, maatschappelijke onrust, stroomuitval of fysieke netwerkuitval die beide regio's tegelijk treft. Isolatie is onderworpen aan de beperkingen binnen de geografie (geografische grootte, beschikbaarheid van stroom-/netwerkinfrastructuur, regelgeving, enz.).  

6. **Door platforms geleverde replicatie** - Sommige services, zoals Geo-Redundant Storage, bieden automatische replicatie aan het gekoppelde gebied.

7. **Regioherstelvolgorde** – In het geval van een brede uitval wordt het herstel van één regio uit elk paar geprioriteerd. Toepassingen die binnen gekoppelde regio’s worden geïmplementeerd hebben de garantie dat een van de regio’s met prioriteit wordt hersteld. Als een toepassing wordt geïmplementeerd in regio's die niet zijn gekoppeld, kan het herstel worden vertraagd , in het ergste geval kunnen de gekozen regio's de laatste twee zijn die worden hersteld.

8. **Sequentiële updates** : Geplande Azure-systeemupdates worden achtereenvolgens naar gekoppelde regio's uitgerold (niet tegelijkertijd) om downtime, het effect van bugs en logische fouten in de zeldzame gebeurtenis van een slechte update te minimaliseren.

9. **Data residency** - Een regio woont binnen dezelfde geografie als het paar (met uitzondering van Brazilië Zuid) om te voldoen aan gegevens ingezetenschap eisen voor fiscale en rechtshandhaving jurisdictie doeleinden.
