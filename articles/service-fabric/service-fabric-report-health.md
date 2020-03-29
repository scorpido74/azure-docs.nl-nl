---
title: Aangepaste statusrapporten van servicefabric toevoegen
description: Beschrijft hoe u aangepaste statusrapporten verzendt naar statusentiteiten van Azure Service Fabric. Geeft aanbevelingen voor het ontwerpen en implementeren van kwaliteitsrapporten.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: d00f740085b15bdb5fe698a069d97f168507f31f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451591"
---
# <a name="add-custom-service-fabric-health-reports"></a>Aangepaste statusrapporten van servicefabric toevoegen
Azure Service Fabric introduceert een [statusmodel](service-fabric-health-introduction.md) dat is ontworpen om ongezonde cluster- en toepassingsvoorwaarden op specifieke entiteiten te markeren. Het gezondheidsmodel maakt gebruik van **gezondheidsverslaggevers** (systeemcomponenten en waakhonden). Het doel is eenvoudig en snel diagnose en reparatie. Service schrijvers moeten vooraf nadenken over gezondheid. Elke aandoening die van invloed kan zijn op de gezondheid moet worden gerapporteerd over, vooral als het kan helpen vlag problemen dicht bij de wortel. De gezondheidsinformatie kan tijd en moeite besparen op foutopsporing en onderzoek. Het nut is vooral duidelijk zodra de service op schaal in de cloud (privé of Azure) actief is.

