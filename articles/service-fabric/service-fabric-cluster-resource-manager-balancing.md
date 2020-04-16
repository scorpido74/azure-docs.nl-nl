---
title: Uw Azure Service Fabric-cluster in evenwicht brengen
description: Een inleiding tot het balanceren van uw cluster met de Service Fabric Cluster Resource Manager.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b6df25b525975f2d4fe6a02064e81f359a804c58
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416256"
---
# <a name="balancing-your-service-fabric-cluster"></a>Het cluster van de servicestructuur in evenwicht brengen
De Cluster resourcemanager van servicestructuur ondersteunt dynamische belastingswijzigingen, reageren op toevoegingen of verwijderingen van knooppunten of services. Het corrigeert ook automatisch schendingen van beperkingen en brengt het cluster proactief opnieuw in evenwicht. Maar hoe vaak worden deze acties ondernomen, en wat triggert hen?

Er zijn drie verschillende categorieën werk die de Cluster Resource Manager uitvoert. Dit zijn:

1. Plaatsing : deze fase houdt zich bezig met het plaatsen van stateful replica's of stateloze exemplaren die ontbreken. Plaatsing omvat zowel nieuwe services als het verwerken van stateful replica's of stateloze exemplaren die zijn mislukt. Replica's of exemplaren verwijderen en laten vallen worden hier verwerkt.
2. Beperkingscontroles : in deze fase worden schendingen van de verschillende plaatsingsbeperkingen (regels) binnen het systeem gecontroleerd en gecorrigeerd. Voorbeelden van regels zijn zaken als ervoor zorgen dat knooppunten niet over capaciteit heen zijn en dat aan de plaatsingsbeperkingen van een service wordt voldaan.
3. Balanceren : deze fase controleert of opnieuw balanceren nodig is op basis van het geconfigureerde gewenste evenwichtsniveau voor verschillende metrische gegevens. Als dat zo is, probeert het een regeling in het cluster te vinden die evenwichtiger is.

## <a name="configuring-cluster-resource-manager-timers"></a>Timers clusterbronbeheer configureren
De eerste set van controles rond balanceren zijn een set van timers. Deze timers bepalen hoe vaak de Cluster Resource Manager het cluster onderzoekt en corrigerende maatregelen neemt.

Elk van deze verschillende soorten correcties die de Cluster Resource Manager kan maken, wordt gecontroleerd door een andere timer die de frequentie bepaalt. Wanneer elke timer wordt geactiveerd, wordt de taak gepland. Standaard de Resource Manager:

* scant de toestand en past updates toe (zoals het opnemen dat een knooppunt naar beneden is) elke 1/10e van een seconde
* stelt elke seconde de plaatsingscontrolevlag in
* stelt elke seconde de vlag van de beperkingscontrole in
* zet de balanceervlag om de vijf seconden

Voorbeelden van de configuratie voor deze timers zijn hieronder:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

via ClusterConfig.json voor zelfstandige implementaties of Template.json voor azure gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinConstraintCheckInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

Vandaag de dag voert de Cluster Resource Manager slechts één van deze acties tegelijk uit, opeenvolgend. Dit is de reden waarom we verwijzen naar deze timers als "minimale intervallen" en de acties die worden ondernomen wanneer de timers af gaan als "het instellen van vlaggen". De Clusterresourcemanager zorgt bijvoorbeeld voor in behandeling zijnde aanvragen om services te maken voordat het cluster in evenwicht wordt gedeeld. Zoals u zien aan de standaardtijdintervallen die zijn opgegeven, scant het Clusterresourcebeheer op alles wat het vaak moet doen. Normaal gesproken betekent dit dat de set van wijzigingen die tijdens elke stap is klein. Als u vaak kleine wijzigingen doorbrengt, kan de Cluster Resource Manager reageren wanneer er dingen gebeuren in het cluster. De standaardtimers bieden een aantal batching, omdat veel van dezelfde soorten gebeurtenissen hebben de neiging om gelijktijdig optreden. 

Wanneer knooppunten bijvoorbeeld uitvallen, kunnen ze dit tegelijkertijd doen met hele foutdomeinen. Al deze fouten worden vastgelegd tijdens de volgende statusupdate na de *PLBRefreshGap.* De correcties worden bepaald tijdens de volgende plaatsings-, beperkingscontrole- en balanceringsuitvoeringen. Standaard scant clusterbronbeheer geen uren aan wijzigingen in het cluster en probeert het alle wijzigingen in één keer aan te pakken. Dit zou leiden tot uitbarstingen van churn.

