---
title: Het laden van Azure Service Fabric-apps beheren met behulp van metrische gegevens
description: Meer informatie over het configureren en gebruiken van statistieken in Service Fabric om het verbruik van servicebronnen te beheren.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ea21502cdab35b261e20af7f23b7b522f77c6667
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451998"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Resourceverbruik beheren en laden in Service Fabric met statistieken
*Statistieken* zijn de resources waar uw services om geven en die worden geleverd door de knooppunten in het cluster. Een statistiek is alles wat u wilt beheren om de prestaties van uw services te verbeteren of te controleren. U bijvoorbeeld het geheugenverbruik bekijken om te weten of uw service overbelast is. Een ander gebruik is om erachter te komen of de service elders kan bewegen waar het geheugen minder beperkt is om betere prestaties te krijgen.

Zaken als geheugen, schijf en CPU-gebruik zijn voorbeelden van statistieken. Deze statistieken zijn fysieke statistieken, resources die overeenkomen met fysieke bronnen op het knooppunt die moeten worden beheerd. Statistieken kunnen ook logische statistieken zijn (en zijn dat ook). Logische statistieken zijn zaken als "MyWorkQueueDepth" of "MessagesToProcess" of "TotalRecords". Logische statistieken zijn toepassingsgedefinieerde en komen indirect overeen met een fysiek resourceverbruik. Logische statistieken komen vaak voor omdat het moeilijk kan zijn om het verbruik van fysieke resources per service te meten en te rapporteren. De complexiteit van het meten en rapporteren van uw eigen fysieke statistieken is ook de reden waarom Service Fabric een aantal standaardstatistieken biedt.

## <a name="default-metrics"></a>Standaardstatistieken
Stel dat u aan de slag wilt met het schrijven en implementeren van uw service. Op dit punt weet je niet welke fysieke of logische bronnen het verbruikt. Dat is prima! De Clusterresourcemanager servicestructuur gebruikt een aantal standaardstatistieken wanneer er geen andere statistieken zijn opgegeven. Dit zijn:

  - Primair aantal gegevens - aantal primaire replica's op het knooppunt 
  - ReplicaCount - aantal total stateful replica's op het knooppunt
  - Aantal - aantal van alle serviceobjecten (stateloos en stateful) op het knooppunt

| Gegevens | Stateless Instance Load | Stateful Secundaire belasting | Stateful Primary Load | Gewicht |
| --- | --- | --- | --- | --- |
| Primairetelling |0 |0 |1 |Hoog |
| ReplicaCount |0 |1 |1 |Middelgroot |
| Count |1 |1 |1 |Laag |


Voor basisworkloads bieden de standaardstatistieken een fatsoenlijke verdeling van het werk in het cluster. Laten we in het volgende voorbeeld eens kijken wat er gebeurt als we twee services maken en vertrouwen op de standaardstatistieken voor het balanceren. De eerste service is een stateful service met drie partities en een doelreplica set grootte van drie. De tweede service is een stateless service met één partitie en een instantietelling van drie.

Dit zijn de onderdelen:

<center>

![Clusterindeling met standaardstatistieken][Image1]
</center>

Let op het volgende:
  - Primaire replica's voor de stateful service worden verdeeld over verschillende knooppunten
  - Replica's voor dezelfde partitie bevinden zich op verschillende knooppunten
  - Het totale aantal voorverkiezingen en secondaries wordt verdeeld in het cluster
  - Het totale aantal serviceobjecten wordt gelijkmatig toegewezen aan elk knooppunt

Goede!

De standaardstatistieken werken geweldig als begin. De standaardstatistieken dragen u echter alleen tot nu toe. Bijvoorbeeld: Hoe groot is de kans dat het partitieschema dat u hebt gekozen, perfect gelijkmatig wordt benut door alle partities? Wat is de kans dat de belasting voor een bepaalde service constant is na verloop van tijd, of zelfs gewoon hetzelfde over meerdere partities op dit moment?

Je zou kunnen draaien met alleen de standaard statistieken. Echter, dit betekent meestal dat uw cluster gebruik lager is en meer ongelijk dan je zou willen. Dit komt omdat de standaardstatistieken niet adaptief zijn en veronderstellen dat alles gelijkwaardig is. Een primair primair thema dat bezet is en een primair dat niet beide '1' bijdraagt aan de statistiek PrimaryCount. In het ergste geval kan het gebruik van alleen de standaardstatistieken ook leiden tot overgeplande knooppunten die resulteren in prestatieproblemen. Als u het meeste uit uw cluster wilt halen en prestatieproblemen wilt voorkomen, moet u aangepaste statistieken en dynamische belastingrapportage gebruiken.

