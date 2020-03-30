---
title: Levenscyclus van Azure Service Fabric Reliable Services
description: Meer informatie over de levenscyclusgebeurtenissen in een Azure Service Fabric Reliable Services-toepassing die Java gebruikt voor stateful en stateless services.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 1d3be958a0649ed3e80df2d63adbdf0b91831dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639595"
---
# <a name="reliable-services-lifecycle"></a>Levenscyclus van Reliable Services
> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java op Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services is een van de programmeermodellen die beschikbaar zijn in Azure Service Fabric. Wanneer u meer te weten komt over de levenscyclus van Betrouwbare Services, is het vooral belangrijk om de basisgebeurtenissen voor de levenscyclus te begrijpen. De exacte volgorde van gebeurtenissen is afhankelijk van configuratiegegevens. 

In het algemeen bevat de levenscyclus van Reliable Services de volgende gebeurtenissen:

* Tijdens het opstarten:
  * Diensten zijn gebouwd.
  * Services hebben de mogelijkheid om nul of meer luisteraars te bouwen en terug te geven.
  * Alle geretourneerde luisteraars worden geopend, voor communicatie met de service.
  * De methode `runAsync` van de service wordt aangeroepen, zodat de service langlopend of achtergrondwerk kan uitvoeren.
* Tijdens het afsluiten:
  * Het annuleringstoken dat `runAsync` is doorgegeven aan wordt geannuleerd en de listeners zijn gesloten.
  * Het serviceobject zelf is vernietigd.

De volgorde van gebeurtenissen in Betrouwbare services kan enigszins veranderen, afhankelijk van of de betrouwbare service stateloos of stateful is. 

Ook moet u voor stateful services het primaire swapscenario aanpakken. Tijdens deze reeks wordt de rol van primair overgebracht naar een andere replica (of komt terug) zonder dat de service wordt afgesloten. 

Ten slotte moet je nadenken over fout- of foutvoorwaarden.

## <a name="stateless-service-startup"></a>Stateless service opstarten
De levenscyclus van een stateless service is vrij eenvoudig. Hier is de volgorde van de gebeurtenissen:

1. De service is gebouwd.
2. Deze gebeurtenissen vinden parallel plaats:
    - `StatelessService.createServiceInstanceListeners()`wordt aangeroepen en alle geretourneerde luisteraars worden geopend. `CommunicationListener.openAsync()`wordt opgeroepen op elke luisteraar.
    - De methode `runAsync` van`StatelessService.runAsync()`de service ( ) wordt aangeroepen.
3. Indien aanwezig, wordt de `onOpenAsync` eigen methode van de service aangeroepen. Specifiek, `StatelessService.onOpenAsync()` wordt genoemd. Dit is een ongewone override, maar het is beschikbaar.

Het is belangrijk op te merken dat er geen volgorde tussen de `runAsync`oproep te maken en open de luisteraars en de oproep naar . De luisteraars kunnen `runAsync` openen voordat wordt gestart. Op dezelfde `runAsync` manier kan worden aangeroepen voordat de communicatie luisteraars open zijn, of voordat ze zelfs zijn gebouwd. Als een synchronisatie vereist is, moet dit worden gedaan door de uitvoerder. Hier zijn enkele veelvoorkomende oplossingen:

* Soms kunnen luisteraars niet functioneren totdat andere informatie is gemaakt of ander werk is gedaan. Voor stateless diensten, dat werk kan meestal worden gedaan in de constructor van de dienst. Het kan worden `createServiceInstanceListeners()` gedaan tijdens het gesprek, of als onderdeel van de bouw van de luisteraar zelf.
* Soms wordt `runAsync` de code niet gestart totdat de luisteraars zijn geopend. In dit geval is extra coördinatie noodzakelijk. Een veelvoorkomende oplossing is het toevoegen van een vlag in de luisteraars. De vlag geeft aan wanneer de listeners klaar zijn. De `runAsync` methode controleert dit voordat het eigenlijke werk wordt voortgezet.

## <a name="stateless-service-shutdown"></a>Shutdown van stateloze service
Bij het afsluiten van een staatloze service wordt hetzelfde patroon gevolgd, maar omgekeerd:

1. Deze gebeurtenissen vinden parallel plaats:
    - Alle geopende luisteraars zijn gesloten. `CommunicationListener.closeAsync()`wordt opgeroepen op elke luisteraar.
    - Het annuleringstoken dat `runAsync()` is doorgegeven aan wordt geannuleerd. Als u de `isCancelled` eigenschap `true`van het annuleringstoken controleert, keert dit terug en als de methode van `throwIfCancellationRequested` het token wordt aangeroepen, wordt een `CancellationException`.
