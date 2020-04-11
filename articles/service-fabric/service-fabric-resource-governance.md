---
title: Resourcebeheer voor containers en services
description: Met Azure Service Fabric u resourcelimieten opgeven voor services die binnen of buiten containers worden uitgevoerd.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 11ca6e29829d911717a829b3e4dee0a190856a52
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115150"
---
# <a name="resource-governance"></a>Resourcebeheer

Wanneer u meerdere services uitvoert op hetzelfde knooppunt of cluster, is het mogelijk dat één service meer resources verbruikt, waardoor andere services worden uitgehongerd. Dit probleem wordt aangeduid als de "luidruchtige buurman" probleem. Azure Service Fabric stelt de ontwikkelaar in staat reserveringen en limieten per service op te geven om resources te garanderen en het gebruik van resources te beperken.

> Voordat u verder gaat met dit artikel, raden we u aan vertrouwd te raken met het [Service Fabric-toepassingsmodel](service-fabric-application-model.md) en het [Service Fabric-hostingmodel.](service-fabric-hosting-model.md)
>

## <a name="resource-governance-metrics"></a>Statistieken voor resourcebeheer

Resource governance wordt ondersteund in Service Fabric in overeenstemming met het [servicepakket.](service-fabric-application-model.md) De resources die aan het servicepakket zijn toegewezen, kunnen verder worden verdeeld over codepakketten. De opgegeven resourcelimieten betekenen ook de reservering van de resources. Service Fabric ondersteunt het opgeven van CPU en geheugen per servicepakket, met twee ingebouwde [statistieken:](service-fabric-cluster-resource-manager-metrics.md)

* *CPU* (metrische `servicefabric:/_CpuCores`naam): een logische kern die beschikbaar is op de hostmachine. Alle kernen op alle knooppunten worden hetzelfde gewogen.

* *Geheugen* (metrische `servicefabric:/_MemoryInMB`naam): Geheugen wordt uitgedrukt in megabytes, en het kaarten aan fysiek geheugen dat beschikbaar is op de machine.

Voor deze twee statistieken houdt [Cluster Resource Manager](service-fabric-cluster-resource-manager-cluster-description.md) de totale clustercapaciteit, de belasting van elk knooppunt in het cluster en de resterende resources in het cluster bij. Deze twee statistieken zijn gelijk aan elke andere gebruiker of aangepaste statistiek. Alle bestaande functies kunnen ermee worden gebruikt:

* Het cluster kan worden [gebalanceerd](service-fabric-cluster-resource-manager-balancing.md) op basis van deze twee statistieken (standaardgedrag).
* Het cluster kan worden [gedefragmenteerd](service-fabric-cluster-resource-manager-defragmentation-metrics.md) op basis van deze twee statistieken.
* Bij [het beschrijven van een cluster](service-fabric-cluster-resource-manager-cluster-description.md)kan gebufferde capaciteit worden ingesteld voor deze twee statistieken.

> [!NOTE]
> [Dynamische belastingrapportage](service-fabric-cluster-resource-manager-metrics.md) wordt niet ondersteund voor deze statistieken; belastingen voor deze statistieken worden gedefinieerd op het moment van creatie.

## <a name="resource-governance-mechanism"></a>Mechanisme voor resourcebeheer

De runtime van Service Fabric biedt momenteel geen reservering voor resources. Wanneer een proces of een container wordt geopend, stelt de runtime de resourcelimieten in op de belastingen die zijn gedefinieerd tijdens het maken. Bovendien wordt de runtime de opening van nieuwe servicepakketten afgewezen die beschikbaar zijn wanneer resources worden overschreden. Om beter te begrijpen hoe het proces werkt, laten we een voorbeeld nemen van een knooppunt met twee CPU-kernen (mechanisme voor geheugenbeheer is gelijkwaardig):

1. Eerst wordt een container op het knooppunt geplaatst, waarbij één CPU-kern wordt aangevraagd. De runtime opent de container en stelt de CPU-limiet in op één kern. De container kan niet meer dan één kern gebruiken.

