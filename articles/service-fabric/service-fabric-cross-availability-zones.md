---
title: Een cluster implementeren in beschikbaarheidszones
description: Meer informatie over het maken van een Azure Service Fabric-cluster in beschikbaarheidszones.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 6da9517f822c9c157d26a1bda8dab2c694b08b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609975"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Een Azure Service Fabric-cluster implementeren in beschikbaarheidszones
Beschikbaarheidszones in Azure is een aanbieding met hoge beschikbaarheid die uw toepassingen en gegevens beschermt tegen datacenterfouten. Een beschikbaarheidszone is een unieke fysieke locatie die is uitgerust met onafhankelijke stroom- en koelingszone binnen een Azure-regio.

Service Fabric ondersteunt clusters die zich uitstrekken over beschikbaarheidszones door knooppunttypen te implementeren die zijn vastgemaakt aan specifieke zones. Dit zorgt voor een hoge beschikbaarheid van uw applicaties. Azure Availability Zones zijn alleen beschikbaar in bepaalde regio's. Zie [Overzicht van Azure Availability Zones voor](https://docs.microsoft.com/azure/availability-zones/az-overview)meer informatie.

Voorbeeldsjablonen zijn beschikbaar: [sjabloon voor beschikbaarheidszone voor servicefabric](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Aanbevolen topologie voor primaire knooppunttypen Azure Service Fabric-clusters verspreid over beschikbaarheidszones
Een cluster servicestructuur verdeeld over beschikbaarheidszones zorgt voor een hoge beschikbaarheid van de clusterstatus. Als u een cluster servicestructuur over verschillende zones wilt overspannen, moet u een primair knooppunttype maken in elke beschikbaarheidszone die door de regio wordt ondersteund. Dit zal zaadknooppunten gelijkmatig verdelen over elk van de primaire knooppunttypen.

De aanbevolen topologie voor het primaire knooppunttype vereist de onderstaande resources:

* Het betrouwbaarheidsniveau van het cluster is ingesteld op Platinum.
* Drie knooppunttypen gemarkeerd als primair.
    * Elk knooppunttype moet worden toegewezen aan zijn eigen virtuele machineschaalset in verschillende zones.
    * Elke virtuele machineschaalset moet ten minste vijf knooppunten (Silver Sustainability) hebben.
* Eén openbare IP-bron met standaard SKU.
* Een single load balancer-bron met standaard SKU.
* Een NSG waarnaar wordt verwezen door het subnet waarin u uw virtuele machineschaalsets implementeert.

>[!NOTE]
> De eigenschap van de virtuele machineschaalset set single placement group moet worden ingesteld op true, omdat Service Fabric geen ondersteuning biedt voor één virtuele machineschaalset die zones overspant.

 ![Beschikbaarheidszonearchitectuur azure-servicestructuur][sf-architecture]

## <a name="networking-requirements"></a>Netwerkvereisten
### <a name="public-ip-and-load-balancer-resource"></a>Bron voor openbare IP en load balancer
Om de eigenschap zones op een resource met virtuele machineschaalset in te schakelen, moeten de load balancer en IP-resource waarnaar wordt verwezen door die virtuele machineschaalset beide een *Standaard* SKU gebruiken. Als u een load balancer- of IP-bron maakt zonder de eigenschap SKU, wordt een BasisSKU gemaakt, die beschikbaarheidszones niet ondersteunt. Een standaard SKU-load balancer blokkeert standaard al het verkeer van buitenaf; om het verkeer van buitenaf mogelijk te maken, moet een NSG worden ingezet op het subnet.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> Het is niet mogelijk om een in-place verandering van SKU op de openbare IP en load balancer middelen uit te voeren. Als u migreert uit bestaande bronnen met een Basis-SKU, raadpleegt u het migratiegedeelte van dit artikel.

### <a name="virtual-machine-scale-set-nat-rules"></a>Nat-regels voor virtuele machineschaal instellen
De inkomende NAT-regels voor de load balancer moeten overeenkomen met de NAT-pools van de virtuele machineschaalset. Elke virtuele machineschaalset moet een unieke inkomende NAT-pool hebben.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Uitgaande regels voor standaard SKU Load Balancer
Standard Load Balancer en Standard Public IP introduceren nieuwe vaardigheden en verschillende gedragingen voor uitgaande connectiviteit in vergelijking met het gebruik van Basic SKU's. Als u uitgaande connectiviteit wilt wanneer u met standaard SKU's werkt, moet u deze expliciet definiëren met standaard IP-adressen voor openbare apparaten of met standaardopenbare load balancer. Zie [Uitgaande verbindingen](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) en Azure Standard [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)voor meer informatie.

>[!NOTE]
> De standaardsjabloon verwijst naar een NSG waarmee standaard al het uitgaande verkeer kan worden weergegeven. Binnenkomend verkeer is beperkt tot de poorten die nodig zijn voor servicefabricbeheerbewerkingen. De NSG-regels kunnen worden aangepast om aan uw eisen te voldoen.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Zones inschakelen op een virtuele machineschaalset
Als u een zone wilt inschakelen, moet u op een virtuele machineschaalset de volgende drie waarden opnemen in de bron voor de virtuele machineschaalset.

* De eerste waarde is de eigenschap **zones,** die aangeeft op welke beschikbaarheidszone de virtuele machineschaalset wordt geïmplementeerd.
* De tweede waarde is de eigenschap "singlePlacementGroup", die moet worden ingesteld op true.
* De derde waarde is de eigenschap "faultDomainOverride" in de extensie van de virtuele machineschaalvan de Service Fabric. De waarde voor deze eigenschap moet de regio en zone omvatten waarin deze virtuele machineschaalset wordt geplaatst. Voorbeeld: 'faultDomainOverride': 'eastus/az1' Alle resources voor het instellen van virtuele machines moeten in dezelfde regio worden geplaatst omdat Azure Service Fabric-clusters geen ondersteuning voor verschillende regio's hebben.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric.Test",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "eastus/az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Meerdere primaire knooppunttypen inschakelen in de bron van het cluster servicestructuur
Als u een of meer knooppunttypen wilt instellen als primair in een clusterbron, stelt u de eigenschap 'isPrimair' in op 'true'. Wanneer u een cluster servicestructuur implementeert in beschikbaarheidszones, moet u drie knooppunttypen in verschillende zones hebben.

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migreren naar het gebruik van beschikbaarheidszones uit een cluster met behulp van een Basic SKU Load Balancer en een Basic SKU IP
Als u een cluster wilt migreren, dat een Load Balancer en IP met een basis-SKU gebruikte, moet u eerst een geheel nieuwe Load Balancer- en IP-bron maken met behulp van de standaard SKU. Het is niet mogelijk om deze resources op hun plaats bij te werken.

De nieuwe LB en IP moet worden verwezen in de nieuwe cross Availability Zone node types die u wilt gebruiken. In het bovenstaande voorbeeld zijn drie nieuwe bronnen voor virtuele machineschaalsets toegevoegd in zones 1,2 en 3. Deze virtuele machineschaalsets verwijzen naar de nieuw gemaakte LB en IP en zijn gemarkeerd als primaire knooppunttypen in de clusterbron servicestructuur.

Om te beginnen moet u de nieuwe resources toevoegen aan uw bestaande resourcemanagersjabloon. Deze bronnen omvatten:
* Een openbare IP-bron met standaard SKU.
* Een Load Balancer-bron met standaard SKU.
* Een NSG waarnaar wordt verwezen door het subnet waarin u uw virtuele machineschaalsets implementeert.
* Drie knooppunttypen gemarkeerd als primair.
    * Elk knooppunttype moet worden toegewezen aan de eigen virtuele machineschaalset in verschillende zones.
    * Elke virtuele machineschaalset moet ten minste vijf knooppunten (Silver Sustainability) hebben.

Een voorbeeld van deze bronnen is te vinden in de [voorbeeldsjabloon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Zodra de resources zijn geïmplementeerd, u beginnen met het uitschakelen van de knooppunten in het primaire knooppunttype van het oorspronkelijke cluster. Als de knooppunten zijn uitgeschakeld, migreren de systeemservices naar het nieuwe primaire knooppunttype dat in de bovenstaande stap is geïmplementeerd.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Zodra de knooppunten allemaal zijn uitgeschakeld, worden de systeemservices uitgevoerd op het primaire knooppunttype, dat is verspreid over zones. Vervolgens u de uitgeschakelde knooppunten uit het cluster verwijderen. Zodra de knooppunten zijn verwijderd, u de oorspronkelijke bronnen voor ip-, load balancer en virtuele machineschaal verwijderen.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

U moet vervolgens de verwijzingen naar deze bronnen verwijderen uit de sjabloon Resourcemanager die u hebt geïmplementeerd.

De laatste stap omvat het bijwerken van de DNS-naam en openbare IP.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