De Clusterresourcemanager heeft ook aanvullende informatie nodig om te bepalen of het cluster onevenwichtig is. Daarvoor hebben we nog twee andere configuraties: *BalancingThresholds* en *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Balancerenvan drempels
Een balanceerdrempel is de belangrijkste controle voor het activeren van herbalancering. De balanceerdrempel voor een statistiek is een _verhouding_. Als de belasting voor een statistiek op het meest geladen knooppunt gedeeld door de hoeveelheid belasting op het minst geladen knooppunt de *balanceerdrempel*van die statistiek overschrijdt, is het cluster onevenwichtig. Als gevolg hiervan wordt de balans geactiveerd wanneer de clusterbronbeheer de volgende keer controleert. De timer *MinLoadBalancingInterval* bepaalt hoe vaak de ClusterResourceManager moet controleren of opnieuw balanceren nodig is. Controleren betekent niet dat er iets gebeurt. 

Balanceringsdrempels worden per metrisch niveau gedefinieerd als onderdeel van de clusterdefinitie. Voor meer informatie over statistieken, kijk op [dit artikel](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

via ClusterConfig.json voor zelfstandige implementaties of Template.json voor azure gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

<center>

![Voorbeeld van balanceerdrempel][Image1]
</center>

In dit voorbeeld verbruikt elke service één eenheid van een bepaalde statistiek. In het bovenste voorbeeld is de maximale belasting op een knooppunt vijf en het minimum twee. Laten we zeggen dat de balanceringsdrempel voor deze statistiek drie is. Aangezien de verhouding in het cluster 5/2 = 2,5 is en dat lager is dan de opgegeven balanceringsdrempel van drie, is het cluster in evenwicht. Er wordt geen evenwicht geactiveerd wanneer de clusterbronbeheer controleert.

In het onderste voorbeeld is de maximale belasting op een knooppunt 10, terwijl het minimum twee is, wat resulteert in een verhouding van vijf. Vijf is groter dan de aangewezen evenwichtsdrempel van drie voor die statistiek. Als gevolg hiervan wordt de volgende keer dat de balanceertimer wordt geactiveerd, opnieuw in evenwicht brengen. In een situatie als deze wordt sommige lading gewoonlijk verdeeld aan Node3. Omdat de Service Fabric Cluster Resource Manager geen gebruik maakt van een hebzuchtige benadering, kan een bepaalde belasting ook worden gedistribueerd naar Knooppunt2. 

<center>

![Voorbeeldacties voor het balanceren van drempelwaarden][Image2]
</center>

> [!NOTE]
> "Balancing" verwerkt twee verschillende strategieën voor het beheren van de belasting in uw cluster. De standaardstrategie die de ClusterResourceManager gebruikt, is het verdelen van de belasting over de knooppunten in het cluster. De andere strategie is [defragmentatie](service-fabric-cluster-resource-manager-defragmentation-metrics.md). Defragmentatie wordt uitgevoerd tijdens dezelfde afwegingsrun. De balancerings- en defragmentatiestrategieën kunnen worden gebruikt voor verschillende statistieken binnen hetzelfde cluster. Een service kan zowel balancerende als defragmentatiestatistieken hebben. Voor defragmentatiestatistieken activeert de verhouding van de belastingen in het cluster opnieuw in evenwicht wanneer deze _onder_ de balanceringsdrempel ligt. 
>

Onder de balanceringsdrempel komen is geen expliciet doel. Balanceren Drempels zijn slechts een *trigger*. Bij het balanceren van uitvoeringen bepaalt de Cluster Resource Manager welke verbeteringen het kan aanbrengen, indien van toepassing. Alleen omdat een balancerende zoektocht wordt afgetrapt betekent niet dat er iets beweegt. Soms is het cluster onevenwichtig, maar te beperkt om te corrigeren. Als alternatief, de verbeteringen vereisen bewegingen die te [duur](service-fabric-cluster-resource-manager-movement-cost.md)zijn ).

## <a name="activity-thresholds"></a>Activiteitsdrempels
Soms, hoewel knooppunten relatief onevenwichtig zijn, is de *totale* hoeveelheid belasting in het cluster laag. Het gebrek aan belasting kan een tijdelijke dip, of omdat het cluster is nieuw en net steeds bootstrapped. In beide gevallen wilt u misschien geen tijd besteden aan het balanceren van het cluster, omdat er weinig te winnen valt. Als het cluster een evenwicht onderging, zou u netwerk- en rekenbronnen besteden om dingen te verplaatsen zonder een groot *absoluut* verschil te maken. Om onnodige bewegingen te voorkomen, is er een ander besturingselement dat bekend staat als Activiteitsdrempels. Met activiteitsdrempels u een absolute ondergrens voor activiteit opgeven. Als er geen knooppunt boven deze drempelwaarde is, wordt de balancering niet geactiveerd, zelfs niet als de balanceerdrempel is bereikt.

Laten we zeggen dat we onze balanceerdrempel van drie behouden voor deze statistiek. Laten we ook zeggen dat we een activiteitsdrempel van 1536 hebben. In het eerste geval, terwijl het cluster onevenwichtig is volgens de balanceerdrempel, voldoet er geen knooppunt aan die activiteitsdrempel, dus er gebeurt niets. In het onderste voorbeeld is Node1 boven de activiteitsdrempel. Aangezien zowel de balanceerdrempel als de activiteitsdrempel voor de statistiek worden overschreden, wordt de balancering gepland. Laten we bijvoorbeeld naar het volgende diagram kijken: 

