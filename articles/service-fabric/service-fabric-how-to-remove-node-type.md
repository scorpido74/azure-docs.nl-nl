---
title: Een knooppunt type verwijderen in azure Service Fabric
description: Meer informatie over het verwijderen van een knooppunt type van een Service Fabric cluster dat in azure wordt uitgevoerd.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: f3dc3210fdb436038174bb8d9347424f14d3faa3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464508"
---
# <a name="remove-a-service-fabric-node-type"></a>Een Service Fabric knooppunt type verwijderen
In dit artikel wordt beschreven hoe u een Azure Service Fabric cluster kunt schalen door een bestaand knooppunt type uit een cluster te verwijderen. Een Service Fabric cluster is een met het netwerk verbonden reeks virtuele of fysieke machines waarop uw micro services worden geïmplementeerd en beheerd. Een computer of virtuele machine die deel uitmaakt van een cluster, wordt een knoop punt genoemd. Virtuele-machine schaal sets vormen een Azure Compute-resource die u gebruikt om een verzameling virtuele machines als een set te implementeren en te beheren. Elk knooppunt type dat in een Azure-cluster is gedefinieerd, wordt [ingesteld als een afzonderlijke schaalset](service-fabric-cluster-nodetypes.md). Elk knooppunt type kan vervolgens afzonderlijk worden beheerd. Nadat u een Service Fabric cluster hebt gemaakt, kunt u horizon taal een cluster schalen door een knooppunt type (virtuele-machine schaalset) en alle knoop punten te verwijderen.  U kunt het cluster op elk gewenst moment schalen, zelfs wanneer werk belastingen op het cluster worden uitgevoerd.  Naarmate het cluster wordt geschaald, worden uw toepassingen ook automatisch geschaald.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Gebruik [Remove-AzServiceFabricNodeType](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype) om een service Fabric knooppunt type te verwijderen.

De drie bewerkingen die optreden wanneer Remove-AzServiceFabricNodeType wordt aangeroepen, zijn:
1.  De virtuele-machine schaalset achter het knooppunt type wordt verwijderd.
2.  Het knooppunt type wordt uit het cluster verwijderd.
3.  Voor elk knoop punt in dat knooppunt type wordt de hele status voor dat knoop punt uit het systeem verwijderd. Als er services op dat knoop punt zijn, worden de services voor het eerst naar een ander knoop punt verplaatst. Als cluster beheer geen knoop punt voor de replica/service kan vinden, wordt de bewerking uitgesteld/geblokkeerd.

> [!WARNING]
> Het gebruik van Remove-AzServiceFabricNodeType om een knooppunt type van een productie cluster te verwijderen, wordt niet aanbevolen om regel matig te worden gebruikt. Het is een gevaarlijke opdracht, omdat hiermee de bron van de virtuele-machine schaalset wordt verwijderd achter het knooppunt type. 

## <a name="durability-characteristics"></a>Duurzaamheids kenmerken
De veiligheid heeft de prioriteit van de snelheid bij het gebruik van Remove-AzServiceFabricNodeType. Het knooppunt type moet zilver of goud- [duurzaamheids niveau](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)zijn. de reden hiervoor is:
- Bronzen geeft u geen garanties over het opslaan van status informatie.
- Met Silver en Gold duurzaamheid worden wijzigingen in de schaalset onderschept.
- Daarnaast biedt Gold u de controle over de updates van Azure onder de schaalset.

Met Service Fabric worden onderliggende wijzigingen en updates gecoördineerd zodat er geen gegevens verloren gaan. Wanneer u echter een knoop punt met Bronze duurzaamheid verwijdert, kunt u status gegevens verliezen. Als u een primair knooppunt type verwijdert en de toepassing stateless is, is Bronze acceptabel. Wanneer u stateful workloads in productie uitvoert, moet de minimale configuratie zilver zijn. Voor productie scenario's geldt dat het primaire knooppunt type altijd zilver of goud moet zijn.

### <a name="more-about-bronze-durability"></a>Meer informatie over bronzen duurzaamheid

Bij het verwijderen van een type knoop punt dat bronzen, worden alle knoop punten in het knooppunt type onmiddellijk doorlopend. Service Fabric onderschept geen updates voor een schaalset van Bronze-knoop punten, waardoor alle Vm's onmiddellijk beschikbaar zijn. Als u iets op deze knoop punten hebt, gaan de gegevens verloren. Nu, zelfs als u stateless was, worden alle knoop punten in de Service Fabric deel nemen aan de ring, dus kan een hele groep verloren gaan, waardoor het cluster zelf kan worden overschreven.

## <a name="recommended-node-type-removal-process"></a>Proces voor het verwijderen van het aanbevolen knooppunt type

Als u het knooppunt type wilt verwijderen, voert u de cmdlet [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) uit.  Het duurt enige tijd voordat de cmdlet is voltooid.  Zodra alle Vm's zijn verdwenen (weer gegeven als ' omlaag '), wordt in de fabric:/System/InfrastructureService/[nodetypenaam] een fout status weer gegeven.

```powershell
$groupname = "mynodetype"
$nodetype = "nt2vm"
$clustername = "mytestcluster"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mytestcluster.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

Vervolgens kunt u de cluster bron bijwerken om het knooppunt type te verwijderen. U kunt de implementatie van de ARM-sjabloon gebruiken of de cluster bron bewerken via [Azure Resource Manager](https://resources.azure.com). Hiermee start u een cluster upgrade waarmee de service Fabric:/System/InfrastructureService/[nodeType name] met de fout status wordt verwijderd.

De knoop punten zijn nog steeds zichtbaar in de Service Fabric Explorer. Voer [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) uit op elk van de knoop punten die u wilt verwijderen.


```powershell
$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [kenmerken](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)van de cluster duurzaamheid.
- Meer informatie over [knooppunt typen en virtual machine Scale sets](service-fabric-cluster-nodetypes.md).
- Meer informatie over het [schalen van service Fabric cluster](service-fabric-cluster-scaling.md).