## <a name="custom-metrics"></a>Aangepaste metrische gegevens
Metrische gegevens worden geconfigureerd op basis van een service-exemplaar per naam wanneer u de service maakt.

Elke statistiek heeft een aantal eigenschappen die deze beschrijven: een naam, een gewicht en een standaardbelasting.

* Metrische naam: de naam van de statistiek. De metrische naam is een unieke id voor de statistiek binnen het cluster vanuit het perspectief van Resource Manager.
* Gewicht: het metrische gewicht bepaalt hoe belangrijk deze statistiek is ten opzichte van de andere statistieken voor deze service.
* Standaardbelasting: de standaardbelasting wordt anders weergegeven, afhankelijk van of de service stateloos of stateful is.
  * Voor stateloze services heeft elke statistiek één eigenschap met de naam DefaultLoad
  * Voor stateful services die u definieert:
    * PrimaryDefaultLoad: de standaardhoeveelheid van deze statistiek die deze service verbruikt wanneer deze een primaire standaardwaarde is
    * SecondaryDefaultLoad: de standaardhoeveelheid van deze statistiek die deze service verbruikt wanneer deze secundair is

> [!NOTE]
> Als u aangepaste statistieken definieert en u _ook_ de standaardstatistieken wilt gebruiken, moet u de standaardstatistieken _expliciet_ weer toevoegen en gewichten en waarden voor deze statistieken definiëren. Dit komt omdat u de relatie tussen de standaardstatistieken en uw aangepaste statistieken moet definiëren. Misschien geeft u bijvoorbeeld meer om ConnectionCount of WorkQueueDepth dan primaire distributie. Het gewicht van de statistiek PrimaryCount is standaard Hoog, dus u wilt deze verlagen tot Gemiddeld wanneer u uw andere statistieken toevoegt om ervoor te zorgen dat deze prioriteit krijgen.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Statistieken definiëren voor uw service - een voorbeeld
Stel dat u de volgende configuratie wilt:

  - Uw service rapporteert een statistiek met de naam 'ConnectionCount'
  - U wilt ook de standaardstatistieken gebruiken 
  - U hebt een aantal metingen gedaan en weet dat normaal gesproken een primaire replica van die service 20 eenheden van "ConnectionCount" in neemt
  - Secondaries gebruiken 5 eenheden van "ConnectionCount"
  - U weet dat "ConnectionCount" de belangrijkste statistiek is voor het beheer van de prestaties van deze specifieke service
  - U wilt nog steeds primaire replica's in evenwicht brengen. Balanceren primaire replica's is over het algemeen een goed idee maakt niet uit wat. Dit helpt voorkomen dat het verlies van een knooppunt of fout domein van invloed op een meerderheid van de primaire replica's samen met het. 
  - Anders zijn de standaardstatistieken prima

Hier is de code die u zou schrijven om een service te maken met die metrische configuratie:

