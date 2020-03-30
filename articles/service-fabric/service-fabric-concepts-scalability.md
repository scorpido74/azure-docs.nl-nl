---
title: Schaalbaarheid van servicefabric-services
description: Meer informatie over schalen in Azure Service Fabric en de verschillende technieken die worden gebruikt om toepassingen te schalen.
author: masnider
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: masnider
ms.openlocfilehash: 17827342b67d37d9fbeb56654824e004367823ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282560"
---
# <a name="scaling-in-service-fabric"></a>Schalen in servicestof
Azure Service Fabric maakt het eenvoudig om schaalbare toepassingen te bouwen door de services, partities en replica's op de knooppunten van een cluster te beheren. Het uitvoeren van veel workloads op dezelfde hardware maakt een maximaal gebruik van resources mogelijk, maar biedt ook flexibiliteit in termen van hoe u ervoor kiest om uw workloads te schalen. In deze Kanaal 9-video wordt beschreven hoe u schaalbare microservices-toepassingen bouwen:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

Schalen in Service Fabric wordt op verschillende manieren uitgevoerd:

1. Schalen door stateless service-exemplaren te maken of te verwijderen
2. Schalen door nieuwe benoemde services te maken of te verwijderen
3. Schalen door nieuwe benoemde toepassingsinstanties te maken of te verwijderen
4. Schalen met partitioned services
5. Schalen door knooppunten uit het cluster toe te voegen en te verwijderen 
6. Schalen met behulp van statistieken clusterbronbeheer

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Schalen door stateless service-exemplaren te maken of te verwijderen
Een van de eenvoudigste manieren om te schalen binnen Service Fabric werkt met stateless services. Wanneer u een stateless service maakt, krijgt `InstanceCount`u de kans om een . `InstanceCount`definieert hoeveel lopende kopieën van de code van die service worden gemaakt wanneer de service wordt opgestart. Laten we bijvoorbeeld zeggen dat er 100 knooppunten in het cluster zijn. Laten we ook zeggen dat een `InstanceCount` service is gemaakt met een van 10. Tijdens runtime kunnen die 10 lopende exemplaren van de code allemaal te druk worden (of niet druk genoeg zijn). Een manier om die werkbelasting te schalen, is door het aantal exemplaren te wijzigen. Een stuk bewakings- of beheercode kan bijvoorbeeld het bestaande aantal exemplaren wijzigen in 50 of in 5, afhankelijk van of de werkbelasting moet worden in- of uitgeschaald op basis van de belasting. 

C#:

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell:

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Dynamisch aantal instance's gebruiken
Speciaal voor stateless services biedt Service Fabric een automatische manier om het aantal instance's te wijzigen. Hierdoor kan de service dynamisch schalen met het aantal knooppunten dat beschikbaar is. De manier om op dit gedrag te kiezen is door het aantal instantie = -1 in te stellen. InstanceCount = -1 is een instructie aan Service Fabric met de tekst 'Voer deze stateloze service uit op elk knooppunt'. Als het aantal knooppunten verandert, wijzigt Service Fabric automatisch het aantal instantieen om overeen te komen, zodat de service op alle geldige knooppunten wordt uitgevoerd. 

C#:

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Schalen door nieuwe benoemde services te maken of te verwijderen
Een benoemde serviceinstantie is een specifiek exemplaar van een servicetype (zie [levenscyclus van servicefabric-toepassingen)](service-fabric-application-lifecycle.md)binnen een benoemde toepassingsinstantie in het cluster. 

Nieuwe benoemde service-exemplaren kunnen worden gemaakt (of verwijderd) als services min of meer bezet worden. Hierdoor kunnen aanvragen worden verspreid over meer service-exemplaren, waardoor de belasting van bestaande services meestal kan afnemen. Bij het maken van services plaatst de Clusterresourcemanager Service Fabric de services op een gedistribueerde manier in het cluster. De exacte beslissingen worden bepaald door de [statistieken](service-fabric-cluster-resource-manager-metrics.md) in het cluster en andere plaatsingsregels. Services kunnen op verschillende manieren worden gemaakt, maar de meest [`New-ServiceFabricService`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)voorkomende zijn [`CreateServiceAsync`](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet)door middel van administratieve acties zoals iemand die belt, of door code aante roepen . `CreateServiceAsync`kan zelfs worden aangeroepen vanuit andere services die in het cluster worden uitgevoerd.

