---
title: ServiceFabric Cluster Resource Manager - Toepassingsgroepen
description: Overzicht van de functionaliteit van de toepassingsgroep in de clusterbronbeheer van servicestructuur
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 988c7ce52125800c16aa785d5b1458604a927ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452155"
---
# <a name="introduction-to-application-groups"></a>Inleiding tot toepassingsgroepen
Cluster Resource Manager van Service Fabric beheert doorgaans clusterbronnen door de belasting (weergegeven via [metrics)](service-fabric-cluster-resource-manager-metrics.md)gelijkmatig over het cluster te spreiden. Service Fabric beheert de capaciteit van de knooppunten in het cluster en het cluster als geheel via [capaciteit.](service-fabric-cluster-resource-manager-cluster-description.md) Statistieken en capaciteit werken geweldig voor veel workloads, maar patronen die intensief gebruik maken van verschillende Service Fabric Application Instances brengen soms extra vereisten met zich mee. U bijvoorbeeld:

- Reserveer een bepaalde capaciteit op de knooppunten in het cluster voor de services binnen een benoemde toepassingsinstantie
- Het totale aantal knooppunten beperken waarop de services binnen een benoemde toepassingsinstantie worden uitgevoerd (in plaats van ze over het hele cluster te verspreiden)
- Capaciteit definiëren op de benoemde toepassingsinstantie zelf om het aantal services of het totale resourceverbruik van de services daarin te beperken

Om aan deze vereisten te voldoen, ondersteunt de Clusterresourcemanager servicestructuur een functie genaamd Toepassingsgroepen.

## <a name="limiting-the-maximum-number-of-nodes"></a>Het maximumaantal knooppunten beperken
De eenvoudigste use case voor toepassingscapaciteit is wanneer een toepassingsinstantie moet worden beperkt tot een bepaald maximum aantal knooppunten. Hiermee worden alle services binnen die toepassingsinstantie geconsolideerd op een bepaald aantal machines. Consolidatie is handig wanneer u het fysieke resourcegebruik probeert te voorspellen of beperken door de services binnen de benoemde toepassingsinstantie. 

In de volgende afbeelding wordt een toepassingsinstantie weergegeven met en zonder een maximum aantal knooppunten dat is gedefinieerd:

<center>

![Toepassingsinstantie die maximumaantal knooppunten definieert][Image1]
</center>

In het linkervoorbeeld heeft de toepassing geen maximum aantal knooppunten gedefinieerd en heeft het drie services. De Cluster resource manager heeft alle replica's verspreid over zes beschikbare knooppunten om de beste balans in het cluster te bereiken (het standaardgedrag). In het juiste voorbeeld zien we dezelfde toepassing beperkt tot drie knooppunten.

De parameter die dit gedrag regelt, wordt MaximumNodes genoemd. Deze parameter kan worden ingesteld tijdens het maken van toepassingen of worden bijgewerkt voor een toepassingsinstantie die al is uitgevoerd.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –ApplicationName fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
await fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
await fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

```

Binnen de set knooppunten garandeert de ClusterResourcemanager niet welke serviceobjecten bij elkaar worden geplaatst of welke knooppunten worden gebruikt.

## <a name="application-metrics-load-and-capacity"></a>Toepassingsstatistieken, laden en capaciteit
Met toepassingsgroepen u ook statistieken definiëren die zijn gekoppeld aan een bepaalde benoemde toepassingsinstantie en de capaciteit van die toepassingsinstantie voor die statistieken. Met toepassingsstatistieken u het resourceverbruik van de services in die toepassingsinstantie bijhouden, reserveren en beperken.

Voor elke toepassingsstatistiek zijn er twee waarden die kunnen worden ingesteld:

- **Totale toepassingscapaciteit** : deze instelling vertegenwoordigt de totale capaciteit van de toepassing voor een bepaalde statistiek. De clusterbronbeheer verbiedt het maken van nieuwe services binnen deze toepassingsinstantie waardoor de totale belasting deze waarde overschrijdt. Stel dat de toepassingsinstantie een capaciteit van 10 had en al een belasting van vijf had. Het maken van een service met een totale standaardbelasting van 10 wordt niet toegestaan.
- **Maximale knooppuntcapaciteit** : deze instelling geeft de maximale totale belasting voor de toepassing op één knooppunt aan. Als de belasting over deze capaciteit heen gaat, verplaatst het clusterbronbeheer replica's naar andere knooppunten, zodat de belasting afneemt.


Powershell:

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -Metrics @("MetricName:Metric1,MaximumNodeCapacity:100,MaximumApplicationCapacity:1000")
```