2. Wanneer `closeAsync()` deze op elke `runAsync()` listener is voltooid en `StatelessService.onCloseAsync()` ook is voltooid, wordt de methode van de service aangeroepen, als deze aanwezig is. Nogmaals, dit is geen veelvoorkomende overschrijving, maar het kan worden gebruikt om resources veilig te sluiten, achtergrondverwerking te stoppen, externe status op te slaan of bestaande verbindingen te sluiten.
3. Na `StatelessService.onCloseAsync()` afloop wordt het serviceobject vernietigd.

## <a name="stateful-service-startup"></a>Stateful service opstarten
Stateful diensten hebben een patroon dat vergelijkbaar is met stateless diensten, met een paar veranderingen.  Hier is de volgorde van de gebeurtenissen voor het starten van een stateful service:

1. De service is gebouwd.
2. `StatefulServiceBase.onOpenAsync()`wordt genoemd. Deze oproep wordt niet vaak overschreven in de service.
3. Deze gebeurtenissen vinden parallel plaats:
    - `StatefulServiceBase.createServiceReplicaListeners()`wordt aangeroepen. 
      - Als de service een primaire service is, worden alle geretourneerde listeners geopend. `CommunicationListener.openAsync()`wordt opgeroepen op elke luisteraar.
      - Als de service een secundaire service `listenOnSecondary = true` is, worden alleen listeners die zijn gemarkeerd als geopend. Het hebben van luisteraars die open zijn op secondaries is minder gebruikelijk.
    - Als de service momenteel een primaire `StatefulServiceBase.runAsync()` is, wordt de methode van de service aangeroepen.
4. Na alle replica luisteraar `openAsync()` oproepen `runAsync()` eindigen en `StatefulServiceBase.onChangeRoleAsync()` wordt opgeroepen, wordt opgeroepen. Deze oproep wordt niet vaak overschreven in de service.

Net als aan stateless diensten, in stateful service, is er geen coördinatie `runAsync` tussen de volgorde waarin de luisteraars worden gemaakt en geopend en wanneer wordt aangeroepen. Als u coördinatie nodig hebt, zijn de oplossingen vrijwel hetzelfde. Maar er is nog een extra zaak voor stateful service. Stel dat de gesprekken die aankomen bij de communicatie luisteraars vereisen informatie bewaard in een aantal [betrouwbare collecties](service-fabric-reliable-services-reliable-collections.md). Omdat de communicatieluisteraars kunnen openen voordat de betrouwbare collecties `runAsync` leesbaar of schrijfbaar zijn, en voordat het begint, is wat extra coördinatie noodzakelijk. De eenvoudigste en meest voorkomende oplossing is voor de communicatie luisteraars om een foutcode terug te keren. De client gebruikt de foutcode om te weten dat de aanvraag opnieuw moet worden geprobeerd.

## <a name="stateful-service-shutdown"></a>Stateful service shutdown
Net als stateless services zijn de levenscyclusgebeurtenissen tijdens het afsluiten hetzelfde als tijdens het opstarten, maar omgekeerd. Wanneer een stateful service wordt afgesloten, treden de volgende gebeurtenissen op:

1. Deze gebeurtenissen vinden parallel plaats:
    - Alle geopende luisteraars zijn gesloten. `CommunicationListener.closeAsync()`wordt opgeroepen op elke luisteraar.
    - Het annuleringstoken dat `runAsync()` is doorgegeven aan wordt geannuleerd. Een aanroep naar de `isCancelled()` methode `true`van het annuleringstoken `throwIfCancellationRequested()` keert terug `OperationCanceledException`en als de methode van het token wordt aangeroepen, wordt een .
2. Na `closeAsync()` afloop van elke `runAsync()` luisteraar en ook klaar `StatefulServiceBase.onChangeRoleAsync()` is, wordt de service gebeld. Deze oproep wordt niet vaak overschreven in de service.

   > [!NOTE]  
   > Wachten `runAsync` tot voltooid is alleen nodig als deze replica een primaire replica is.

3. Nadat `StatefulServiceBase.onChangeRoleAsync()` de methode is `StatefulServiceBase.onCloseAsync()` voltooid, wordt de methode aangeroepen. Deze oproep is een ongewone override, maar het is beschikbaar.
3. Na `StatefulServiceBase.onCloseAsync()` afloop wordt het serviceobject vernietigd.

