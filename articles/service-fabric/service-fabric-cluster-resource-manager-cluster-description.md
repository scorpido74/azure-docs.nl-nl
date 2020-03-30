---
title: Een cluster beschrijven met Clusterresourcebeheer
description: Beschrijf een cluster servicestructuur door foutdomeinen, upgradedomeinen, knooppunteigenschappen en knooppuntcapaciteit voor Clusterresourcebeheer op te geven.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7142e3f9aaa25e7ba327194c04ad6a9b5f4e3ad1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258770"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Een cluster van servicefabric beschrijven met Clusterresourcebeheer
De functie Clusterresourcebeheer van Azure Service Fabric biedt verschillende mechanismen voor het beschrijven van een cluster:

* Foutdomeinen
* Domeinen upgraden
* Knooppunteigenschappen
* Knooppuntcapaciteiten

Tijdens runtime gebruikt Cluster Resource Manager deze informatie om een hoge beschikbaarheid van de services in het cluster te garanderen. Terwijl het handhaven van deze belangrijke regels, het probeert ook om het verbruik van resources binnen het cluster te optimaliseren.

## <a name="fault-domains"></a>Foutdomeinen
Een foutdomein is elk gebied van gecoördineerde storing. Eén machine is een foutdomein. Het kan mislukken op zijn eigen om verschillende redenen, van stroomuitval tot storingen rijden tot slechte NIC firmware. 

Machines die op dezelfde Ethernet-switch zijn aangesloten, bevinden zich in hetzelfde storingsdomein. Net als machines die één energiebron delen of op één locatie. 

Omdat het normaal is dat hardwarefouten elkaar overlappen, zijn foutdomeinen inherent hiërarchisch. Ze worden weergegeven als URI's in Service Fabric.

Het is belangrijk dat foutdomeinen correct zijn ingesteld, omdat Service Fabric deze informatie gebruikt om services veilig te plaatsen. Service Fabric wil geen services zodanig plaatsen dat het verlies van een foutdomein (veroorzaakt door het uitvallen van een onderdeel) ervoor zorgt dat een service naar beneden gaat. 

In de Azure-omgeving gebruikt Service Fabric de foutdomeingegevens die door de omgeving worden verstrekt om de knooppunten in het cluster namens u correct te configureren. Voor zelfstandige exemplaren van Service Fabric worden foutdomeinen gedefinieerd op het moment dat het cluster is ingesteld. 

