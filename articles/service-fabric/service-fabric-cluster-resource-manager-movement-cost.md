---
title: 'Service Fabric Cluster Resource Manager: Bewegingskosten'
description: Meer informatie over de bewegingskosten voor Service Fabric-services en hoe deze kunnen worden gespecificeerd om aan elke architectonische behoefte te voldoen, inclusief dynamische configuratie.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: af3e01d0d5a605c052be24eed8e14ee3449e2c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563340"
---
# <a name="service-movement-cost"></a>Kosten voor serviceverkeer
Een factor die de clusterbronbeheer voor servicestructuur in acht neemt wanneer u probeert te bepalen welke wijzigingen in een cluster moeten worden aangebracht, zijn de kosten van deze wijzigingen. Het begrip "kosten" wordt verhandeld tegen hoeveel het cluster kan worden verbeterd. Kosten worden meegewogen bij het verplaatsen van services voor het balanceren, defragmentatie en andere vereisten. Het doel is om op de minst storende of dure manier aan de eisen te voldoen.

Het verplaatsen van services kost cpu-tijd en netwerkbandbreedte minimaal. Voor stateful services vereist het kopiëren van de status van deze services, het verbruiken van extra geheugen en schijf. Door de kosten van oplossingen die de Azure Service Fabric Cluster Resource Manager biedt, te minimaliseren, wordt ervoor gezorgd dat de bronnen van het cluster niet onnodig worden besteed. U wilt echter ook geen oplossingen negeren die de toewijzing van resources in het cluster aanzienlijk zouden verbeteren.

De Cluster Resource Manager heeft twee manieren van computergebruik en deze te beperken terwijl het probeert om het cluster te beheren. Het eerste mechanisme is gewoon tellen elke beweging die het zou maken. Als twee oplossingen worden gegenereerd met ongeveer hetzelfde saldo (score), geeft de Cluster Resource Manager de voorkeur aan de oplossing met de laagste kosten (totaal aantal zetten).

Deze strategie werkt goed. Maar net als bij standaard of statische belastingen, is het onwaarschijnlijk in een complex systeem dat alle bewegingen gelijk zijn. Sommige zijn waarschijnlijk veel duurder.

## <a name="setting-move-costs"></a>Verhuiskosten instellen 
U de standaardverhuiskosten voor een service opgeven wanneer deze wordt gemaakt:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C#: 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

U MoveCost ook dynamisch opgeven of bijwerken voor een service nadat de service is gemaakt: 

PowerShell: 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>De verhuiskosten dynamisch opgeven op basis van een replica

De voorgaande fragmenten zijn allemaal voor het opgeven van MoveCost voor een hele service in een keer van buiten de service zelf. De verhuiskosten zijn echter het handigst wanneer de verhuiskosten van een specifiek serviceobject gedurende de levensduur veranderen. Aangezien de services zelf waarschijnlijk het beste idee hebben van hoe duur ze zijn om een bepaalde tijd te verplaatsen, is er een API voor services om hun eigen individuele verhuiskosten tijdens runtime te rapporteren. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Impact van verhuiskosten
MoveCost heeft vijf niveaus: Nul, Laag, Gemiddeld, Hoog en VeryHigh. De volgende regels zijn van toepassing:

* Verhuiskosten zijn relatief ten opzichte van elkaar, met uitzondering van Zero en VeryHigh. 
* Zero move kosten betekent dat beweging vrij is en mag niet meetellen voor de score van de oplossing.
* Het instellen van uw verhuiskosten op Hoog of VeryHigh biedt *geen* garantie dat de replica *nooit* zal worden verplaatst.
* Replica's met VeryHigh verhuiskosten worden alleen verplaatst als er een beperkingsschending in het cluster is die niet op een andere manier kan worden opgelost (zelfs als er veel andere replica's moeten worden verplaatst om de schending op te lossen)



<center>

![Verhuiskosten als factor bij het selecteren van replica's voor beweging][Image1]
</center>

MoveCost helpt u bij het vinden van de oplossingen die de minste verstoring in het algemeen veroorzaken en het gemakkelijkst te bereiken zijn terwijl u nog steeds in een gelijkwaardig evenwicht komt. Het begrip kosten van een service kan relatief zijn ten opzichte van veel dingen. De meest voorkomende factoren bij het berekenen van uw verhuiskosten zijn:

- De hoeveelheid status of gegevens die de service moet verplaatsen.
- De kosten van het ontkoppelen van klanten. Het verplaatsen van een primaire replica is meestal duurder dan de kosten van het verplaatsen van een secundaire replica.
- De kosten van het onderbreken van een operatie tijdens de vlucht. Sommige bewerkingen op het niveau van de gegevensopslag of bewerkingen die worden uitgevoerd in reactie op een clientoproep zijn duur. Na een bepaald punt, wil je ze niet stoppen als het niet hoeft. Dus terwijl de bewerking aan de gang is, verhoogt u de verhuiskosten van dit serviceobject om de kans te verkleinen dat het wordt verplaatst. Wanneer de bewerking is uitgevoerd, stelt u de kosten weer op normaal.

> [!IMPORTANT]
> Het gebruik van de VeryHigh-verhuiskosten moet zorgvuldig worden overwogen, omdat het de mogelijkheid van Cluster Resource Manager om een wereldwijd optimale plaatsingsoplossing in het cluster te vinden aanzienlijk beperkt. Replica's met VeryHigh verhuiskosten worden alleen verplaatst als er een beperkingsschending in het cluster is die niet op een andere manier kan worden opgelost (zelfs als er veel andere replica's moeten worden verplaatst om de schending op te lossen)

## <a name="enabling-move-cost-in-your-cluster"></a>Verhuiskosten in uw cluster inschakelen
Om rekening te houden met de gedetailleerdere MoveCosts, moet MoveCost in uw cluster zijn ingeschakeld. Zonder deze instelling wordt de standaardmodus voor het tellen van verplaatsingen gebruikt voor het berekenen van MoveCost en worden MoveCost-rapporten genegeerd.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

via ClusterConfig.json voor zelfstandige implementaties of Template.json voor azure gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Volgende stappen
- Service Fabric Cluster Resource Manger gebruikt statistieken om het verbruik en de capaciteit in het cluster te beheren. Voor meer informatie over statistieken en hoe u deze configureren, raadpleegt [u Resourceverbruik beheren en laden in Service Fabric met statistieken.](service-fabric-cluster-resource-manager-metrics.md)
- Bekijk [Het cluster Balanceren van uw servicestructuur](service-fabric-cluster-resource-manager-balancing.md)voor meer informatie over hoe de clusterresourcemanager de belasting in het cluster beheert en in evenwicht brengt.

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
