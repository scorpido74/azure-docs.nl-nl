---
title: Implementatiearchitectuur van - Azure toegewezen HSM | Microsoft Docs
description: Basisontwerp overwegingen bij het gebruik van Azure toegewezen HSM als onderdeel van de toepassingsarchitectuur van een
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
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048556"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Implementatiearchitectuur van Azure Dedicated HSM

Azure toegewezen HSM biedt opslag van cryptografische sleutels in Azure. Deze voldoet aan strenge beveiligingsvereisten. Klanten profiteren van Azure toegewezen HSM als ze:

* FIPS moet 140-2 Level 3-certificering voldoen
* Vereisen dat ze exclusieve toegang tot de HSM hebben
* volledige controle over hun apparaten moeten hebben

De HSM's worden verdeeld over de Microsoft-datacenters en kunnen eenvoudig worden ingericht als een paar apparaten als de basis van een maximaal beschikbare oplossing. Ze kunnen ook worden geïmplementeerd in regio's voor een robuuste oplossing voor noodherstel. De regio's met toegewezen HSM die beschikbaar zijn op dit moment:

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

Elk van deze regio's heeft HSM rekken geïmplementeerd in datacenters met twee onafhankelijke of ten minste twee onafhankelijke beschikbaarheidszones. Zuidoost-Azië heeft drie beschikbaarheidszones en VS-Oost 2 heeft twee. Er is een totaal van acht regio's in Europa, Azië en de VS die worden geboden door de toegewezen HSM-service. Zie de officiële [informatie over Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/)voor meer informatie over Azure-regio's.
Bepaalde factoren ontwerp voor een oplossing op basis van toegewezen HSM locatie/latentie, hoge beschikbaarheid en ondersteuning voor andere gedistribueerde toepassingen.

## <a name="device-location"></a>Locatie apparaat

Locatie van het apparaat optimale HSM is in de dichtstbijzijnde nabijheid tot de toepassingen uitvoeren van cryptografische bewerkingen. Regionale latentie wordt verwacht één cijfer milliseconden. Regio-overschrijdende latentie kan 5 tot 10 keer hoger zijn dan dit.

## <a name="high-availability"></a>Hoge beschikbaarheid

Voor het bereiken van hoge beschikbaarheid, moet een klant twee HSM-apparaten in een regio die zijn geconfigureerd met behulp van Gemalto software als een paar met hoge beschikbaarheid gebruiken. Dit type implementatie zorgt ervoor dat de beschikbaarheid van sleutels als er een probleem te voorkomen dat deze sleutel verwerkingen is een enkel apparaat. Bovendien vermindert het risico bij het uitvoeren van onderhoud, zoals power supply vervanging ondersteuning voor probleemoplossing. Het is belangrijk voor een ontwerp voor elk soort regionaal niveau. Storingen in andere niveau kunnen gebeuren wanneer er natuurrampen zoals orkanen, overstromingen of aardbevingen. Deze typen gebeurtenissen moeten worden verholpen door het inrichten van HSM-apparaten in een andere regio. Apparaten die zijn geïmplementeerd in een andere regio kunnen samen worden gekoppeld via de softwareconfiguratie voor Gemalto. Dit betekent dat de minimale implementatie voor een maximaal beschikbare en noodherstel flexibele oplossing is vier HSM-apparaten in twee regio's. Lokale redundantie en redundantie in regio's kunnen worden gebruikt als een basislijn toe te voegen verdere HSM-apparaat implementaties voor de ondersteuning van latentie, capaciteit of om te voldoen aan andere vereisten toepassingsspecifieke.

## <a name="distributed-application-support"></a>Gedistribueerde toepassingsondersteuning

Toegewezen HSM-apparaten worden doorgaans geïmplementeerd ter ondersteuning van toepassingen die nodig zijn om uit te voeren van de opslag van sleutels en bewerkingen voor het sleutel ophalen. Toegewezen HSM apparaten hebben 10 partities voor ondersteuning van onafhankelijke toepassing. Locatie van het apparaat moet worden gebaseerd op een holistische weergave van alle toepassingen die u nodig hebt om de service te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zodra de implementatie-architectuur wordt bepaald, worden de meeste configuratieactiviteiten te implementeren die architectuur wordt geleverd door Gemalto. Dit omvat zowel apparaatconfiguratie als toepassing integratiescenario's. Voor meer informatie gebruikt u de Gemalto-portal voor [klanten ondersteuning](https://supportportal.gemalto.com/csm/) en downloadt u beheer-en configuratie handleidingen. De website van Microsoft-partner heeft een aantal integratiehandleidingen.
Het is raadzaam dat alle belangrijke concepten van de service, zoals hoge beschikbaarheid en beveiliging bijvoorbeeld duidelijk zijn voordat het apparaat inrichten of toepassingsontwerp en implementatie.
Verdere concept niveau onderwerpen:

* [Hoge beschikbaarheid](high-availability.md)
* [Fysieke beveiliging](physical-security.md)
* [Netwerken](networking.md)
* [Ondersteuning](supportability.md)
* [Controle](monitoring.md)
