---
title: Overzicht van de levenscyclus van Betrouwbare Diensten
description: Meer informatie over de levenscyclusgebeurtenissen in een Azure Service Fabric Reliable Services-toepassing voor stateful en stateless services.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: fe338ca3f25cd606da7f95f6c9437a3cd3dc4e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258276"
---
# <a name="reliable-services-lifecycle-overview"></a>Overzicht van de levenscyclus van Betrouwbare Services
> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java op Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Wanneer u nadenkt over de levenscyclus van Azure Service Fabric Reliable Services, zijn de basisprincipes van de levenscyclus het belangrijkst. In het algemeen omvat de levenscyclus het volgende:

- Tijdens het opstarten:
  - Diensten zijn gebouwd.
  - De diensten hebben de mogelijkheid om nul of meer luisteraars te bouwen en terug te keren.
  - Alle geretourneerde luisteraars worden geopend, waardoor communicatie met de service mogelijk is.
  - De **RunAsync-methode** van de service wordt aangeroepen, zodat de service langlopende taken of achtergrondwerk kan uitvoeren.
- Tijdens het afsluiten:
  - Het annuleringstoken dat is doorgegeven aan **RunAsync** wordt geannuleerd en de listeners worden gesloten.
  - Nadat de luisteraars zijn gesloten, wordt het serviceobject zelf vernietigd.

Er zijn details over de exacte volgorde van deze evenementen. De volgorde van gebeurtenissen kan enigszins veranderen, afhankelijk van of de betrouwbare service stateloos of stateful is. Bovendien, voor stateful diensten, moeten we omgaan met de primaire swap scenario. Tijdens deze reeks wordt de rol van Primair overgebracht naar een andere replica (of komt terug) zonder dat de service wordt afgesloten. Tot slot moeten we nadenken over fouten of faalomstandigheden.

## <a name="stateless-service-startup"></a>Stateless service opstarten
De levenscyclus van een stateless service is eenvoudig. Hier is de volgorde van de gebeurtenissen:

1. De service is gebouwd.
2. Dan, parallel, twee dingen gebeuren:
    - `StatelessService.CreateServiceInstanceListeners()`wordt aangeroepen en eventuele geretourneerde luisteraars worden geopend. `ICommunicationListener.OpenAsync()`wordt opgeroepen op elke luisteraar.
    - De methode `StatelessService.RunAsync()` van de service wordt aangeroepen.
3. Indien aanwezig, wordt `StatelessService.OnOpenAsync()` de methode van de service aangeroepen. Deze oproep is een ongewone override, maar het is beschikbaar. Uitgebreide serviceinitialisatietaken kunnen op dit moment worden gestart.

Houd er rekening mee dat er geen volgorde is tussen de oproepen om de listeners te maken en te openen en **RunAsync**. De luisteraars kunnen openen voordat **RunAsync** wordt gestart. U **RunAsync** ook aanroepen voordat de communicatielisteners open of zelfs zijn opgebouwd. Als een synchronisatie vereist is, wordt het overgelaten als een oefening aan de uitvoerder. Hier zijn enkele veelvoorkomende oplossingen:

  - Soms kunnen luisteraars niet functioneren totdat er andere informatie is gemaakt of het werk is gedaan. Voor staatloze services kan dat werk meestal op andere locaties worden gedaan, zoals: 
    - In de constructeur van de dienst.
    - Tijdens `CreateServiceInstanceListeners()` het gesprek.
    - Als onderdeel van de constructie van de luisteraar zelf.
  - Soms wordt de code in **RunAsync** pas gestart als de listeners zijn geopend. In dit geval is extra coördinatie noodzakelijk. Een veelvoorkomende oplossing is dat er een vlag binnen de luisteraars is die aangeeft wanneer ze klaar zijn. Deze vlag wordt vervolgens gecontroleerd in **RunAsync** voordat u verder gaat met het werkelijke werk.

## <a name="stateless-service-shutdown"></a>Shutdown van stateloze service
Voor het afsluiten van een staatloze service wordt hetzelfde patroon gevolgd, net in omgekeerde richting:

1. Parallel:
    - Alle geopende luisteraars zijn gesloten. `ICommunicationListener.CloseAsync()`wordt opgeroepen op elke luisteraar.
    - Het annuleringstoken `RunAsync()` dat is doorgegeven aan wordt geannuleerd. Een controle van de `IsCancellationRequested` eigenschap van het annuleringstoken retourneert waar en als de methode van `ThrowIfCancellationRequested` het token wordt aangeroepen, wordt een `OperationCanceledException`.