C#:

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;
appMetric.MaximumNodeCapacity = 100;
ad.Metrics.Add(appMetric);
await fc.ApplicationManager.CreateApplicationAsync(ad);
```

## <a name="reserving-capacity"></a>Capaciteit reserveren
Een ander gemeenschappelijk gebruik voor toepassingsgroepen is ervoor te zorgen dat resources binnen het cluster zijn gereserveerd voor een bepaalde toepassingsinstantie. De ruimte wordt altijd gereserveerd wanneer de toepassingsinstantie wordt gemaakt.

Ruimte reserveren in het cluster voor de toepassing gebeurt onmiddellijk, zelfs wanneer:
- de toepassingsinstantie is gemaakt, maar heeft nog geen services binnen het
- het aantal services binnen de toepassingsinstantie verandert elke keer 
- de services bestaan, maar verbruiken de resources niet 

Voor het reserveren van resources voor een toepassingsinstantie moeten twee extra parameters worden opgegeven: *MinimumNodes* en *NodeReservationCapacity*

- **MinimumKnooppunten** - Hiermee definieert u het minimumaantal knooppunten waarop de toepassingsinstantie moet worden uitgevoerd.  
- **NodeReservationCapacity** - Deze instelling is per statistiek voor de toepassing. De waarde is het bedrag van die statistiek dat is gereserveerd voor de toepassing op een knooppunt waar de services in die toepassing worden uitgevoerd.

Het combineren van **MinimumNodes** en **NodeReservationCapacity** garandeert een minimale belastingsreservering voor de toepassing binnen het cluster. Als er minder resterende capaciteit in het cluster is dan de vereiste totale reservering, mislukt het maken van de toepassing. 

Laten we eens kijken naar een voorbeeld van capaciteitsreservering:

<center>

![Toepassingsinstanties die gereserveerde capaciteit definiëren][Image2]
</center>

In het linkervoorbeeld hebben toepassingen geen toepassingscapaciteit gedefinieerd. De Cluster Resource Manager balanceert alles volgens de normale regels.

In het voorbeeld aan de rechterkant, laten we zeggen dat Application1 is gemaakt met de volgende instellingen:

- Minimumknooppunten ingesteld op twee
- Een toepassingmetric gedefinieerd met
  - NodeReservationCapacity van 20

PowerShell

 ``` posh
 New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MinimumNodes 2 -Metrics @("MetricName:Metric1,NodeReservationCapacity:20")
 ```

C#

 ``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MinimumNodes = 2;

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.NodeReservationCapacity = 20;

ad.Metrics.Add(appMetric);

await fc.ApplicationManager.CreateApplicationAsync(ad);
```

Service Fabric reserveert capaciteit op twee knooppunten voor Application1 en staat niet toe dat services van Application2 die capaciteit verbruiken, zelfs als er geen belasting is die op dat moment wordt verbruikt door de services binnen Application1. Deze gereserveerde toepassingscapaciteit wordt als verbruikt beschouwd en telt mee voor de resterende capaciteit op dat knooppunt en binnen het cluster.  De reservering wordt onmiddellijk van de resterende clustercapaciteit afgetrokken, maar het gereserveerde verbruik wordt alleen van de capaciteit van een specifiek knooppunt afgetrokken wanneer er ten minste één serviceobject op wordt geplaatst. Deze latere reservering zorgt voor flexibiliteit en een beter gebruik van resources, omdat resources alleen worden gereserveerd op knooppunten wanneer dat nodig is.

## <a name="obtaining-the-application-load-information"></a>Informatie over het laden van de toepassing verkrijgen
Voor elke toepassing met een toepassingscapaciteit die is gedefinieerd voor een of meer statistieken, u de informatie verkrijgen over de totale belasting die wordt gerapporteerd door replica's van zijn services.

Powershell:

``` posh
Get-ServiceFabricApplicationLoadInformation –ApplicationName fabric:/MyApplication1
```

C#

``` csharp
var v = await fc.QueryManager.GetApplicationLoadInformationAsync("fabric:/MyApplication1");
var metrics = v.ApplicationLoadMetricInformation;
foreach (ApplicationLoadMetricInformation metric in metrics)
{
    Console.WriteLine(metric.ApplicationCapacity);  //total capacity for this metric in this application instance
    Console.WriteLine(metric.ReservationCapacity);  //reserved capacity for this metric in this application instance
    Console.WriteLine(metric.ApplicationLoad);  //current load for this metric in this application instance
}
```