## <a name="stateful-service-primary-swaps"></a>Stateful service primaire swaps
Terwijl een stateful service wordt uitgevoerd, `runAsync` worden communicatielisteners geopend en wordt de methode alleen aangeroepen voor de primaire replica's van die stateful services. Secundaire replica's zijn gebouwd, maar zie geen verdere oproepen. Terwijl een stateful service wordt uitgevoerd, kan de replica die momenteel de primaire is, worden gewijzigd. De levenscyclusgebeurtenissen die een stateful replica kan zien, zijn afhankelijk van de vraag of het de replica is die tijdens de swap wordt gedegradeerd of gepromoot.

### <a name="for-the-demoted-primary"></a>Voor de gedegradeerde primaire
Service Fabric heeft de primaire replica nodig die is gedegradeerd om de verwerking van berichten te stoppen en achtergrondwerk te stoppen. Deze stap is vergelijkbaar met wanneer de service wordt afgesloten. Een verschil is dat de service niet wordt vernietigd of gesloten, omdat het blijft als een secundaire. De volgende gebeurtenissen vinden plaats:

1. Deze gebeurtenissen vinden parallel plaats:
    - Alle geopende luisteraars zijn gesloten. `CommunicationListener.closeAsync()`wordt opgeroepen op elke luisteraar.
    - Het annuleringstoken dat `runAsync()` is doorgegeven aan wordt geannuleerd. Een controle van de `isCancelled()` methode `true`van het annuleringstoken retourneert . Als de `throwIfCancellationRequested()` token methode wordt `OperationCanceledException`aangeroepen, wordt een .
2. Na `closeAsync()` afloop van elke `runAsync()` luisteraar en ook klaar `StatefulServiceBase.onChangeRoleAsync()` is, wordt de service gebeld. Deze oproep wordt niet vaak overschreven in de service.

### <a name="for-the-promoted-secondary"></a>Voor de gepromoveerde secundaire
Op dezelfde manier heeft Service Fabric de secundaire replica nodig die is gepromoot om te beginnen met het luisteren naar berichten op de draad en om eventuele achtergrondtaken te starten die moet worden voltooid. Dit proces is vergelijkbaar met wanneer de service wordt gemaakt. Het verschil is dat de replica zelf al bestaat. De volgende gebeurtenissen vinden plaats:

1. Deze gebeurtenissen vinden parallel plaats:
    - `StatefulServiceBase.createServiceReplicaListeners()`wordt aangeroepen en eventuele geretourneerde luisteraars worden geopend. `CommunicationListener.openAsync()`wordt opgeroepen op elke luisteraar.
    - De methode `StatefulServiceBase.runAsync()` van de service wordt aangeroepen.
2. Na alle replica luisteraar `openAsync()` oproepen `runAsync()` eindigen en `StatefulServiceBase.onChangeRoleAsync()` wordt opgeroepen, wordt opgeroepen. Deze oproep wordt niet vaak overschreven in de service.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Veelvoorkomende problemen tijdens stateful service shutdown en primaire degradatie
Service Fabric wijzigt de primaire van een stateful service om meerdere redenen. De meest voorkomende redenen zijn [cluster rebalancing](service-fabric-cluster-resource-manager-balancing.md) en [application upgrade](service-fabric-application-upgrade.md). Tijdens deze operaties is het belangrijk dat `cancellationToken`de service de . Dit geldt ook tijdens het afsluiten van de normale service, bijvoorbeeld als de service is verwijderd.

Services die annuleringniet netjes afhandelen, kunnen verschillende problemen ondervinden. Deze bewerkingen zijn traag omdat Service Fabric wacht tot de services op een elegante manier stoppen. Dit kan uiteindelijk leiden tot mislukte upgrades die time-out en rollback. Het niet nakomen van het annuleringstoken kan ook onevenwichtige clusters veroorzaken. Clusters worden uit balans omdat knooppunten heet worden. De services kunnen echter niet opnieuw in evenwicht worden gebracht omdat het te lang duurt om ze naar elders te verplaatsen. 

Omdat de services stateful zijn, is het ook waarschijnlijk dat de services [betrouwbare collecties](service-fabric-reliable-services-reliable-collections.md)gebruiken. In Service Fabric wordt een van de eerste dingen die gebeurt, wanneer een primaire wordt gedegradeerd, ingetrokken. Dit leidt tot een tweede reeks problemen die van invloed kunnen zijn op de levenscyclus van de service. De verzamelingen retourneren uitzonderingen op basis van de timing en of de replica wordt verplaatst of afgesloten. Het is belangrijk om deze uitzonderingen correct te behandelen. 

