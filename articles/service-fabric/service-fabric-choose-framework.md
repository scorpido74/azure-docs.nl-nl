---
title: Overzicht van het Service Fabric-programmeermodel
description: 'Service Fabric biedt twee frameworks voor het bouwen van diensten: het actor framework en het dienstenframework. Ze bieden verschillende trade-offs in eenvoud en controle.'
author: vturecek
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: 11e32c9d1290227e638a314ed8417b1bed906842
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749533"
---
# <a name="service-fabric-programming-model-overview"></a>Overzicht van het Service Fabric-programmeermodel

Service Fabric biedt meerdere manieren om uw services te schrijven en te beheren. Services kunnen ervoor kiezen om de API's van Service Fabric te gebruiken om optimaal te profiteren van de functies en toepassingsframeworks van het platform. Services kunnen ook elk gecompileerd uitvoerbaar programma zijn dat is geschreven in elke taal of code die wordt uitgevoerd in een container die wordt gehost op een cluster van ServiceFabric.

## <a name="guest-executables"></a>Uitvoerbare gastbestanden

Een [gast uitvoerbaar](service-fabric-guest-executables-introduction.md) is een bestaande, willekeurige uitvoerbare (geschreven in elke taal) die kan worden uitgevoerd als een dienst in uw toepassing. Gastexecutables bellen niet rechtstreeks naar de Service Fabric SDK API's. Ze profiteren echter nog steeds van functies die het platform biedt, zoals servicevindbaarheid, aangepaste status en belastingrapportage door REST-API's aan te roepen die door Service Fabric worden blootgesteld. Ze hebben ook volledige ondersteuning voor de levenscyclus van toepassingen.

Ga aan de slag met gastuitvoerbare bestanden door uw eerste [gastuitvoerbare toepassing te](service-fabric-deploy-existing-app.md)implementeren.

## <a name="containers"></a>Containers

Service Fabric implementeert en activeert services standaard als processen. Service Fabric kan ook services implementeren in [containers.](service-fabric-containers-overview.md) Service Fabric ondersteunt implementatie van Linux-containers en Windows-containers op Windows Server 2016 en hoger. Containerafbeeldingen kunnen uit elke containeropslagplaats worden getrokken en naar de machine worden geïmplementeerd. U bestaande toepassingen implementeren als gastuitvoerbare, servicefabriceerbare of stateful Betrouwbare services of Betrouwbare Actoren in containers, en u services in processen en services in containers in dezelfde toepassing mengen.

[Meer informatie over het containeriseren van uw services in Windows of Linux](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services

Reliable Services is een lichtgewicht framework voor het schrijven van services die integreren met het Service Fabric-platform en profiteren van de volledige set platformfuncties. Betrouwbare services bieden een minimale set API's waarmee de runtime van De Service Fabric de levenscyclus van uw services kan beheren en waarmee uw services kunnen communiceren met de runtime. Het toepassingskader is minimaal, waardoor u volledige controle hebt over ontwerp- en implementatiekeuzes en kan worden gebruikt om een ander toepassingskader te hosten, zoals ASP.NET Core.

Betrouwbare services kunnen stateloos zijn, vergelijkbaar met de meeste serviceplatforms, zoals webservers, waarin elke instantie van de service gelijk wordt gemaakt en de status wordt gehandhaafd in een externe oplossing, zoals Azure DB of Azure Table Storage.

Reliable Services is exclusief voor Service Fabric en kan ook stateful zijn, waarbij de staat rechtstreeks in de service zelf wordt gebruikt met behulp van Betrouwbare Collecties. Status wordt zeer beschikbaar gemaakt door middel van replicatie en gedistribueerd via partitionering, allemaal automatisch beheerd door Service Fabric.

[Meer informatie over betrouwbare services](service-fabric-reliable-services-introduction.md) of ga aan de slag met het schrijven van uw eerste betrouwbare [service.](service-fabric-reliable-services-quick-start.md)

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core is een open-source, cross-platform framework voor het bouwen van moderne cloudgebaseerde internetgebaseerde applicaties, zoals webapps, IoT-apps en mobiele backends. Service Fabric integreert met ASP.NET Core, zodat u zowel stateless als stateful ASP.NET Core-toepassingen schrijven die profiteren van de geavanceerde orchestration-mogelijkheden van Reliable Collections en Service Fabric.

[Meer informatie over ASP.NET Core in Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) of ga aan de slag door je eerste ASP.NET Core Service [Fabric-toepassing](service-fabric-tutorial-create-dotnet-app.md)te schrijven.

## <a name="reliable-actors"></a>Reliable Actors

Gebouwd op de top van Betrouwbare Services, de Reliable Actor kader is een applicatie kader dat de [Virtual Actor](https://research.microsoft.com/en-us/projects/orleans/) patroon implementeert, gebaseerd op de computationele [actor model](https://en.wikipedia.org/wiki/Actor_model). De Reliable Actor kader maakt gebruik van onafhankelijke eenheden van compute en staat met single-threaded uitvoering genaamd *acteurs*. Het Reliable Actor-framework biedt ingebouwde communicatie voor actoren en vooraf ingestelde staatspersistentie en scale-outconfiguraties.

Omdat Reliable Actors een applicatieframework is dat is gebaseerd op betrouwbare services, is het volledig geïntegreerd met het Service Fabric-platform en profiteert het van de volledige set functies die door het platform worden aangeboden.

[Meer informatie over betrouwbare acteurs](service-fabric-reliable-actors-introduction.md) of aan de slag met het schrijven van uw eerste Reliable [Actor-service](service-fabric-reliable-actors-get-started.md)

[Een front-end service bouwen met ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Service Fabric en containers](service-fabric-containers-overview.md)

[Overzicht van Reliable Services](service-fabric-reliable-services-introduction.md)

[Overzicht van Reliable Actors](service-fabric-reliable-actors-introduction.md)

[Service fabric en ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)
