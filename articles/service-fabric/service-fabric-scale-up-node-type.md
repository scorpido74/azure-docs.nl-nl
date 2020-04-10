---
title: Een Azure Service Fabric-knooppunttype opschalen
description: Meer informatie over het schalen van een cluster van Servicefabric door een virtuele machineschaalset toe te voegen.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 4dbb9e4fbfeb27c5b8b13f70207888cf37bbb0e0
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998927"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Een primair knooppunttype voor Service Fabric-clusters omhoog schalen
In dit artikel wordt beschreven hoe u een primaire knooppunttype servicestructuur opschalen door de bronnen voor virtuele machines te vergroten. Een Service Fabric-cluster is een met het netwerk verbonden set virtuele of fysieke machines waarin uw microservices worden geïmplementeerd en beheerd. Een machine of VM die deel uitmaakt van een cluster wordt een knooppunt genoemd. Virtuele machineschaalsets zijn een Azure-compute resource die u gebruikt om een verzameling virtuele machines als set te implementeren en te beheren. Elk knooppunttype dat is gedefinieerd in een Azure-cluster, is [ingesteld als een afzonderlijke schaalset.](service-fabric-cluster-nodetypes.md) Elk knooppunttype kan vervolgens afzonderlijk worden beheerd. Nadat u een cluster servicestructuur hebt gemaakt, u een clusterknooppunttype verticaal schalen (de bronnen van de knooppunten wijzigen) of het besturingssysteem van het knooppunttype VM's upgraden.  U het cluster op elk gewenst moment schalen, zelfs wanneer workloads op het cluster worden uitgevoerd.  Naarmate het cluster schaalt, schalen uw toepassingen ook automatisch.

> [!WARNING]
> Begin niet met het wijzigen van de vm-sku voor het primaire nodetype als de clusterstatus niet in orde is. Als de clusterstatus niet in orde is, destabiliseert u het cluster alleen verder als u de VM SKU probeert te wijzigen.
>
> We raden u aan de VM SKU van een schaalset/knooppunttype niet te wijzigen, tenzij deze wordt uitgevoerd op [Silver-duurzaamheid of meer.](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Het wijzigen van VM SKU-grootte is een gegevensdestructieve infrastructuurbewerking op plaats van gegevens. Zonder enige mogelijkheid om deze wijziging uit te stellen of te controleren, is het mogelijk dat de bewerking gegevensverlies kan veroorzaken voor stateful services of andere onvoorziene operationele problemen kan veroorzaken, zelfs voor stateloze workloads. Dit betekent dat uw primaire knooppunttype, dat stateful service fabric-systeemservices uitvoert, of een knooppunttype dat uw stateful toepassingswerkbelasting uitvoert.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>De grootte en het besturingssysteem van de VM's van het primaire knooppunttype bijwerken
Hier is het proces voor het bijwerken van de VM-grootte en het besturingssysteem van het primaire knooppunttype VM's.  Na de upgrade zijn de vm's van het primaire knooppunttype standaard D4_V2 en wordt Windows Server 2016 Datacenter with Containers uitgevoerd.

> [!WARNING]
> Voordat u deze procedure in een productiecluster probeert, raden we u aan de voorbeeldsjablonen te bestuderen en het proces te verifiëren tegen een testcluster. Het cluster is ook een tijd lang niet beschikbaar. U geen wijzigingen aanbrengen in meerdere VMSS's die tegelijkertijd als hetzelfde NodeType zijn gedeclareerd; u moet gescheiden implementatiebewerkingen uitvoeren om wijzigingen afzonderlijk toe te passen op elke NodeType VMSS.

1. Implementeer het eerste cluster met twee knooppunttypen en twee schaalsets (één schaalset per knooppunttype) met behulp van deze [voorbeeldsjabloon-](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) en [parametersbestanden.](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json)  Beide schaalsets zijn grootte Standaard D2_V2 en met Windows Server 2012 R2 Datacenter.  Wacht tot het cluster de basislijnupgrade heeft voltooid.   
2. Optioneel: implementeer een stateful sample in het cluster.
3. Nadat u hebt besloten de VM's voor het primaire knooppunttype te upgraden, voegt u een nieuwe schaalset toe aan het primaire knooppunttype met behulp van deze [voorbeeldsjabloon-](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) en [parametersbestanden,](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) zodat het primaire knooppunttype nu twee schaalsets heeft.  Systeemservices en gebruikerstoepassingen kunnen migreren tussen VM's in de twee verschillende schaalsets.  De nieuwe vm's van de schaalset zijn standaardD4_V2 en draaien Windows Server 2016 Datacenter with Containers.  Een nieuwe load balancer en openbaar IP-adres worden ook toegevoegd met de nieuwe schaalset.  
    Als u de nieuwe schaalset in de sjabloon wilt vinden, zoekt u naar de bron 'Microsoft.Compute/virtualMachineScaleSets' die is benoemd door de parameter *vmNodeType2Name.*  De nieuwe schaalset wordt toegevoegd aan het primaire knooppunttype met behulp van de instelling properties->virtualMachineProfile->extensieProfiel->->eigenschappen->-instellingen->-nodeTypeRef.
4. Controleer de clusterstatus en controleer of alle knooppunten in orde zijn.
5. Schakel de knooppunten in de oude schaalset van het primaire knooppunttype uit met de bedoeling om knooppunt te verwijderen. U alles tegelijk uitschakelen en de bewerkingen staan in de wachtrij. Wacht tot alle knooppunten zijn uitgeschakeld, wat enige tijd kan duren.  Als de oudere knooppunten in het knooppunttype zijn uitgeschakeld, migreren de systeemservices en seedknooppunten naar de VM's van de nieuwe schaaldie is ingesteld in het primaire knooppunttype.
6. Verwijder de oudere schaalset uit het primaire knooppunttype. (Nadat de knooppunten zijn uitgeschakeld als in stap 5, wordt in het setblad voor virtuele machineschaal in de Azure-portal de knooppunten van het oude knooppunttype één voor één toegewezen.)
7. Verwijder de load balancer die is gekoppeld aan de oude schaalset. Het cluster is niet beschikbaar, terwijl het nieuwe openbare IP-adres en de load balancer zijn geconfigureerd voor de nieuwe schaalset.  
8. Sla DNS-instellingen op van het openbare IP-adres dat is gekoppeld aan de oude primaire knooppunttypeschaal die is ingesteld in een variabele en verwijder dat openbare IP-adres.
9. Vervang de DNS-instellingen van het openbare IP-adres dat is gekoppeld aan de nieuwe primaire nodetypeschaalset door de DNS-instellingen van het verwijderde openbare IP-adres.  Het cluster is nu weer bereikbaar.
10. Verwijder de knooppuntstatus van de knooppunten uit het cluster.  Als het duurzaamheidsniveau van de oude schaalset zilver of goud was, wordt deze stap automatisch door het systeem uitgevoerd.
11. Als u de stateful-toepassing in een vorige stap hebt geïmplementeerd, controleert u of de toepassing functioneel is.

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
* Meer informatie over het [toevoegen van een knooppunttype aan een cluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Meer informatie over [schaalbaarheid van toepassingen](service-fabric-concepts-scalability.md).
* [Een Azure-cluster in- of uitchecken](service-fabric-tutorial-scale-cluster.md).
* [Schaal een Azure-cluster programmatisch](service-fabric-cluster-programmatic-scaling.md) met de vloeiende Azure compute SDK.
* [Een zelfstandig cluster in- of uitchecken](service-fabric-cluster-windows-server-add-remove-nodes.md).

