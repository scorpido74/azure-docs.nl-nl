---
title: Bedrijfs continuïteit & herstel na nood geval-Azure gekoppelde regio's
description: Meer informatie over de regionale koppeling van Azure, om ervoor te zorgen dat toepassingen flexibeler zijn tijdens fouten in het Data Center.
author: rayne-wiselman
manager: carmon
ms.service: multiple
ms.topic: article
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: c1e14db9dafc8b03acbeb1c6b97e5ac0e27cb0fd
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163045"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Bedrijfs continuïteit en herstel na nood gevallen (BCDR): gekoppelde Azure-regio's

## <a name="what-are-paired-regions"></a>Wat zijn gekoppelde regio's?

Azure werkt in meerdere geographs over de hele wereld. Een Azure-geografie is een gedefinieerd gebied van de wereld dat ten minste één Azure-regio bevat. Een Azure-regio is een gebied binnen een geografie met een of meer data centers.

Elke Azure-regio is gekoppeld aan een andere regio binnen dezelfde geografie, waarbij een regionale koppeling wordt gemaakt. De uitzonde ring is Brazilië-zuid, die is gekoppeld aan een regio buiten de geografische wereld. In de regio paren worden de platform updates van Azure geserialiseerd (gepland onderhoud), zodat er slechts één gekoppelde regio tegelijk wordt bijgewerkt. In het geval van een storing die betrekking heeft op meerdere regio's, wordt voor het herstel ten minste één regio in elk paar een prioriteit gegeven.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

> [!NOTE]
> Toegewezen Azure-regionale paren kunnen niet worden gewijzigd.

Afbeelding 1: Azure-regionale paren

| Geografie | Gekoppelde regio's |  |
|:--- |:--- |:--- |
| Azië |Azië - oost |Azië - zuidoost |
| Australië |Australië - oost |Australië - zuidoost |
| Australië |Australië - centraal |Australië - centraal 2 |
| Brazilië |Brazilië - zuid |VS - zuid-centraal |
| Canada |Canada - midden |Canada - oost |
| China |China - noord |China East|
| China |China-noord 2 |China-oost 2|
| Europa |Europa-noord (Ierland) |Europa-west (Nederland) |
| Frankrijk |Frankrijk - centraal|Frankrijk - zuid|
| Duitsland |Duitsland - centraal |Duitsland - noordoost |
| India |India - centraal |India - zuid |
| India |India - west |India - zuid |
| Japan |Japan - oost |Japan - west |
| Korea |Korea - centraal |Korea - zuid |
| Noord-Amerika |VS - oost |VS - west |
| Noord-Amerika |VS - oost 2 |VS - centraal |
| Noord-Amerika |VS - noord-centraal |VS - zuid-centraal |
| Noord-Amerika |VS - west 2 |VS - west-centraal 
| Zuid-Afrika | Zuid-Afrika - noord | Zuid-Afrika - west
| VK |Verenigd Koninkrijk West |Verenigd Koninkrijk Zuid |
| Verenigde Arabische Emiraten | UAE - noord | UAE - centraal
| Amerikaans Ministerie van defensie |US DoD East |US DoD Central |
| Amerikaanse overheid |VS (overheid) - Arizona |VS (overheid) - Texas |
| Amerikaanse overheid |US Gov - Iowa |VS (overheid) - Virginia |
| Amerikaanse overheid |VS (overheid) - Virginia |VS (overheid) - Texas |

Tabel 1-toewijzing van Azure regionale paren

- West-India is in één richting gekoppeld. De secundaire regio van West-India is India-zuid, maar India-zuid secundaire regio is centraal India.
- Brazilië-zuid is uniek omdat het is gekoppeld aan een regio buiten de eigen geografie. De secundaire regio van Brazilië-zuid is Zuid-Centraal vs. De secundaire regio van Zuid-Centraal (VS) is niet Brazilië-zuid.
- De secundaire regio van US Gov-Iowa is US Gov-Virginia.
- De secundaire regio van US Gov-Virginia is US Gov-Texas.
- De secundaire regio van US Gov-Texas is US Gov-Arizona.


We raden u aan om bedrijfs continuïteit nood herstel (BCDR) over regionale paren te configureren om te profiteren van het isolatie-en beschikbaarheids beleid van Azure. Voor toepassingen die ondersteuning bieden voor meerdere actieve regio's, wordt u aangeraden beide regio's in een regio paar waar mogelijk te gebruiken. Dit zorgt voor optimale Beschik baarheid van toepassingen en een geminimaliseerd herstel tijdstip bij een nood geval. 