Het dynamisch maken van services kan in allerlei scenario's worden gebruikt en is een veelvoorkomend patroon. Denk bijvoorbeeld aan een stateful service die een bepaalde werkstroom vertegenwoordigt. Oproepen die werk vertegenwoordigen, worden weergegeven aan deze service en deze service gaat de stappen uitvoeren naar die werkstroom en de voortgang registreren. 

Hoe zou u deze specifieke serviceschaal maken? De service kan multi-tenant in een bepaalde vorm, en accepteren oproepen en kick-off stappen voor veel verschillende exemplaren van dezelfde workflow allemaal tegelijk. Dat kan de code echter complexer maken, omdat deze zich nu zorgen moet maken over veel verschillende exemplaren van dezelfde workflow, allemaal in verschillende stadia en van verschillende klanten. Ook het verwerken van meerdere werkstromen op hetzelfde moment lost het schaalprobleem niet op. Dit komt omdat op een gegeven moment deze service te veel resources zal verbruiken om op een bepaalde machine te passen. Veel diensten niet gebouwd voor dit patroon in de eerste plaats ook problemen ondervinden als gevolg van een aantal inherente knelpunt of vertraging in hun code. Dit soort problemen zorgt ervoor dat de service niet zo goed werkt wanneer het aantal gelijktijdige workflows dat wordt bijgehouden groter wordt.  

Een oplossing is het maken van een instantie van deze service voor elk ander exemplaar van de workflow die u wilt bijhouden. Dit is een groot patroon en werkt of de dienst stateless of stateful is. Om dit patroon te laten werken, is er meestal een andere service die fungeert als een "Workload Manager Service". Het is de taak van deze service om aanvragen te ontvangen en deze verzoeken door te sturen naar andere services. De manager kan dynamisch een instantie van de werkbelastingservice maken wanneer deze het bericht ontvangt en vervolgens aanvragen doorgeven aan die services. De managerservice kan ook callbacks ontvangen wanneer een bepaalde werkstroomservice zijn taak voltooit. Wanneer de manager deze callbacks ontvangt, kan deze deze instantie van de werkstroomservice verwijderen of verlaten als er meer oproepen worden verwacht. 

Geavanceerde versies van dit type manager kunnen zelfs pools maken van de services die het beheert. Het zwembad helpt ervoor te zorgen dat wanneer er een nieuw verzoek binnenkomt, het niet hoeft te wachten tot de service afloopt. In plaats daarvan kan de manager gewoon een werkstroomservice kiezen die momenteel niet wordt bezet vanuit de groep of willekeurig route. Door een pool van services beschikbaar te houden, wordt het sneller afhandelen van nieuwe aanvragen, omdat het minder waarschijnlijk is dat de aanvraag hoeft te wachten tot een nieuwe service wordt samengevoegd. Het maken van nieuwe services is snel, maar niet gratis of onmiddellijk. Het zwembad helpt de hoeveelheid tijd die het verzoek moet wachten voordat het wordt onderhouden, te minimaliseren. U ziet vaak dit manager- en poolpatroon wanneer de responstijden het belangrijkst zijn. Het in de rij plaatsen van de aanvraag en het maken van de service op de achtergrond en deze _vervolgens_ doorgeven is ook een populair managerpatroon, evenals het maken en verwijderen van services op basis van het bijhouden van de hoeveelheid werk die momenteel in behandeling is. 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Schalen door nieuwe benoemde toepassingsinstanties te maken of te verwijderen
Het maken en verwijderen van hele toepassingsinstanties is vergelijkbaar met het patroon van het maken en verwijderen van services. Voor dit patroon is er een managerservice die de beslissing neemt op basis van de aanvragen die worden gezien en de informatie die het ontvangt van de andere services binnen het cluster. 

