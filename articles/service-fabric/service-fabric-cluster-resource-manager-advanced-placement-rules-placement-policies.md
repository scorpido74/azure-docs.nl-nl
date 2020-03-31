---
title: ServiceFabric Cluster Resource Manager - Plaatsingsbeleid
description: Overzicht van aanvullende plaatsingsbeleid en -regels voor Service Fabric Services
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9aea157d03f344e07a81f0588d3e0127f17ca75d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834430"
---
# <a name="placement-policies-for-service-fabric-services"></a>Plaatsingsbeleid voor services fabric services
Plaatsingsbeleid zijn aanvullende regels die kunnen worden gebruikt om serviceplaatsing in bepaalde specifieke, minder voorkomende scenario's te regelen. Enkele voorbeelden van deze scenario's zijn:

- Uw Service Fabric-cluster bestrijkt geografische afstanden, zoals meerdere on-premises datacenters of in Azure-regio's
- Uw omgeving omvat meerdere gebieden van geopolitieke of juridische controle, of een ander geval waarin u beleidsgrenzen hebt die u moet afdwingen
- Er zijn communicatieprestaties of latentieoverwegingen als gevolg van grote afstanden of het gebruik van tragere of minder betrouwbare netwerkkoppelingen
- U moet bepaalde workloads als een beste inspanning houden, hetzij met andere workloads, hetzij in de nabijheid van klanten

De meeste van deze vereisten sluiten aan bij de fysieke lay-out van het cluster, weergegeven als de foutdomeinen van het cluster. 

Het geavanceerde plaatsingsbeleid waarmee deze scenario's kunnen worden aangepakt, zijn:

1. Ongeldige domeinen
2. Vereiste domeinen
3. Voorkeursdomeinen
4. Het niet toestaan van het verpakken van replica's

De meeste van de volgende besturingselementen kunnen worden geconfigureerd via knooppunteigenschappen en plaatsingsbeperkingen, maar sommige zijn ingewikkelder. Om het eenvoudiger te maken, biedt de Service Fabric Cluster Resource Manager dit aanvullende plaatsingsbeleid. Plaatsingsbeleid wordt geconfigureerd op basis van service-instanties per naam. Ze kunnen ook dynamisch worden bijgewerkt.

## <a name="specifying-invalid-domains"></a>Ongeldige domeinen opgeven
Met het **plaatsingsbeleid Ongeldig Domein** u opgeven dat een bepaald foutdomein ongeldig is voor een specifieke service. Dit beleid zorgt ervoor dat een bepaalde dienst nooit op een bepaald gebied draait, bijvoorbeeld om geopolitieke of zakelijke beleidsredenen. Meerdere ongeldige domeinen kunnen worden opgegeven via afzonderlijke beleidsregels.

<center>

![Voorbeeld van ongeldig domein][Image1]
</center>

Code:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Vereiste domeinen opgeven
Het vereiste domeinplaatsingsbeleid vereist dat de service alleen in het opgegeven domein aanwezig is. Meerdere vereiste domeinen kunnen worden opgegeven via afzonderlijke beleidsregels.

<center>

![Vereist domeinvoorbeeld][Image2]
</center>

Code:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Een voorkeursdomein opgeven voor de primaire replica's van een stateful service
Het voorkeursprimair domein geeft het foutdomein op waarin het primaire werkgebied moet worden ingevoerd. Het primaire eindigt in dit domein wanneer alles gezond is. Als het domein of de primaire replica mislukt of wordt afgesloten, wordt het primaire nummer naar een andere locatie verplaatst, idealiter in hetzelfde domein. Als deze nieuwe locatie zich niet in het voorkeursdomein bevindt, verplaatst het Clusterresourcebeheer deze locatie zo snel mogelijk terug naar het voorkeursdomein. Natuurlijk is deze instelling alleen zinvol voor stateful diensten. Dit beleid is het meest handig in clusters die zijn overspannen in Azure-regio's of meerdere datacenters, maar services hebben die de voorkeur geven aan plaatsing op een bepaalde locatie. Het houden van Voorverkiezingen dicht bij hun gebruikers of andere diensten helpt bieden lagere latentie, vooral voor leest, die worden behandeld door Primaries standaard.