De query ApplicationLoad retourneert de basisinformatie over toepassingscapaciteit die voor de toepassing is opgegeven. Deze informatie bevat de informatie over minimale knooppunten en maximale knooppunten en het aantal dat de toepassing momenteel bezet. Het bevat ook informatie over elke statistiek voor het laden van toepassingen, waaronder:

* Metrische naam: naam van de statistiek.
* Reserveringscapaciteit: clustercapaciteit die is gereserveerd in het cluster voor deze toepassing.
* Toepassingsbelasting: totale belasting van de onderliggende replica's van deze toepassing.
* Toepassingscapaciteit: Maximaal toegestane waarde van toepassingsbelasting.

## <a name="removing-application-capacity"></a>Toepassingscapaciteit verwijderen
Zodra de toepassingscapaciteitsparameters zijn ingesteld voor een toepassing, kunnen ze worden verwijderd met behulp van Update Application API's of PowerShell-cmdlets. Bijvoorbeeld:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Met deze opdracht worden alle parameters voor toepassingscapaciteitsbeheer uit de toepassingsinstantie verwijderd. Dit omvat minimumknooppunten, maximaal knooppunten en de eventuele statistieken van de toepassing. Het effect van de opdracht is onmiddellijk. Nadat deze opdracht is voltooid, gebruikt de Clusterresourcemanager het standaardgedrag voor het beheren van toepassingen. De parameters van toepassingscapaciteit `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`kunnen opnieuw worden opgegeven via .

### <a name="restrictions-on-application-capacity"></a>Beperkingen op de toepassingscapaciteit
Er zijn verschillende beperkingen op de parameters voor toepassingscapaciteit die moeten worden gerespecteerd. Als er validatiefouten zijn, vinden er geen wijzigingen plaats.

- Alle gehele parameters moeten niet-negatieve getallen zijn.
- MinimumNodes mag nooit groter zijn dan MaximumNodes.
- Als de capaciteit voor een load metric wordt gedefinieerd, moeten ze deze regels volgen:
  - De reserveringscapaciteit van het knooppunt mag niet groter zijn dan de maximale knooppuntcapaciteit. U bijvoorbeeld de capaciteit voor de metrische CPU op het knooppunt niet beperken tot twee eenheden en proberen drie eenheden op elk knooppunt te reserveren.
  - Als MaximumNodes is opgegeven, mag het product van MaximumNodes en Maximale knooppuntcapaciteit niet groter zijn dan de totale toepassingscapaciteit. Stel dat de maximale knooppuntcapaciteit voor loadmetric "CPU" is ingesteld op acht. Stel ook dat u de maximale knooppunten instelt op 10. In dit geval moet de totale toepassingscapaciteit groter zijn dan 80 voor deze belastingsstatistiek.

De beperkingen worden zowel tijdens het maken van de toepassing als bij het maken van de toepassing afgedwongen.

## <a name="how-not-to-use-application-capacity"></a>Toepassingscapaciteit niet gebruiken
- Probeer de functies van de groep toepassing niet te gebruiken om de toepassing te beperken tot een _specifieke_ subset van knooppunten. Met andere woorden, u opgeven dat de toepassing op ten hoogste vijf knooppunten wordt uitgevoerd, maar niet op welke specifieke vijf knooppunten in het cluster. Het beperken van een toepassing tot specifieke knooppunten kan worden bereikt met behulp van plaatsingsbeperkingen voor services.
- Probeer de toepassingscapaciteit niet te gebruiken om ervoor te zorgen dat twee services van dezelfde toepassing op dezelfde knooppunten worden geplaatst. Gebruik in plaats daarvan [affiniteits-](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) of [plaatsingsbeperkingen](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het configureren van services, [meer informatie over het configureren van Services](service-fabric-cluster-resource-manager-configure-services.md)
- Als u wilt weten hoe de clusterresourcemanager de belasting in het cluster beheert en in evenwicht brengt, raadpleegt u het artikel over [het balanceren van de belasting](service-fabric-cluster-resource-manager-balancing.md)
- Begin vanaf het begin en [krijg een inleiding tot de Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Lees voor meer informatie over hoe statistieken over het algemeen werken, informatie over [Service Fabric Load Metrics](service-fabric-cluster-resource-manager-metrics.md)
- De Clusterresourcemanager heeft veel opties voor het beschrijven van het cluster. Voor meer informatie over hen, check out dit artikel over [het beschrijven van een Service Fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