2. Vervolgens wordt een replica van een service op het knooppunt geplaatst en het bijbehorende servicepakket geeft een limiet van één CPU-kern op. De runtime opent het codepakket en stelt de CPU-limiet in op één kern.

Op dit punt is de som van de limieten gelijk aan de capaciteit van het knooppunt. Een proces en een container worden uitgevoerd met elk één kern en storen elkaar niet. Service Fabric plaatst geen containers of replica's meer wanneer ze de CPU-limiet opgeven.

Er zijn echter twee situaties waarin andere processen kunnen strijden voor CPU. In deze situaties kunnen een proces en een container uit ons voorbeeld het luidruchtige probleem van de buren ervaren:

* *Beheerde en niet-geregelde services en containers mengen:* Als een gebruiker een service maakt zonder dat resourcegovernance is opgegeven, ziet de runtime deze als het verbruiken van geen resources en kan deze op het knooppunt in ons voorbeeld plaatsen. In dit geval verbruikt dit nieuwe proces effectief een cpu ten koste van de services die al op het knooppunt worden uitgevoerd. Er zijn twee oplossingen voor dit probleem. Meng geen beheerde en niet-beheerde services op hetzelfde cluster of gebruik [plaatsingsbeperkingen](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) zodat deze twee typen services niet op dezelfde set knooppunten terechtkomen.

* *Wanneer een ander proces wordt gestart op het knooppunt, buiten Service Fabric (bijvoorbeeld een OS-service)*: In deze situatie, het proces buiten Service Fabric ook stelt voor CPU met bestaande diensten. De oplossing voor dit probleem is het opzetten van knooppuntcapaciteiten correct om rekening te houden met de overhead van het besturingssysteem, zoals weergegeven in de volgende sectie.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Clusterinstellingen voor het inschakelen van resourcegovernance

Wanneer een knooppunt wordt gestart en lid wordt van het cluster, detecteert Service Fabric de beschikbare hoeveelheid geheugen en het beschikbare aantal cores en stelt u vervolgens de knooppuntcapaciteit in voor deze twee resources.

Als u bufferruimte wilt laten voor het besturingssysteem en voor andere processen mogelijk op het knooppunt wordt uitgevoerd, gebruikt Service Fabric slechts 80% van de beschikbare resources op het knooppunt. Dit percentage is configureerbaar en kan worden gewijzigd in het clustermanifest.

Hier is een voorbeeld van hoe servicefabric te instrueren om 50% van de beschikbare CPU en 70% van het beschikbare geheugen te gebruiken:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Voor de meeste klanten en scenario's is automatische detectie van knooppuntcapaciteiten voor de CPU en het geheugen de aanbevolen configuratie (automatische detectie is standaard ingeschakeld). Als u echter volledige handmatige installatie van knooppuntcapaciteiten nodig hebt, u deze per knooppunttype configureren met behulp van het mechanisme voor het beschrijven van de knooppunten in het cluster. Hier is een voorbeeld van het instellen van het knooppunttype met vier cores en 2 GB geheugen:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Wanneer automatische detectie van beschikbare resources is ingeschakeld en knooppuntcapaciteiten handmatig worden gedefinieerd in het clustermanifest, controleert Service Fabric of het knooppunt voldoende resources heeft om de capaciteit te ondersteunen die de gebruiker heeft gedefinieerd:

* Als knooppuntcapaciteiten die in het manifest zijn gedefinieerd, kleiner zijn dan of gelijk zijn aan de beschikbare resources op het knooppunt, gebruikt Service Fabric de capaciteiten die in het manifest zijn opgegeven.

* Als knooppuntcapaciteiten die in het manifest zijn gedefinieerd groter zijn dan de beschikbare resources, gebruikt Service Fabric de beschikbare resources als knooppuntcapaciteit.