> [!WARNING]
> Het is belangrijk dat de informatie over het foutdomein die aan Service Fabric wordt verstrekt, juist is. Stel dat de knooppunten van uw Service Fabric-cluster worden uitgevoerd in 10 virtuele machines, die worden uitgevoerd op 5 fysieke hosts. In dit geval, ook al zijn er 10 virtuele machines, zijn er slechts 5 verschillende (top level) foutdomeinen. Het delen van dezelfde fysieke host zorgt ervoor dat VM's hetzelfde hoofdfoutdomein delen, omdat de VM's een gecoördineerde fout ervaren als hun fysieke host mislukt.  
>
> Service Fabric verwacht dat het foutdomein van een knooppunt niet verandert. Andere mechanismen om een hoge beschikbaarheid van de VM's te garanderen, zoals [HA-VM's,](https://technet.microsoft.com/library/cc967323.aspx)kunnen conflicten veroorzaken met Service Fabric. Deze mechanismen maken gebruik van transparante migratie van VM's van de ene host naar de andere. Ze configureren of melden de loopcode in de VM niet opnieuw. Als zodanig worden ze *niet ondersteund* als omgevingen voor het uitvoeren van Service Fabric-clusters. 
>
> Service Fabric moet de enige technologie met hoge beschikbaarheid zijn die wordt gebruikt. Mechanismen zoals live VM-migratie en SAN's zijn niet nodig. Als deze mechanismen worden gebruikt in combinatie met Service Fabric, verminderen ze _de_ beschikbaarheid en betrouwbaarheid van toepassingen. De reden hiervoor is dat ze extra complexiteit introduceren, gecentraliseerde bronnen van falen toevoegen en betrouwbaarheids- en beschikbaarheidsstrategieën gebruiken die in strijd zijn met die in Service Fabric. 
>
>

In de volgende afbeelding kleuren we alle entiteiten die bijdragen aan foutdomeinen en vermelden we alle verschillende foutdomeinen die resulteren. In dit voorbeeld hebben we datacenters ('DC'), racks ('R') en blades ('B'). Als elk blad meer dan één virtuele machine bevat, kan er een andere laag in de hiërarchie van het foutdomein zijn.

<center>

![Knooppunten georganiseerd via foutdomeinen][Image1]
</center>

Tijdens runtime houdt Service Fabric Cluster Resource Manager rekening met de foutdomeinen in het cluster en plannen indelingen. De stateful replica's of stateless exemplaren voor een service worden gedistribueerd, zodat ze zich in afzonderlijke foutdomeinen bevinden. Het distribueren van de service over foutdomeinen zorgt ervoor dat de beschikbaarheid van de service niet in het gedrang komt wanneer een foutdomein op elk niveau van de hiërarchie uitvalt.

Het maakt clusterresourcebeheer niet uit hoeveel lagen er in de hiërarchie van het foutdomein zijn. Het probeert ervoor te zorgen dat het verlies van een deel van de hiërarchie geen invloed heeft op services die erin worden uitgevoerd. 

Het is het beste als hetzelfde aantal knooppunten zich op elk diepteniveau in de hiërarchie van het foutdomein bevindt. Als de 'structuur' van foutdomeinen niet in balans is in uw cluster, is het moeilijker voor Cluster Resource Manager om de beste toewijzing van services te achterhalen. Onevenwichtige foutdomeinlay-outs betekenen dat het verlies van sommige domeinen de beschikbaarheid van services meer beïnvloedt dan andere domeinen. Als gevolg hiervan wordt Cluster Resource Manager verscheurd tussen twee doelen: 

* Het wil de machines te gebruiken in dat "zware" domein door het plaatsen van diensten op hen. 
* Het wil services in andere domeinen plaatsen, zodat het verlies van een domein geen problemen veroorzaakt. 

Hoe zien onevenwichtige domeinen eruit? In het volgende diagram ziet u twee verschillende clusterindelingen. In het eerste voorbeeld worden de knooppunten gelijkmatig verdeeld over de foutdomeinen. In het tweede voorbeeld heeft één foutdomein veel meer knooppunten dan de andere foutdomeinen. 

<center>

![Twee verschillende clusterindelingen][Image2]
</center>

In Azure wordt de keuze van welk foutdomein een knooppunt bevat, voor u beheerd. Maar afhankelijk van het aantal knooppunten dat u indient, u nog steeds eindigen met foutdomeinen die meer knooppunten bevatten dan in andere. 

Stel dat u vijf foutdomeinen in het cluster hebt, maar zeven knooppunten indient voor een knooppunttype **(NodeType).** In dit geval eindigen de eerste twee foutdomeinen met meer knooppunten. Als u meer **NodeType-exemplaren** blijft implementeren met slechts een paar exemplaren, wordt het probleem nog groter. Daarom raden we aan dat het aantal knooppunten in elk knooppunttype een veelvoud is van het aantal foutdomeinen.

## <a name="upgrade-domains"></a>Domeinen upgraden
Upgradedomeinen zijn een andere functie waarmee Service Fabric Cluster Resource Manager inzicht krijgt in de lay-out van het cluster. Upgradedomeinen definiëren sets knooppunten die tegelijkertijd worden geüpgraded. Upgradedomeinen helpen Cluster Resource Manager beheerbewerkingen zoals upgrades te begrijpen en te orkestreren.

Upgrade domeinen zijn een partij als fout domeinen, maar met een paar belangrijke verschillen. Ten eerste definiëren gebieden met gecoördineerde hardwarefouten foutdomeinen. Upgradedomeinen daarentegen worden gedefinieerd door beleid. U krijgt om te beslissen hoeveel je wilt, in plaats van te laten de omgeving dicteren het nummer. U evenveel upgradedomeinen hebben als knooppunten. Een ander verschil tussen foutdomeinen en upgradedomeinen is dat upgradedomeinen niet hiërarchisch zijn. In plaats daarvan zijn ze meer als een eenvoudige tag. 

In het volgende diagram ziet u drie upgradedomeinen die zijn gestreept over drie foutdomeinen. Het toont ook een mogelijke plaatsing voor drie verschillende replica's van een stateful service, waar elk eindigt in verschillende fout en upgrade domeinen. Deze plaatsing maakt het verlies van een fout domein, terwijl in het midden van een service-upgrade en nog steeds een kopie van de code en gegevens.  

<center>

![Plaatsing met fout- en upgradedomeinen][Image3]
</center>

Er zijn voors en tegens aan het hebben van grote aantallen verbeteringsdomeinen. Meer upgradedomeinen betekenen dat elke stap van de upgrade gedetailleerder is en van invloed is op een kleiner aantal knooppunten of services. Minder diensten hoeven te bewegen op een moment, de invoering van minder churn in het systeem. Dit heeft de neiging om de betrouwbaarheid te verbeteren, omdat minder van de service wordt beïnvloed door een probleem geïntroduceerd tijdens de upgrade. Meer upgradedomeinen betekenen ook dat u minder beschikbare buffer op andere knooppunten nodig hebt om de impact van de upgrade te verwerken. 

Als u bijvoorbeeld vijf upgradedomeinen hebt, verwerken de knooppunten in elk van deze knooppunten ongeveer 20 procent van uw verkeer. Als u dat upgradedomein moet uitschakelen voor een upgrade, moet die belasting meestal ergens heen. Omdat u nog vier upgradedomeinen hebt, moet elk ruimte hebben voor ongeveer 5 procent van het totale verkeer. Meer upgradedomeinen betekenen dat u minder buffer nodig hebt op de knooppunten in het cluster. 

Overweeg of u in plaats daarvan 10 upgradedomeinen had. In dat geval zou elk upgradedomein slechts ongeveer 10 procent van het totale verkeer verwerken. Wanneer een upgrade door het cluster wordt uitgevoerd, moet elk domein ruimte hebben voor slechts ongeveer 1,1 procent van het totale verkeer. Met meer upgradedomeinen u uw knooppunten over het algemeen bij een hoger gebruik uitvoeren, omdat u minder gereserveerde capaciteit nodig hebt. Hetzelfde geldt voor foutdomeinen.  

Het nadeel van het hebben van veel upgrade domeinen is dat upgrades hebben de neiging om langer te duren. Service Fabric wacht een korte periode nadat een upgradedomein is voltooid en voert controles uit voordat u begint met het upgraden van het volgende domein. Deze vertragingen maken het mogelijk om problemen op te sporen die door de upgrade zijn geïntroduceerd voordat de upgrade wordt uitgevoerd. De afweging is aanvaardbaar omdat het voorkomt dat slechte veranderingen te veel van de service tegelijk beïnvloeden.

De aanwezigheid van te weinig upgrade domeinen heeft veel negatieve bijwerkingen. Hoewel elk upgradedomein is uitgeschakeld en wordt bijgewerkt, is een groot deel van uw totale capaciteit niet beschikbaar. Als u bijvoorbeeld slechts drie upgradedomeinen hebt, haalt u ongeveer een derde van uw totale service- of clustercapaciteit tegelijk weg. Het is niet wenselijk om zoveel van uw service in één keer te laten werken, omdat u voldoende capaciteit in de rest van uw cluster nodig hebt om de werkbelasting te verwerken. Het handhaven van die buffer betekent dat tijdens de normale werking, die knooppunten zijn minder geladen dan ze anders zou zijn. Dit verhoogt de kosten van het uitvoeren van uw service.

Er is geen echte limiet aan het totale aantal fout- of upgradedomeinen in een omgeving of beperkingen voor de manier waarop ze elkaar overlappen. Maar er zijn gemeenschappelijke patronen:

- Foutdomeinen en upgradedomeinen toegewezen 1:1
- Eén upgradedomein per knooppunt (fysieke of virtuele os-instantie)
- Een "gestreept" of "matrix" model waarbij de foutdomeinen en upgradedomeinen een matrix vormen met machines die meestal de diagonalen afwerken

<center>

![Indelingen van fout- en upgradedomeinen][Image4]
</center>

Er is geen beste antwoord voor welke lay-out te kiezen. Elk heeft voor- en nadelen. Het 1FD:1UD-model is bijvoorbeeld eenvoudig in te stellen. Het model van één upgradedomein per knooppuntmodel lijkt het meest op wat mensen gewend zijn. Tijdens upgrades wordt elk knooppunt onafhankelijk bijgewerkt. Dit is vergelijkbaar met hoe kleine sets van machines handmatig werden opgewaardeerd in het verleden.

Het meest voorkomende model is de FD/UD-matrix, waarbij de foutdomeinen en upgradedomeinen een tabel vormen en knooppunten worden geplaatst vanaf de diagonaal. Dit is het model dat standaard wordt gebruikt in clusters van servicefabric in Azure. Voor clusters met veel knooppunten, alles eindigt op zoek als een dichte matrix patroon.

> [!NOTE]
> Servicefabricclusters die in Azure worden gehost, ondersteunen het wijzigen van de standaardstrategie niet. Alleen standalone clusters bieden die aanpassing.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Beperkingen van het domein en upgrades en het resulterende gedrag
### <a name="default-approach"></a>Standaardbenadering
Cluster Resource Manager houdt services standaard in balans tussen fout- en upgradedomeinen. Dit is gemodelleerd als een [beperking](service-fabric-cluster-resource-manager-management-integration.md). De beperking voor fout- en upgradedomeinen luidt: "Voor een bepaalde servicepartitie mag er nooit een groter verschil zijn dan één verschil in het aantal serviceobjecten (stateless service-exemplaren of stateful servicereplica's) tussen twee domeinen op dezelfde het niveau van de hiërarchie."

Laten we zeggen dat deze beperking een "maximaal verschil" garantie biedt. De beperking voor fout- en upgradedomeinen voorkomt bepaalde verplaatsingen of afspraken die in strijd zijn met de regel.

Stel dat we een cluster hebben met zes knooppunten, geconfigureerd met vijf foutdomeinen en vijf upgradedomeinen.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **Ud4** | | | | |N5 |

Laten we nu zeggen dat we een service maken met een **TargetReplicaSetSize** -waarde (of, voor een stateless service, **InstanceCount),** van vijf. De replica's landen op n1-N5. In feite wordt N6 nooit gebruikt, ongeacht hoeveel diensten als deze u maakt. Maar waarom? Laten we eens kijken naar het verschil tussen de huidige lay-out en wat er zou gebeuren als N6 wordt gekozen.

Hier is de lay-out die we hebben en het totale aantal replica's per fout en upgrade domein:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotaal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **Ud4** | | | | |R5 |1 |
| **FDTotaal** |1 |1 |1 |1 |1 |- |

Deze lay-out is gebalanceerd in termen van knooppunten per foutdomein en upgradedomein. Het is ook evenwichtig in termen van het aantal replica's per fout en upgrade domein. Elk domein heeft hetzelfde aantal knooppunten en hetzelfde aantal replica's.

Laten we eens kijken wat er zou gebeuren als we N6 hadden gebruikt in plaats van N2. Hoe zouden de replica's dan worden verspreid?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotaal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **Ud4** | | | | |R4 |1 |
| **FDTotaal** |2 |0 |1 |1 |1 |- |

Deze lay-out is in strijd met onze definitie van de "maximale verschil" garantie voor de beperking van het foutdomein. FD0 heeft twee replica's, terwijl FD1 nul heeft. Het verschil tussen FD0 en FD1 is een totaal van twee, wat groter is dan het maximale verschil van één. Omdat de beperking wordt geschonden, staat Cluster Resource Manager deze indeling niet toe. Op dezelfde manier, als we kozen N2 en N6 (in plaats van N1 en N2), zouden we krijgen:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotaal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **Ud4** | | | | |R4 |1 |
| **FDTotaal** |1 |1 |1 |1 |1 |- |

Deze lay-out is evenwichtig in termen van foutdomeinen. Maar nu is het in strijd met de upgrade domein beperking, omdat UD0 heeft nul replica's en UD1 heeft twee. Deze indeling is ook ongeldig en wordt niet gekozen door Cluster Resource Manager.

Deze benadering van de verdeling van stateful replica's of stateless exemplaren biedt de best mogelijke fouttolerantie. Als één domein uitvalt, gaat het minimale aantal replica's/exemplaren verloren. 

Aan de andere kant kan deze aanpak te streng zijn en het cluster niet toestaan om alle resources te gebruiken. Voor bepaalde clusterconfiguraties kunnen bepaalde knooppunten niet worden gebruikt. Hierdoor kan Service Fabric uw services niet plaatsen, wat resulteert in waarschuwingsberichten. In het vorige voorbeeld kunnen sommige clusterknooppunten niet worden gebruikt (N6 in het voorbeeld). Zelfs als u knooppunten aan dat cluster (N7-N10) hebt toegevoegd, worden replica's/instanties alleen op N1-N5 geplaatst vanwege beperkingen op fout- en upgradedomeinen. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **Ud4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>Alternatieve aanpak

Clusterresourcebeheer ondersteunt een andere versie van de beperking voor fout- en upgradedomeinen. Het maakt plaatsing mogelijk en garandeert toch een minimumniveau van veiligheid. De alternatieve beperking kan als volgt worden vermeld: "Voor een bepaalde servicepartitie moet replicadistributie over domeinen ervoor zorgen dat de partitie geen quorumverlies lijdt." Stel dat deze beperking een "quorumveilige" garantie biedt. 

> [!NOTE]
> Voor een stateful service definiëren we *quorumverlies* in een situatie waarin een meerderheid van de partitiereplica's tegelijkertijd is uitgeschakeld. Als **TargetReplicaSetSize** bijvoorbeeld vijf is, vertegenwoordigt een set van drie replica's quorum. Als **TargetReplicaSetSize** zes is, zijn er vier replica's nodig voor het quorum. In beide gevallen kunnen niet meer dan twee replica's tegelijkertijd worden afgebroken als de partitie normaal wil blijven functioneren. 
>
> Voor een staatloze dienst bestaat er niet zoiets als *quorumverlies.* Staatloze diensten blijven normaal functioneren, zelfs als de meeste exemplaren tegelijkertijd naar beneden gaan. Dus, we zullen ons richten op stateful diensten in de rest van dit artikel.
>

Laten we teruggaan naar het vorige voorbeeld. Met de "quorumveilige" versie van de beperking zijn alle drie de lay-outs geldig. Zelfs als FD0 is mislukt in de tweede lay-out of UD1 is mislukt in de derde lay-out, heeft de partitie nog steeds quorum. (Een meerderheid van de replica's zou nog steeds up.) Met deze versie van de beperking, N6 kan bijna altijd worden gebruikt.

De "quorumveilige" benadering biedt meer flexibiliteit dan de "maximale verschil"-benadering. De reden is dat het gemakkelijker is om replica-distributies te vinden die geldig zijn in bijna elke clustertopologie. Deze aanpak kan echter niet garanderen dat de beste fouttolerantie kenmerken, omdat sommige mislukkingen zijn erger dan anderen. 

In het ergste geval kan een meerderheid van de replica's verloren gaan met het mislukken van één domein en een extra replica. In plaats van dat er bijvoorbeeld drie fouten nodig zijn om het quorum met vijf replica's of instanties te verliezen, u nu een meerderheid verliezen met slechts twee fouten. 

### <a name="adaptive-approach"></a>Adaptieve aanpak
Omdat beide benaderingen sterke en zwakke punten hebben, hebben we een adaptieve aanpak geïntroduceerd die deze twee strategieën combineert.

> [!NOTE]
> Dit is het standaardgedrag dat begint met Service Fabric-versie 6.2. 
> 
> De adaptieve benadering gebruikt standaard de logica "maximaal verschil" en schakelt alleen over naar de "quorumveilige" logica wanneer dat nodig is. Cluster Resource Manager zoekt automatisch uit welke strategie nodig is door te kijken naar hoe het cluster en de services worden geconfigureerd.
> 
> Clusterresourcebeheer moet de logica 'op quorum gebaseerd' gebruiken voor een service die beide voorwaarden waar hebben:
>
> * **TargetReplicaSetSize** voor de service is gelijkmatig deelbaar door het aantal foutdomeinen en het aantal upgradedomeinen.
> * Het aantal knooppunten is kleiner dan of gelijk aan het aantal foutdomeinen vermenigvuldigd met het aantal upgradedomeinen.
>
> Houd er rekening mee dat Cluster Resource Manager deze aanpak zal gebruiken voor zowel stateloze als stateful services, ook al is quorumverlies niet relevant voor stateless services.

Laten we teruggaan naar het vorige voorbeeld en aannemen dat een cluster nu acht knooppunten heeft. Het cluster is nog steeds geconfigureerd met vijf foutdomeinen en vijf upgradedomeinen en de **TargetReplicaSetSize-waarde** van een service die op dat cluster wordt gehost, blijft vijf. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **Ud4** | | | | |N5 |

Omdat aan alle vereiste voorwaarden is voldaan, gebruikt Cluster Resource Manager de logica 'quorumgebaseerd' bij het distribueren van de service. Dit maakt het gebruik van N6-N8 mogelijk. Een mogelijke servicedistributie in dit geval ziet er als volgt uit:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotaal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **Ud4** | | | | |R5 |1 |
| **FDTotaal** |2 |1 |1 |0 |1 |- |

Als de **targetreplicasetgroottewaarde** van uw service is teruggebracht tot vier (bijvoorbeeld), merkt Cluster Resource Manager die wijziging. Het wordt hervat met behulp van de logica "maximaal verschil", omdat **TargetReplicaSetSize** niet meer te delen is door het aantal foutdomeinen en upgradedomeinen. Als gevolg hiervan zullen bepaalde replicabewegingen optreden om de resterende vier replica's op knooppunten N1-N5 te distribueren. Op die manier wordt de "maximale verschil" versie van het foutdomein en de domeinlogica van het upgradedomein niet geschonden. 

Als de waarde **TargetReplicaSetSize** in de vorige lay-out vijf is en N1 uit het cluster wordt verwijderd, wordt het aantal upgradedomeinen gelijk aan vier. Nogmaals, Cluster Resource Manager begint met het gebruik van "maximale verschil" logica, omdat het aantal upgrade domeinen niet gelijkmatig verdelen van de service **TargetReplicaSetSize** waarde meer. Als gevolg hiervan moet replica R1, wanneer het opnieuw wordt gebouwd, op N4 landen, zodat de beperking voor het fout- en upgradedomein niet wordt geschonden.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotaal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N.v.t. |N.v.t. |N.v.t. |N.v.t. |N.v.t. |N.v.t. |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **Ud4** | | | | |R5 |1 |
| **FDTotaal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Fout- en upgradedomeinen configureren
In Azure-gehoste Service Fabric-implementaties worden foutdomeinen en upgradedomeinen automatisch gedefinieerd. Service Fabric pikt en gebruikt de omgevingsinformatie van Azure.

Als u uw eigen cluster maakt (of een bepaalde topologie in ontwikkeling wilt uitvoeren), u het foutdomein zelf verstrekken en domeininformatie upgraden. In dit voorbeeld definiëren we een lokaal ontwikkelingscluster met negen nodes dat drie datacenters omvat (elk met drie racks). Dit cluster heeft ook drie upgradedomeinen die zijn verwijderd in deze drie datacenters. Hier is een voorbeeld van de configuratie in ClusterManifest.xml:

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one box/one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

In dit voorbeeld wordt ClusterConfig.json gebruikt voor zelfstandige implementaties:

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Wanneer u clusters definieert via Azure Resource Manager, wijst Azure foutdomeinen toe en upgradedomeinen. De definitie van uw knooppunttypen en virtuele machineschaalsets in uw Azure Resource Manager-sjabloon bevat dus geen informatie over het foutdomein of het upgradedomein.
>

## <a name="node-properties-and-placement-constraints"></a>Eigenschappen en plaatsingsbeperkingen voor knooppunten
Soms (in feite, de meeste van de tijd) wilt u ervoor zorgen dat bepaalde workloads alleen worden uitgevoerd op bepaalde soorten knooppunten in het cluster. Voor sommige workloads zijn bijvoorbeeld GPU's of SSD's nodig en andere niet. 

Een goed voorbeeld van het targeten van hardware op bepaalde workloads is bijna elke n-tier architectuur. Bepaalde machines dienen als front-end of API-serving kant van de toepassing en worden blootgesteld aan de clients of het internet. Verschillende machines, vaak met verschillende hardwarebronnen, verwerken het werk van de reken- of opslaglagen. Deze zijn meestal _niet_ direct blootgesteld aan klanten of het internet. 

Service Fabric verwacht dat in sommige gevallen bepaalde workloads op bepaalde hardwareconfiguraties moeten worden uitgevoerd. Bijvoorbeeld:

* Een bestaande n-tier applicatie is "opgeheven en verschoven" naar een Service Fabric omgeving.
* Een werkbelasting moet worden uitgevoerd op specifieke hardware om redenen van prestaties, schaal of beveiligingsisolatie.
* Een werkbelasting moet worden geïsoleerd van andere workloads om redenen van beleid of resourceverbruik.

Om dit soort configuraties te ondersteunen, bevat Service Fabric tags die u toepassen op knooppunten. Deze tags worden *knooppunteigenschappen*genoemd. *Plaatsingsbeperkingen* zijn de instructies die zijn gekoppeld aan afzonderlijke services die u selecteert voor een of meer knooppunteigenschappen. Plaatsingsbeperkingen bepalen waar services moeten worden uitgevoerd. De reeks beperkingen is uitbreidbaar. Elk sleutel/waardepaar kan werken. 

<center>

![Verschillende workloads voor een clusterindeling][Image5]
</center>

### <a name="built-in-node-properties"></a>Ingebouwde knooppunteigenschappen
Service Fabric definieert een aantal standaardknooppunteigenschappen die automatisch kunnen worden gebruikt, zodat u ze niet hoeft te definiëren. De standaardeigenschappen die bij elk knooppunt zijn gedefinieerd, zijn **NodeType** en **NodeName**. 

U bijvoorbeeld een plaatsingsbeperking schrijven als `"(NodeType == NodeType03)"`. **NodeType** is een veelgebruikte eigenschap. Het is handig omdat het overeenkomt met 1:1 met een type van een machine. Elk type machine komt overeen met een type werkbelasting in een traditionele n-tier toepassing.

<center>

![Plaatsingsbeperkingen en knooppunteigenschappen][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>Plaatsingsbeperkingen en syntaxis van de eigenschap knooppunt 
De waarde die is opgegeven in de eigenschap knooppunt kan een tekenreeks, Booleaan of lang ondertekend zijn. De instructie bij de service wordt een *plaatsingsbeperking* genoemd omdat deze beperkt waar de service in het cluster kan worden uitgevoerd. De beperking kan elke Booleaanse instructie zijn die werkt op de knooppunteigenschappen in het cluster. De geldige selectors in deze Booleaanse uitspraken zijn:

* Voorwaardelijke controles voor het maken van bepaalde verklaringen:

  | Verklaring | Syntaxis |
  | --- |:---:|
  | "Gelijk aan" | "==" |
  | "Niet gelijk aan" | "!=" |
  | "groter dan" | ">" |
  | "groter dan of gelijk aan" | ">=" |
  | "minder dan" | "<" |
  | "minder dan of gelijk aan" | "<=" |

* Booleaanse instructies voor groepering en logische bewerkingen:

  | Verklaring | Syntaxis |
  | --- |:---:|
  | "en" | "&&" |
  | "of" | "&#124;&#124;" |
  | "Niet" | "!" |
  | "groep als enkele verklaring" | "()" |

Hier volgen enkele voorbeelden van basisdrukverklaringen:

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Alleen knooppunten waarvan de algemene plaatsingsbeperkingsinstructie wordt geëvalueerd op 'True' kunnen de service erop plaatsen. Knooppunten die geen eigenschap hebben gedefinieerd, komen niet overeen met een plaatsingsbeperking die de eigenschap bevat.

Stel dat de volgende knooppunteigenschappen zijn gedefinieerd voor een knooppunttype in ClusterManifest.xml:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

In het volgende voorbeeld worden knooppunteigenschappen weergegeven die zijn gedefinieerd via ClusterConfig.json voor zelfstandige implementaties of Template.json voor azure-gehoste clusters. 

> [!NOTE]
> In de sjabloon Azure Resource Manager wordt het knooppunttype meestal geparameteriseerd. Het zou `"[parameters('vmNodeType1Name')]"` eruit zien in plaats van NodeType01.
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

U *serviceplaatsingsbeperkingen* voor een service als volgt maken:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// Add other required ServiceDescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Als alle knooppunten van NodeType01 geldig zijn, u `"(NodeType == NodeType01)"`ook dat knooppunttype selecteren met de beperking.

De plaatsingsbeperkingen van een service kunnen dynamisch worden bijgewerkt tijdens runtime. Als dat nodig is, u een service verplaatsen in het cluster, vereisten toevoegen en verwijderen, enzovoort. Service Fabric zorgt ervoor dat de service up- en beschikbaar blijft, zelfs wanneer dit soort wijzigingen worden aangebracht.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Plaatsingsbeperkingen worden opgegeven voor elke benoemde serviceinstantie. Updates nemen altijd de plaats in van (overschrijven) wat eerder was opgegeven.

De clusterdefinitie definieert de eigenschappen op een knooppunt. Als u de eigenschappen van een knooppunt wijzigt, moet u de clusterconfiguratie bijwerken. Voor het upgraden van de eigenschappen van een knooppunt moet elk getroffen knooppunt opnieuw worden gestart om de nieuwe eigenschappen te rapporteren. Service Fabric beheert deze rolling upgrades.

## <a name="describing-and-managing-cluster-resources"></a>Clusterbronnen beschrijven en beheren
Een van de belangrijkste taken van een orchestrator is het beheren van het verbruik van resources in het cluster. Het beheren van clusterbronnen kan een paar verschillende dingen betekenen. 

Ten eerste is er ervoor te zorgen dat machines niet overbelast zijn. Dit betekent ervoor zorgen dat machines niet meer services uitvoeren dan ze aankunnen. 

Ten tweede is er balanceren en optimaliseren, die essentieel zijn voor het efficiënt uitvoeren van services. Kosteneffectieve of prestatiegevoelige serviceaanbiedingen kunnen sommige knooppunten niet toestaan om warm te zijn, terwijl andere het koud hebben. Hot nodes leiden tot resource twist en slechte prestaties. Koude knooppunten vertegenwoordigen verspilde resources en hogere kosten. 

Service Fabric vertegenwoordigt resources als *metrics*. Statistieken zijn een logische of fysieke bron die u wilt beschrijven aan Service Fabric. Voorbeelden van statistieken zijn 'WorkQueueDepth' of 'MemoryInMb'. Zie [Resourcegovernance](service-fabric-resource-governance.md)voor informatie over de fysieke resources die Service Fabric op knooppunten kan beheren. Zie [dit artikel](service-fabric-cluster-resource-manager-metrics.md)voor informatie over de standaardstatistieken die door clusterbronbeheer worden gebruikt en hoe aangepaste statistieken kunnen worden geconfigureerd.

Statistieken verschillen van plaatsingsbeperkingen en knooppunteigenschappen. Knooppunteigenschappen zijn statische beschrijvingen van de knooppunten zelf. Statistieken beschrijven resources die knooppunten hebben en die services verbruiken wanneer ze op een knooppunt worden uitgevoerd. Een knooppunt eigenschap kan **worden HasSSD** en kan worden ingesteld op waar of onwaar. De hoeveelheid ruimte die beschikbaar is op die SSD en hoeveel wordt verbruikt door diensten zou een metrische zoals "DriveSpaceInMb." 

Net als bij plaatsingsbeperkingen en knooppunteigenschappen, begrijpt Service Fabric Cluster Resource Manager niet wat de namen van de statistieken betekenen. Metrische namen zijn slechts tekenreeksen. Het is een goede gewoonte om eenheden te declareren als onderdeel van de metrische namen die u maakt wanneer ze dubbelzinnig kunnen zijn.

## <a name="capacity"></a>Capaciteit
Als u alle *resourcebalancing*hebt uitgeschakeld, zorgt Service Fabric Cluster Resource Manager er nog steeds voor dat knooppunt over de capaciteit gaat. Het beheren van capaciteitsoverschrijdingen is mogelijk, tenzij het cluster te vol is of de werkbelasting groter is dan een knooppunt. Capaciteit is een andere *beperking* die Cluster Resource Manager gebruikt om te begrijpen hoeveel van een resource een knooppunt heeft. De resterende capaciteit wordt ook bijgehouden voor het cluster als geheel. 

Zowel de capaciteit als het verbruik op serviceniveau worden uitgedrukt in metrics. De statistiek kan bijvoorbeeld 'ClientConnections' zijn en een knooppunt kan een capaciteit hebben voor 'ClientConnections' van 32.768. Andere knooppunten kunnen andere limieten hebben. Een service die op dat knooppunt wordt uitgevoerd, kan zeggen dat deze momenteel 32.256 van de metrische 'ClientConnections' verbruikt.

Tijdens runtime houdt Cluster Resource Manager de resterende capaciteit in het cluster en op knooppunten bij. Als u de capaciteit wilt bijhouden, trekt Cluster Resource Manager het gebruik van elke service af van de capaciteit van een knooppunt waar de service wordt uitgevoerd. Met deze informatie kan Cluster Resource Manager achterhalen waar replica's moeten worden plaatst of verplaatst, zodat knooppunten niet over de capaciteit gaan.

<center>

![Clusterknooppunten en -capaciteit][Image7]
</center>

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

U capaciteiten zien die zijn gedefinieerd in het clustermanifest. Hier is een voorbeeld voor ClusterManifest.xml:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Hier vindt u een voorbeeld van capaciteiten die zijn gedefinieerd via ClusterConfig.json voor zelfstandige implementaties of Template.json voor azure-gehoste clusters: 

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

De belasting van een service verandert vaak dynamisch. Stel dat de belasting van een replica van 'ClientConnections' is gewijzigd van 1.024 naar 2048. Het knooppunt waarop het liep, had toen een capaciteit van slechts 512 resterende voor die statistiek. Nu is de plaatsing van replica's of instantie ongeldig, omdat er niet genoeg ruimte is op dat knooppunt. Clusterresourcebeheer moet het knooppunt weer onder de capaciteit krijgen. Het vermindert de belasting op het knooppunt dat over capaciteit is door een of meer van de replica's of exemplaren van dat knooppunt naar andere knooppunten te verplaatsen. 

Clusterresourcebeheer probeert de kosten van het verplaatsen van replica's te minimaliseren. U meer te weten komen over [bewegingskosten](service-fabric-cluster-resource-manager-movement-cost.md) en over [het opnieuw in evenwicht brengen van strategieën en regels.](service-fabric-cluster-resource-manager-metrics.md)

## <a name="cluster-capacity"></a>Clustercapaciteit
Hoe voorkomt de clusterbronbeheer van servicestructuur dat het totale cluster te vol is? Met dynamische belasting, er is niet veel dat het kan doen. Services kunnen hun belastingspiek hebben onafhankelijk van acties die Cluster Resource Manager onderneemt. Als gevolg daarvan, uw cluster met veel hoofdruimte vandaag misschien onderpowered als er een piek morgen. 

Besturingselementen in Clusterresourcebeheer helpen problemen te voorkomen. Het eerste wat u doen is voorkomen dat het maken van nieuwe workloads die ervoor zorgen dat het cluster vol raakt.

Stel dat u een stateless service maakt en er een bepaalde belasting aan heeft. De service geeft om de statistiek "DiskSpaceInMb". De service verbruikt vijf eenheden van "DiskSpaceInMb" voor elk exemplaar van de service. U wilt drie exemplaren van de service maken. Dat betekent dat u 15 eenheden van DiskSpaceInMb nodig hebt om aanwezig te zijn in het cluster zodat u zelfs deze service-exemplaren maken.

Clusterresourcebeheer berekent voortdurend de capaciteit en het verbruik van elke statistiek, zodat deze de resterende capaciteit in het cluster kan bepalen. Als er niet genoeg ruimte is, weigert Cluster Resource Manager de aanroep om een service te maken.

Omdat de vereiste is dat er slechts 15 eenheden beschikbaar zijn, u deze ruimte op veel verschillende manieren toewijzen. Er kan bijvoorbeeld één resterende capaciteitseenheid zijn op 15 verschillende knooppunten of drie resterende capaciteitseenheden op vijf verschillende knooppunten. Als Clusterresourcebeheer de zaken kan herschikken zodat er vijf eenheden beschikbaar zijn op drie knooppunten, wordt de service verplaatst. Het herschikken van het cluster is meestal mogelijk, tenzij het cluster bijna vol is of de bestaande services om de een of andere reden niet kunnen worden geconsolideerd.

## <a name="buffered-capacity"></a>Gebufferde capaciteit
Gebufferde capaciteit is een andere functie van Cluster Resource Manager. Het maakt reservering van een deel van de totale knooppuntcapaciteit mogelijk. Deze capaciteitsbuffer wordt alleen gebruikt om services te plaatsen tijdens upgrades en knooppuntfouten. 

Buffercapaciteit wordt globaal per metric opgegeven voor alle knooppunten. De waarde die u kiest voor de gereserveerde capaciteit is een functie van het aantal fout- en upgradedomeinen dat u in het cluster hebt. Meer fout- en upgradedomeinen betekenen dat u een lager getal kiezen voor uw gebufferde capaciteit. Als u meer domeinen hebt, u verwachten dat kleinere hoeveelheden van uw cluster niet beschikbaar zijn tijdens upgrades en fouten. Het opgeven van gebufferde capaciteit heeft alleen zin als u ook de knooppuntcapaciteit voor een statistiek hebt opgegeven.

Hier volgt een voorbeeld van het opgeven van gebufferde capaciteit in ClusterManifest.xml:

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

Hier volgt een voorbeeld van het opgeven van gebufferde capaciteit via ClusterConfig.json voor zelfstandige implementaties of Template.json voor azure-gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

Het maken van nieuwe services mislukt wanneer het cluster geen gebufferde capaciteit meer heeft voor een statistiek. Het voorkomen van het maken van nieuwe services om de buffer te behouden, zorgt ervoor dat upgrades en fouten er niet toe leiden dat knooppunten over capaciteit gaan. Gebufferde capaciteit is optioneel, maar we raden deze aan in elk cluster dat een capaciteit voor een statistiek definieert.

Clusterresourcebeheer legt deze belastingsinformatie bloot. Voor elke statistiek omvat deze informatie: 
- De gebufferde capaciteitsinstellingen.
- De totale capaciteit.
- Het huidige verbruik.
- Of elke statistiek als evenwichtig wordt beschouwd of niet.
- Statistieken over de standaarddeviatie.
- De knooppunten met de meeste en minste belasting.  
  
In de volgende code ziet u een voorbeeld van die uitvoer:

```PowerShell
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>Volgende stappen
* Zie [Cluster Resource Manager-architectuuroverzicht](service-fabric-cluster-resource-manager-architecture.md)voor informatie over de architectuur en informatiestroom binnen Cluster Resource Manager.
* Het definiëren van defragmentatiestatistieken is een manier om de belasting op knooppunten te consolideren in plaats van deze uit te spreiden. Zie [Defragmentatie van statistieken en belasting in Service Fabric voor](service-fabric-cluster-resource-manager-defragmentation-metrics.md)meer informatie over het configureren van defragmentatie.
* Begin vanaf het begin en [krijg een inleiding tot Service Fabric Cluster Resource Manager.](service-fabric-cluster-resource-manager-introduction.md)
* Zie Het cluster [Servicefabric](service-fabric-cluster-resource-manager-balancing.md)in evenwicht brengen voor meer informatie over hoe clusterresourcebeheer de belasting in het cluster beheert en in evenwicht brengt.

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
