---
title: Aanbevolen procedures voor het ontwerpen van Azure Service Fabric-toepassingen
description: Aanbevolen procedures en ontwerpoverwegingen voor het ontwikkelen van toepassingen en services met Azure Service Fabric.
author: markfussell
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: mfussell
ms.openlocfilehash: 56df6e28940eb15597a3d6bccca3f85e5f690f89
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991651"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Aanbevolen procedures voor het ontwerpen van Azure Service Fabric-toepassingen

In dit artikel vindt u richtlijnen voor de beste praktijken voor het bouwen van toepassingen en services op Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Vertrouwd raken met Service Fabric
* Lees het [Artikel Zo wilt u meer weten over Service Fabric?](service-fabric-content-roadmap.md)
* Lees meer over [toepassingsscenario's van Service Fabric](service-fabric-application-scenarios.md).
* Begrijp de opties voor het programmeermodel door het [programmeermodeloverzicht van Service Fabric te](service-fabric-choose-framework.md)lezen.



## <a name="application-design-guidance"></a>Richtlijnen voor het ontwerpen van toepassingen
Maak kennis met de [algemene architectuur](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) van Service Fabric-toepassingen en hun [ontwerpoverwegingen.](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)

### <a name="choose-an-api-gateway"></a>Een API-gateway kiezen
Gebruik een API-gatewayservice die communiceert met back-endservices die vervolgens kunnen worden opgeschaald. De meest gebruikte API-gatewayservices zijn:

- [Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), dat is [geïntegreerd met Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) of [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/), met behulp van de [ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor) om te lezen vanuit Event Hub-partities.
- [Træfik reverse proxy](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/), met behulp van de [Azure Service Fabric provider](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/).
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > Azure Application Gateway is niet direct geïntegreerd met Service Fabric. Azure API Management is meestal de voorkeurskeuze.
- Uw eigen op maat gemaakte ASP.NET Core-webtoepassingsgateway. [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore)

### <a name="stateless-services"></a>Staatloze diensten
We raden u aan om altijd stateless services te bouwen met behulp van [Betrouwbare Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) en status op te slaan in een Azure-database, Azure Cosmos DB of Azure Storage. De externe status is de bekendere benadering voor de meeste ontwikkelaars. Met deze aanpak u ook profiteren van querymogelijkheden in de winkel.  

### <a name="when-to-use-stateful-services"></a>Wanneer stateful services gebruiken
Houd rekening met stateful services wanneer u een scenario hebt voor lage latentie en de gegevens dicht bij de compute moet houden. Enkele voorbeeldscenario's zijn IoT digital twin devices, game state, session state, caching data from a database en langlopende workflows om oproepen naar andere services bij te houden.

Bepaal het tijdsbestek van de gegevensbewaring:

- **Gegevens in cache**opgeslagen . Gebruik caching wanneer latentie naar externe opslag een probleem is. Gebruik een stateful service als uw eigen gegevenscache of overweeg de [open-source SoCreate Service Fabric Distributed Cache te](https://github.com/SoCreate/service-fabric-distributed-cache)gebruiken. In dit scenario hoeft u zich geen zorgen te maken als u alle gegevens in de cache verliest.
- **Tijdgebonden gegevens**. In dit scenario moet u gegevens gedurende een bepaalde periode dicht bij de berekening houden voor latentie, maar u het zich veroorloven om de gegevens te verliezen bij een *ramp.* In veel IoT-oplossingen moeten gegevens bijvoorbeeld dicht bij de berekening zijn, zoals wanneer de gemiddelde temperatuur van de afgelopen dagen wordt berekend, maar als deze gegevens verloren gaan, zijn de geregistreerde gegevensniet zo belangrijk. Ook in dit scenario hoeft u niet typisch de zorg over een back-up van de afzonderlijke gegevens punten. U maakt alleen een back-up van berekende gemiddelde waarden die periodiek naar externe opslag worden geschreven.  
- **Langetermijngegevens**. Betrouwbare collecties kunnen uw gegevens permanent opslaan. Maar in dit geval moet u [zich voorbereiden op herstel na noodgevallen,](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery)inclusief het configureren van periodiek [back-upbeleid](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) voor uw clusters. In feite configureert u wat er gebeurt als uw cluster wordt vernietigd in een ramp, waar u een nieuw cluster moet maken en hoe u nieuwe toepassingsinstanties implementeert en herstelt van de nieuwste back-up.

Bespaar kosten en verbeter de beschikbaarheid:
- U de kosten verlagen door stateful services te gebruiken omdat u geen kosten voor gegevenstoegang en transactiekosten maakt vanuit de externe winkel en omdat u geen andere service hoeft te gebruiken, zoals Azure Cache voor Redis.
- Het gebruik van stateful services voornamelijk voor opslag en niet voor compute is duur, en we raden het niet aan. Denk aan stateful diensten als compute met goedkope lokale opslag.
- Door afhankelijkheden van andere services te verwijderen, u de beschikbaarheid van uw service verbeteren. Als u de status met HA in het cluster beheert, wordt u geïsoleerd van andere service-downtimes of latentieproblemen.

## <a name="how-to-work-with-reliable-services"></a>Werken met betrouwbare services
Service Fabric Reliable Services stelt u in staatsloze en stateful services te creëren. Zie voor meer informatie de [inleiding tot Betrouwbare Diensten](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Houd u altijd aan het [annuleringstoken](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) in `RunAsync()` de `ChangeRole()` methode voor stateloze en statelijke services en de methode voor stateful services. Als u dit niet doet, weet Service Fabric niet of uw service kan worden gesloten. Als u bijvoorbeeld het annuleringstoken niet nakomt, kunnen er veel langere upgradetijden voor toepassingen optreden.
-    Open en sluit [communicatieluisteraars](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) tijdig en eer de annuleringstokens.
-    Meng nooit synchronisatiecode met async-code. Gebruik bijvoorbeeld niet `.GetAwaiter().GetResult()` in uw async-gesprekken. Gebruik async *helemaal* door de callstack.

## <a name="how-to-work-with-reliable-actors"></a>Hoe te werken met betrouwbare actoren
Service Fabric Reliable Actors stelt u in staat om eenvoudig stateful, virtuele acteurs te maken. Zie voor meer informatie de [inleiding tot betrouwbare actoren.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)

- Serieus overwegen met behulp van pub / sub messaging tussen uw acteurs voor het schalen van uw toepassing. Hulpprogramma's die deze service bieden, zijn de [open-source SoCreate Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) en [Azure Service Bus.](https://docs.microsoft.com/azure/service-bus/)
- Maak de actorstatus zo [gedetailleerd mogelijk](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Beheer de levenscyclus van de [acteur.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices) Verwijder acteurs als je ze niet meer gaat gebruiken. Het verwijderen van overbodige acteurs is vooral belangrijk wanneer u de [vluchtige statusprovider](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication)gebruikt, omdat alle status in het geheugen is opgeslagen.
- Vanwege hun [turn-based gelijktijdigheid](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency), acteurs kunnen het best worden gebruikt als onafhankelijke objecten. Maak geen grafieken van multi-actor, synchrone methodeaanroepen (waarvan het meest waarschijnlijk een afzonderlijk netwerkgesprek wordt) of maak geen ronde actoraanvragen. Deze zullen de prestaties en schaal aanzienlijk beïnvloeden.
- Meng synchronisatiecode niet met een synchronisatiecode. Gebruik async consequent om prestatieproblemen te voorkomen.
- Maak geen langlopende gesprekken met acteurs. Langlopende gesprekken blokkeren andere oproepen naar dezelfde actor, vanwege de turn-based gelijktijdigheid.
- Als u communiceert met andere services met behulp van Service `ServiceProxyFactory`Fabric [remoting](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) en u maakt een , maak de fabriek op het niveau van de [actor-service](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) en *niet* op actor-niveau.


## <a name="application-diagnostics"></a>Toepassingsdiagnostiek
Wees grondig over het toevoegen van [applicatie logging](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) in service calls. Het zal u helpen bij het diagnosticeren van scenario's waarin services elkaar bellen. Wanneer A-gesprekken B bijvoorbeeld C-aanroept, kan het gesprek overal mislukken. Als u niet genoeg logboekregistratie hebt, zijn fouten moeilijk te diagnosticeren. Als de services te veel registreren vanwege oproepvolumes, moet u op zijn minst fouten en waarschuwingen registreren.

## <a name="iot-and-messaging-applications"></a>IoT- en berichtentoepassingen
Wanneer u berichten leest vanuit [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) of [Azure Event Hubs,](https://docs.microsoft.com/azure/event-hubs/)gebruikt u [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/ServiceFabricProcessor). ServiceFabricProcessor integreert met Service Fabric Reliable Services om de staat van lezen van de gebeurtenishubpartities te behouden en stuurt nieuwe berichten naar uw services via de `IEventProcessor::ProcessEventsAsync()` methode.


## <a name="design-guidance-on-azure"></a>Ontwerprichtlijnen voor Azure
* Ga naar het [Azure-architectuurcentrum](https://docs.microsoft.com/azure/architecture/microservices/) voor ontwerprichtlijnen voor [het bouwen van microservices op Azure.](https://docs.microsoft.com/azure/architecture/microservices/)

* Ga [aan de slag met Azure for Gaming](https://docs.microsoft.com/gaming/azure/) voor ontwerprichtlijnen voor het gebruik van Service Fabric in [gamingservices.](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)
