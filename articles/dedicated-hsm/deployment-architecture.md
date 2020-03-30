---
title: Implementatiearchitectuur - Azure Dedicated HSM | Microsoft Documenten
description: Basisontwerpoverwegingen bij het gebruik van Azure Dedicated HSM als onderdeel van een toepassingsarchitectuur
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 89e3bf95a6b048e5e97cfb151ef9302b70eac1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048556"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Implementatiearchitectuur van Azure Dedicated HSM

Azure Dedicated HSM biedt cryptografische sleutelopslag in Azure. Het voldoet aan strenge veiligheidseisen. Klanten profiteren van het gebruik van Azure Dedicated HSM als ze:

* Moet voldoen aan fips 140-2 niveau 3 certificering
* Vereisen dat ze exclusieve toegang hebben tot de HSM
* moeten volledige controle over hun apparaten hebben

De HSM's worden verspreid over de datacenters van Microsoft en kunnen eenvoudig worden ingericht als een paar apparaten als basis voor een zeer beschikbare oplossing. Ze kunnen ook worden ingezet in verschillende regio's voor een rampbestendige oplossing. De regio's met Dedicated HSM die momenteel beschikbaar zijn, zijn:

* VS - oost
* VS - oost 2
* VS - west
* VS - zuid-centraal
* Azië - zuidoost
* Azië - oost
* India - centraal
* India - zuid
* Japan - oost
* Japan - west
* Europa - noord
* Europa -west
* Verenigd Koninkrijk Zuid
* Verenigd Koninkrijk West
* Canada - midden
* Canada - oost
* Australië - oost
* Australië - zuidoost

Elk van deze regio's heeft HSM-racks die zijn geïmplementeerd in twee onafhankelijke datacenters of ten minste twee onafhankelijke beschikbaarheidszones. Zuidoost-Azië heeft drie beschikbaarheidszones en East US 2 heeft er twee. Er zijn in totaal acht regio's in Europa, Azië en de VS die de Dedicated HSM-service aanbieden. Zie de officiële [azure-regio's voor](https://azure.microsoft.com/global-infrastructure/regions/)meer informatie over Azure-regio's.
Sommige ontwerpfactoren voor een dedicated HSM-gebaseerde oplossing zijn locatie/latentie, hoge beschikbaarheid en ondersteuning voor andere gedistribueerde toepassingen.

## <a name="device-location"></a>Locatie apparaat

Optimale HSM-apparaatlocatie is in de buurt van de toepassingen die cryptografische bewerkingen uitvoeren. In-region latency wordt verwacht dat single-digit milliseconden. De latentie tussen regio's kan 5 tot 10 keer hoger zijn dan dit.

## <a name="high-availability"></a>Hoge beschikbaarheid

Om een hoge beschikbaarheid te bereiken, moet een klant twee HSM-apparaten gebruiken in een regio die zijn geconfigureerd met Behulp van Gemalto-software als een combinatie met hoge beschikbaarheid. Dit type implementatie zorgt voor de beschikbaarheid van sleutels als een enkel apparaat een probleem ondervindt waardoor het sleutelbewerkingen niet kan verwerken. Het vermindert ook aanzienlijk risico bij het uitvoeren van break / fix onderhoud, zoals voeding vervanging. Het is belangrijk dat een ontwerp rekening houdt met elke vorm van falen op regionaal niveau. Storingen op regionaal niveau kunnen optreden wanneer er natuurrampen zijn, zoals orkanen, overstromingen of aardbevingen. Dit soort gebeurtenissen moet worden beperkt door HSM-apparaten in een andere regio in te richten. Apparaten die in een andere regio worden geïmplementeerd, kunnen worden gekoppeld via de configuratie van de Gemalto-software. Dit betekent dat de minimale implementatie voor een zeer beschikbare en rampbestendige oplossing vier HSM-apparaten in twee regio's is. Lokale redundantie en redundantie in verschillende regio's kunnen worden gebruikt als basislijn om eventuele verdere HSM-apparaatimplementaties toe te voegen om latentie, capaciteit of andere toepassingsspecifieke vereisten te ondersteunen.

## <a name="distributed-application-support"></a>Ondersteuning voor gedistribueerde toepassingen

Speciale HSM-apparaten worden doorgaans geïmplementeerd ter ondersteuning van toepassingen die belangrijke opslag- en sleutelophaalbewerkingen moeten uitvoeren. Speciale HSM-apparaten hebben 10 partities voor onafhankelijke toepassingsondersteuning. Apparaatlocatie moet gebaseerd zijn op een holistische weergave van alle toepassingen die de service moeten gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zodra de implementatiearchitectuur is bepaald, worden de meeste configuratieactiviteiten om die architectuur te implementeren door Gemalto geleverd. Dit omvat apparaatconfiguratie en toepassingsintegratiescenario's. Voor meer informatie u gebruikmaken van de [Gemalto customer support](https://supportportal.gemalto.com/csm/) portal en download beheer en configuratie gidsen. De Microsoft-partnersite heeft een verscheidenheid aan integratiehandleidingen.
Het wordt aanbevolen dat alle belangrijke concepten van de service, zoals hoge beschikbaarheid en beveiliging bijvoorbeeld, goed worden begrepen voordat apparaatinrichting of toepassingsontwerp en -implementatie worden geïmplementeerd.
Verdere concept niveau onderwerpen:

* [Hoge beschikbaarheid](high-availability.md)
* [Fysieke beveiliging](physical-security.md)
* [Networking](networking.md)
* [Ondersteuning](supportability.md)
* [Monitoring](monitoring.md)