<center>

![Primaire hoofddomeinen en failover met voorkeur][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(primaryDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Replicadistributie vereisen en verpakking verbieden
Replica's worden _normaal gesproken_ verdeeld over fout- en upgradedomeinen wanneer het cluster in orde is. Er zijn echter gevallen waarin meer dan één replica voor een bepaalde partitie tijdelijk in één domein kan worden verpakt. Stel dat het cluster negen knooppunten heeft in drie foutdomeinen, fd:/0, fd:/1 en fd:/2. Laten we ook zeggen dat uw service drie replica's heeft. Laten we zeggen dat de knooppunten die werden gebruikt voor die replica's in fd:/1 en fd:/2 naar beneden gingen. Normaal gesproken geeft de ClusterResourcemanager de voorkeur aan andere knooppunten in dezelfde foutdomeinen. In dit geval, laten we zeggen als gevolg van capaciteitsproblemen geen van de andere knooppunten in die domeinen geldig waren. Als de Cluster Resource Manager vervangingen voor deze replica's bouwt, moet deze knooppunten kiezen in fd:/0. Als _u dat_ doet, ontstaat er echter een situatie waarin de beperking foutdomein wordt geschonden. Het inpakken van replica's vergroot de kans dat de hele replica set naar beneden kan gaan of verloren gaat. 

> [!NOTE]
> Voor meer informatie over beperkingen en beperkingen prioriteiten in het algemeen, check out [dit onderwerp](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Als je ooit hebt gezien een gezondheidsbericht zoals "`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`", dan heb je getroffen deze aandoening of iets dergelijks. Meestal worden slechts één of twee replica's tijdelijk bij elkaar verpakt. Zolang er minder dan een quorum van replica's in een bepaald domein zijn, bent u veilig. Verpakking is zeldzaam, maar het kan gebeuren, en meestal deze situaties zijn van voorbijgaande aard, omdat de knooppunten terugkomen. Als de knooppunten wel laag blijven en de Cluster Resource Manager vervangingen moet bouwen, zijn er meestal andere knooppunten beschikbaar in de ideale foutdomeinen.

Sommige workloads hebben liever altijd het doelaantal replica's, zelfs als ze in minder domeinen zijn verpakt. Deze workloads zijn wedden tegen totale gelijktijdige permanente domeinfouten en kan meestal herstellen lokale staat. Andere workloads nemen de downtime liever eerder dan risicocorrectie of verlies van gegevens. De meeste productieworkloads worden uitgevoerd met meer dan drie replica's, meer dan drie foutdomeinen en veel geldige knooppunten per foutdomein. Hierdoor staat het standaardgedrag standaard domeinverpakking toe. Het standaardgedrag maakt het mogelijk om deze extreme gevallen te balanceren en failover te verwerken, zelfs als dat betekent dat u tijdelijk domeinverpakking hebt.

Als u dergelijke verpakking voor een bepaalde werkbelasting `RequireDomainDistribution` wilt uitschakelen, u het beleid voor de service opgeven. Wanneer dit beleid is ingesteld, zorgt clusterbronbeheer ervoor dat geen twee replica's van dezelfde partitie worden uitgevoerd in hetzelfde fout- of upgradedomein.

Code:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Nu, zou het mogelijk zijn om deze configuraties te gebruiken voor diensten in een cluster die niet geografisch overspannen was? Dat zou kunnen, maar er is ook geen goede reden. De vereiste, ongeldige en gewenste domeinconfiguraties moeten worden vermeden, tenzij de scenario's dit vereisen. Het heeft geen zin om te proberen een bepaalde werkbelasting te forceren om in één rack te draaien, of om een bepaald segment van uw lokale cluster boven een ander segment te verkiezen. Verschillende hardwareconfiguraties moeten worden verspreid over foutdomeinen en worden afgehandeld via normale plaatsingsbeperkingen en knooppunteigenschappen.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het configureren van services, [meer informatie over het configureren van Services](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
