---
title: Stateful services voor het testen van eenheden in Azure Service Fabric
description: Meer informatie over de concepten en praktijken van unit testing Service Fabric Stateful Services.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 12e8a47d9685dee12594f4e2afaa848d9688d185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433911"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Unit testing stateful services in Service Fabric

Dit artikel behandelt de concepten en praktijken van unit testing Service Fabric Stateful Services. Unit testing binnen Service Fabric verdient zijn eigen overwegingen vanwege het feit dat de applicatiecode actief wordt uitgevoerd onder meerdere verschillende contexten. In dit artikel worden de praktijken beschreven die worden gebruikt om ervoor te zorgen dat toepassingscode onder elk van de verschillende contexten valt die kan worden uitgevoerd.

## <a name="unit-testing-and-mocking"></a>Het testen en bespotten van eenheden
Unit testen in de context van dit artikel is geautomatiseerdtesten die kunnen worden uitgevoerd in het kader van een testrunner zoals MSTest of NUnit. De eenheidstests in dit artikel voeren geen bewerkingen uit op basis van een externe bron, zoals een database of RESTFul API. Deze externe bronnen moeten worden bespot. Spotten in de context van dit artikel zal nep, opnemen, en de controle van de terugkeer waarden voor externe bronnen.

### <a name="service-fabric-considerations"></a>Overwegingen voor servicestof
Unit testen van een Service Fabric stateful service heeft verschillende overwegingen. Ten eerste wordt de servicecode uitgevoerd op meerdere knooppunten, maar onder verschillende rollen. Unit tests moeten evalueren van de code onder elke rol om volledige dekking te bereiken. De verschillende rollen zijn Primair, Actief Secundair, Niet-secundair en Onbekend. De rol Geen heeft doorgaans geen speciale dekking nodig, omdat Service Fabric deze rol als ongeldig of nietig beschouwt. Ten tweede zal elk knooppunt zijn rol op een bepaald punt veranderen. Om volledige dekking te bereiken, moeten code uitvoeringspad's worden getest met rolwijzigingen die zich voordoen.

## <a name="why-unit-test-stateful-services"></a>Waarom unit test stateful diensten? 
Unit testen stateful diensten kunnen helpen om een aantal veel voorkomende fouten die worden gemaakt die niet noodzakelijkerwijs zou worden gevangen door conventionele toepassing of domein-specifieke eenheid testen bloot te leggen. Als de stateful-service bijvoorbeeld een status in het geheugen heeft, kan dit type test controleren of deze status in het geheugen gesynchroniseerd blijft voor elke replica. Dit type testen kan ook controleren of een stateful service reageert op annuleringstokens die op de juiste manier zijn doorgegeven door de Service Fabric-orchestration. Wanneer annuleringen worden geactiveerd, moet de service langdurige en/of asynchrone bewerkingen stoppen.  

## <a name="common-practices"></a>Gangbare praktijken

De volgende sectie adviseert over de meest voorkomende praktijken voor het testen van een stateful service. Het adviseert ook wat een spottende laag moet hebben om nauw af te stemmen op de Service Fabric orchestration en state management. [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) vanaf 3.3.0 of hoger is een dergelijke bibliotheek die de aanbevolen spotfunctionaliteit biedt en volgt de onderstaande praktijken.

### <a name="arrangement"></a>Regeling

#### <a name="use-multiple-service-instances"></a>Meerdere service-exemplaren gebruiken
Eenheidstests moeten meerdere exemplaren van een stateful service uitvoeren. Dit simuleert wat er werkelijk gebeurt op het cluster waar Service Fabric meerdere replica's bevat waarop uw service op verschillende knooppunten wordt uitgevoerd. Elk van deze exemplaren wordt echter uitgevoerd onder een andere context. Bij het uitvoeren van de test moet elke instantie worden geprimed met de rolconfiguratie die op het cluster wordt verwacht. Als de service bijvoorbeeld een doelreplicagrootte van 3 heeft, worden drie replica's op verschillende knooppunten ingericht. Een daarvan is de primaire en de andere twee zijn Actieve Secundaire's.