2. Na `CloseAsync()` afloop van elke `RunAsync()` luisteraar en ook klaar `StatelessService.OnCloseAsync()` is, wordt de methode van de service aangeroepen, indien aanwezig.  OnCloseAsync wordt aangeroepen wanneer de stateless service-instantie op een elegante manier wordt afgesloten. Dit kan gebeuren wanneer de servicecode wordt bijgewerkt, de service-instantie wordt verplaatst vanwege taakverdeling of er een tijdelijke fout wordt gedetecteerd. Het is ongebruikelijk `StatelessService.OnCloseAsync()`om te overschrijven, maar het kan worden gebruikt om bronnen veilig te sluiten, achtergrondverwerking te stoppen, externe status op te slaan of bestaande verbindingen te sluiten.
3. Na `StatelessService.OnCloseAsync()` afloop wordt het serviceobject vernietigd.

## <a name="stateful-service-startup"></a>Stateful service opstarten
Stateful diensten hebben een vergelijkbaar patroon als stateless diensten, met een paar veranderingen. Voor het opstarten van een stateful service is de volgorde van gebeurtenissen als volgt:

1. De service is gebouwd.
2. `StatefulServiceBase.OnOpenAsync()`wordt genoemd. Deze oproep wordt niet vaak overschreven in de service.
3. De volgende dingen gebeuren parallel:
    - `StatefulServiceBase.CreateServiceReplicaListeners()`wordt aangeroepen. 
      - Als de service een primaire service is, worden alle geretourneerde listeners geopend. `ICommunicationListener.OpenAsync()`wordt opgeroepen op elke luisteraar.
      - Als de service een secundaire service `ListenOnSecondary = true` is, worden alleen de listeners die zijn gemarkeerd als geopend. Het hebben van luisteraars die open zijn op secondaries is minder gebruikelijk.
    - Als de service momenteel een primair `StatefulServiceBase.RunAsync()` is, wordt de methode van de service aangeroepen.
4. Na alle replica luisteraar `OpenAsync()` oproepen `RunAsync()` eindigen en `StatefulServiceBase.OnChangeRoleAsync()` wordt opgeroepen, wordt opgeroepen. Deze oproep wordt niet vaak overschreven in de service.

Net als bij stateless services is er geen coördinatie tussen de volgorde waarin de listeners worden gemaakt en geopend en wanneer **RunAsync** wordt aangeroepen. Als u coördinatie nodig hebt, zijn de oplossingen vrijwel hetzelfde. Er is een extra geval voor stateful service. Stel dat de gesprekken die aankomen bij de communicatie luisteraars vereisen informatie bewaard in een aantal [betrouwbare collecties](service-fabric-reliable-services-reliable-collections.md).

   > [!NOTE]  
   > Omdat de communicatieluisteraars kunnen openen voordat de betrouwbare collecties leesbaar of schrijfbaar zijn, en voordat **RunAsync** kon starten, is wat extra coördinatie nodig. De eenvoudigste en meest voorkomende oplossing is dat de communicatielisteners een foutcode retourneren waarvan de client weet dat ze de aanvraag opnieuw proberen.

## <a name="stateful-service-shutdown"></a>Stateful service shutdown
Net als stateless services zijn de levenscyclusgebeurtenissen tijdens het afsluiten hetzelfde als tijdens het opstarten, maar omgekeerd. Wanneer een stateful service wordt afgesloten, treden de volgende gebeurtenissen op:

1. Parallel:
    - Alle geopende luisteraars zijn gesloten. `ICommunicationListener.CloseAsync()`wordt opgeroepen op elke luisteraar.
    - Het annuleringstoken `RunAsync()` dat is doorgegeven aan wordt geannuleerd. Een controle van de `IsCancellationRequested` eigenschap van het annuleringstoken retourneert waar en als de methode van `ThrowIfCancellationRequested` het token wordt aangeroepen, wordt een `OperationCanceledException`.
2. Na `CloseAsync()` afloop van elke `RunAsync()` luisteraar en ook klaar `StatefulServiceBase.OnChangeRoleAsync()` is, wordt de service gebeld. Deze oproep wordt niet vaak overschreven in de service.

   > [!NOTE]  
   > De noodzaak om te wachten tot **RunAsync** is voltooid, is alleen nodig als deze replica een primaire replica is.

3. Nadat `StatefulServiceBase.OnChangeRoleAsync()` de methode is `StatefulServiceBase.OnCloseAsync()` voltooid, wordt de methode aangeroepen. Deze oproep is een ongewone override, maar het is beschikbaar.
3. Na `StatefulServiceBase.OnCloseAsync()` afloop wordt het serviceobject vernietigd.