## <a name="an-example-of-paired-regions"></a>Een voor beeld van gekoppelde regio's
In afbeelding 2 hieronder ziet u een hypothetische toepassing die gebruikmaakt van het regionale paar voor nood herstel. De groene aantallen markeren de activiteiten in meerdere regio's van drie Azure-Services (Azure compute, Storage en data base) en hoe ze zijn geconfigureerd voor replicatie in verschillende regio's. De unieke voor delen van de implementatie in gekoppelde regio's worden gemarkeerd met de Oranje cijfers.

![Overzicht van de voor delen van gekoppelde regio's](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Afbeelding 2: hypothetisch Azure regionaal paar

## <a name="cross-region-activities"></a>Activiteiten tussen regio's
Zoals bedoeld in afbeelding 2.

![IaaS](./media/best-practices-availability-paired-regions/1Green.png) **Azure Compute (IaaS)** : u moet extra reken resources vooraf inrichten om ervoor te zorgen dat resources beschikbaar zijn in een andere regio tijdens een nood geval. Zie voor meer informatie [Azure tolerante technische richt lijnen](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md).

![Storage-](./media/best-practices-availability-paired-regions/2Green.png) **Azure Storage** : als u beheerde schijven gebruikt, kunt u meer informatie vinden over [Cross-Region back-ups](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) met Azure backup en hoe u virtuele machines van de ene naar de andere regio [repliceert](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) met Azure site Recovery. Als u opslag accounts gebruikt, wordt de geo-redundante opslag (GRS) standaard geconfigureerd wanneer een Azure Storage-account wordt gemaakt. Met GRS worden uw gegevens automatisch drie keer binnen de primaire regio gerepliceerd, en drie keer in de gekoppelde regio. Zie [Azure Storage redundantie opties](storage/common/storage-redundancy.md)voor meer informatie.

![Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL database** – met Azure SQL database geo-replicatie kunt u asynchrone replicatie van trans acties naar elke regio in de wereld configureren. We raden u echter aan deze bronnen in een gekoppelde regio te implementeren voor de meeste scenario's voor herstel na nood gevallen. Zie [geo-replicatie in Azure SQL database](sql-database/sql-database-geo-replication-overview.md)voor meer informatie.

![Resource Manager](./media/best-practices-availability-paired-regions/4Green.png) **Azure Resource Manager** -Resource Manager is inherent aan de logische isolatie van onderdelen in verschillende regio's. Dit betekent dat logische fouten in één regio minder waarschijnlijk van invloed zijn op de andere.

## <a name="benefits-of-paired-regions"></a>Voor delen van gekoppelde regio's
Zoals bedoeld in afbeelding 2.  

![isolatie](./media/best-practices-availability-paired-regions/5Orange.png)
**fysieke isolatie** : als dat mogelijk is, geeft Azure de voor keur aan een schei ding van ten minste 300 mijl tussen data centers in een regionale combi natie, hoewel dit niet praktisch of mogelijk is in alle geografische gebieden. Fysieke datacenter schei ding vermindert de kans op natuur rampen, burgerlijke, stroom storingen of fysieke netwerk storingen die beide regio's tegelijkertijd beïnvloeden. Voor isolatie gelden de beperkingen binnen de Geografie (geografische grootte, Beschik baarheid van de infra structuur, voeding, netwerk infrastructuur, enz.).  

![replicatie](./media/best-practices-availability-paired-regions/6Orange.png)
door het **platform geleverde replicatie** -sommige services, zoals geografisch redundante opslag, bieden automatische replicatie naar de gekoppelde regio.

herstel volgorde van ![herstel](./media/best-practices-availability-paired-regions/7Orange.png)
**regio** : in het geval van een grote storing wordt het herstellen van één regio voor elk paar voor rang gegeven. Toepassingen die zijn geïmplementeerd in gekoppelde regio's, hebben gegarandeerd dat een van de regio's met prioriteit wordt hersteld. Als een toepassing wordt geïmplementeerd in meerdere niet-gekoppelde regio's, kan het herstel worden vertraagd: in het ergste geval kunnen de gekozen regio's de laatste twee zijn om te worden hersteld.

![updates](./media/best-practices-availability-paired-regions/8Orange.png)
**opeenvolgende updates** : geplande Azure-systeem updates worden na elkaar uitgerold naar gekoppelde regio's (niet op hetzelfde moment) om de downtime te minimaliseren, het effect van fouten en logische fouten in zeldzame gevallen van een ongeldige update.

![data](./media/best-practices-availability-paired-regions/9Orange.png)
**Data locatie** : een regio bevindt zich in dezelfde geografie als het bijbehorende paar (met uitzonde ring van Brazilië-Zuid) om te voldoen aan de vereisten voor gegevens locatie voor de jurisdictie van belasting en rechts afdwing.
