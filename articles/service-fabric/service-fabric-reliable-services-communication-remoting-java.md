---
title: Serviceremoting met Java in Azure Service Fabric
description: Service Fabric remoting stelt klanten en services in staat om te communiceren met Java-services met behulp van een externe procedureoproep.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: eef63d7a2c8a4b15938dfbffd7db5f9d1b22d426
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426640"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Service remoting in Java met betrouwbare services
> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java op Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Voor services die niet zijn gekoppeld aan een bepaald communicatieprotocol of -stack, zoals WebAPI, Windows Communication Foundation (WCF) of andere, biedt het Framework Betrouwbare Services een remoting-mechanisme om snel en eenvoudig externe procedureoproepen in te stellen Diensten.  In dit artikel wordt besproken hoe u externe procedureoproepen instellen voor services die met Java zijn geschreven.

## <a name="set-up-remoting-on-a-service"></a>Remoting instellen op een service
Remoting instellen voor een service gebeurt in twee eenvoudige stappen:

1. Maak een interface die uw service kan implementeren. Deze interface definieert de methoden die beschikbaar zijn voor een externe procedureaanroep op uw service. De methoden moeten asynchrone methoden voor taakterugsturen zijn. De interface `microsoft.serviceFabric.services.remoting.Service` moet implementeren om aan te geven dat de service een remoting-interface heeft.
2. Gebruik een remoting luisteraar in uw dienst. Dit is `CommunicationListener` een implementatie die remoting mogelijkheden biedt. `FabricTransportServiceRemotingListener`kan worden gebruikt om een remoting listener te maken met behulp van de standaard remoting transport protocol.

De volgende stateloze service legt bijvoorbeeld één methode bloot om 'Hello World' te krijgen via een externe procedureoproep.

```java
import java.util.ArrayList;
import java.util.concurrent.CompletableFuture;
import java.util.List;
import microsoft.servicefabric.services.communication.runtime.ServiceInstanceListener;
import microsoft.servicefabric.services.remoting.Service;
import microsoft.servicefabric.services.runtime.StatelessService;

public interface MyService extends Service {
    CompletableFuture<String> helloWorldAsync();
}

class MyServiceImpl extends StatelessService implements MyService {
    public MyServiceImpl(StatelessServiceContext context) {
       super(context);
    }

    public CompletableFuture<String> helloWorldAsync() {
        return CompletableFuture.completedFuture("Hello!");
    }

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
        listeners.add(new ServiceInstanceListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        return listeners;
    }
}
```

> [!NOTE]
> De argumenten en de retourtypen in de service-interface kunnen elke eenvoudige, complexe of aangepaste typen zijn, maar ze moeten serializable zijn.
>
>

## <a name="call-remote-service-methods"></a>Externe servicemethoden aanroepen
Aanroepen methoden op een service met behulp van de remoting stack `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` wordt gedaan met behulp van een lokale proxy om de dienst via de klasse. De `ServiceProxyBase` methode maakt een lokale proxy met dezelfde interface die de service implementeert. Met die proxy u eenvoudig methoden op de interface op afstand aanroepen.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

Het remoting-framework propt uitzonderingen die bij de service naar de client worden gegooid. Dus uitzondering-handling logica op `ServiceProxyBase` de client met behulp van kan direct omgaan met uitzonderingen die de service gooit.

## <a name="service-proxy-lifetime"></a>Levensduur van serviceproxy
ServiceProxy creatie is een lichtgewicht bewerking, dus je er zoveel maken als je nodig hebt. Serviceproxy-exemplaren kunnen worden hergebruikt zolang ze nodig zijn. Als een externe procedureoproep een uitzondering genereert, u nog steeds dezelfde proxy-instantie opnieuw gebruiken. Elke ServiceProxy bevat een communicatieclient die wordt gebruikt om berichten via de draad te verzenden. Terwijl u een beroep doet op externe oproepen, worden interne controles uitgevoerd om te bepalen of de communicatieclient geldig is. Op basis van de resultaten van deze controles wordt de communicatieclient indien nodig opnieuw gemaakt. Als er een uitzondering optreedt, hoeft `ServiceProxy`u daarom niet opnieuw te maken.

### <a name="serviceproxyfactory-lifetime"></a>Levensduur van ServiceProxyFactory
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) is een fabriek die proxy creëert voor verschillende remoting interfaces. Als u `ServiceProxyBase.create` API gebruikt voor het `FabricServiceProxyFactory`maken van proxy, maakt framework een .
Het is handig om er handmatig een te maken wanneer u [serviceremotingclientfactory-eigenschappen](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory) moet overschrijven.
Fabriek is een dure operatie. `FabricServiceProxyFactory`onderhoudt cache van communicatieclients.
Het beste is `FabricServiceProxyFactory` om cache zo lang mogelijk.

## <a name="remoting-exception-handling"></a>Verwerking van remoting-uitzonderingen
Alle externe uitzondering die door de service-API wordt gegenereerd, wordt teruggestuurd naar de client als RuntimeException of FabricException.

ServiceProxy verwerkt wel alle Failover uitzondering voor de servicepartitie waarvoor deze is gemaakt. De eindpunten worden opnieuw opgelost als er failoveruitzonderingen (niet-tijdelijke uitzonderingen) zijn en wordt de aanroep opnieuw geprobeerd met het juiste eindpunt. Aantal nieuwe pogingen voor failover uitzondering is voor onbepaalde tijd.
In het geval van Tijdelijke Uitzonderingen wordt de oproep alleen opnieuw geprobeerd.

Standaardparameters voor het opnieuw proberen worden toegestaan door [OperationRetrySettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
U deze waarden configureren door het object OperationRetrySettings door te geven aan de constructor ServiceProxyFactory.

## <a name="next-steps"></a>Volgende stappen
* [Communicatie beveiligen voor betrouwbare services](service-fabric-reliable-services-secure-communication-java.md)