## <a name="stateful-service-primary-swaps"></a>Stateful service Primaire swaps
Terwijl een stateful service wordt uitgevoerd, alleen de primaire replica's van die stateful diensten hebben hun communicatie luisteraars geopend en hun **RunAsync** methode genoemd. Secundaire replica's zijn gebouwd, maar zie geen verdere oproepen. Terwijl een stateful service wordt uitgevoerd, kan de replica die momenteel het primaire werkis, worden gewijzigd als gevolg van optimalisatie van de fout of clusterbalancering. Wat betekent dit voor de levenscyclusgebeurtenissen die een replica kan zien? Het gedrag dat de stateful replica ziet, hangt af van de vraag of het de replica is die tijdens de swap wordt gedegradeerd of gepromoot.

### <a name="for-the-primary-thats-demoted"></a>Voor het primaire werk dat is gedegradeerd
Voor de primaire replica die is gedegradeerd, heeft Service Fabric deze replica nodig om de verwerking van berichten te stoppen en het achtergrondwerk dat het doet te stoppen. Als gevolg hiervan ziet deze stap eruit alsof deze deed wanneer de service wordt afgesloten. Een verschil is dat de service niet wordt vernietigd of gesloten omdat het blijft als een secundaire. De volgende API's worden genoemd:

1. Parallel:
    - Alle geopende luisteraars zijn gesloten. `ICommunicationListener.CloseAsync()`wordt opgeroepen op elke luisteraar.
    - Het annuleringstoken `RunAsync()` dat is doorgegeven aan wordt geannuleerd. Een controle van de `IsCancellationRequested` eigenschap van het annuleringstoken retourneert waar en als de methode van `ThrowIfCancellationRequested` het token wordt aangeroepen, wordt een `OperationCanceledException`.
2. Na `CloseAsync()` afloop van elke `RunAsync()` luisteraar en ook klaar `StatefulServiceBase.OnChangeRoleAsync()` is, wordt de service gebeld. Deze oproep wordt niet vaak overschreven in de service.

### <a name="for-the-secondary-thats-promoted"></a>Voor de secundaire die wordt bevorderd
Op dezelfde manier heeft Service Fabric de secundaire replica nodig die is gepromoot om te beginnen met het luisteren naar berichten op de draad en alle achtergrondtaken te starten die het moet voltooien. Als gevolg hiervan ziet dit proces eruit zoals het deed wanneer de service wordt gemaakt, behalve dat de replica zelf al bestaat. De volgende API's worden genoemd:

1. Parallel:
    - `StatefulServiceBase.CreateServiceReplicaListeners()`wordt aangeroepen en eventuele geretourneerde luisteraars worden geopend. `ICommunicationListener.OpenAsync()`wordt opgeroepen op elke luisteraar.
    - De methode `StatefulServiceBase.RunAsync()` van de service wordt aangeroepen.
2. Na alle replica luisteraar `OpenAsync()` oproepen `RunAsync()` eindigen en `StatefulServiceBase.OnChangeRoleAsync()` wordt opgeroepen, wordt opgeroepen. Deze oproep wordt niet vaak overschreven in de service.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Veelvoorkomende problemen tijdens stateful service shutdown en Primary degradatie
Service Fabric wijzigt het primaire van een stateful service om verschillende redenen. De meest voorkomende zijn [cluster rebalancing](service-fabric-cluster-resource-manager-balancing.md) en [applicatie-upgrade](service-fabric-application-upgrade.md). Tijdens deze bewerkingen (evenals tijdens het afsluiten van de normale service, zoals u zou zien `CancellationToken`of de service is verwijderd), is het belangrijk dat de service de . 

Services die annuleringen niet netjes afhandelen, kunnen verschillende problemen ondervinden. Deze bewerkingen zijn traag omdat Service Fabric wacht tot de services op een elegante manier stoppen. Dit kan uiteindelijk leiden tot mislukte upgrades die time-out en terug te draaien. Het niet nakomen van het annuleringstoken kan ook onevenwichtige clusters veroorzaken. Clusters worden uit balans omdat knooppunten hot worden, maar de services kunnen niet opnieuw worden gebalanceerd omdat het te lang duurt om ze ergens anders naartoe te verplaatsen. 