Wanneer moet het maken van een nieuwe benoemde toepassingsinstantie worden gebruikt in plaats van een nieuwe benoemde service-exemplaren te maken in een bestaande toepassing? Er zijn een paar zaken:

  * De nieuwe toepassingsinstantie is voor een klant wiens code moet worden uitgevoerd onder een bepaalde identiteits- of beveiligingsinstellingen.
    * Service Fabric maakt het mogelijk om verschillende codepakketten te definiëren onder bepaalde identiteiten. Om hetzelfde codepakket onder verschillende identiteiten te starten, moeten de activeringen in verschillende toepassingsinstanties plaatsvinden. Houd rekening met een geval waarin u de workloads van een bestaande klant hebt geïmplementeerd. Deze kunnen worden uitgevoerd onder een bepaalde identiteit, zodat u de toegang tot andere bronnen, zoals externe databases of andere systemen, controleren en beheren. In dit geval, wanneer een nieuwe klant zich aanmeldt, wilt u waarschijnlijk niet om hun code te activeren in dezelfde context (procesruimte). Hoewel dit zou kunnen, maakt dit het moeilijker voor uw servicecode om te handelen binnen de context van een bepaalde identiteit. U moet doorgaans meer beveiligings-, isolatie- en identiteitsbeheercode hebben. In plaats van verschillende benoemde service-exemplaren te gebruiken in dezelfde toepassingsinstantie en dus dezelfde procesruimte, u verschillende exemplaren van de ServiceFabric-toepassing gebruiken. Dit maakt het gemakkelijker om verschillende identiteitscontexten te definiëren.
  * De nieuwe toepassingsinstantie dient ook als configuratiemiddel
    * Standaard worden alle benoemde serviceexemplaren van een bepaald servicetype in een toepassingsinstantie in hetzelfde proces op een bepaald knooppunt uitgevoerd. Wat dit betekent is dat, terwijl u elke service-instantie anders configureren, dit te doen is ingewikkeld. Services moeten een token hebben dat ze gebruiken om hun config op te zoeken binnen een configuratiepakket. Meestal is dit alleen de naam van de dienst. Dit werkt prima, maar koppelt de configuratie aan de namen van de afzonderlijke benoemde service-exemplaren binnen die toepassingsinstantie. Dit kan verwarrend en moeilijk te beheren zijn, omdat configuratie normaal gesproken een artefact met ontwerptijdis met specifieke waarden voor toepassingsinstanties. Het maken van meer services betekent altijd meer applicatie-upgrades om de informatie binnen de config-pakketten te wijzigen of om nieuwe te implementeren, zodat de nieuwe services hun specifieke informatie kunnen opzoeken. Het is vaak eenvoudiger om een geheel nieuwe toepassingsinstantie met een nieuwe naam te maken. Vervolgens u de toepassingsparameters gebruiken om in te stellen welke configuratie nodig is voor de services. Op deze manier kunnen alle services die zijn gemaakt binnen de benoemde toepassingsinstantie bepaalde configuratie-instellingen overnemen. In plaats van bijvoorbeeld één configuratiebestand met de instellingen en aanpassingen voor elke klant, zoals geheimen of limieten voor klantbronnen, zou u in plaats daarvan een ander toepassingsexemplaar voor elke klant met deze instellingen hebben Overschreven. 
  * De nieuwe applicatie dient als een upgrade grens
    * Binnen Service Fabric dienen verschillende benoemde toepassingsinstanties als grenzen voor een upgrade. Een upgrade van een benoemde toepassingsinstantie heeft geen invloed op de code die een andere benoemde toepassingsinstantie uitvoert. De verschillende toepassingen zullen uiteindelijk draaien verschillende versies van dezelfde code op dezelfde knooppunten. Dit kan een factor zijn wanneer u een schaalbeslissing moet nemen, omdat u kiezen of de nieuwe code dezelfde upgrades moet volgen als een andere service of niet. Stel dat een oproep bij de managerservice aankomt en die verantwoordelijk is voor het schalen van de workloads van een bepaalde klant door services dynamisch te maken en te verwijderen. In dit geval is de oproep echter voor een werkbelasting die is gekoppeld aan een _nieuwe_ klant. De meeste klanten willen worden geïsoleerd van elkaar niet alleen voor de veiligheid en configuratie redenen die eerder vermeld, maar omdat het biedt meer flexibiliteit in termen van het uitvoeren van specifieke versies van de software en kiezen wanneer ze een upgrade. U ook een nieuwe toepassingsinstantie maken en de service daar maken om het bedrag van uw services dat een upgrade aangaat verder te verdelen. Afzonderlijke toepassingsexemplaren bieden een grotere granulariteit bij het uitvoeren van toepassingsupgrades en schakelen ook A/B-tests en blauw/groene implementaties in. 
  * De bestaande toepassingsinstantie is vol
    * In Service Fabric is [toepassingscapaciteit](service-fabric-cluster-resource-manager-application-groups.md) een concept dat u gebruiken om de hoeveelheid beschikbare resources voor bepaalde toepassingsinstanties te beheren. U bijvoorbeeld besluiten dat een bepaalde service een andere instantie moet hebben gemaakt om te kunnen schalen. Deze toepassingsinstantie heeft echter geen capaciteit voor een bepaalde statistiek. Als deze specifieke klant of werkbelasting nog steeds meer resources moet krijgen, u de bestaande capaciteit voor die toepassing vergroten of een nieuwe toepassing maken. 