In de meeste gevallen variëren de serviceuitvoeringspaden enigszins voor elk van deze rollen. Als de service bijvoorbeeld geen aanvragen van een actieve secundaire service moet accepteren, kan de service een controle hebben op deze aanvraag om een informatieve uitzondering terug te gooien die aangeeft dat een aanvraag is geprobeerd op een secundaire. Met meerdere instanties kan deze situatie worden getest.

Bovendien kunnen de tests met meerdere instanties de rollen van elk van deze instanties omschakelen om te controleren of de antwoorden consistent zijn, ondanks de rolwijzigingen.

#### <a name="mock-the-state-manager"></a>Bespot de statusbeheerder
De State Manager moet worden behandeld als een externe bron en daarom bespot. Bij het bespotten van de statusmanager moet er een onderliggende opslag in het geheugen zijn voor het bijhouden van wat wordt opgeslagen in de statusbeheerder, zodat deze kan worden gelezen en geverifieerd. Een eenvoudige manier om dit te bereiken is het maken van mock exemplaren van elk van de soorten betrouwbare collecties. Gebruik binnen deze mocks een gegevenstype dat nauw aansluit bij de bewerkingen die zijn uitgevoerd met die verzameling. De volgende zijn enkele voorgestelde gegevenstypen voor elke betrouwbare verzameling

- IReliableDictionary<TKey, TValue> -> System.Collections.Concurrent.ConcurrentDictionary<TKey, TValue>
- IReliableQueue\<T> -> System.Collections.Generic.Queue\<T>
- IReliableConcurrentQueue\<T> -> System.Collections.Concurrent.ConcurrentQueue\<T>

#### <a name="many-state-manager-instances-single-storage"></a>Veel Statusbeheer-exemplaren, één opslag
Zoals eerder vermeld, moet de State Manager en Betrouwbare Collecties worden behandeld als een externe bron. Daarom moeten en zullen deze bronnen worden bespot binnen de eenheidstests. Wanneer u echter meerdere exemplaren van een stateful service uitvoert, is het een uitdaging om elke bespote statusbeheerder gesynchroniseerd te houden in verschillende stateful service-exemplaren. Wanneer de stateful service op het cluster wordt uitgevoerd, zorgt de Service Fabric ervoor dat de statusmanager van elke secundaire replica consistent blijft met de primaire replica. Daarom moeten de tests zich hetzelfde gedragen, zodat ze rolwijzigingen kunnen simuleren.

Een eenvoudige manier waarop deze synchronisatie kan worden bereikt, is het gebruik van een singleton patroon voor het onderliggende object dat de gegevens geschreven op elke betrouwbare verzameling slaat. Als een stateful service bijvoorbeeld `IReliableDictionary<string, string>`een . De mock state manager moet `IReliableDictionary<string, string>`een mock van terug te keren . Die mock kan `ConcurrentDictionary<string, string>` gebruik maken van een om bij te houden van de sleutel / waarde paren geschreven. Het `ConcurrentDictionary<string, string>` moet een singleton gebruikt door alle instanties van de staat managers doorgegeven aan de service.

#### <a name="keep-track-of-cancellation-tokens"></a>Annuleringstokens bijhouden
Annuleringstokens zijn een belangrijk maar vaak over het hoofd gezien aspect van stateful services. Wanneer Service Fabric een primaire replica opstart voor een stateful service, wordt een annuleringstoken verstrekt. Dit annuleringstoken is bedoeld om aan de service te signaleren wanneer het wordt verwijderd of gedegradeerd naar een andere rol. De stateful service moet langdurige of asynchrone bewerkingen stoppen, zodat Service Fabric de werkstroom voor rolwijziging kan voltooien.