Omdat de diensten stateful zijn, is het ook waarschijnlijk dat ze gebruik maken van de [betrouwbare collecties](service-fabric-reliable-services-reliable-collections.md). In Service Fabric wordt een van de eerste dingen die gebeurt, wanneer een primaire wordt gedegradeerd, ingetrokken. Dit leidt tot een tweede reeks problemen die van invloed kunnen zijn op de levenscyclus van de service. De verzamelingen retourneren uitzonderingen op basis van de timing en of de replica wordt verplaatst of afgesloten. Deze uitzonderingen moeten correct worden behandeld. Uitzonderingen die door Service Fabric worden gegooid, vallen in permanente [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) en tijdelijke [(`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) categorieën. Permanente uitzonderingen moeten worden geregistreerd en gegooid, terwijl de tijdelijke uitzonderingen opnieuw kunnen worden geprobeerd op basis van een aantal logica voor het opnieuw proberen.

Het afhandelen van de `ReliableCollections` uitzonderingen die voortvloeien uit het gebruik van de in combinatie met gebeurtenissen in de levenscyclus van services is een belangrijk onderdeel van het testen en valideren van een betrouwbare service. We raden u aan uw service altijd onder belasting uit te voeren terwijl u upgrades en [chaostests uitvoert](service-fabric-controlled-chaos.md) voordat u deze uitvoert in productie. Met deze basisstappen u ervoor zorgen dat uw service correct is geïmplementeerd en dat de levenscyclusgebeurtenissen correct worden verwerkt.


## <a name="notes-on-the-service-lifecycle"></a>Opmerkingen over de levenscyclus van de service
  - Zowel `RunAsync()` de methode `CreateServiceReplicaListeners/CreateServiceInstanceListeners` als de aanroepen zijn optioneel. Een service kan een van hen hebben, beide, of geen van beide. Als de service bijvoorbeeld al zijn werk doet als reactie op oproepen `RunAsync()`van gebruikers, hoeft deze niet te worden geïmplementeerd. Alleen de communicatieluisteraars en de bijbehorende code zijn noodzakelijk. Op dezelfde manier is het maken en retourneren van communicatielisteners optioneel, omdat de `RunAsync()`service alleen achtergrondwerk te doen heeft en dus alleen hoeft te worden geïmplementeerd.
  - Het is geldig voor `RunAsync()` een service om succesvol te voltooien en terug te keren van het. Invullen is geen storingsvoorwaarde. Het `RunAsync()` invullen geeft aan dat het achtergrondwerk van de service is voltooid. Voor stateful betrouwbare `RunAsync()` services wordt opnieuw gebeld als de replica wordt gedegradeerd van primair naar secundair en vervolgens wordt gepromoot naar primair.
  - Als een service `RunAsync()` wordt afgesloten door een onverwachte uitzondering te maken, vormt dit een fout. Het serviceobject wordt afgesloten en er wordt een statusfout gerapporteerd.
  - Hoewel er geen tijdslimiet is voor het terugkeren van deze methoden, verliest u onmiddellijk de mogelijkheid om naar Betrouwbare collecties te schrijven en kan u daarom geen echt werk voltooien. We raden u aan om zo snel mogelijk terug te keren na ontvangst van het annuleringsverzoek. Als uw service niet binnen een redelijke tijd op deze API-aanroepen reageert, kan Service Fabric uw service met geweld beëindigen. Meestal gebeurt dit alleen tijdens toepassingsupgrades of wanneer een service wordt verwijderd. Deze time-out is standaard 15 minuten.
  - Fouten in `OnCloseAsync()` het `OnAbort()` pad resulteren in wordt opgeroepen, dat is een last-chance best-chance kans voor de dienst op te ruimen en vrij te geven alle middelen die ze hebben geclaimd. Dit wordt meestal aangeroepen wanneer een permanente fout wordt gedetecteerd op het knooppunt of wanneer Service Fabric de levenscyclus van de serviceinstantie niet betrouwbaar kan beheren als gevolg van interne fouten.
  - `OnChangeRoleAsync()`wordt aangeroepen wanneer de stateful servicereplica de rol wijzigt, bijvoorbeeld naar primair of secundair. Primaire replica's krijgen de schrijfstatus (mogen betrouwbare verzamelingen maken en schrijven). Secundaire replica's krijgen leesstatus (kan alleen lezen uit bestaande betrouwbare collecties). Het meeste werk in een stateful service wordt uitgevoerd op de primaire replica. Secundaire replica's kunnen alleen-lezen validatie, rapportgeneratie, datamining of andere alleen-lezen taken uitvoeren.

## <a name="next-steps"></a>Volgende stappen
- [Inleiding tot betrouwbare services](service-fabric-reliable-services-introduction.md)
- [Betrouwbare services snel van start](service-fabric-reliable-services-quick-start.md)
- [Replica's en exemplaren](service-fabric-concepts-replica-lifecycle.md)