De Service Fabric verslaggevers controleren geïdentificeerde voorwaarden van belang. Zij rapporteren over deze voorwaarden op basis van hun lokale visie. Het [gezondheidsarchief](service-fabric-health-introduction.md#health-store) verzamelt gezondheidsgegevens die door alle verslaggevers worden verzonden om te bepalen of entiteiten wereldwijd gezond zijn. Het model is bedoeld om rijk, flexibel en gebruiksvriendelijk te zijn. De kwaliteit van de statusrapporten bepaalt de nauwkeurigheid van de statusweergave van het cluster. False positives die ten onrechte ongezonde problemen weergeven, kunnen een negatieve invloed hebben op upgrades of andere services die gezondheidsgegevens gebruiken. Voorbeelden van dergelijke diensten zijn reparatiediensten en waarschuwingsmechanismen. Daarom is enige gedachte nodig om rapporten te verstrekken die de voorwaarden van belang op de best mogelijke manier vastleggen.

Voor het ontwerpen en implementeren van gezondheidsrapportages moeten waakhonden en systeemcomponenten:

* Definieer de voorwaarde waarin ze geïnteresseerd zijn, de manier waarop deze wordt gecontroleerd en de impact op de cluster- of toepassingsfunctionaliteit. Op basis van deze informatie, beslissen over het gezondheidsrapport eigendom en gezondheidstoestand.
* Bepaal op welke [entiteit](service-fabric-health-introduction.md#health-entities-and-hierarchy) het rapport van toepassing is.
* Bepaal waar de rapportage wordt gedaan, vanuit de service of van een interne of externe waakhond.
* Definieer een bron die wordt gebruikt om de melder te identificeren.
* Kies een rapportagestrategie, periodiek of op overgangen. De aanbevolen manier is periodiek, omdat het vereist eenvoudiger code en is minder gevoelig voor fouten.
* Bepaal hoe lang het rapport voor ongezonde omstandigheden in de gezondheidsopslag moet blijven en hoe het moet worden gewist. Bepaal met deze informatie de tijd van het rapport om te leven en gedrag te verwijderen.Using this information, decide the report's time to live and remove-on-expiration behavior.

Zoals vermeld, rapportage kan worden gedaan van:

* De bewaakte servicereplica van de service service service service.
* Interne waakhonden die worden geïmplementeerd als servicefabric-service (bijvoorbeeld een serviceloze service die de voorwaarden bewaakt en rapporten uitgeeft). De waakhonden kunnen worden ingezet een alle knooppunten of kan worden geinitiseerd om de bewaakte dienst.
* Interne waakhonden die op de Service Fabric-knooppunten draaien, maar *niet* worden geïmplementeerd als Service Fabric-services.
* Externe waakhonden die de bron van *buiten* het cluster Service Fabric onderzoeken (bijvoorbeeld bewakingsservice zoals Gomez).

> [!NOTE]
> Uit het nieuwe vak wordt het cluster gevuld met statusrapporten die door de systeemcomponenten worden verzonden. Lees meer bij [Systeemstatusrapporten gebruiken voor het oplossen van problemen.](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) De gebruikersrapporten moeten worden verzonden naar [gezondheidsinstanties](service-fabric-health-introduction.md#health-entities-and-hierarchy) die al door het systeem zijn gemaakt.
> 
> 

Zodra het ontwerp van de gezondheidsrapportage duidelijk is, kunnen gezondheidsrapporten eenvoudig worden verzonden. U [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) gebruiken om de status te rapporteren als het cluster niet [veilig](service-fabric-cluster-security.md) is of als de fabricclient beheerdersrechten heeft. Rapportage kan via de API via [FabricClient.HealthManager.ReportHealth,](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth)via PowerShell of via REST. Configuratieknoppen batchrapporten voor verbeterde prestaties.

> [!NOTE]
> Rapportstatus is synchroon en vertegenwoordigt alleen het validatiewerk aan de clientzijde. Het feit dat het rapport wordt geaccepteerd `Partition` `CodePackageActivationContext` door de statusclient of de or-objecten, betekent niet dat het in de winkel wordt toegepast. Het wordt asynchroon verzonden en eventueel gebatched met andere rapporten. De verwerking op de server kan nog steeds mislukken: het volgnummer kan verouderd zijn, de entiteit waarop het rapport moet worden toegepast, is verwijderd, enz.
> 
> 

## <a name="health-client"></a>Gezondheidsclient
De gezondheidsrapporten worden verzonden naar de gezondheidsmanager via een gezondheidscliënt, die binnen de stofcliënt leeft. De gezondheidsmanager slaat rapporten op in de gezondheidswinkel. De statusclient kan worden geconfigureerd met de volgende instellingen:

* **HealthReportSendInterval:** de vertraging tussen het moment dat het rapport aan de client wordt toegevoegd en de tijd dat het naar de statusbeheerder wordt verzonden. Wordt gebruikt om rapporten in één bericht te batchten, in plaats van één bericht voor elk rapport te verzenden. De batching verbetert de prestaties. Standaard: 30 seconden.
* **HealthReportRetrySendInterval:** het interval waarmee de statusclient geaccumuleerde statusrapporten opnieuw verzendt naar de statusbeheerder. Standaard: 30 seconden, minimaal: 1 seconde.
* **HealthOperationTimeout**: De time-outperiode voor een rapportbericht dat naar de gezondheidsmanager wordt verzonden. Als een bericht een keer uitvalt, probeert de statusclient het opnieuw totdat de statusbeheerder bevestigt dat het rapport is verwerkt. Standaard: twee minuten.

> [!NOTE]
> Wanneer de rapporten worden batched, moet de fabric client in leven worden gehouden voor ten minste de HealthReportSendInterval om ervoor te zorgen dat ze worden verzonden. Als het bericht verloren gaat of als de gezondheidsmanager ze niet kan toepassen vanwege tijdelijke fouten, moet de fabricclient langer in leven worden gehouden om het een kans te geven om het opnieuw te proberen.
> 
> 

De buffering op de klant houdt rekening met de uniciteit van de rapporten. Als een bepaalde slechte reporter bijvoorbeeld 100 rapporten per seconde rapporteert over dezelfde eigenschap van dezelfde entiteit, worden de rapporten vervangen door de laatste versie. Hoogstens bestaat één dergelijk rapport in de clientwachtrij. Als batching is geconfigureerd, is het aantal rapporten dat naar de statusbeheerder wordt verzonden slechts één per verzendinterval. Dit rapport is het laatst toegevoegde rapport, dat de meest recente status van de entiteit weergeeft.
Geef configuratieparameters `FabricClient` op wanneer deze worden gemaakt door [FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings) door te geven aan de gewenste waarden voor statusgerelateerde vermeldingen.

In het volgende voorbeeld wordt een fabricclient gemaakt en wordt aangegeven dat de rapporten moeten worden verzonden wanneer ze worden toegevoegd. Op time-outs en fouten die opnieuw kunnen worden geprobeerd, opnieuw pogingen gebeuren elke 40 seconden.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

We raden u aan de standaardinstellingen van de fabricclient te behouden, die zijn ingesteld `HealthReportSendInterval` op 30 seconden. Deze instelling zorgt voor optimale prestaties door batching. Voor kritieke rapporten die zo snel mogelijk `HealthReportSendOptions` moeten `true` worden verzonden, u gebruik maken met Direct in [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API. Directe rapporten omzeilen het batchinterval. Gebruik deze vlag met zorg; we willen waar mogelijk gebruik maken van de health client batching. Direct verzenden is ook handig wanneer de fabricclient sluit (het proces heeft bijvoorbeeld de ongeldige status bepaald en moet worden afgesloten om bijwerkingen te voorkomen). Het zorgt voor een best-effort verzenden van de verzamelde rapporten. Wanneer een rapport wordt toegevoegd met de vlag Onmiddellijk, batches de statusclient alle verzamelde rapporten sinds het laatste verzenden.

Dezelfde parameters kunnen worden opgegeven wanneer een verbinding met een cluster wordt gemaakt via PowerShell. In het volgende voorbeeld wordt een verbinding met een lokaal cluster gestart:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

Net als bij API kunnen `-Immediate` rapporten worden verzonden met behulp `HealthReportSendInterval` van switch om onmiddellijk te worden verzonden, ongeacht de waarde.

Voor REST worden de rapporten verzonden naar de Service Fabric-gateway, die een interne fabricclient heeft. Standaard is deze client geconfigureerd om rapporten te verzenden die elke 30 seconden worden gebatcheerd. U het batch-interval wijzigen `HttpGatewayHealthReportSendInterval` `HttpGateway`met de clusterconfiguratieinstelling op. Zoals gezegd, een betere optie is `Immediate` om de rapporten te sturen met ware. 

> [!NOTE]
> Als u wilt weten dat onbevoegde services de status niet kunnen melden tegen de entiteiten in het cluster, configureert u de server om alleen aanvragen van beveiligde clients te accepteren. De `FabricClient` gebruikte voor rapportage moet beveiliging hebben ingeschakeld om te kunnen communiceren met het cluster (bijvoorbeeld met Kerberos of certificaatverificatie). Lees meer over [clusterbeveiliging](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Rapporteren vanuit services met een laag privilege
Als Service Fabric-services geen beheerderstoegang tot het cluster hebben, u `Partition` `CodePackageActivationContext`de status rapporteren over entiteiten uit de huidige context via of.

* Voor stateless services gebruikt u [IStatelessServicePartition.ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) om te rapporteren over het huidige serviceexemplaar.
* Voor stateful services gebruikt u [IStatefulServicePartition.ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) om te rapporteren over de huidige replica.
* Gebruik [IServicePartition.ReportPartitionHealth](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) om te rapporteren over de huidige partitieentiteit.
* Gebruik [CodePackageActivationContext.ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) om te rapporteren over de huidige toepassing.
* Gebruik [CodePackageActivationContext.ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) om te rapporteren over de huidige toepassing die is geïmplementeerd op het huidige knooppunt.
* Gebruik [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) om te rapporteren over een servicepakket voor de toepassing die is geïmplementeerd op het huidige knooppunt.

> [!NOTE]
> Intern is `Partition` de `CodePackageActivationContext` statusclient en de statusclient geconfigureerd met standaardinstellingen. Zoals uitgelegd voor de [gezondheid client](service-fabric-report-health.md#health-client), rapporten worden batched en verzonden op een timer. De objecten moeten in leven worden gehouden om een kans te hebben om het rapport te verzenden.
> 
> 

U kunt `HealthReportSendOptions` opgeven bij `Partition` `CodePackageActivationContext` het verzenden van rapporten via en gezondheid API's. Als u kritieke rapporten hebt die zo snel `HealthReportSendOptions` mogelijk `true`moeten worden verzonden, gebruikt u onmiddellijk . Onmiddellijke rapporten omzeilen het batchinterval van de interne statusclient. Zoals eerder vermeld, gebruik deze vlag met zorg; we willen waar mogelijk gebruik maken van de health client batching.

## <a name="design-health-reporting"></a>Rapportage over de status van ontwerp
De eerste stap in het genereren van rapporten van hoge kwaliteit is het identificeren van de voorwaarden die van invloed kunnen zijn op de gezondheid van de service. Elke voorwaarde die kan helpen vlag problemen in de service of cluster wanneer het begint - of zelfs beter, voordat een probleem gebeurt - kan mogelijk miljarden dollars besparen. De voordelen omvatten minder down time, minder nachtelijke uren besteed aan het onderzoeken en repareren van problemen, en een hogere klanttevredenheid.

Zodra de voorwaarden zijn geïdentificeerd, waakhond schrijvers moeten uitzoeken van de beste manier om ze te controleren op evenwicht tussen overhead en nut. Overweeg bijvoorbeeld een service die complexe berekeningen uitvoert die bepaalde tijdelijke bestanden op een share gebruiken. Een waakhond zou het aandeel kunnen controleren om ervoor te zorgen dat er voldoende ruimte beschikbaar is. Het kan luisteren naar meldingen van bestands- of directorywijzigingen. Het kan een waarschuwing melden als een drempel vooraf wordt bereikt en een fout melden als het aandeel vol is. Bij een waarschuwing kan een reparatiesysteem beginnen met het opschonen van oudere bestanden op het aandeel. Bij een fout kan een reparatiesysteem de servicereplica naar een ander knooppunt verplaatsen. Merk op hoe de toestanden worden beschreven in termen van gezondheid: de toestand van de aandoening die kan worden beschouwd als gezond (ok) of ongezond (waarschuwing of fout).

Zodra de monitoring details zijn ingesteld, een waakhond schrijver moet uitzoeken hoe de waakhond uit te voeren. Als de voorwaarden vanuit de dienst kunnen worden bepaald, kan de waakhond deel uitmaken van de bewaakte dienst zelf. De servicecode kan bijvoorbeeld het gebruik van delen controleren en vervolgens elke keer rapporteren wanneer het een bestand probeert te schrijven. Het voordeel van deze aanpak is dat rapportage eenvoudig is. Er moet voor worden gezorgd dat watchdogbugs geen invloed hebben op de functionaliteit van de service.

Rapporteren vanuit de bewaakte service is niet altijd een optie. Een waakhond binnen de dienst kan de omstandigheden mogelijk niet detecteren. Het kan niet de logica of gegevens om de bepaling te maken. De overhead van het toezicht op de omstandigheden kan hoog zijn. De voorwaarden kunnen ook niet specifiek zijn voor een service, maar in plaats daarvan van invloed op interacties tussen services. Een andere optie is om waakhonden in het cluster als afzonderlijke processen. De waakhonden houden toezicht op de voorwaarden en rapporteren, zonder dat dit gevolgen heeft voor de belangrijkste diensten. Deze waakhonden kunnen bijvoorbeeld worden geïmplementeerd als stateloze services in dezelfde toepassing, geïmplementeerd op alle knooppunten of op dezelfde knooppunten als de service.

Soms is een waakhond die in het cluster draait ook geen optie. Als de bewaakte voorwaarde de beschikbaarheid of functionaliteit van de service is zoals gebruikers deze zien, is het het beste om de waakhonden op dezelfde plaats te hebben als de gebruikersclients. Daar kunnen ze de bewerkingen testen op dezelfde manier waarop gebruikers ze noemen. U bijvoorbeeld een waakhond hebben die buiten het cluster woont, aanvragen aan de service uitgeeft en de latentie en juistheid van het resultaat controleert. (Voor een rekenmachine service, bijvoorbeeld, geeft 2+2 4 in een redelijke hoeveelheid tijd terug?)

Zodra de watchdog details zijn afgerond, moet u beslissen over een bron-ID die uniek identificeert het. Als meerdere waakhonden van hetzelfde type in het cluster wonen, moeten ze rapporteren over verschillende entiteiten of, als ze rapporteren over dezelfde entiteit, verschillende bron-ID of eigenschap gebruiken. Op deze manier kunnen hun rapporten naast elkaar bestaan. De eigenschap van het gezondheidsrapport moet de bewaakte toestand vastleggen. (In het bovenstaande voorbeeld kan de eigenschap **ShareSize**zijn .) Als meerdere rapporten op dezelfde voorwaarde van toepassing zijn, moet de eigenschap dynamische informatie bevatten waarmee rapporten naast elkaar kunnen bestaan. Als er bijvoorbeeld meerdere aandelen moeten worden gecontroleerd, kan de naam van de eigenschap **ShareSize-sharename**zijn.

> [!NOTE]
> Gebruik het gezondheidsarchief *niet* om statusgegevens bij te houden. Alleen gezondheidsgerelateerde informatie mag als gezondheid worden gerapporteerd, omdat deze informatie van invloed is op de gezondheidsevaluatie van een entiteit. De gezondheidswinkel is niet ontworpen als een winkel voor algemene doeleinden. Het maakt gebruik van de logica voor gezondheidsevaluatie om alle gegevens samen te voegen in de status. Het verzenden van informatie die geen verband houdt met de status (zoals rapportagestatus met een status van OK) heeft geen invloed op de geaggregeerde status, maar heeft een negatieve invloed op de prestaties van het gezondheidsarchief.
> 
> 

Het volgende beslissingspunt is over welke entiteit moet worden verslag uitgemaakt. Meestal identificeert de voorwaarde duidelijk de entiteit. Kies de entiteit met de best mogelijke granulariteit. Als een voorwaarde gevolgen heeft voor alle replica's in een partitie, rapporteert u over de partitie en niet op de service. Er zijn hoek gevallen waar meer aandacht nodig is, dat wel. Als de voorwaarde van invloed is op een entiteit, zoals een replica, maar de wens is om de voorwaarde te laten markeren voor meer dan de duur van de levensduur van de replica, dan moet deze worden gerapporteerd op de partitie. Als de replica anders wordt verwijderd, worden al zijn rapporten opgeschoond wanneer de replica wordt verwijderd. Watchdog schrijvers moeten nadenken over de levensduur van de entiteit en het rapport. Het moet duidelijk zijn wanneer een rapport moet worden opgeschoond vanuit een winkel (bijvoorbeeld wanneer een fout die op een entiteit wordt gerapporteerd niet langer van toepassing is).

Laten we eens kijken naar een voorbeeld dat de punten die ik beschreven samenbrengt. Overweeg een Service Fabric-toepassing die bestaat uit een hoofdstateful persistente service en secundaire stateloze services die op alle knooppunten zijn geïmplementeerd (één secundair servicetype voor elk type taak). De stramien heeft een verwerkingswachtrij met opdrachten die door secondaries moeten worden uitgevoerd. De secondaries voeren de binnenkomende aanvragen uit en sturen bevestigingssignalen terug. Een voorwaarde die kan worden gecontroleerd is de lengte van de hoofdverwerkingswachtrij. Als de lengte van de hoofdwachtrij een drempelwaarde bereikt, wordt een waarschuwing gerapporteerd. De waarschuwing geeft aan dat de secondaries de belasting niet aankunnen. Als de wachtrij de maximale lengte bereikt en opdrachten worden verwijderd, wordt een fout gerapporteerd, omdat de service niet kan herstellen. De rapporten kunnen worden op de eigenschap **QueueStatus**. De waakhond woont binnen de dienst, en het wordt periodiek verzonden op de master primaire replica. De tijd om te leven is twee minuten, en het wordt periodiek verzonden om de 30 seconden. Als de primaire naar beneden gaat, wordt het rapport automatisch opgeschoond vanuit de winkel. Als de servicereplica is up, maar deze vastzit of andere problemen heeft, verloopt het rapport in het gezondheidsarchief. In dit geval wordt de entiteit foutloos geëvalueerd.

Een andere voorwaarde die kan worden gecontroleerd is taakuitvoeringstijd. Het stramien verdeelt taken naar de secondaries op basis van het taaktype. Afhankelijk van het ontwerp kan de master de secondaries peilen voor de taakstatus. Het kan ook wachten tot secondaries terug te sturen bevestiging signalen wanneer ze klaar zijn. In het tweede geval moet ervoor worden gezorgd dat situaties worden gedetecteerd waarin secondaries sterven of berichten verloren gaan. Een optie is dat de master een ping-aanvraag naar hetzelfde secundaire stuurt, dat de status terugstuurt. Als er geen status is ontvangen, beschouwt de master deze als een fout en plant de taak opnieuw. Dit gedrag gaat ervan uit dat de taken idempotent zijn.

De bewaakte toestand kan worden vertaald als een waarschuwing als de taak niet wordt uitgevoerd in een bepaalde tijd **(t1**, bijvoorbeeld 10 minuten). Als de taak niet op tijd is voltooid **(t2,** bijvoorbeeld 20 minuten), kan de bewaakte toestand worden vertaald als Fout. Deze rapportage kan op meerdere manieren worden uitgevoerd:

* De hoofdprimaire replica rapporteert periodiek over zichzelf. U één eigenschap hebben voor alle lopende taken in de wachtrij. Als ten minste één taak langer duurt, is de rapportstatus van de eigenschap **PendingTasks** een waarschuwing of fout, naar gelang van het geval. Als er geen lopende taken of alle taken zijn gestart met de uitvoering, is de rapportstatus OK. De taken zijn blijvend. Als de primaire gaat naar beneden, de nieuw gepromoveerde primaire kan blijven rapporteren goed.
* Een ander watchdog-proces (in de cloud of extern) controleert de taken (van buitenaf, op basis van het gewenste taakresultaat) om te zien of ze zijn voltooid. Als ze de drempelwaarden niet respecteren, wordt er een rapport verzonden naar de hoofdservice. Er wordt ook een rapport verzonden voor elke taak die de taak-id bevat, zoals **PendingTask+taskId**. Rapporten mogen alleen worden verzonden over ongezonde toestanden. Stel de tijd in om te leven op een paar minuten en markeer de rapporten die moeten worden verwijderd wanneer ze verlopen om te zorgen voor opschoning.
* Het secundaire dat een taak uitvoert, wordt gerapporteerd wanneer het langer duurt dan verwacht om het uit te voeren. Het rapporteert over de service-instantie op de eigenschap **PendingTasks**. Het rapport wijst de serviceinstantie aan die problemen heeft, maar het geeft niet vast waar de instantie sterft. De rapporten worden dan opgeruimd. Het zou kunnen rapporteren over de secundaire dienst. Als de secundaire taak is voltooid, wordt het rapport in de secundaire instantie uit de winkel gewist. Het rapport geeft niet de situatie weer waarin het bevestigingsbericht verloren gaat en de taak niet is voltooid vanuit het oogpunt van de master.

Maar de rapportage wordt gedaan in de hierboven beschreven gevallen, de rapporten worden vastgelegd in de status van toepassing wanneer de status wordt geëvalueerd.

## <a name="report-periodically-vs-on-transition"></a>Periodiek rapporteren vs. over overgang
Door gebruik te maken van het health reporting model kunnen waakhonden periodiek of over overgangen rapporten versturen. De aanbevolen manier voor watchdog rapportage is periodiek, omdat de code is veel eenvoudiger en minder gevoelig voor fouten. De waakhonden moeten ernaar streven om zo eenvoudig mogelijk te zijn om bugs te voorkomen die onjuiste rapporten veroorzaken. Onjuiste *ongezonde* rapporten hebben invloed op gezondheidsevaluaties en -scenario's op basis van gezondheid, inclusief upgrades. *Onjuiste, gezonde* rapporten verbergen problemen in het cluster, wat niet gewenst is.

Voor periodieke rapportage kan de waakhond worden geïmplementeerd met een timer. Op een timer callback, kan de waakhond controleren van de staat en stuur een rapport op basis van de huidige staat. Het is niet nodig om te zien welk rapport eerder is verzonden of optimalisaties te maken op het gebied van messaging. De statusclient heeft batchlogica om te helpen met de prestaties. Terwijl de statusclient in leven wordt gehouden, wordt deze intern opnieuw geprobeerd totdat het rapport wordt erkend door de gezondheidswinkel of de waakhond een nieuwer rapport genereert met dezelfde entiteit, eigenschap en bron.

Rapportage over overgangen vereist een zorgvuldige behandeling van de staat. De waakhond houdt een aantal voorwaarden en rapporten alleen in de gaten als de omstandigheden veranderen. Het voordeel van deze aanpak is dat er minder meldingen nodig zijn. Het nadeel is dat de logica van de waakhond complex is. De waakhond moet de voorwaarden of de rapporten handhaven, zodat ze kunnen worden geïnspecteerd om staatswijzigingen te bepalen. Bij failover, zorg moet worden genomen met rapporten toegevoegd, maar nog niet verzonden naar de health store. Het volgnummer moet steeds groter worden. Zo niet, dan worden de rapporten afgewezen als muf. In de zeldzame gevallen waarin gegevensverlies wordt geleden, kan synchronisatie nodig zijn tussen de toestand van de melder en de status van het gezondheidsarchief.

Rapportage over overgangen is zinvol voor `Partition` diensten `CodePackageActivationContext`die over zichzelf rapporteren, via of . Wanneer het lokale object (replica of geïmplementeerd servicepakket / geïmplementeerde toepassing) wordt verwijderd, worden ook alle rapporten verwijderd. Deze automatische opruiming ontspant de noodzaak voor synchronisatie tussen reporter en health store. Als het rapport is voor bovenliggende partitie of bovenliggende toepassing, moet ervoor zorgen dat failover wordt genomen om verouderde rapporten in het gezondheidsarchief te voorkomen. Logica moet worden toegevoegd om de juiste status te behouden en het rapport uit de winkel te wissen wanneer dit niet meer nodig is.

## <a name="implement-health-reporting"></a>Gezondheidsrapportage implementeren
Zodra de entiteit en rapportdetails duidelijk zijn, kan het verzenden van statusrapporten worden uitgevoerd via de API, PowerShell of REST.

### <a name="api"></a>API
Als u de API wilt rapporteren, moet u een statusrapport maken dat specifiek is voor het entiteitstype waarover ze willen rapporteren. Geef het rapport aan een gezondheidscliënt. U ook een gezondheidsinformatie maken en deze `Partition` `CodePackageActivationContext` doorgeven aan de juiste rapportagemethoden op of rapporteren over huidige entiteiten.

In het volgende voorbeeld ziet u periodieke rapportage van een waakhond binnen het cluster. De waakhond controleert of een externe bron toegankelijk is vanuit een knooppunt. De resource is nodig door een servicemanifest binnen de toepassing. Als de resource niet beschikbaar is, kunnen de andere services binnen de toepassing nog steeds goed functioneren. Daarom wordt het rapport elke 30 seconden verzonden op de entiteit van het geïmplementeerde servicepakket.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
Statusrapporten verzenden met **Send-ServiceFabric*EntityType*HealthReport**.

In het volgende voorbeeld ziet u periodieke rapportage over CPU-waarden op een knooppunt. De rapporten moeten worden verzonden om de 30 seconden, en ze hebben een tijd om te leven van twee minuten. Als ze verlopen, heeft de melder problemen, dus het knooppunt wordt foutop beoordeeld. Wanneer de CPU zich boven een drempelwaarde bevindt, heeft het rapport een status van waarschuwing. Wanneer de CPU langer dan de geconfigureerde tijd boven een drempelwaarde blijft, wordt deze gerapporteerd als een fout. Anders stuurt de verslaggever een gezondheidstoestand van OK.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

In het volgende voorbeeld wordt een tijdelijke waarschuwing op een replica gerapporteerd. Het krijgt eerst de partitie-ID en vervolgens de replica-ID voor de service waarin het geïnteresseerd is. Het stuurt vervolgens een rapport van **PowershellWatcher** op de eigenschap **ResourceDependency**. Het rapport is slechts twee minuten van belang en wordt automatisch uit de winkel verwijderd.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

### <a name="rest"></a>REST
Stuur gezondheidsrapporten met BEHULP VAN REST met POST-verzoeken die naar de gewenste entiteit gaan en de beschrijving van het gezondheidsrapport in het lichaam hebben. Zie bijvoorbeeld hoe u [REST-clusterstatusrapporten](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) of [servicestatusrapporten](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)verzendt. Alle entiteiten worden ondersteund.

## <a name="next-steps"></a>Volgende stappen
Op basis van de gezondheidsgegevens kunnen serviceschrijvers en cluster-/applicatiebeheerders manieren bedenken om de informatie te consumeren. Ze kunnen bijvoorbeeld waarschuwingen instellen op basis van de gezondheidsstatus om ernstige problemen op te vangen voordat ze storingen veroorzaken. Beheerders kunnen ook reparatiesystemen instellen om problemen automatisch op te lossen.

[Inleiding tot Service Fabric health Monitoring](service-fabric-health-introduction.md)

[Gezondheidsrapporten van Service Fabric weergeven](service-fabric-view-entities-aggregated-health.md)

[Hoe de servicestatus te rapporteren en te controleren](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Systeemstatusrapporten gebruiken voor het oplossen van problemen](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Services lokaal controleren en een diagnose uitvoeren](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade van servicefabric-toepassingen](service-fabric-application-upgrade.md)

