---
title: Azure Service Fabric resource governance voor containers en services | Microsoft Docs
description: Met Azure Service Fabric kunt u resource limieten opgeven voor services die binnen of buiten de containers worden uitgevoerd.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: atsenthi
ms.openlocfilehash: aa388a688e76b0ba69231d8a11aa1bfa686f7f51
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166552"
---
# <a name="resource-governance"></a>Resourcebeheer

Wanneer u meerdere services op hetzelfde knoop punt of cluster uitvoert, is het mogelijk dat een service meer bronnen kan gebruiken, Starving andere services in het proces. Dit probleem wordt aangeduid als het probleem ' ruis in de buur '. Met Azure Service Fabric kan de ontwikkelaar reserve ringen en limieten per service opgeven om resources te garanderen en het resource gebruik te beperken.

> Voordat u verder gaat met dit artikel, raden we u aan om vertrouwd te raken met het [service Fabric-toepassings model](service-fabric-application-model.md) en het [model van service Fabric hosting](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Metrische gegevens van resource governance

Resource governance wordt ondersteund in Service Fabric conform het [service pakket](service-fabric-application-model.md). De resources die zijn toegewezen aan het service pakket kunnen verder worden verdeeld tussen code pakketten. De resource limieten die zijn opgegeven, betekenen ook de reserve ring van de resources. Service Fabric ondersteunt het opgeven van CPU en geheugen per service pakket, met twee ingebouwde [metrische gegevens](service-fabric-cluster-resource-manager-metrics.md):

* *CPU* (metrische naam `servicefabric:/_CpuCores`): Een logische kern die beschikbaar is op de hostmachine. Alle kernen op alle knoop punten worden hetzelfde gewogen.

* *Geheugen* (metrische naam `servicefabric:/_MemoryInMB`): Het geheugen wordt uitgedrukt in mega bytes en wordt toegewezen aan het fysieke geheugen dat beschikbaar is op de computer.

Voor deze twee metrische gegevens houdt [cluster resource manager](service-fabric-cluster-resource-manager-cluster-description.md) de totale cluster capaciteit bij, de belasting van elk knoop punt in het cluster en de resterende bronnen in het cluster. Deze twee meet waarden zijn gelijk aan die van andere gebruikers of aangepaste metrische gegevens. Alle bestaande functies kunnen met ze worden gebruikt:

* Het cluster kan worden [gesaldeerd](service-fabric-cluster-resource-manager-balancing.md) op basis van deze twee metrische gegevens (standaard gedrag).
* Het cluster kan worden [gedefragmenteerd](service-fabric-cluster-resource-manager-defragmentation-metrics.md) op basis van deze twee metrische gegevens.
* Bij [het beschrijven van een cluster](service-fabric-cluster-resource-manager-cluster-description.md)kan de gebufferde capaciteit worden ingesteld voor deze twee metrische gegevens.

[Dynamische belasting rapportage](service-fabric-cluster-resource-manager-metrics.md) wordt niet ondersteund voor deze metrische gegevens en de belasting voor deze metrische gegevens wordt gedefinieerd tijdens de aanmaak tijd.

## <a name="resource-governance-mechanism"></a>Resource governance-mechanisme

De Service Fabric-runtime biedt momenteel geen reserve ring voor resources. Wanneer een proces of een container wordt geopend, stelt de runtime de resource limieten in voor de belasting die tijdens de aanmaak is gedefinieerd. Bovendien weigert de runtime het openen van nieuwe service pakketten die beschikbaar zijn wanneer resources worden overschreden. Voor een beter begrip van de werking van het proces gaat u een voor beeld van een knoop punt met twee CPU-kernen (mechanisme voor Memory governance is equivalent):

1. Eerst wordt een container op het knoop punt geplaatst en wordt er één CPU-kern aangevraagd. De runtime opent de container en stelt de CPU-limiet in op één kern. De container kan niet meer dan één kern gebruiken.

2. Vervolgens wordt een replica van een service op het knoop punt geplaatst en wordt in het bijbehorende service pakket een limiet van één CPU-kern opgegeven. De runtime opent het code pakket en stelt de CPU-limiet in op één kern.

Op dit moment is de som van de limieten gelijk aan de capaciteit van het knoop punt. Een proces en een container worden uitgevoerd met één kern, die elk niet interfereren. Service Fabric plaatst geen containers of replica's meer wanneer ze de CPU-limiet opgeven.

Er zijn echter twee situaties waarin andere processen kunnen concurreren voor de CPU. In deze situaties kan een proces en een container uit het voor beeld het probleem met de ruis in de buur ondervinden:

* *Onder andere vallende en niet-bestuurde diensten en containers*: Als een gebruiker een service maakt zonder dat hiervoor resource beheer is opgegeven, ziet de runtime dat er geen resources worden gebruikt en kan deze in het knoop punt in het voor beeld worden geplaatst. In dit geval verbruikt dit nieuwe proces effectief een CPU voor de kosten van de services die al op het knoop punt worden uitgevoerd. Er zijn twee oplossingen voor dit probleem. Combi neer geen onderhevige en niet-onderhevige Services op hetzelfde cluster, of gebruik [plaatsings beperkingen](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) zodat deze twee typen services niet eindigen op dezelfde set knoop punten.

* *Wanneer een ander proces wordt gestart op het knoop punt, buiten service Fabric (bijvoorbeeld een OS-service)* : In dit geval is het proces buiten Service Fabric ook voor een CPU met bestaande services. De oplossing voor dit probleem is om de capaciteit van knoop punten correct in te stellen op account voor besturingssysteem overhead, zoals wordt weer gegeven in de volgende sectie.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Cluster installatie voor het inschakelen van resource governance

Wanneer een knoop punt wordt gestart en aan het cluster wordt toegevoegd, detecteert Service Fabric de beschik bare hoeveelheid geheugen en het beschik bare aantal kernen, en worden vervolgens de capaciteit van het knoop punt voor die twee resources ingesteld.

Als u buffer ruimte voor het besturings systeem wilt verlaten en voor andere processen mogelijk op het knoop punt wordt uitgevoerd, gebruikt Service Fabric slechts 80% van de beschik bare resources op het knoop punt. Dit percentage kan worden geconfigureerd en kan worden gewijzigd in het cluster manifest.

Hier volgt een voor beeld van het instrueren van Service Fabric om 50% van de beschik bare CPU en 70% van het beschik bare geheugen te gebruiken:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Als u volledige hand matige configuratie van knooppunt capaciteit nodig hebt, kunt u het normale mechanisme gebruiken voor het beschrijven van de knoop punten in het cluster. Hier volgt een voor beeld van het instellen van het knoop punt met vier kernen en 2 GB aan geheugen:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Als automatische detectie van beschik bare bronnen is ingeschakeld en knooppunt capaciteit hand matig is gedefinieerd in het cluster manifest, Service Fabric controleert of het knoop punt voldoende bronnen heeft ter ondersteuning van de capaciteit die de gebruiker heeft gedefinieerd:

* Als knooppunt capaciteiten die in het manifest zijn gedefinieerd, kleiner zijn dan of gelijk zijn aan de beschik bare resources op het knoop punt, gebruikt Service Fabric de capaciteit die is opgegeven in het manifest.

* Als de knooppunt capaciteit die in het manifest is gedefinieerd, groter is dan de beschik bare resources, Service Fabric gebruikt de beschik bare resources als knooppunt capaciteit.

Het automatisch detecteren van beschik bare bronnen kan worden uitgeschakeld als dit niet vereist is. Als u deze functie wilt uitschakelen, wijzigt u de volgende instelling:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Voor optimale prestaties moet u ook de volgende instelling inschakelen in het cluster manifest:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

## <a name="specify-resource-governance"></a>Resource governance opgeven

De limieten voor resource beheer worden opgegeven in het manifest van de toepassing (ServiceManifestImport sectie), zoals wordt weer gegeven in het volgende voor beeld:

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

In dit voor beeld haalt het service pakket met de naam **ServicePackageA** één kern op de knoop punten waar deze zich bevindt. Dit service pakket bevat twee code pakketten (**CodeA1** en **CodeA2**) en de para meter `CpuShares` opgeven. Het aandeel van kunnen 512:256 deelt de kern over de twee code pakketten.

In dit voor beeld wordt CodeA1 bijvoorbeeld twee derde van een kern opgehaald en CodeA2 wordt een-derde van een kern (en een tijdelijke reserve ring van hetzelfde) opgehaald. Als kunnen niet zijn opgegeven voor code pakketten, Service Fabric de kernen onderling gelijkmatig verdeeld.

Geheugen limieten zijn absoluut, dus beide code pakketten zijn beperkt tot 1024 MB aan geheugen (en een zachte garantie reservering van hetzelfde). Code pakketten (containers of processen) kunnen niet meer geheugen toewijzen dan deze limiet. Als u dit wel doet, wordt er een out-of-Memory-uitzonde ring veroorzaakt. Voor een effectieve handhaving van resourcebeperkingen moeten voor alle pakketten binnen een servicepakket geheugenlimieten zijn opgegeven.

### <a name="using-application-parameters"></a>Toepassings parameters gebruiken

Bij het opgeven van resource governance is het mogelijk om [toepassings parameters](service-fabric-manage-multiple-environment-app-configuration.md) te gebruiken voor het beheren van meerdere app-configuraties. In het volgende voor beeld wordt het gebruik van toepassings parameters weer gegeven:

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

In dit voor beeld worden standaard parameter waarden ingesteld voor de productie omgeving, waarbij elk service pakket 4 kernen en 2 GB geheugen krijgt. Het is mogelijk om standaard waarden te wijzigen met toepassings parameter bestanden. In dit voor beeld kan één parameter bestand worden gebruikt om de toepassing lokaal te testen, waarbij minder resources worden opgehaald dan in productie:

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
> Het opgeven van het resource governance met toepassings parameters is beschikbaar vanaf Service Fabric versie 6,1.<br>
>
> Wanneer toepassings parameters worden gebruikt om resource governance op te geven, kan Service Fabric niet naar een eerdere versie dan versie 6,1 worden gedowngraded.

## <a name="other-resources-for-containers"></a>Andere resources voor containers

Naast CPU en geheugen is het mogelijk om andere resource limieten op te geven voor containers. Deze limieten zijn opgegeven op het niveau van code-pakket en worden toegepast wanneer de container wordt gestart. In tegens telling tot de CPU en het geheugen, is cluster resource manager niet op de hoogte van deze resources en worden er geen capaciteits controles of taak verdeling voor hen uitgevoerd.

* *MemorySwapInMB*: De hoeveelheid wissel geheugen die een container kan gebruiken.
* *MemoryReservationInMB*: De zachte limiet voor Memory governance die alleen wordt afgedwongen wanneer geheugen conflicten worden gedetecteerd op het knoop punt.
* *CpuPercent*: Het percentage van de CPU dat de container kan gebruiken. Als er CPU-limieten voor het service pakket zijn opgegeven, wordt deze para meter in feite genegeerd.
* *MaximumIOps*: De maximale IOPS die een container kan gebruiken (lezen en schrijven).
* *MaximumIOBytesps*: De maximale IO (bytes per seconde) die een container kan gebruiken (lezen en schrijven).
* *BlockIOWeight*: Het blok-IO-gewicht voor ten opzichte van andere containers.

Deze resources kunnen worden gecombineerd met de CPU en het geheugen. Hier volgt een voor beeld van hoe u aanvullende resources voor containers kunt opgeven:

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

* Lees [Inleiding tot de service Fabric cluster resource manager](service-fabric-cluster-resource-manager-introduction.md)voor meer informatie over cluster resource manager.
* Als u meer wilt weten over het toepassings model, service pakketten en code pakketten, en hoe replica's aan hen zijn toegewezen, moet u [een toepassing in service Fabric model](service-fabric-application-model.md)lezen.
