---
title: Aanbevolen procedures voor langlopende bewerkingen in Azure Analysis Services | Microsoft Documenten
description: In dit artikel worden aanbevolen procedures voor langlopende bewerkingen beschreven.
author: minewiskan
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 3f6b2194cc422a827bbc7a15c012173b3f814b52
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428106"
---
# <a name="best-practices-for-long-running-operations"></a>Aanbevolen procedures voor langlopende bewerkingen

In Azure Analysis Services vertegenwoordigt een *knooppunt* een virtuele hostmachine waarin een serverbron wordt uitgevoerd. Sommige bewerkingen, zoals langlopende query's, vernieuwingsbewerkingen en synchronisatie van query's, kunnen mislukken als een serverbron naar een ander knooppunt wordt verplaatst. Veelvoorkomende foutberichten in dit scenario zijn onder andere:

- "Er is een fout opgetreden tijdens het zoeken naar een langlopendxmla-verzoek. De aanvraag is mogelijk onderbroken door een upgrade van de service of het opnieuw opstarten van de server."
- 'Job with<guid>ID '<database>for model ' ' is geannuleerd vanwege servicefout (inactiviteit) met bericht 'Het vernieuwen van het verzoek annuleren omdat het zonder updates vastzat. Dit is een interne service kwestie. Dien de taak opnieuw in of dien een ticket in om hulp te krijgen als dit probleem herhaaldelijk gebeurt."

Er zijn vele redenen waarom langdurige bewerkingen kunnen worden verstoord. Bijvoorbeeld updates in Azure, zoals: 
- Patches voor het besturingssysteem 
- Beveiligingsupdates
- Updates van azure Analysis Services-service
- Service Fabric-updates. Service Fabric is een platformcomponent die wordt gebruikt door een aantal Microsoft-cloudservices, waaronder Azure Analysis Services.

Naast updates die in de service voorkomen, is er een natuurlijke beweging van services over knooppunten als gevolg van load balancing. Knooppuntbewegingen zijn een verwacht onderdeel van een cloudservice. Azure Analysis Services probeert de impact van knooppuntbewegingen te minimaliseren, maar het is onmogelijk om ze volledig te elimineren. 

Naast knooppuntbewegingen zijn er andere storingen die kunnen optreden. Een gegevensbrondatabasesysteem kan bijvoorbeeld offline zijn of de netwerkverbinding gaat verloren. Als tijdens het vernieuwen een partitie 10 miljoen rijen heeft en er een fout optreedt op de 9 miljoenste rij, is er geen manier om vernieuwen op het punt van fout opnieuw te starten. De service moet opnieuw beginnen vanaf het begin. 

## <a name="refresh-rest-api"></a>REST-API vernieuwen

Serviceonderbrekingen kunnen een uitdaging zijn voor langdurige bewerkingen, zoals het vernieuwen van gegevens. Azure Analysis Services bevat een REST-API om negatieve effecten van serviceonderbrekingen te beperken. Zie [Asynchrone vernieuwing met de REST API](analysis-services-async-refresh.md)voor meer informatie.
 
Naast de REST API zijn er andere benaderingen die u gebruiken om potentiële problemen tijdens langdurige vernieuwingsbewerkingen te minimaliseren. Het belangrijkste doel is om te voorkomen dat u de vernieuwingsbewerking vanaf het begin opnieuw moet starten en in plaats daarvan vernieuwingen uit te voeren in kleinere batches die in fasen kunnen worden vastgelegd. 
 
De REST API maakt een dergelijke herstart mogelijk, maar het biedt geen volledige flexibiliteit van het maken en verwijderen van partities mogelijk. Als een scenario complexe gegevensbeheerbewerkingen vereist, moet uw oplossing een of andere vorm van batching in de logica bevatten. Door bijvoorbeeld transacties te gebruiken om gegevens in meerdere batches te verwerken, zorgt afzonderlijke batches ervoor dat een fout niet opnieuw hoeft te worden opgestart vanaf het begin, maar vanuit een tussenliggend controlepunt. 
 
## <a name="scale-out-query-replicas"></a>Scale-out queryreplica's

Of u nu REST of aangepaste logica gebruikt, clienttoepassingsquery's kunnen nog steeds inconsistente of tussentijdse resultaten retourneren terwijl batches worden verwerkt. Als consistente gegevens die worden geretourneerd door queryquery's van clienttoepassingen vereist tijdens de verwerking en modelgegevens in een tussenliggende status zijn, u [scale-out](analysis-services-scale-out.md) gebruiken met alleen-lezen queryreplica's.

Door alleen-lezen queryreplica's te gebruiken, terwijl vernieuwingen in batches worden uitgevoerd, kunnen gebruikers van clienttoepassingen de oude momentopname van gegevens op de alleen-lezen replica's blijven opvragen. Zodra de vernieuwingen zijn voltooid, kan een Synch-bewerking worden uitgevoerd om de alleen-lezen replica's up-to-date te brengen.


## <a name="next-steps"></a>Volgende stappen

[Asynchroon vernieuwen met de REST API](analysis-services-async-refresh.md)  
[Uitschalen van Azure Analysis Services](analysis-services-scale-out.md)  
[Analysis Services hoge beschikbaarheid](analysis-services-bcdr.md)  
[Richtlijnen opnieuw proberen voor Azure-services](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)   

