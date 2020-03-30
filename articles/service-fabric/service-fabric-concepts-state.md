---
title: Status beheren in Azure Service Fabric-services
description: Meer informatie over status in Azure Service Fabric, inclusief het definiëren en beheren van de servicestatus in Service Fabric-services.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9acd3031d1071d1822791b333976aaf76161600f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614567"
---
# <a name="service-state"></a>Servicestatus
**Servicestatus** verwijst naar de in-memory of op schijfgegevens die een service nodig heeft om te functioneren. Het bevat bijvoorbeeld de gegevensstructuren en lidvariabelen die de service leest en schrijft om werk te doen. Afhankelijk van de manier waarop de service is ontworpen, kan deze ook bestanden of andere bronnen bevatten die op schijf zijn opgeslagen. De bestanden die een database bijvoorbeeld gebruikt om gegevens en transactielogboeken op te slaan.

Als voorbeeldservice, laten we eens kijken naar een rekenmachine. Een basisrekenmachineservice neemt twee nummers en geeft de som terug. Het uitvoeren van deze berekening omvat geen lid variabelen of andere informatie.

Overweeg nu dezelfde rekenmachine, maar met een extra methode voor het opslaan en retourneren van de laatste som die het heeft berekend. Deze dienst is nu stateful. Stateful betekent dat het bevat een staat die het schrijft wanneer het berekent een nieuwe som en leest vanaf het moment dat u het vraagt om de laatste berekende som terug te keren.

In Azure Service Fabric wordt de eerste service een stateless service genoemd. De tweede service wordt een stateful service genoemd.

## <a name="storing-service-state"></a>Servicestatus opslaan
Status kan worden geexternaliseerd of naast de code die de status manipuleert. Externalisatie van de status wordt meestal gedaan met behulp van een externe database of andere gegevens op te slaan die wordt uitgevoerd op verschillende machines via het netwerk of buiten het proces op dezelfde machine. In ons voorbeeld van rekenmachine kan het gegevensarchief een SQL-database of instantie van Azure Table Store zijn. Elk verzoek om de som te berekenen voert een update uit voor deze gegevens en verzoekt de service om de waarde te retourneren resulteren in de huidige waarde die uit het archief wordt opgehaald. 

Status kan ook worden geplaatst met de code die de status manipuleert. Stateful services in Service Fabric worden meestal met behulp van dit model gebouwd. Service Fabric biedt de infrastructuur om ervoor te zorgen dat deze status zeer beschikbaar, consistent en duurzaam is en dat de op deze manier gebouwde services gemakkelijk kunnen worden geschaald.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over servicefabric-concepten:

* [Beschikbaarheid van Service Fabric-services](service-fabric-availability-services.md)
* [Schaalbaarheid van servicefabric-services](service-fabric-concepts-scalability.md)
* [Services voor partitioneringsservice](service-fabric-concepts-partitioning.md)
* [Betrouwbare services voor servicefabric](service-fabric-reliable-services-introduction.md)
