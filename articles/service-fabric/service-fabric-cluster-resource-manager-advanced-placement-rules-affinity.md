---
title: Service Fabric Cluster Resource Manager - Affiniteit
description: Overzicht van serviceaffiniteit voor Azure Service Fabric-services en richtlijnen voor de configuratie van serviceaffiniteiten.
services: service-fabric
documentationcenter: .net
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7bfd261802fbf891b8f45079255783cb1e8ac7d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551740"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Serviceaffiniteit configureren en gebruiken in Service Fabric
Affinity is een controle die vooral wordt geleverd om de overgang van grotere monolithische applicaties naar de cloud- en microserviceswereld te vergemakkelijken. Het wordt ook gebruikt als een optimalisatie voor het verbeteren van de prestaties van diensten, hoewel dit kan bijwerkingen hebben.

Stel dat u een grotere app, of een app die gewoon niet is ontworpen met microservices in het achterhoofd, naar Service Fabric (of een gedistribueerde omgeving) brengt. Dit type overgang komt vaak voor. U begint met het tillen van de hele app in het milieu, verpakking, en ervoor te zorgen dat het soepel loopt. Dan begin je het op te splitsen in verschillende kleinere diensten die allemaal met elkaar praten.

Uiteindelijk u vinden dat de toepassing sommige kwesties ondervindt. De problemen vallen meestal in een van deze categorieën:

1. Sommige component X in de monolithische app had een ongedocumenteerde afhankelijkheid van component Y, en je hebt die componenten net omgezet in afzonderlijke services. Aangezien deze services nu worden uitgevoerd op verschillende knooppunten in het cluster, zijn ze verbroken.
2. Deze componenten communiceren via (lokale naam pijpen | gedeeld geheugen | bestanden op schijf) en ze moeten echt in staat zijn om te schrijven naar een gedeelde lokale bron om prestatieredenen nu. Die harde afhankelijkheid wordt later verwijderd, misschien.
3. Alles is prima, maar het blijkt dat deze twee componenten zijn eigenlijk spraakzaam / prestatiegevoelig. Toen ze verhuisden ze naar afzonderlijke diensten algemene applicatie prestaties getankt of latentie verhoogd. Als gevolg hiervan voldoet de algehele toepassing niet aan de verwachtingen.

In deze gevallen willen we ons refactoringwerk niet verliezen en willen we niet terug naar de monoliet. De laatste voorwaarde kan zelfs wenselijk zijn als een eenvoudige optimalisatie. Echter, totdat we kunnen herontwerpen van de componenten om natuurlijk te werken als diensten (of totdat we de prestaties verwachtingen op een andere manier op te lossen) we gaan een gevoel van plaats nodig hebben.

Wat u moet doen? Je proberen affiniteit aan te zetten.

## <a name="how-to-configure-affinity"></a>Affiniteit configureren
Als u affiniteit wilt instellen, definieert u een affiniteitsrelatie tussen twee verschillende services. U affiniteit als wijzen een dienst naar een andere en zeggen: Deze service kan alleen worden uitgevoerd waar die service wordt uitgevoerd. Soms verwijzen we naar affiniteit als een ouder/kind relatie (waarbij je het kind naar de ouder richt). Affiniteit zorgt ervoor dat de replica's of exemplaren van één service op dezelfde knooppunten worden geplaatst als die van een andere service.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Een kinddienst kan slechts deelnemen aan één affiniteitsrelatie. Als u wilde dat het kind te worden geinitiseerd om twee ouder diensten in een keer heb je een paar opties:
> - De relaties omkeren (bovenliggendeService1- en bovenliggendeService2-punt hebben bij de huidige onderliggende service), of
> - Wijs een van de ouders aan als hub bij overeenkomst en laat alle diensten op die service wijzen. 
>
> Het resulterende plaatsingsgedrag in het cluster moet hetzelfde zijn.
>

## <a name="different-affinity-options"></a>Verschillende affiniteitsopties
Affiniteit wordt weergegeven via een van de verschillende correlatieschema's, en heeft twee verschillende modi. De meest voorkomende manier van affiniteit is wat we NonAlignedAffinity noemen. In NonAlignedAffinity worden de replica's of exemplaren van de verschillende services op dezelfde knooppunten geplaatst. De andere modus is AlignedAffinity. Aligned Affinity is alleen nuttig met stateful services. Het configureren van twee stateful services om affiniteit op één lijn te hebben, zorgt ervoor dat de voorverkiezingen van deze services op dezelfde knooppunten als elkaar worden geplaatst. Het zorgt er ook voor dat elk paar secondaries voor deze diensten worden geplaatst op dezelfde knooppunten. Het is ook mogelijk (hoewel minder vaak) om NonAlignedAffinity te configureren voor stateful services. Voor NonAlignedAffinity worden de verschillende replica's van de twee stateful services uitgevoerd op dezelfde knooppunten, maar hun voorverkiezingen kunnen op verschillende knooppunten terechtkomen.

