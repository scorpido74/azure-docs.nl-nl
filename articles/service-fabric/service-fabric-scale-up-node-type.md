---
title: Een Azure Service Fabric-knooppunt type omhoog schalen | Microsoft Docs
description: Meer informatie over hoe u een Service Fabric cluster kunt schalen door een Schaalset voor virtuele machines toe te voegen.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/13/2019
ms.author: atsenthi
ms.openlocfilehash: 272bc571a0ea71fd6e7bd45a426460d2e0faf1d7
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599296"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Het primaire knooppunt type van een Service Fabric cluster omhoog schalen
In dit artikel wordt beschreven hoe u het primaire knooppunt type van een Service Fabric cluster omhoog kunt schalen door de resources van de virtuele machine te verg Roten. Een Service Fabric cluster is een met het netwerk verbonden reeks virtuele of fysieke machines waarop uw micro services worden geïmplementeerd en beheerd. Een computer of virtuele machine die deel uitmaakt van een cluster, wordt een knoop punt genoemd. Virtuele-machine schaal sets vormen een Azure Compute-resource die u gebruikt om een verzameling virtuele machines als een set te implementeren en te beheren. Elk knooppunt type dat in een Azure-cluster is gedefinieerd, wordt [ingesteld als een afzonderlijke schaalset](service-fabric-cluster-nodetypes.md). Elk knooppunt type kan vervolgens afzonderlijk worden beheerd. Nadat u een Service Fabric cluster hebt gemaakt, kunt u het type van een cluster knooppunt verticaal schalen (de resources van de knoop punten wijzigen) of het besturings systeem van het knooppunt type Vm's bijwerken.  U kunt het cluster op elk gewenst moment schalen, zelfs wanneer werk belastingen op het cluster worden uitgevoerd.  Naarmate het cluster wordt geschaald, worden uw toepassingen ook automatisch geschaald.