Automatische detectie van beschikbare resources kan worden uitgeschakeld als dit niet nodig is. Als u de instelling wilt uitschakelen, wijzigt u de volgende instelling:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Voor optimale prestaties moet ook de volgende instelling in het clustermanifest worden ingeschakeld:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> Vanaf Service Fabric-versie 7.0 hebben we de regel bijgewerkt voor de manier waarop knooppuntresourcecapaciteiten worden berekend in de gevallen waarin de gebruiker handmatig de waarden voor knooppuntresourcecapaciteiten verstrekt. Laten we eens kijken naar het volgende scenario:
>
> * Er zijn 10 cpu-cores in totaal op het knooppunt
> * SF is geconfigureerd om 80% van de totale resources te gebruiken voor de gebruikersservices (standaardinstelling), waardoor een buffer van 20% overblijft voor de andere services die op het knooppunt worden uitgevoerd (incl. Service Fabric-systeemservices)
> * De gebruiker besluit de noderesourcecapaciteit voor de statistiek cpu-cores handmatig over te schrijven en stelt deze in op 5 cores
>
> We hebben de regel voor de manier waarop de beschikbare capaciteit voor servicefabric-gebruikersservices wordt berekend op de volgende manier gewijzigd:
>
> * Vóór Service Fabric 7.0 wordt de beschikbare capaciteit voor gebruikersservices berekend op **5 cores** (capaciteitsbuffer van 20% wordt genegeerd)
> * Te beginnen met Service Fabric 7.0, zou de beschikbare capaciteit voor gebruikersservices worden berekend op **4 cores** (capaciteitsbuffer van 20% wordt niet genegeerd)

## <a name="specify-resource-governance"></a>Resourcebeheer opgeven