## <a name="scaling-at-the-partition-level"></a>Schalen op partitieniveau
Service Fabric ondersteunt partitionering. Partitionering splitst een service op in verschillende logische en fysieke secties, die elk onafhankelijk werken. Dit is handig met stateful diensten, omdat geen enkele set replica's heeft om alle gesprekken te behandelen en alle van de staat te manipuleren in een keer. Het [partitioneringsoverzicht](service-fabric-concepts-partitioning.md) bevat informatie over de typen partitieschema's die worden ondersteund. De replica's van elke partitie zijn verspreid over de knooppunten in een cluster, verdelen de belasting van die service en zorgen ervoor dat noch de service als geheel of een partitie één storingspunt heeft. 

Overweeg een service die gebruik maakt van een bereikverdelingsschema met een lage toets van 0, een hoge toets van 99 en een partitietelling van 4. In een cluster met drie delen kan de service worden ingedeeld met vier replica's die de bronnen op elk knooppunt delen, zoals hier wordt weergegeven:

<center>

![Partitieindeling met drie knooppunten](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Als u het aantal knooppunten verhoogt, verplaatst Service Fabric enkele bestaande replica's daar. Stel dat het aantal knooppunten toeneemt tot vier en de replica's opnieuw worden verdeeld. Nu heeft de service nu drie replica's die op elk knooppunt worden uitgevoerd, elk behorend tot verschillende partities. Dit maakt een beter gebruik van resources mogelijk omdat het nieuwe knooppunt niet koud is. Doorgaans verbetert het ook de prestaties omdat elke service meer resources tot zijn beschikking heeft.

<center>

![Partitieindeling met vier knooppunten](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Schalen met behulp van de clusterbronbeheer en metrische gegevens van de servicestructuur
[Statistieken](service-fabric-cluster-resource-manager-metrics.md) zijn hoe services hun bronverbruik uitdrukken aan Service Fabric. Het gebruik van metrische gegevens geeft de Cluster Resource Manager de mogelijkheid om de lay-out van het cluster te reorganiseren en te optimaliseren. Er kunnen bijvoorbeeld voldoende resources in het cluster zijn, maar deze worden mogelijk niet toegewezen aan de services die momenteel aan het werk zijn. Met behulp van metrische gegevens kan clusterbronbeheer het cluster reorganiseren om ervoor te zorgen dat services toegang hebben tot de beschikbare bronnen. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Schalen door knooppunten uit het cluster toe te voegen en te verwijderen 
Een andere optie voor schalen met Service Fabric is het wijzigen van de grootte van het cluster. Als u de grootte van het cluster wijzigt, worden knooppunten voor een of meer van de knooppunttypen in het cluster toegevoegd of verwijderd. Houd bijvoorbeeld rekening met een geval waarin alle knooppunten in het cluster hot zijn. Dit betekent dat de bronnen van het cluster bijna allemaal worden verbruikt. In dit geval is het toevoegen van meer knooppunten aan het cluster de beste manier om te schalen. Zodra de nieuwe knooppunten lid worden van het cluster, verplaatst de Service Fabric Cluster Resource Manager services naar hen, wat resulteert in minder totale belasting op de bestaande knooppunten. Voor stateloze services met instantietelling = -1 worden automatisch meer service-exemplaren gemaakt. Hierdoor kunnen sommige oproepen van de bestaande knooppunten naar de nieuwe knooppunten gaan. 

Zie [clusterschaling](service-fabric-cluster-scaling.md)voor meer informatie .

## <a name="choosing-a-platform"></a>Een platform kiezen

Vanwege implementatieverschillen tussen besturingssystemen kan het kiezen om Service Fabric met Windows of Linux te gebruiken een essentieel onderdeel zijn van het schalen van uw toepassing. Een mogelijke barrière is hoe geënsceneerde logging wordt uitgevoerd. Service Fabric op Windows maakt gebruik van een kernel driver voor een one-per-machine log, gedeeld tussen stateful service replica's. Dit logboek weegt ongeveer 8 GB. Linux, aan de andere kant, maakt gebruik van een 256 MB staging log voor elke replica, waardoor het minder ideaal voor toepassingen die willen het aantal lichtgewicht service replica's draaien op een bepaald knooppunt te maximaliseren. Deze verschillen in tijdelijke opslagvereisten kunnen mogelijk het gewenste platform voor de implementatie van Service Fabric-clusteren bepalen.

## <a name="putting-it-all-together"></a>Alles samenvoegen
Laten we alle ideeën die we hier hebben besproken nemen en een voorbeeld geven. Overweeg de volgende service: u probeert een service op te bouwen die fungeert als een adresboek, met namen en contactgegevens. 

Recht op voorhand heb je een heleboel vragen met betrekking tot schaal: Hoeveel gebruikers ga je hebben? Hoeveel contactpersonen worden elke gebruiker opgeslagen? Proberen om dit allemaal uit te zoeken wanneer u opstaan uw dienst voor de eerste keer is moeilijk. Stel dat u voor één statische service zou gaan met een specifiek aantal partities. De gevolgen van het kiezen van de verkeerde partitie telling kan ertoe leiden dat u schaal problemen later hebben. Ook als u de juiste telling kiest, beschikt u mogelijk niet over alle informatie die u nodig hebt. U moet bijvoorbeeld ook vooraf de grootte van het cluster bepalen, zowel in termen van het aantal knooppunten als de grootte ervan. Het is meestal moeilijk te voorspellen hoeveel resources een service gaat verbruiken gedurende zijn levensduur. Het kan ook moeilijk zijn om van tevoren het verkeerspatroon te weten dat de service daadwerkelijk ziet. Bijvoorbeeld, misschien mensen toe te voegen en te verwijderen hun contacten alleen eerste ding in de ochtend, of misschien is het gelijkmatig verdeeld in de loop van de dag. Op basis hiervan moet u mogelijk dynamisch uitschalen en inschalen. Misschien kun je leren voorspellen wanneer je moet uitschalen en in, maar hoe dan ook zul je waarschijnlijk moeten reageren op het veranderende verbruik van hulpbronnen door uw service. Dit kan betekenen dat de grootte van het cluster moet worden gewijzigd om meer resources te bieden wanneer het reorganisatievan het gebruik van bestaande resources niet voldoende is. 

Maar waarom zelfs proberen om een partitie schema uit te kiezen voor alle gebruikers? Waarom zou u zich beperken tot één service en één statisch cluster? De werkelijke situatie is meestal dynamischer. 

Houd bij het bouwen voor schaal rekening met het volgende dynamische patroon. Het kan nodig zijn om het aan te passen aan uw situatie:

1. In plaats van te proberen om een verdelingregeling voor iedereen op voorhand te kiezen, bouw een "manager service".
2. De taak van de manager service is om te kijken naar klantinformatie wanneer ze zich aanmelden voor uw service. Afhankelijk van die informatie maakt de managerservice vervolgens een exemplaar van uw _werkelijke_ contactopslagservice _alleen voor die klant._ Als ze bepaalde configuratie, isolatie of upgrades vereisen, u ook besluiten om een toepassingsexemplaar voor deze klant op te draaien. 

Dit dynamische creatiepatroon heeft vele voordelen:

  - U probeert niet de juiste partitietelling voor alle gebruikers vooraf te raden of een enkele service te bouwen die op zichzelf oneindig schaalbaar is. 
  - Verschillende gebruikers hoeven niet hetzelfde aantal partities, replicatelling, plaatsingsbeperkingen, metrische gegevens, standaardbelastingen, servicenamen, dns-instellingen of een van de andere eigenschappen te hebben die zijn opgegeven op service- of toepassingsniveau. 
  - U krijgt extra gegevenssegmentatie. Elke klant heeft zijn eigen kopie van de dienst
    - Elke klantenservice kan anders worden geconfigureerd en meer of minder resources worden toegekend, met meer of minder partities of replica's indien nodig op basis van de verwachte schaal.
      - Stel dat de klant heeft betaald voor de laag 'Goud', maar dat ze meer replica's of meer partities kunnen krijgen en mogelijk resources die zijn toegewezen aan hun services via statistieken en toepassingscapaciteiten.
      - Of zeggen dat ze informatie verstrekt en het aantal contacten dat ze nodig hadden "Klein" waren - ze zouden slechts een paar partities krijgen, of zelfs in een gedeelde servicepool met andere klanten worden geplaatst.
  - U voert geen aantal service-exemplaren of replica's uit terwijl u wacht tot klanten worden weergegeven
  - Als een klant ooit vertrekt, is het verwijderen van zijn of haar gegevens uit uw service net zo eenvoudig als dat de manager die service of toepassing die is gemaakt, verwijdert.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over servicefabric-concepten:

* [Beschikbaarheid van Service Fabric-services](service-fabric-availability-services.md)
* [Services voor partitioneringsservice](service-fabric-concepts-partitioning.md)