Code:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription connectionMetric = new StatefulServiceLoadMetricDescription();
connectionMetric.Name = "ConnectionCount";
connectionMetric.PrimaryDefaultLoad = 20;
connectionMetric.SecondaryDefaultLoad = 5;
connectionMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(connectionMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> De bovenstaande voorbeelden en de rest van dit document beschrijven het beheren van metrische gegevens op basis van een benoemde service. Het is ook mogelijk om statistieken voor uw services te definiëren op _serviceniveau._ Dit wordt bereikt door ze op te geven in uw servicemanifesten. Het definiëren van typeniveaustatistieken wordt om verschillende redenen niet aanbevolen. De eerste reden is dat metrische namen vaak omgevingsspecifiek zijn. Tenzij er een vast contract is, u er niet zeker van zijn dat de metrische Cores in de ene omgeving niet MiliCores of CoReS in andere is. Als uw statistieken worden gedefinieerd in uw manifest, moet u nieuwe manifesten per omgeving maken. Dit leidt meestal tot een wildgroei van verschillende manifesten met slechts kleine verschillen, wat kan leiden tot managementproblemen.  
>
> Metrische belastingwordt vaak toegewezen op basis van een naam-service-instantie. Stel dat u één exemplaar van de service voor CustomerA maakt die van plan is deze slechts licht te gebruiken. Stel ook dat u een andere voor CustomerB maakt die een grotere werklast heeft. In dit geval wilt u waarschijnlijk de standaardbelastingen voor deze services aanpassen. Als u statistieken en belastingen hebt gedefinieerd via manifesten en u dit scenario wilt ondersteunen, zijn voor elke klant verschillende toepassings- en servicetypen vereist. De waarden die zijn gedefinieerd bij het maken van service, overschrijven de waarden die in het manifest zijn gedefinieerd, zodat u dat gebruiken om de specifieke standaardwaarden in te stellen. Echter, doen dat zorgt ervoor dat de waarden aangegeven in de manifesten niet overeenkomen met die de service daadwerkelijk draait met. Dit kan tot verwarring leiden. 
>

Ter herinnering: als u alleen de standaardstatistieken wilt gebruiken, hoeft u de verzameling statistieken helemaal niet aan te raken of iets speciaals te doen bij het maken van uw service. De standaardstatistieken worden automatisch gebruikt wanneer er geen andere gegevens zijn gedefinieerd. 

Nu, laten we gaan door elk van deze instellingen in meer detail en praten over het gedrag dat het beïnvloedt.

## <a name="load"></a>Belasting
Het hele punt van het definiëren van metrische gegevens is om een bepaalde belasting te vertegenwoordigen. *Load* is hoeveel van een bepaalde statistiek wordt verbruikt door een service-instantie of replica op een bepaald knooppunt. Load kan op bijna elk punt worden geconfigureerd. Bijvoorbeeld:

  - Belasting kan worden gedefinieerd wanneer een service wordt gemaakt. Dit wordt _standaardbelasting_genoemd.
  - De metrische informatie, inclusief standaardbelastingen, voor een service kan worden bijgewerkt nadat de service is gemaakt. Dit wordt _het bijwerken van een service_genoemd. 
  - De belastingen voor een bepaalde partitie kunnen worden gereset naar de standaardwaarden voor die service. Dit wordt _het resetten van partitiebelasting_genoemd.
  - Load kan dynamisch worden gerapporteerd op basis van per serviceobject tijdens runtime. Dit wordt _rapportagebelasting_genoemd. 
  
Al deze strategieën kunnen worden gebruikt binnen dezelfde dienst gedurende de levensduur. 

## <a name="default-load"></a>Standaardbelasting
*Standaardbelasting* is hoeveel van de metrische metrische metrische elk serviceobject (stateless instance of stateful replica) van deze service verbruikt. De clusterbronbeheer gebruikt dit nummer voor de belasting van het serviceobject totdat het andere informatie ontvangt, zoals een dynamisch belastingrapport. Voor eenvoudigere services is de standaardbelasting een statische definitie. De standaardbelasting wordt nooit bijgewerkt en wordt gebruikt voor de levensduur van de service. Standaardbelastingen werken geweldig voor eenvoudige capaciteitsplanningsscenario's waarbij bepaalde hoeveelheden resources zijn toegewezen aan verschillende workloads en niet worden gewijzigd.

> [!NOTE]
> Zie [dit artikel](service-fabric-cluster-resource-manager-cluster-description.md#capacity)voor meer informatie over capaciteitsbeheer en het definiëren van capaciteit voor de knooppunten in uw cluster.
> 

Met clusterresourcebeheer kunnen stateful services een andere standaardbelasting opgeven voor hun voorverkiezingen en seconden. Staatloze services kunnen slechts één waarde opgeven die op alle instanties van toepassing is. Voor stateful services zijn de standaardbelasting voor primaire en secundaire replica's meestal verschillend, omdat replica's verschillende soorten werk in elke rol doen. Bijvoorbeeld, Primaries meestal dienen zowel leest en schrijft, en omgaan met het grootste deel van de computationele last, terwijl secondaries niet. Meestal is de standaardbelasting voor een primaire replica hoger dan de standaardbelasting voor secundaire replica's. De werkelijke aantallen moeten afhangen van uw eigen metingen.

## <a name="dynamic-load"></a>Dynamische belasting
Stel dat u uw dienst al een tijdje uitvoert. Met wat controle, heb je gemerkt dat:

1. Sommige partities of exemplaren van een bepaalde service verbruiken meer resources dan andere
2. Sommige services hebben een belasting die in de loop van de tijd varieert.

Er zijn veel dingen die dit soort belastingschommelingen kunnen veroorzaken. Verschillende services of partities zijn bijvoorbeeld gekoppeld aan verschillende klanten met verschillende vereisten. De belasting kan ook veranderen omdat de hoeveelheid werk die de service doet, in de loop van de dag varieert. Ongeacht de reden, er is meestal geen enkel nummer dat u gebruiken voor standaard. Dit geldt vooral als u het meeste gebruik uit het cluster wilt halen. Elke waarde die u kiest voor standaardbelasting is een deel van de tijd verkeerd. Onjuiste standaardbelastingen resulteren in clusterbronbeheer boven of onder toewijzing van resources. Als gevolg hiervan hebt u knooppunten die boven of onder worden gebruikt, hoewel de Cluster Resource Manager denkt dat het cluster in evenwicht is. Standaardbelastingen zijn nog steeds goed omdat ze wat informatie bieden voor de eerste plaatsing, maar ze zijn geen compleet verhaal voor echte workloads. Om veranderende resourcevereisten nauwkeurig vast te leggen, kan elk serviceobject met het clusterbronobject zijn eigen belasting tijdens runtime bijwerken. Dit wordt dynamische belastingrapportage genoemd.

Met dynamische belastingrapporten kunnen replica's of instanties hun toewijzing/gerapporteerde belasting van statistieken gedurende hun levensduur aanpassen. Een servicereplica of instantie die koud was en geen werk deed, meldt meestal dat het lage hoeveelheden van een bepaalde statistiek gebruikte. Een drukke replica of instantie zou melden dat ze meer gebruiken.

Met rapportagebelasting per replica of instantie kan clusterresourcebeheer de afzonderlijke serviceobjecten in het cluster reorganiseren. Het reorganiseren van de services zorgt ervoor dat ze de resources krijgen die ze nodig hebben. Drukke services kunnen effectief resources "terugwinnen" van andere replica's of instanties die momenteel koud zijn of minder werk doen.

Binnen Reliable Services ziet de code voor het dynamisch rapporteren van laden er als volgt uit:

Code:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

Een service kan rapporteren over een van de statistieken die ervoor zijn gedefinieerd tijdens het maken van de statistieken. Als een service belasting rapporteert voor een statistiek die niet is geconfigureerd om te gebruiken, negeert Service Fabric dat rapport. Als er andere statistieken worden gerapporteerd die tegelijkertijd geldig zijn, worden deze rapporten geaccepteerd. Servicecode kan alle statistieken meten en rapporteren die het weet hoe, en operators kunnen de metrische configuratie opgeven die moet worden gebruikt zonder de servicecode te hoeven wijzigen. 

### <a name="updating-a-services-metric-configuration"></a>De metrische configuratie van een service bijwerken
De lijst met statistieken die aan de service zijn gekoppeld en de eigenschappen van deze statistieken kunnen dynamisch worden bijgewerkt terwijl de service live is. Dit zorgt voor experimenten en flexibiliteit. Enkele voorbeelden van wanneer dit nuttig is zijn:

  - een statistiek uitschakelen met een buggyrapport voor een bepaalde service
  - herconfigureren van de gewichten van metrische gegevens op basis van gewenst gedrag
  - een nieuwe statistiek pas inschakelen nadat de code al is geïmplementeerd en gevalideerd via andere mechanismen
  - de standaardbelasting voor een service wijzigen op basis van waargenomen gedrag en verbruik

De belangrijkste API's voor `FabricClient.ServiceManagementClient.UpdateServiceAsync` het wijzigen `Update-ServiceFabricService` van metrische configuratie bevinden zich in C# en in PowerShell. Welke informatie u met deze API's opgeeft, vervangt onmiddellijk de bestaande metrische informatie voor de service. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Standaardbelastingwaarden en dynamische belastingrapporten mengen
Standaardbelasting en dynamische belastingen kunnen voor dezelfde service worden gebruikt. Wanneer een service zowel standaardbelasting als dynamische belastingrapporten gebruikt, dient standaardbelasting als een schatting totdat dynamische rapporten worden weergegeven. Standaardbelasting is goed omdat het de Cluster Resource Manager iets geeft om mee te werken. Met de standaardbelasting kan clusterbronbeheer de serviceobjecten op goede locaties plaatsen wanneer ze worden gemaakt. Als er geen standaardbelastingsinformatie wordt verstrekt, is plaatsing van services effectief willekeurig. Wanneer load reports later aankomen, is de eerste willekeurige plaatsing vaak verkeerd en moet de Cluster Resource Manager services verplaatsen.

Laten we ons vorige voorbeeld nemen en zien wat er gebeurt als we een aantal aangepaste statistieken en dynamische belastingrapportage toevoegen. In dit voorbeeld gebruiken we 'MemoryInMb' als voorbeeldstatistiek.

> [!NOTE]
> Geheugen is een van de systeemstatistieken die Service Fabric kan [resource regelen,](service-fabric-resource-governance.md)en het rapporteren van het zelf is meestal moeilijk. We verwachten eigenlijk niet dat je rapporteert over het geheugenverbruik; Geheugen wordt hier gebruikt als hulpmiddel bij het leren over de mogelijkheden van de Cluster Resource Manager.
>

Laten we aannemen dat we in eerste instantie de stateful service hebben gemaakt met de volgende opdracht:

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Als herinnering is deze syntaxis ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Laten we eens kijken hoe een mogelijke clusterindeling eruit zou kunnen zien:

<center>

![Cluster in balans met zowel standaard- als aangepaste statistieken][Image2]
</center>

Enkele dingen die vermeldenswaard zijn:

* Secundaire replica's binnen een partitie kunnen elk hun eigen lading hebben
* Over het algemeen zien de statistieken er evenwichtig uit. Voor Geheugen is de verhouding tussen de maximale en minimale belasting 1,75 (het knooppunt met de meeste belasting is N3, het minste n2 en 28/16 = 1,75).

Er zijn een aantal dingen die we nog moeten uitleggen:

* Wat bepaalde of een verhouding van 1,75 redelijk was of niet? Hoe weet de Cluster Resource Manager of dat goed genoeg is of dat er meer werk te doen is?
* Wanneer gebeurt balanceren?
* Wat betekent het dat het geheugen werd gewogen "Hoog"?

## <a name="metric-weights"></a>Metrische gewichten
Het is belangrijk om dezelfde statistieken bij te houden voor verschillende services. Die globale weergave is wat de Cluster Resource Manager in staat stelt om het verbruik in het cluster bij te houden, het verbruik tussen knooppunten in evenwicht te brengen en ervoor te zorgen dat knooppunten niet over de capaciteit gaan. Services kunnen echter verschillende opvattingen hebben over het belang van dezelfde statistiek. Ook in een cluster met veel statistieken en veel services, kunnen perfect uitgebalanceerde oplossingen niet bestaan voor alle statistieken. Hoe moet de Cluster Resource Manager met deze situaties omgaan?

Met metrische gewichten kan clusterresourcebeheer bepalen hoe het cluster in evenwicht kan worden gebracht wanneer er geen perfect antwoord is. Metmetrische gewichten stellen de Cluster Resource Manager ook in staat om specifieke services anders in evenwicht te brengen. Statistieken kunnen vier verschillende gewichtsniveaus hebben: Nul, Laag, Gemiddeld en Hoog. Een statistiek met een gewicht van Nul draagt niets bij als je bedenkt of dingen in balans zijn of niet. De belasting draagt echter nog steeds bij aan het capaciteitsbeheer. Statistieken met nulgewicht zijn nog steeds nuttig en worden vaak gebruikt als onderdeel van servicegedrag en prestatiebewaking. [In dit artikel](service-fabric-diagnostics-event-generation-infra.md) vindt u meer informatie over het gebruik van statistieken voor het bewaken en diagnosticeren van uw services. 

De werkelijke impact van verschillende metrische gewichten in het cluster is dat de Cluster Resource Manager verschillende oplossingen genereert. Metrische gewichten vertellen de Cluster Resource Manager dat bepaalde statistieken belangrijker zijn dan andere. Als er geen perfecte oplossing is, kan clusterresourcemanager de voorkeur geven aan oplossingen die de hoger gewogen statistieken beter in evenwicht brengen. Als een service een bepaalde statistiek onbelangrijk vindt, kan het gebruik van die statistiek onevenwichtig zijn. Hierdoor kan een andere service een gelijkmatige verdeling van een metrische die belangrijk is voor het te krijgen.

Laten we eens kijken naar een voorbeeld van enkele belastingrapporten en hoe verschillende metrische gewichten resulteren in verschillende toewijzingen in het cluster. In dit voorbeeld zien we dat het schakelen van het relatieve gewicht van de statistieken ervoor zorgt dat clusterresourcebeheer verschillende regelingen voor services maakt.

<center>

![Voorbeeld van metrische gewicht en de impact ervan op balanceringsoplossingen][Image3]
</center>

In dit voorbeeld zijn er vier verschillende services, die allemaal verschillende waarden rapporteren voor twee verschillende statistieken, MetricA en MetricB. In één geval definiëren alle services MetricA is de belangrijkste (Gewicht = Hoog) en MetricB als onbelangrijk (Gewicht = Laag). Als gevolg hiervan zien we dat de Cluster Resource Manager de services zo plaatst dat MetricA beter in balans is dan MetricB. "Beter uitgebalanceerd" betekent dat MetricA een lagere standaarddeviatie heeft dan MetricB. In het tweede geval keren we de metrische gewichten om. Als gevolg hiervan wisselt clusterresourcebeheer services A en B om een toewijzing te vinden waarbij MetricB beter in balans is dan MetricA.

> [!NOTE]
> Metrische gewichten bepalen hoe de clusterresourcemanager moet balanceren, maar niet wanneer er moet worden gebalanceerd. Voor meer informatie over balanceren, bekijk [dit artikel](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Globale metrische gewichten
Stel dat ServiceA MetricA definieert als gewicht Hoog en ServiceB stelt het gewicht voor MetricA in op Laag of Nul. Wat is het werkelijke gewicht dat uiteindelijk wennen?

Er zijn meerdere gewichten die worden bijgehouden voor elke statistiek. Het eerste gewicht is het gewicht dat is gedefinieerd voor de statistiek wanneer de service wordt gemaakt. Het andere gewicht is een globaal gewicht, dat automatisch wordt berekend. De Cluster Resource Manager gebruikt beide gewichten bij het scoren van oplossingen. Rekening houden met beide gewichten is belangrijk. Hierdoor kan de Cluster Resource Manager elke service in evenwicht brengen volgens zijn eigen prioriteiten en er ook voor zorgen dat het cluster als geheel correct wordt toegewezen.

Wat zou er gebeuren als de Cluster Resource Manager niet de zorg over zowel de wereldwijde en lokale balans? Nou, het is gemakkelijk om oplossingen te bouwen die wereldwijd in balans zijn, maar die resulteren in een slechte resourcebalans voor individuele services. In het volgende voorbeeld bekijken we een service die is geconfigureerd met alleen de standaardstatistieken en zien wat er gebeurt als alleen globaal saldo wordt overwogen:

<center>

![De impact van een globale only-oplossing][Image4]
</center>

In het bovenste voorbeeld dat alleen gebaseerd is op globaal evenwicht, is het cluster als geheel inderdaad in evenwicht. Alle knooppunten hebben dezelfde telling van voorverkiezingen en hetzelfde aantal totale replica's. Als je echter kijkt naar de werkelijke impact van deze toewijzing is het niet zo goed: het verlies van een knooppunt heeft onevenredig veel invloed op een bepaalde werklast, omdat het al zijn voorverkiezingen wegneemt. Als het eerste knooppunt bijvoorbeeld niet voldoet aan de drie voorverkiezingen voor de drie verschillende partities van de Circle-service, gaat deze allemaal verloren. Omgekeerd verliezen de services Driehoek en Zeshoek hun partities een replica. Dit veroorzaakt geen verstoring, anders dan het moeten herstellen van de down replica.

In het onderste voorbeeld heeft clusterresourcebeheer de replica's gedistribueerd op basis van zowel de globale als de balans per service. Bij het berekenen van de score van de oplossing geeft het grootste deel van het gewicht aan de globale oplossing, en een (configureerbaar) gedeelte aan individuele diensten. Het globale saldo voor een statistiek wordt berekend op basis van het gemiddelde van de metrische gewichten van elke service. Elke service wordt afgewogen volgens zijn eigen gedefinieerde metrische gewichten. Dit zorgt ervoor dat de diensten in balans zijn binnen zichzelf volgens hun eigen behoeften. Als hetzelfde eerste knooppunt mislukt, wordt de fout dan ook verdeeld over alle partities van alle services. De impact op elk is hetzelfde.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het configureren van services, [Meer informatie over het configureren van Services](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Het definiëren van Defragmentatie-statistieken is een manier om de belasting op knooppunten te consolideren in plaats van deze uit te spreiden. Raadpleeg [dit artikel](service-fabric-cluster-resource-manager-defragmentation-metrics.md) voor meer informatie over het configureren van defragmentatie
- Als u wilt weten hoe de clusterresourcemanager de belasting in het cluster beheert en in evenwicht brengt, raadpleegt u het artikel over [het balanceren van de belasting](service-fabric-cluster-resource-manager-balancing.md)
- Begin vanaf het begin en [krijg een inleiding tot de Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Bewegingskosten is een manier om aan de Cluster Resource Manager te signaleren dat bepaalde services duurder zijn om te verplaatsen dan andere. Voor meer informatie over bewegingskosten verwijzen wij u naar [dit artikel](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
