---
title: Een cluster servicestructuur in- of uitschalen
description: Schaal een cluster servicestructuur in of uit om aan de vraag te voldoen door regels voor automatische schaal in te stellen voor elke nodetype/virtuele machineschaalset. Knooppunten toevoegen of verwijderen aan een cluster van servicefabric
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 26ef13f38d525e4e493ad933bfb906dd36ed0070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258731"
---
# <a name="scale-a-cluster-in-or-out"></a>Een cluster in- of uitschalen

> [!WARNING]
> Lees deze sectie voordat u schaalt

Voor het schalen van compute resources om de werkbelasting van uw toepassing te sourcen, is een opzettelijke planning vereist, duurt het bijna altijd langer dan een uur om te voltooien voor een productieomgeving en moet u uw werkbelasting en zakelijke context begrijpen; Als u deze activiteit nog nooit eerder hebt gedaan, wordt u aangeraden om te beginnen met het lezen en begrijpen van overwegingen voor het plannen van [servicefabric-clusterstructuren,](service-fabric-cluster-capacity.md)voordat u de rest van dit document voortzet. Deze aanbeveling is om onbedoelde LiveSite-problemen te voorkomen en het wordt ook aanbevolen om de bewerkingen die u besluit uit te voeren te testen tegen een niet-productieomgeving. U op elk moment [productieproblemen melden of betaalde ondersteuning aanvragen voor Azure.](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure) Voor technici die zijn toegewezen om deze bewerkingen uit te voeren die de juiste context hebben, wordt in dit artikel schaalbewerkingen beschreven, maar u moet beslissen en begrijpen welke bewerkingen geschikt zijn voor uw use case; zoals welke resources moeten worden geschaald (CPU, opslag, geheugen), welke richting moet worden geschaald (verticaal of horizontaal) en welke bewerkingen moeten worden uitgevoerd (implementatie van resourcesjablonen, portal, PowerShell/CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Een cluster servicestructuur in- of uitschalen met regels voor automatische schaal of handmatig
Virtuele machineschaalsets zijn een Azure-compute resource die u gebruiken om een verzameling virtuele machines als set te implementeren en te beheren. Elk knooppunttype dat is gedefinieerd in een cluster servicestructuur, is ingesteld als een afzonderlijke virtuele machineschaalset. Elk knooppunttype kan vervolgens onafhankelijk in- of uitschalen, verschillende sets poorten openen en verschillende capaciteitsstatistieken hebben. Lees er meer over in het document [met het document met het servicestructuurknooppunt.](service-fabric-cluster-nodetypes.md) Aangezien de typen Service Fabric-knooppuntin uw cluster zijn gemaakt van virtuele machineschaalsets op de backend, moet u regels voor automatische schaal instellen voor elk knooppunttype/virtuele machineschaalset.

> [!NOTE]
> Uw abonnement moet voldoende cores hebben om de nieuwe VM's toe te voegen die deel uitmaken van dit cluster. Er is momenteel geen modelvalidatie, dus u krijgt een implementatietijdfout als een van de quotumlimieten wordt bereikt. Ook kan een enkel knooppunttype niet zomaar meer dan 100 knooppunten per VMSS overschrijden. Het kan nodig zijn om VMSS's toe te voegen om de beoogde schaal te bereiken, en automatisch schalen kan niet automatisch VMSS'en toevoegen. Het toevoegen van vmss's in-place aan een live cluster is een uitdagende taak, en vaak resulteert dit in gebruikers het inrichten van nieuwe clusters met de juiste knooppunttypen die zijn ingericht bij het maken van de tijd; [clustercapaciteit](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) dienovereenkomstig plannen. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Kies het knooppunttype/Virtuele machine-schaal die is ingesteld op schaal
Op dit moment u de regels voor automatische schaal voor virtuele machineschaalsets niet opgeven met behulp van de portal om een Service Fabric-cluster te maken, dus laten we Azure PowerShell (1.0+) gebruiken om de knooppunttypen weer te geven en er vervolgens regels voor automatisch schalen aan toe te voegen.

Voer de volgende cmdlets uit om de lijst met virtuele machineschaalsets te krijgen die deel uitmaken van uw cluster:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Regels voor automatische schaal instellen voor de nodetype/virtuele machineschaalset
Als uw cluster meerdere knooppunttypen heeft, herhaalt u dit vervolgens voor elke nodetypen/virtuele machineschaalsets die u wilt schalen (in of uit). Bedenk eerst hoeveel knooppunten u moet hebben, voordat u automatisch schalen instelt. Het minimum aantal knooppunten dat u nodig hebt voor het primaire knooppunttype, wordt bepaald door het betrouwbaarheidsniveau dat u hebt gekozen. Lees meer over [betrouwbaarheidsniveaus.](service-fabric-cluster-capacity.md)

> [!NOTE]
> Door het primaire knooppunttype af te schalen tot minder dan het minimumaantal, wordt het cluster instabiel of omlaag getrenkt. Dit kan leiden tot gegevensverlies voor uw toepassingen en voor de systeemservices.
> 
> 

Momenteel wordt de functie automatisch schalen niet aangestuurd door de belastingen die uw toepassingen mogelijk rapporteren aan Service Fabric. Dus op dit moment de auto-schaal die je krijgt is puur gedreven door de prestaties tellers die worden uitgezonden door elk van de virtuele machine schaal set exemplaren.  

Volg deze instructies [om automatisch schalen in te stellen voor elke virtuele machineschaalset.](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)

> [!NOTE]
> In een schaalscenario moet u de [cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) met de juiste knooppuntnaam aanroepen, tenzij uw knooppunttype een [duurzaamheidsniveau][durability] van goud of zilver heeft. Voor de Bronze duurzaamheid is het niet aan te raden om meer dan één knooppunt tegelijk af te schalen.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>VM's handmatig toevoegen aan een knooppunttype/virtuele machineschaalset

Wanneer u uitschaalt, voegt u meer instanties van de virtuele machine toe aan de schaalset. Deze instanties worden de knooppunten die door Service Fabric worden gebruikt. Service Fabric weet wanneer er meer exemplaren aan de schaalset zijn toegevoegd (door uitschaling) en reageert daar automatisch op. 

> [!NOTE]
> Het toevoegen van VM's kost tijd, dus verwacht niet dat de toevoegingen ogenblikkelijk zijn. Dus van plan om capaciteit toe te voegen ruim van tevoren, om meer dan 10 minuten voordat de VM-capaciteit beschikbaar is voor de replica's / service-exemplaren te krijgen geplaatst.
> 

### <a name="add-vms-using-a-template"></a>VM's toevoegen met een sjabloon
Volg het voorbeeld/de instructies in de [galerie met snelbeginsjablonen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) om het aantal VM's in elk knooppunttype te wijzigen. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>VM's toevoegen met PowerShell- of CLI-opdrachten
Met de volgende code verkrijgt u een schaalset op naam en verhoogt u de **capaciteit** van de schaalset met 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Met deze code stelt u de capaciteit in op 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>VM's handmatig verwijderen uit een knooppunttype/virtuele machineschaalset
Wanneer u een knooppunttype schaalt, verwijdert u VM-exemplaren uit de schaalset. Als het knooppunttype het duurzaamheidsniveau van Brons is, weet Service Fabric niet wat er is gebeurd en wordt gemeld dat er een knooppunt is verdwenen. Service Fabric rapporteert daarom een onjuiste status voor het cluster. Als u die slechte status wilt voorkomen, moet u het knooppunt expliciet uit het cluster verwijderen en de knooppuntstatus verwijderen.

De servicefabricsysteemservices worden uitgevoerd in het primaire knooppunttype in uw cluster. Wanneer u het primaire knooppunttype afschaalt, moet u het aantal exemplaren nooit verkleinen tot minder dan wat de [betrouwbaarheidslaag](service-fabric-cluster-capacity.md) rechtvaardigt. 
 
Voor een stateful service hebt u een bepaald aantal knooppunten nodig om altijd beschikbaar te zijn en de status van uw service te behouden. Op zijn minst hebt u het aantal knooppunten nodig dat gelijk is aan het aantal doelreplica's van de partitie/service.

### <a name="remove-the-service-fabric-node"></a>Het Service Fabric-knooppunt verwijderen

De stappen voor het handmatig verwijderen van de knooppuntstatus zijn alleen van toepassing op knooppunttypen met *een* bronzeduurzaamheidslaag.  Voor *silver* en *gold* duurzaamheidslaag worden deze stappen automatisch uitgevoerd door het platform. Voor meer informatie over duurzaamheid raadpleegt u [Service Fabric cluster capacity planning][durability] (Capaciteitsplanning voor Service Fabric-clusters).

Als u de knooppunten van het cluster gelijkmatig verdeeld wilt houden in upgrade- en foutdomeinen en op die manier gelijkmatig gebruik wilt inschakelen, moet het meest recent gemaakte knooppunt eerst worden verwijderd. Met andere woorden - de knooppunten moeten op basis van 'last in, first out' worden verwijderd. Het meest recent gemaakte knooppunt heeft de hoogste `virtual machine scale set InstanceId`-eigenschapswaarde. Met de onderstaande codevoorbeelden wordt het meest recent gemaakte knooppunt geretourneerd.

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```shell
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Het Service Fabric-cluster moet weten dat dit knooppunt zal worden verwijderd. Er zijn drie stappen die u moet uitvoeren:

1. Schakel het knooppunt uit zodat het geen replica meer is voor gegevens.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Stop het knooppunt zodat de Service Fabric-runtime netjes wordt afgesloten en uw app een beëindigingsaanvraag ontvangt.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Verwijder het knooppunt uit het cluster.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

Nadat deze drie stappen op het knooppunt zijn toegepast, kan het knooppunt worden verwijderd uit de schaalset. Als u naast de [bronzen][durability] duurzaamheidslaag nog een andere duurzaamheidslaag gebruikt, worden deze stappen voor u uitgevoerd wanneer de schaalsetinstantie wordt verwijderd.

In het volgende codeblok wordt het laatst gemaakte knooppunt opgehaald en wordt het knooppunt uitgeschakeld, gestopt en uit het cluster verwijderd.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

In onderstaande **sfctl**-code wordt de volgende opdracht gebruikt om de waarde van **knooppuntnaam** op te halen van het laatst gemaakte knooppunt: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```shell
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Gebruik de volgende **sfctl**-query's om de status van elke stap te controleren
>
> **Deactiveringsstatus controleren**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Stopstatus controleren**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>De schaalset inschalen

Nu het Service Fabric-knooppunt is verwijderd uit het cluster, kan de virtuele-machineschaalset worden ingeschaald. In onderstaand voorbeeld wordt de capaciteit van de schaalset verminderd met 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Met deze code stelt u de capaciteit in op 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Gedragingen die u observeren in Service Fabric Explorer
Wanneer u een cluster opschaalt, geeft de Service Fabric Explorer het aantal knooppunten weer (exemplaren van de virtuele machineschaalset) die deel uitmaken van het cluster.  Wanneer u echter een cluster schaalt, ziet u de verwijderde knooppunt/VM-instantie in een ongezonde staat worden weergegeven, tenzij u [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) met de juiste knooppuntnaam aanroept.   

Hier is de verklaring voor dit gedrag.

De knooppunten in Service Fabric Explorer zijn een weerspiegeling van wat de Service Fabric-systeemservices (FM specifiek) weet over het aantal knooppunten dat het cluster had/heeft. Wanneer u de ingestelde virtuele machineschaal schaalt, is de VM verwijderd, maar fm-systeemservice denkt nog steeds dat het knooppunt (dat is toegewezen aan de VM die is verwijderd) terugkomt. Service Fabric Explorer blijft dus dat knooppunt weergeven (hoewel de status fout of onbekend kan zijn).

Om ervoor te zorgen dat een knooppunt wordt verwijderd wanneer een virtuele machine wordt verwijderd, hebt u twee opties:

1. Kies een duurzaamheidsniveau van Goud of Zilver voor de knooppunttypen in uw cluster, waardoor u de infrastructuurintegratie krijgt. Die zal dan automatisch verwijderen van de knooppunten uit onze systeemdiensten (FM) staat wanneer u naar beneden schalen.
Raadpleeg [hier de details over duurzaamheidsniveaus](service-fabric-cluster-capacity.md)

2. Zodra de VM-instantie is verkleind, moet u de [cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate)aanroepen.

> [!NOTE]
> Servicefabricclusters vereisen dat een bepaald aantal knooppunten altijd hoger is om de beschikbaarheid te behouden en de status te behouden - aangeduid als 'quorum behouden'. Het is dus meestal onveilig om alle machines in het cluster uit te schakelen, tenzij u eerst een [volledige back-up van uw status](service-fabric-reliable-services-backup-restore.md)hebt uitgevoerd.
> 
> 

## <a name="next-steps"></a>Volgende stappen
Lees het volgende om ook meer te weten te komen over het plannen van clustercapaciteit, het upgraden van een cluster en partitioneringsservices:

* [Uw clustercapaciteit plannen](service-fabric-cluster-capacity.md)
* [Clusterupgrades](service-fabric-cluster-upgrade.md)
* [Partitiestateful services voor maximale schaal](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