Uitzonderingen die door Service Fabric worden gegooid, zijn permanent [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) of van voorbijgaande aard [(`FabricTransientException`)](https://docs.microsoft.com/java/api/system.fabric.exception.fabrictransientexception). Permanente uitzonderingen moeten worden geregistreerd en gegooid. Tijdelijke uitzonderingen kunnen opnieuw worden geprobeerd op basis van logica voor opnieuw proberen.

Een belangrijk onderdeel van het testen en valideren van Betrouwbare `ReliableCollections` Services is het afhandelen van de uitzonderingen die voortvloeien uit het gebruik van de in combinatie met gebeurtenissen in de levenscyclus van services. Wij raden u aan uw service altijd onder belasting uit te voeren. U moet ook upgrades en [chaostests](service-fabric-controlled-chaos.md) uitvoeren voordat u deze uitvoert op de productie. Met deze basisstappen u ervoor zorgen dat uw service correct is geïmplementeerd en dat levenscyclusgebeurtenissen correct worden verwerkt.

## <a name="notes-on-service-lifecycle"></a>Opmerkingen over de levenscyclus van services
* Zowel `runAsync()` de methode `createServiceInstanceListeners/createServiceReplicaListeners` als de aanroepen zijn optioneel. Een service kan er een hebben, beide of geen van beide. Als de service bijvoorbeeld al zijn werk doet als reactie op oproepen van `runAsync()`gebruikers, hoeft deze niet te worden geïmplementeerd. Alleen de communicatieluisteraars en de bijbehorende code zijn noodzakelijk.  Op dezelfde manier is het maken en retourneren van communicatieluisteraars optioneel. De service heeft mogelijk alleen achtergrondwerk te doen, dus deze hoeft alleen te worden geïmplementeerd. `runAsync()`
* Het is geldig voor een `runAsync()` service om succesvol te voltooien en terug te keren van het. Dit wordt niet beschouwd als een storing voorwaarde. Het vertegenwoordigt het achtergrondwerk van de service afwerking. Voor stateful Reliable `runAsync()` Services, zou opnieuw worden aangeroepen als de service wordt gedegradeerd van primaire, en vervolgens bevorderd terug naar primaire.
* Als een service `runAsync()` wordt afgesloten door een onverwachte uitzondering te geven, is dit een fout. Het serviceobject wordt afgesloten en er wordt een statusfout gerapporteerd.
* Hoewel er geen tijdslimiet is voor het terugkeren van deze methoden, verliest u onmiddellijk de mogelijkheid om te schrijven. Daarom u geen echt werk voltooien. We raden u aan om zo snel mogelijk terug te keren na ontvangst van het annuleringsverzoek. Als uw service niet binnen een redelijke tijd reageert op deze API-aanroepen, kan Service Fabric uw service met geweld beëindigen. Meestal gebeurt dit alleen tijdens toepassingsupgrades of wanneer een service wordt verwijderd. Deze time-out is standaard 15 minuten.
* Fouten in `onCloseAsync()` het `onAbort()` pad resulteren in wordt opgeroepen. Deze oproep is een last-chance, best-effort kans voor de dienst op te ruimen en vrij te geven alle middelen die zij hebben geclaimd. Dit wordt meestal aangeroepen wanneer een permanente fout wordt gedetecteerd op het knooppunt of wanneer Service Fabric de levenscyclus van de serviceinstantie niet betrouwbaar kan beheren als gevolg van interne fouten.
* `OnChangeRoleAsync()`wordt aangeroepen wanneer de stateful servicereplica de rol wijzigt, bijvoorbeeld naar primair of secundair. Primaire replica's krijgen de schrijfstatus (mogen betrouwbare verzamelingen maken en schrijven). Secundaire replica's krijgen leesstatus (kan alleen lezen uit bestaande betrouwbare collecties). Het meeste werk in een stateful service wordt uitgevoerd op de primaire replica. Secundaire replica's kunnen alleen-lezen validatie, rapportgeneratie, datamining of andere alleen-lezen taken uitvoeren.

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot betrouwbare services](service-fabric-reliable-services-introduction.md)
* [Betrouwbare services snel van start](service-fabric-reliable-services-quick-start-java.md)