<center>

![Affiniteitsmodi en hun effecten][Image1]
</center>

### <a name="best-effort-desired-state"></a>Beste inspanning gewenste staat
Een affiniteitrelatie is de beste inspanning. Het biedt niet dezelfde garanties voor collocatie of betrouwbaarheid als in hetzelfde uitvoerbare proces. De services in een affiniteitsrelatie zijn fundamenteel verschillende entiteiten die kunnen falen en onafhankelijk kunnen worden verplaatst. Een affiniteitsrelatie kan ook breken, hoewel deze onderbrekingen tijdelijk zijn. Capaciteitsbeperkingen kunnen bijvoorbeeld betekenen dat slechts enkele serviceobjecten in de affiniteitsrelatie op een bepaald knooppunt passen. In deze gevallen, ook al is er een affiniteitsrelatie, deze kan niet worden afgedwongen vanwege de andere beperkingen. Als dit mogelijk is, wordt de overtreding later automatisch gecorrigeerd.

### <a name="chains-vs-stars"></a>Kettingen versus sterren
Vandaag de dag is de Cluster Resource Manager niet in staat om ketens van affiniteitsrelaties te modelleren. Wat dit betekent is dat een service die een kind is in een affiniteitsrelatie geen ouder kan zijn in een andere affiniteitsrelatie. Als u dit type relatie wilt modelleren, moet u het in feite modelleren als een ster, in plaats van als een keten. Om van een ketting naar een ster te gaan, wordt het onderste kind in plaats daarvan naar de ouder van het eerste kind gebracht. Afhankelijk van de opstelling van uw diensten, moet u dit mogelijk meerdere keren doen. Als er geen natuurlijke bovenliggende service is, moet u er mogelijk een maken die als tijdelijke aanduiding fungeert. Afhankelijk van uw vereisten u ook [toepassingsgroepen](service-fabric-cluster-resource-manager-application-groups.md)bekijken.

<center>

![Ketens versus sterren in de context van affiniteitsrelaties][Image2]
</center>

Een ander ding om op te merken over affiniteit relaties vandaag is dat ze directioneel standaard. Dit betekent dat de affiniteitsregel alleen afdwingt dat het kind bij de ouder wordt geplaatst. Het zorgt er niet voor dat de ouder zich bij het kind bevindt. Daarom, als er een schending van de affiniteit is en om de schending om de een of andere reden te corrigeren, is het niet haalbaar om het kind naar het knooppunt van de ouder te verplaatsen, dan - zelfs als het verplaatsen van de ouder naar het knooppunt van het kind de schending zou hebben gecorrigeerd - wordt de ouder niet verplaatst naar de kinderknooppunt. Als u de config [MoveParentToFixViolation](service-fabric-cluster-fabric-settings.md) instelt op true, wordt de directionaliteit verwijderd. Het is ook belangrijk op te merken dat de affiniteitsrelatie niet perfect of direct kan worden afgedwongen, omdat verschillende services verschillende levenscycli hebben en onafhankelijk kunnen mislukken en kunnen bewegen. Stel dat de ouder plotseling niet naar een ander knooppunt gaat omdat deze is gecrasht. De Cluster Resource Manager en Failover Manager behandelen de failover eerst, omdat het houden van de services up, consistent en beschikbaar is de prioriteit. Zodra de failover is voltooid, wordt de affiniteitsrelatie verbroken, maar de Cluster Resource Manager denkt dat alles in orde is totdat het merkt dat het kind zich niet bij de bovenliggende bevindt. Dit soort controles worden periodiek uitgevoerd. Meer informatie over hoe de Cluster Resource Manager beperkingen evalueert is beschikbaar in [dit artikel,](service-fabric-cluster-resource-manager-management-integration.md#constraint-types)en [deze](service-fabric-cluster-resource-manager-balancing.md) vertelt meer over hoe de cadans waarop deze beperkingen worden geëvalueerd configureren.   


### <a name="partitioning-support"></a>Ondersteuning voor partitionering
Het laatste wat opvalt aan affiniteit is dat affiniteitsrelaties niet worden ondersteund waar de bovenliggende wordt verdeeld. Verdeelde bovenliggende services kunnen uiteindelijk worden ondersteund, maar vandaag is het niet toegestaan.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het configureren van services, [meer informatie over het configureren van Services](service-fabric-cluster-resource-manager-configure-services.md)
- Als u services wilt beperken tot een kleine set machines of de lading services wilt aggregeren, gebruikt u [Toepassingsgroepen](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png