Bij het uitvoeren van de eenheidstests moeten annuleringstokens die worden verstrekt aan RunAsync, ChangeRoleAsync, OpenAsync en CloseAsync tijdens de uitvoering van de test worden bewaard. Als u deze tokens vasthoudt, kan de test een serviceafsluiten of degradatie simuleren en controleren of de service op de juiste manier reageert.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>End-to-end testen met bespotte externe bronnen
Unit tests moeten zo veel mogelijk van de toepassingscode uitvoeren die de status van de stateful service kan wijzigen. Het is aanbevolen dat de tests meer end-to-end in de natuur. De enige mocks die er zijn, zijn het opnemen, simuleren en/of verifiëren van externe resourceinteracties. Dit omvat interacties met de State Manager en Betrouwbare Collecties. Het volgende fragment is een voorbeeld van augurk voor een test die end-to-end testen aantoont:

```
    Given stateful service named "fabric:/MyApp/MyService" is created
    And a new replica is created as "Primary" with id "111"
    And a new replica is created as "IdleSecondary" with id "222"
    And a new replica is created as "IdleSecondary" with id "333"
    And all idle secondary replicas are promoted to active secondary
    When a request is made to add the an employee "John Smith"
    And the active secondary replica "222" is promoted to primary
    And a request is made to get all employees
    Then the request should should return the "John Smith" employee
```

Deze test beweert dat de gegevens die worden vastgelegd op een replica beschikbaar is voor een secundaire replica wanneer deze wordt gepromoot naar primaire. Ervan uitgaande dat een betrouwbare verzameling is de backing store voor de werknemer gegevens, Aa potentiële `CommitAsync` fout die kunnen worden gevangen met deze test is als de toepassingscode niet uitgevoerd op de transactie om de nieuwe werknemer op te slaan. In dat geval zou het tweede verzoek om werknemers te krijgen niet terug werknemer toegevoegd door het eerste verzoek.

### <a name="acting"></a>Acteren
#### <a name="mimic-service-fabric-replica-orchestration"></a>Nabootsen Service Fabric replica orchestration
Bij het beheren van meerdere service-exemplaren moeten de tests deze services op dezelfde manier initialiseren en afbreken als de orchestration Service Fabric. Wanneer een service bijvoorbeeld wordt gemaakt op een nieuwe primaire replica, wordt CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync en RunAsync aangeroepen. De levenscyclusgebeurtenissen worden gedocumenteerd in de volgende artikelen:

- [Stateful Service Opstarten](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Stateful Service Shutdown](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Stateful Service Primary Swaps](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Replicarolwijzigingen uitvoeren
De eenheidstests moeten de rollen van de service-instanties op dezelfde manier wijzigen als de orchestration Service Fabric. De rolstatusmachine is gedocumenteerd in het volgende artikel:

[Replica Role State Machine](service-fabric-concepts-replica-lifecycle.md#replica-role)

Het simuleren van rolwijzigingen is een van de meer kritieke aspecten van het testen en kan problemen aan het licht brengen waarbij de status van de replica niet consistent is met elkaar. Inconsistente replicastatus kan optreden als gevolg van het opslaan van de status in het geheugen in statische of klasseniveauinstantievariabelen. Voorbeelden hiervan zijn annuleringstokens, enums en configuratieobjecten/waarden. Dit zorgt er ook voor dat de service de annuleringstokens respecteert die tijdens RunAsync zijn verstrekt om de rolwijziging mogelijk te maken. Het simuleren van rolwijzigingen kan ook problemen aan het licht brengen die zich kunnen voordoen als code niet is geschreven om een aanroep van RunAsync meerdere keren mogelijk te maken.

#### <a name="cancel-cancellation-tokens"></a>Annuleringstokens annuleren
Er moeten eenheidstests bestaan waarbij het annuleringstoken dat aan RunAsync wordt verstrekt, wordt geannuleerd. Hierdoor kan de test controleren of de service op een elegante manier wordt afgesloten. Tijdens deze uitschakeling moeten langlopende of asynchrone bewerkingen worden gestopt. Voorbeeld van een langlopend proces dat mogelijk op een service aanwezig is, is een proces dat luistert naar berichten in een betrouwbare wachtrij. Dit kan direct binnen RunAsync of een achtergrondthread bestaan. De implementatie moet logica bevatten voor het afsluiten van de bewerking als dit annuleringstoken wordt geannuleerd.

Als de stateful services gebruik maken van een cache of in-memory status die alleen zou moeten bestaan op de primaire, moet worden verwijderd op dit moment. Dit is om ervoor te zorgen dat deze status consistent is als het knooppunt later weer een primaire wordt. Met annuleringstests kan de test worden uitgevoerd om te controleren of deze status correct is verwijderd.

#### <a name="execute-requests-against-multiple-replicas"></a>Aanvragen uitvoeren op meerdere replica's
Assert tests moeten uitvoeren hetzelfde verzoek tegen verschillende replica's. In combinatie met rolwijzigingen kunnen consistentieproblemen worden ontdekt. Een voorbeeldtest kan de volgende stappen uitvoeren:
1. Een schrijfaanvraag uitvoeren op basis van de huidige primaire
2. Voer een leesaanvraag uit waarin de gegevens die in stap 1 zijn geschreven, worden geretourneerd ten opzichte van de huidige primaire
3. Een secundair e-voor-primair bevorderen. Dit moet ook de huidige primaire tot secundaire
4. Voer hetzelfde leesverzoek uit vanaf stap 2 ten opzichte van het nieuwe secundaire.

In de laatste stap kan de test beweren dat de geretourneerde gegevens consistent zijn. Een potentieel probleem dat dit zou kunnen ontdekken is dat de gegevens die worden geretourneerd door de dienst kan worden in het geheugen, maar uiteindelijk ondersteund door een betrouwbare verzameling. Die in-memory gegevens kunnen niet goed worden bewaard met wat er bestaat in de betrouwbare verzameling.

In-memory gegevens worden meestal gebruikt om secundaire indexen of aggregaties van gegevens die bestaat in een betrouwbare verzameling te maken.

### <a name="asserting"></a>Beweren
#### <a name="ensure-responses-match-across-replicas"></a>Reacties overeenkomen met replica's
Eenheidstests moeten beweren dat een antwoord op een bepaald verzoek consistent is voor meerdere replica's nadat ze zijn overgestapt naar primaire. Dit kan potentiële problemen aan het oppervlak brengen waarbij gegevens die in het antwoord worden weergegeven, niet worden ondersteund door een betrouwbare verzameling of in het geheugen worden bewaard zonder een mechanisme om die gegevens over replica's te synchroniseren. Dit zorgt ervoor dat de service consistente antwoorden terugstuurt nadat Service Fabric opnieuw in evenwicht is gebracht of niet overloopt naar een nieuwe primaire replica.

#### <a name="verify-service-respects-cancellation"></a>Controleer of service de annulering respecteert
Langlopende of asynchrone processen die moeten worden beëindigd wanneer een annuleringstoken wordt geannuleerd, moeten worden geverifieerd dat ze daadwerkelijk zijn beëindigd na annulering. Dit zorgt ervoor dat, ondanks de replica veranderende rollen, processen die niet zijn bedoeld om te blijven draaien op niet-primaire replica te stoppen voordat de overgang is voltooid. Dit kan ook problemen aan het licht brengen waarbij een dergelijk proces een rolwijziging of afsluitaanvraag van Service Fabric blokkeert om te voltooien.

#### <a name="verify-which-replicas-should-serve-requests"></a>Controleren welke replica's aanvragen moeten worden gebruikt
De tests moeten het verwachte gedrag doen gelden als een aanvraag wordt doorgestuurd naar een niet-primaire replica. Service Fabric biedt wel de mogelijkheid om secundaire replica's aanvragen te laten serveren. Schrijven naar betrouwbare verzamelingen kan echter alleen plaatsvinden vanaf de primaire replica. Als uw toepassing alleen primaire replica's wil uitvoeren of als alleen een subset van aanvragen door een secundaire aanvraag kan worden behandeld, moeten de tests het verwachte gedrag voor zowel de positieve als negatieve gevallen doen gelden. De negatieve aanvraag is een aanvraag wordt doorgestuurd naar een replica die niet moet omgaan met de aanvraag en, het positieve is het tegenovergestelde.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [testen van stateful services.](service-fabric-how-to-unit-test-stateful-services.md)
