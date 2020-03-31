---
title: Maak uw eerste betrouwbare service in Java
description: Inleiding tot het maken van een Microsoft Azure Service Fabric-toepassing met stateloze en stateful services.
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: c3b301a7a9039f1fe8095950f0a5a4e23eb52a9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614210"
---
# <a name="get-started-with-reliable-services-in-java"></a>Aan de slag met Betrouwbare Services in Java
> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-services-quick-start.md)
> * [Java op Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

In dit artikel worden de basisprincipes van Azure Service Fabric Reliable Services uitgelegd en u een eenvoudige Reliable Service-toepassing maken en implementeren die op Java is geschreven. 

## <a name="installation-and-setup"></a>Installatie en installatie
Zorg ervoor dat u de ontwikkelomgeving servicestof op uw machine hebt ingesteld voordat u begint.
Als je het moet instellen, ga dan aan [de slag op Mac](service-fabric-get-started-mac.md) of aan de slag op [Linux.](service-fabric-get-started-linux.md)

## <a name="basic-concepts"></a>Basisbegrippen
Om aan de slag te gaan met Betrouwbare Services, hoeft u slechts een paar basisconcepten te begrijpen:

* **Servicetype**: Dit is uw service-implementatie. Het wordt gedefinieerd door de klasse `StatelessService` die u schrijft die zich uitbreidt en alle andere code of afhankelijkheden die daarin worden gebruikt, samen met een naam en een versienummer.
* **Benoemde serviceinstantie**: Als u uw service wilt uitvoeren, maakt u benoemde exemplaren van uw servicetype, net zoals u objectinstanties van een klassetype maakt. Service-exemplaren zijn in feite objectinstantiaties van uw serviceklasse die u schrijft.
* **Servicehost:** de benoemde service-exemplaren die u maakt, moeten worden uitgevoerd in een host. De servicehost is slechts een proces waarbij exemplaren van uw service kunnen worden uitgevoerd.
* **Serviceregistratie**: Registratie brengt alles samen. Het servicetype moet zijn geregistreerd bij de runtime van Service Fabric in een servicehost, zodat Service Fabric exemplaren van de run kan maken.  

## <a name="create-a-stateless-service"></a>Een stateloze service maken
Begin met het maken van een Service Fabric-toepassing. De Service Fabric SDK voor Linux bevat een Yeoman generator om de steigers voor een Service Fabric applicatie te voorzien van een stateless service. Begin met de volgende opdracht Yeoman:

```bash
$ yo azuresfjava
```

Volg de instructies om een **betrouwbare service zonder staat**te maken. Voor deze zelfstudie, de naam van de toepassing "HelloWorldApplication" en de dienst "HelloWorld". Het resultaat omvat mappen `HelloWorldApplication` `HelloWorld`voor de en .

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```
### <a name="service-registration"></a>Serviceregistratie
Servicetypen moeten zijn geregistreerd bij de runtime van Service Fabric. Het servicetype wordt gedefinieerd `ServiceManifest.xml` in de serviceklasse `StatelessService`en de serviceklasse die implementeert. Serviceregistratie wordt uitgevoerd in het hoofdtoegangspunt van het proces. In dit voorbeeld is `HelloWorldServiceHost.java`het hoofdtoegangspunt van het proces:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="implement-the-service"></a>De service implementeren

Open **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Deze klasse definieert het servicetype en kan elke code uitvoeren. De service-API biedt twee toegangspunten voor uw code:

* Een open-end entry point-methode, genaamd `runAsync()`, waar u beginnen met het uitvoeren van workloads, inclusief langlopende compute-workloads.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Een communicatietoegangspunt waar u uw communicatiestack naar keuze aansluiten. Hier u aanvragen ontvangen van gebruikers en andere services.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Deze zelfstudie `runAsync()` richt zich op de instapmethode. Hier u meteen beginnen met het uitvoeren van uw code.

### <a name="runasync"></a>RunAsync
Het platform roept deze methode aan wanneer een instantie van een service is geplaatst en klaar is om uit te voeren. Voor een staatloze service betekent dit wanneer de service-instantie wordt geopend. Er wordt een annuleringstoken verstrekt om te coördineren wanneer uw service-instantie moet worden gesloten. In Service Fabric kan deze open/sluitcyclus van een service-instantie gedurende de levensduur van de service als geheel vele malen voorkomen. Dit kan om verschillende redenen gebeuren, waaronder:

* Het systeem verplaatst uw service-exemplaren voor resourcebalancing.
* Er treden fouten op in uw code.
* De toepassing of het systeem wordt geüpgraded.
* De onderliggende hardware ondervindt een storing.

Deze orkestratie wordt beheerd door Service Fabric om uw service zeer beschikbaar en goed in balans te houden.

`runAsync()`mag niet synchroon blokkeren. Uw implementatie van runAsync moet een CompletableFuture retourneren zodat de runtime kan worden voortgezet. Als uw werkbelasting een langlopende taak moet implementeren die moet worden uitgevoerd binnen de CompletableFuture.

#### <a name="cancellation"></a>Opzegging
Het annuleren van uw werklast is een coöperatieve inspanning die wordt georkestreerd door het opgegeven annuleringstoken. Het systeem wacht tot uw taak is beëindigd (door succesvolle voltooiing, annulering of fout) voordat deze wordt uitgevoerd. Het is belangrijk om het annuleringstoken te `runAsync()` eren, alle werkzaamheden af te ronden en zo snel mogelijk af te sluiten wanneer het systeem annulering aanvraagt. In het volgende voorbeeld wordt uitgelegd hoe u een annuleringsgebeurtenis moet afhandelen:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

    // TODO: Replace the following sample code with your own logic
    // or remove this runAsync override if it's not needed in your service.

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

In dit stateless servicevoorbeeld wordt het aantal opgeslagen in een lokale variabele. Maar omdat dit een statusloze service is, bestaat de waarde die is opgeslagen alleen voor de huidige levenscyclus van de serviceinstantie. Wanneer de service wordt verplaatst of opnieuw wordt opgestart, gaat de waarde verloren.

## <a name="create-a-stateful-service"></a>Een stateful service maken
Service Fabric introduceert een nieuw soort service die stateful is. Een stateful service kan de status betrouwbaar behouden binnen de service zelf, naast de code die deze gebruikt. Staat wordt zeer beschikbaar gesteld door Service Fabric zonder de noodzaak om te blijven staan staat naar een externe winkel.

Als u een tegenwaarde wilt converteren van stateloos naar zeer beschikbaar en persistent, zelfs wanneer de service wordt verplaatst of opnieuw wordt opgestart, hebt u een stateful service nodig.

In dezelfde map als de HelloWorld-toepassing u `yo azuresfjava:AddService` een nieuwe service toevoegen door de opdracht uit te voeren. Kies de "Betrouwbare Stateful Service" voor uw framework en noem de service "HelloWorldStateful". 

Uw applicatie moet nu twee diensten: de stateless service HelloWorld en de stateful service HelloWorldStateful.

Een stateful service heeft dezelfde toegangspunten als een staatloze service. Het belangrijkste verschil is de beschikbaarheid van een statusprovider die de status betrouwbaar kan opslaan. Service Fabric wordt geleverd met een implementatie van de overheidsprovider genaamd Betrouwbare verzamelingen, waarmee u gerepliceerde gegevensstructuren maken via de Reliable State Manager. Een stateful Reliable Service maakt standaard gebruik van deze statusprovider.

Open HelloWorldStateful.java in **HelloWorldStateful -> src**, die de volgende RunAsync methode bevat:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>RunAsync
`RunAsync()`werkt op dezelfde manier in stateful en stateless diensten. In een stateful service voert het platform echter extra werk `RunAsync()`voor u uit voordat het wordt uitgevoerd. Dit werk kan onder meer ervoor zorgen dat de Reliable State Manager en Betrouwbare Collecties klaar zijn voor gebruik.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Betrouwbare collecties en de betrouwbare staatsmanager
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections.reliablehashmap) is een woordenboekimplementatie die u gebruiken om de status in de service betrouwbaar op te slaan. Met Service Fabric en Reliable HashMaps u gegevens rechtstreeks in uw service opslaan zonder dat u een externe permanente winkel nodig hebt. Betrouwbare HashMaps maken uw gegevens zeer beschikbaar. Service Fabric bereikt dit door meerdere *replica's* van uw service voor u te maken en te beheren. Het biedt ook een API die de complexiteit van het beheer van deze replica's en hun statusovergangen abstraheert.

Betrouwbare collecties kunnen elk Java-type opslaan, inclusief uw aangepaste typen, met een paar kanttekeningen:

* Service Fabric maakt uw status zeer beschikbaar door de status op verschillende knooppunten *te repliceren* en Betrouwbare HashMap slaat uw gegevens op lokale schijf op elke replica op. Dit betekent dat alles wat is opgeslagen in Betrouwbare HashMaps *moet serializable*zijn. 
* Objecten worden gerepliceerd voor hoge beschikbaarheid wanneer u transacties uitvoert op betrouwbare HashMaps. Objecten die zijn opgeslagen in Betrouwbare HashMaps, worden bewaard in het lokale geheugen in uw service. Dit betekent dat u een lokale verwijzing naar het object hebt.
  
   Het is belangrijk dat u lokale exemplaren van die objecten niet muteert zonder een updatebewerking uit te voeren op de betrouwbare verzameling in een transactie. Dit komt omdat wijzigingen in lokale exemplaren van objecten niet automatisch worden gerepliceerd. U moet het object opnieuw in het woordenboek plaatsen of een van de *updatemethoden* in het woordenboek gebruiken.

De Reliable State Manager beheert betrouwbare HashMaps voor u. U de Reliable State Manager op elk gewenst moment en op elke plaats in uw dienst om een betrouwbare collectie op naam vragen. De Reliable State Manager zorgt ervoor dat u een referentie terugkrijgt. We raden u niet aan verwijzingen op te slaan naar betrouwbare verzamelingsinstanties in klasse-lidvariabelen of -eigenschappen. Er moet speciale zorg worden getroffen om ervoor te zorgen dat de verwijzing te allen tijde in de levenscyclus van de service naar een instantie wordt ingesteld. De Reliable State Manager verzorgt dit werk voor u en is geoptimaliseerd voor herhaalde bezoeken.


### <a name="transactional-and-asynchronous-operations"></a>Transactionele en asynchrone bewerkingen
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

Bewerkingen op betrouwbare HashMaps zijn asynchroon. Dit komt omdat schrijfbewerkingen met betrouwbare verzamelingen I/O-bewerkingen uitvoeren om gegevens naar schijf te repliceren en te blijven bestaan.

Betrouwbare HashMap-bewerkingen zijn *transactioneel,* zodat u de status consistent houden in meerdere betrouwbare HashMaps en bewerkingen. U bijvoorbeeld een werkitem uit een betrouwbaar woordenboek halen, er een bewerking op uitvoeren en het resultaat opslaan in een andere betrouwbare HashMap, allemaal binnen één transactie. Dit wordt behandeld als een atoomoperatie, en het garandeert dat ofwel de hele operatie zal slagen of de hele operatie zal terugdraaien. Als er een fout optreedt nadat u het item in de wachtrij hebt geplaatst, maar voordat u het resultaat hebt opgeslagen, wordt de hele transactie teruggedraaid en blijft het item in de wachtrij voor verwerking.


## <a name="build-the-application"></a>De toepassing bouwen

De Yeoman steigers bevat een gradle script om de toepassing te bouwen en bash scripts te implementeren en de toepassing te verwijderen. Als u de toepassing wilt uitvoeren, bouwt u eerst de toepassing met gradle:

```bash
$ gradle
```

Dit levert een Service Fabric-toepassingspakket op dat kan worden geïmplementeerd met Service Fabric CLI.

## <a name="deploy-the-application"></a>De toepassing implementeren

Nadat de toepassing is gemaakt, kunt u deze implementeren in het lokale cluster.

1. Maak verbinding met het lokale cluster van Service Fabric.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Voer het installatiescript uit dat is opgegeven in de sjabloon om het toepassingspakket te kopiëren naar de installatiekopieopslag van het cluster, het toepassingstype te registreren en een exemplaar van de toepassing te maken.

    ```bash
    ./install.sh
    ```

De implementatie van de gemaakte toepassing werkt hetzelfde als van andere Service Fabric-toepassingen. Zie de documentatie over het [beheren van een Service Fabric-toepassing met de Service Fabric-CLI](service-fabric-application-lifecycle-sfctl.md) voor gedetailleerde instructies.

Parameters voor deze opdrachten vindt u in de gegenereerde manifesten binnen het toepassingspakket.

Nadat de toepassing is geïmplementeerd, opent u een browser en gaat u naar [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) op [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Vouw vervolgens het knooppunt **Toepassingen** uit. U ziet dat er nu een vermelding is voor uw toepassingstype en nog een voor het eerste exemplaar van dat type.

> [!IMPORTANT]
> Als u de toepassing wilt implementeren op een beveiligd Linux-cluster in Azure, moet u een certificaat configureren om uw toepassing te valideren met de runtime van Service Fabric. Hierdoor kunnen uw Reliable Services-services communiceren met de onderliggende Service Fabric runtime API's. Zie Een [app Voor betrouwbare services configureren om te draaien op Linux-clusters](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters)voor meer informatie.  
>

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met de Service Fabric-CLI](service-fabric-cli.md)