> [!WARNING]
> Wijzig de primaire NodeType-VM-SKU niet als de status van het cluster beschadigd is. Als de status van het cluster niet in orde is, kunt u het cluster alleen verder Insta Biel doen als u de VM-SKU probeert te wijzigen.
>
> We raden u aan de VM-SKU van een schaalset/knooppunt type alleen te wijzigen als deze wordt uitgevoerd in een [zilver duurzaamheid of hoger](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Het wijzigen van de SKU-grootte van de VM is een in-place infrastructuur bewerking voor het uitvoeren van gegevens. Zonder enige mogelijkheid om deze wijziging te vertragen of te bewaken, is het mogelijk dat de bewerking gegevens verlies kan veroorzaken voor stateful Services of andere onvoorziene operationele problemen veroorzaakt, zelfs voor stateless workloads. Dit betekent uw primaire knooppunt type, dat wordt uitgevoerd stateful service infrastructuur systeem services, of een type knoop punt waarop de werk belasting van de stateful-toepassing wordt uitgevoerd.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Voer een upgrade uit voor de grootte en het besturings systeem van de virtuele machines van het primaire knoop punt
Hier volgt het proces voor het bijwerken van de VM-grootte en het besturings systeem van de virtuele machines van het primaire knooppunt type.  Na de upgrade zijn de virtuele machines van het primaire knoop punt grootte standaard D4_V2 en wordt Windows Server 2016 Data Center uitgevoerd met containers.

> [!WARNING]
> Voordat u deze procedure uitvoert op een productie cluster, raden we u aan de voorbeeld sjablonen te bestuderen en het proces te controleren op basis van een test cluster. Het cluster is ook niet beschikbaar voor een tijd. U kunt geen wijzigingen aanbrengen in meerdere VMSS die als hetzelfde NodeType worden gedeclareerd, parallel. u moet gescheiden implementatie bewerkingen uitvoeren om wijzigingen toe te passen op elke NodeType VMSS afzonderlijk.

1. Implementeer het eerste cluster met twee knooppunt typen en twee schaal sets (één schaalset per knooppunt type) met behulp van deze voorbeeld [sjabloon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) en [parameter](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) bestanden.  Beide schaal sets hebben de grootte standaard D2_V2 en voeren Windows Server 2012 R2 Data Center uit.  Wacht totdat het cluster de basislijn upgrade heeft voltooid.   
2. Optioneel: Implementeer een stateful voor beeld naar het cluster.
3. Nadat u hebt besloten om een upgrade uit te voeren van het primaire knooppunt type Vm's, voegt u een nieuwe schaalset toe aan het primaire knooppunt type met behulp van deze voorbeeld [sjabloon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) en [parameter](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) bestanden zodat het primaire knooppunt type nu twee schaal sets heeft.  Systeem services en gebruikers toepassingen kunnen migreren tussen virtuele machines in de twee verschillende schaal sets.  De nieuwe virtuele machines van de schaalset hebben de grootte standaard D4_V2 en voeren Windows Server 2016 Data Center uit met containers.  Er wordt ook een nieuwe load balancer en een openbaar IP-adres toegevoegd met de nieuwe schaalset.  
    Als u de nieuwe schaalset in de sjabloon wilt zoeken, zoekt u naar de resource ' micro soft. Compute/virtualMachineScaleSets ' met de para meter *vmNodeType2Name* .  De nieuwe schaalset wordt toegevoegd aan het primaire knooppunt type met behulp van de eigenschappen-> virtualMachineProfile-> extensionProfile-> Extensions-> Eigenschappen-> Instellingen-> nodeTypeRef instelling.
4. Controleer de cluster status en controleer of alle knoop punten in orde zijn.
5. Schakel de knoop punten in de oude schaalset van het primaire knooppunt type uit met het doel om het knoop punt te verwijderen. U kunt alle tegelijk uitschakelen en de bewerkingen worden in de wachtrij geplaatst. Wacht totdat alle knoop punten zijn uitgeschakeld. Dit kan enige tijd duren.  Als de oudere knoop punten in het knooppunt type zijn uitgeschakeld, worden de systeem services en Seed-knoop punten gemigreerd naar de virtuele machines van de nieuwe schaalset in het primaire knooppunt type.
6. Verwijder de oudere schaalset van het primaire knooppunt type.
7. Verwijder het load balancer dat is gekoppeld aan de oude schaalset. Het cluster is niet beschikbaar terwijl het nieuwe open bare IP-adres en load balancer zijn geconfigureerd voor de nieuwe schaalset.  
8. Sla de DNS-instellingen op van het open bare IP-adres dat is gekoppeld aan de oude schaalset van het primaire knoop punt in een variabele en verwijder het open bare IP-adres.
9. Vervang de DNS-instellingen van het open bare IP-adres dat is gekoppeld aan de nieuwe schaalset van het primaire knoop punt door de DNS-instellingen van het verwijderde open bare IP-adres.  Het cluster is nu weer bereikbaar.
10. Verwijder de knooppunt status van de knoop punten uit het cluster.  Als het duurzaamheids niveau van de oude schaalset zilver of goud is, wordt deze stap automatisch door het systeem uitgevoerd.
11. Als u de stateful toepassing in een vorige stap hebt geïmplementeerd, controleert u of de toepassing functioneel is.

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.json" -Verbose

# Connect to the cluster and check the cluster health.
$ClusterName= "sfupgradetest.southcentralus.cloudapp.azure.com:19000"
$thumb="F361720F4BD5449F6F083DDE99DC51A86985B25B"

Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Get-ServiceFabricClusterHealth

# Deploy a new scale set into the primary node type.  Create a new load balancer and public IP address for the new scale set.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.json" -Verbose

# Check the cluster health again. All 15 nodes should be healthy.
Get-ServiceFabricClusterHealth

# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}

Write-Host "Checking node status..."
foreach($name in $nodeNames){
 
    $state = Get-ServiceFabricNode -NodeName $name 

    $loopTimeout = 50

    do{
        Start-Sleep 5
        $loopTimeout -= 1
        $state = Get-ServiceFabricNode -NodeName $name
        Write-Host "$name state: " $state.NodeDeactivationInfo.Status
    }

    while (($state.NodeDeactivationInfo.Status -ne "Completed") -and ($loopTimeout -ne 0))
    

    if ($state.NodeStatus -ne [System.Fabric.Query.NodeStatus]::Disabled)
    {
        Write-Error "$name node deactivation failed with state" $state.NodeStatus
        exit
    }
}

# Remove the scale set
$scaleSetName="NTvm1"
Remove-AzVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP

# Check the cluster health
Get-ServiceFabricClusterHealth

# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [toevoegen van een knooppunt type aan een cluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Meer informatie over [schaal baarheid van toepassingen](service-fabric-concepts-scalability.md).
* [Een Azure-cluster in-of uitschalen](service-fabric-tutorial-scale-cluster.md).
* [Schaal een Azure-cluster programmatisch](service-fabric-cluster-programmatic-scaling.md) met behulp van de Fluent Azure Compute SDK.
* [Een zelfstandige cluster in-of uitschalen](service-fabric-cluster-windows-server-add-remove-nodes.md).