Limieten voor resourcegovernance worden opgegeven in het toepassingsmanifest (sectie ServiceManifestImport), zoals weergegeven in het volgende voorbeeld:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric sums the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```

In dit voorbeeld krijgt het servicepakket genaamd **ServicePackageA** één kern op de knooppunten waar het wordt geplaatst. Dit servicepakket bevat twee codepakketten **(CodeA1** en **CodeA2)** en beide specificeren de `CpuShares` parameter. Het aandeel van CpuShares 512:256 verdeelt de kern over de twee codepakketten.

Zo krijgt CodeA1 in dit voorbeeld twee derde van een kern en krijgt CodeA2 een derde van een kern (en een reservering met zachte garantie van hetzelfde). Als CpuShares niet zijn opgegeven voor codepakketten, verdeelt Service Fabric de kernen gelijk onder hen.

Geheugenlimieten zijn absoluut, dus beide codepakketten zijn beperkt tot 1024 MB geheugen (en een reservering met zachte garantie van hetzelfde). Codepakketten (containers of processen) kunnen niet meer geheugen toewijzen dan deze limiet, en een poging om dit te doen resulteert in een uitzondering buiten het geheugen. Voor een effectieve handhaving van resourcebeperkingen moeten voor alle pakketten binnen een servicepakket geheugenlimieten zijn opgegeven.

### <a name="using-application-parameters"></a>Toepassingsparameters gebruiken

Bij het opgeven van resourcegovernance-instellingen is het mogelijk om [toepassingsparameters](service-fabric-manage-multiple-environment-app-configuration.md) te gebruiken om meerdere app-configuraties te beheren. In het volgende voorbeeld ziet u het gebruik van toepassingsparameters:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

In dit voorbeeld worden standaardparameterwaarden ingesteld voor de productieomgeving, waarbij elk servicepakket 4 cores en 2 GB geheugen krijgt. Het is mogelijk om standaardwaarden te wijzigen met toepassingsparameterbestanden. In dit voorbeeld kan één parameterbestand worden gebruikt voor het lokaal testen van de toepassing, waarbij het minder resources krijgt dan in productie:

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> Het opgeven van resourcegovernance met toepassingsparameters is beschikbaar vanaf Service Fabric-versie 6.1.<br>
>
> Wanneer toepassingsparameters worden gebruikt om resourcebeheer op te geven, kan Service Fabric niet worden gedegradeerd naar een versie vóór versie 6.1.

## <a name="enforcing-the-resource-limits-for-user-services"></a>De resourcelimieten voor gebruikersservices afdwingen

Hoewel het toepassen van resource governance op uw Service Fabric-services garandeert dat deze door resources beheerde services hun resourcequotum niet kunnen overschrijden, moeten veel gebruikers nog steeds een aantal van hun Service Fabric-services in onbestuurde modus uitvoeren. Bij het gebruik van niet-geregelde Service Fabric-services is het mogelijk om situaties tegen te komen waarin "op hol geslagen" onbeheerde services alle beschikbare resources op de Service Fabric-knooppunten verbruiken, wat kan leiden tot ernstige problemen zoals:

* Resourcehonger van andere services die op de knooppunten worden uitgevoerd (inclusief servicefabric-systeemservices)
* Knooppunten die in een ongezonde staat terechtkomen
* Api's voor niet-reagerende servicefabricclusterbeheer

Om te voorkomen dat deze situaties zich voordoen, u met Service Fabric *de resourcelimieten afdwingen voor alle servicefabric-gebruikersservices die op het knooppunt worden uitgevoerd* (zowel geregeld als onbeheerst) om te garanderen dat gebruikersservices nooit meer dan de opgegeven hoeveelheid resources zullen gebruiken. Dit wordt bereikt door de waarde voor de config EnforceUserServiceMetricCapacitys in te stellen in de sectie Plaatsingenloadbalancing van het Clustermanifest op true. Deze instelling is standaard uitgeschakeld.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Aanvullende opmerkingen:

* Handhaving van resourcelimiet `servicefabric:/_CpuCores` is `servicefabric:/_MemoryInMB` alleen van toepassing op de resourcestatistieken en resourcestatistieken
* Handhaving van resourcelimiet werkt alleen als knooppuntcapaciteiten voor de resourcestatistieken beschikbaar zijn voor Service Fabric, hetzij via het mechanisme voor automatische detectie, hetzij via gebruikers die handmatig de knooppuntcapaciteiten opgeven (zoals uitgelegd in de [clusterinstelling voor het inschakelen van resourcegovernance).](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance)Als knooppuntcapaciteiten niet zijn geconfigureerd, kan de handhavingscapaciteit voor resourcelimiet niet worden gebruikt omdat Service Fabric niet weet hoeveel resources u moet reserveren voor gebruikersservices.Service Fabric geeft een statuswaarschuwing als 'EnforceUserServiceMetricCapacitys' waar is, maar knooppuntcapaciteiten niet zijn geconfigureerd.

## <a name="other-resources-for-containers"></a>Andere bronnen voor containers

Naast CPU en geheugen is het mogelijk om andere resourcelimieten voor containers op te geven. Deze limieten worden opgegeven op code-package niveau en worden toegepast wanneer de container wordt gestart. In tegenstelling tot CPU en geheugen is Cluster Resource Manager zich niet bewust van deze bronnen en doet het geen capaciteitscontroles of taakverdeling voor hen.

* *MemorySwapInMB*: De hoeveelheid swapgeheugen die een container kan gebruiken.
* *MemoryReservationInMB*: De zachte limiet voor geheugenbeheer die alleen wordt afgedwongen wanneer geheugenstelling wordt gedetecteerd op het knooppunt.
* *CpuPercent:* het percentage CPU dat de container kan gebruiken. Als CPU-limieten zijn opgegeven voor het servicepakket, wordt deze parameter effectief genegeerd.
* *MaximumIOps*: De maximale IOPS die een container kan gebruiken (lezen en schrijven).
* *MaximumIOBytesps*: De maximale IO (bytes per seconde) die een container kan gebruiken (lezen en schrijven).
* *BlockIOWeight*: Het blok IO gewicht ten opzichte van andere containers.

Deze bronnen kunnen worden gecombineerd met CPU en geheugen. Hier volgt een voorbeeld van het opgeven van extra resources voor containers:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Volgende stappen

* Lees De [clusterbronbeheerder](service-fabric-cluster-resource-manager-introduction.md)van de servicestructuur introduceren voor meer informatie over Clusterresourcebeheer .
* Voor meer informatie over het toepassingsmodel, servicepakketten en codepakketten en hoe replica's naar hen worden toegewezen, lees [model een toepassing in Service Fabric](service-fabric-application-model.md).