<center>

![Voorbeeld van activiteitsdrempel][Image3]
</center>

Net als balanceringsdrempels worden activiteitsdrempels per statistiek gedefinieerd via de clusterdefinitie:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

via ClusterConfig.json voor zelfstandige implementaties of Template.json voor azure gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

Balancerings- en activiteitsdrempels zijn beide gekoppeld aan een specifieke statistiek - balanceren wordt alleen geactiveerd als zowel de balanceringsdrempel als de activiteitsdrempel voor dezelfde statistiek worden overschreden.

> [!NOTE]
> Als deze niet is opgegeven, is de balanceerdrempel voor een statistiek 1 en de activiteitsdrempel 0. Dit betekent dat de Cluster Resource Manager zal proberen om die statistiek perfect in evenwicht te houden voor een bepaalde belasting. Als u aangepaste statistieken gebruikt, wordt aanbevolen dat u expliciet uw eigen balancerings- en activiteitsdrempels voor uw statistieken definieert. 
>

## <a name="balancing-services-together"></a>Diensten samen in evenwicht brengen
Of het cluster onevenwichtig is of niet, is een clusterbrede beslissing. Echter, de manier waarop we gaan over de vaststelling van het is het verplaatsen van individuele service replica's en instanties rond. Dit is logisch, toch? Als het geheugen op één knooppunt wordt gestapeld, kunnen meerdere replica's of instanties hieraan bijdragen. Als u de onbalans oplost, moet mogelijk een van de stateful replica's of stateloze exemplaren worden verplaatst die de onevenwichtige statistiek gebruiken.

Af en toe echter, een dienst die niet zelf onevenwichtig wordt verplaatst (denk aan de discussie van de lokale en mondiale gewichten eerder). Waarom zou een service worden verplaatst als alle statistieken van die service in evenwicht waren? Laten we eenvoorbeeld geven:

- Stel dat er vier services zijn, Service1, Service2, Service3 en Service4. 
- Service1 rapporteert metrische gegevens Metric1 en Metric2. 
- Service2 rapporteert metrische gegevens Metric2 en Metric3. 
- Service3 rapporteert metrische gegevens Metric3 en Metric4.
- Service4 rapporteert metrische Metric99. 

Je toch wel zien waar we naartoe gaan: Er is een ketting! We hebben niet echt vier onafhankelijke diensten, we hebben drie diensten die gerelateerd zijn en een die is uitgeschakeld op zijn eigen.

<center>

![Diensten samen balanceren][Image4]
</center>

Vanwege deze keten is het mogelijk dat een onbalans in statistieken 1-4 kan leiden tot replica's of instanties die behoren tot services 1-3 om te bewegen. We weten ook dat een onbalans in Statistieken 1, 2 of 3 geen bewegingen in Service4 kan veroorzaken. Het zou geen zin hebben, omdat het verplaatsen van de replica's of instanties die behoren tot Service4 rond kan absoluut niets doen om het saldo van metrics 1-3 beïnvloeden.

De Cluster Resource Manager zoekt automatisch uit welke services gerelateerd zijn. Het toevoegen, verwijderen of wijzigen van de statistieken voor services kan van invloed zijn op hun relaties. Tussen twee uitvoeringen van service2 kan bijvoorbeeld zijn bijgewerkt om Metric2 te verwijderen. Dit breekt de keten tussen Service1 en Service2. Nu in plaats van twee groepen van verwante diensten, zijn er drie:

<center>

![Diensten samen balanceren][Image5]
</center>

## <a name="next-steps"></a>Volgende stappen
* Statistieken zijn hoe de Clusterresourcemanger van de servicestructuur het verbruik en de capaciteit in het cluster beheert. Bekijk [dit artikel](service-fabric-cluster-resource-manager-metrics.md) voor meer informatie over statistieken en hoe u deze configureren
* Bewegingskosten is een manier om aan de Cluster Resource Manager te signaleren dat bepaalde services duurder zijn om te verplaatsen dan andere. Voor meer informatie over bewegingskosten, raadpleeg [dit artikel](service-fabric-cluster-resource-manager-movement-cost.md)
* De clusterbronbeheer heeft verschillende gashendels die u configureren om de verloop in het cluster te vertragen. Ze zijn normaal gesproken niet nodig, maar als je ze nodig hebt kun je [hier](service-fabric-cluster-resource-manager-advanced-throttling.md) leren over hen
* De Clusterresourcemanager kan subclustering herkennen en verwerken (een situatie die zich soms voordoet wanneer u plaatsingsbeperkingen en balanceren gebruikt). Zie [hier](cluster-resource-manager-subclustering.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
