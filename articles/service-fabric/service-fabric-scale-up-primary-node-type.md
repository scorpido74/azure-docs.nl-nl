---
title: Een Azure Service Fabric primaire knooppunt type omhoog schalen
description: Meer informatie over het schalen van een Service Fabric cluster door een knooppunt type toe te voegen.
ms.topic: article
ms.date: 08/06/2020
ms.author: pepogors
ms.openlocfilehash: a18a40cc9e467b089ea9d6be3d0ca81a21d2c474
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228712"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type-by-adding-a-node-type"></a>Het primaire knooppunt type van een Service Fabric cluster omhoog schalen door een knooppunt type toe te voegen
In dit artikel wordt beschreven hoe u het primaire knooppunt type van een Service Fabric cluster omhoog kunt schalen door een extra knooppunt type aan het cluster toe te voegen. Een Service Fabric-cluster is een met het netwerk verbonden reeks virtuele of fysieke machines waarop uw microservices worden geïmplementeerd en beheerd. Een computer of virtuele machine die deel uitmaakt van een cluster, wordt een knoop punt genoemd. Virtuele-machine schaal sets vormen een Azure Compute-resource die u gebruikt om een verzameling virtuele machines als een set te implementeren en te beheren. Elk knooppunt type dat in een Azure-cluster is gedefinieerd, wordt [ingesteld als een afzonderlijke schaalset](service-fabric-cluster-nodetypes.md). Elk knooppunttype kan vervolgens afzonderlijk worden beheerd.

De voorbeeld sjablonen in de volgende zelf studie vindt u hier: voor beelden van schalen van het [primaire knooppunt type service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Primary-NodeType-Scaling-Sample)

> [!WARNING]
> Probeer niet een procedure van een primair knooppunt type scale up uit te voeren als de cluster status niet in orde is, omdat hierdoor het cluster alleen verder wordt beschadigd.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type"></a>Proces voor het bijwerken van de grootte en het besturings systeem van het primaire knooppunt type
Hier volgt het proces voor het bijwerken van de VM-grootte en het besturings systeem van de virtuele machines van het primaire knooppunt type.  Na de upgrade zijn de virtuele machines van het primaire knoop punt grootte standaard D4_V2 en worden Windows Server 2019 Data Center uitgevoerd met containers.

> [!WARNING]
> Voordat u deze procedure uitvoert op een productie cluster, raden we u aan de voorbeeld sjablonen te bestuderen en het proces te controleren op basis van een test cluster. Het cluster kan ook korte tijd niet beschikbaar zijn. 

### <a name="deploy-the-initial-service-fabric-cluster"></a>Het eerste Service Fabric-cluster implementeren 
Als u samen met het voor beeld wilt volgen, implementeert u het eerste cluster met één primair knooppunt type en een enkele schaalset [service Fabric-eerste cluster](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-1.json). U kunt deze stap overs Laan als u al een bestaand Service Fabric cluster hebt geïmplementeerd. 

1. Meld u aan bij uw Azure-account. 
```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```
2. Een nieuwe resourcegroep maken. 
```powershell
# create a resource group for your cluster deployment
$resourceGroupName = "myResourceGroup"
$location = "WestUS"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location
```
3. Vul de parameter waarden in de sjabloon bestanden in. 
4. Implementeer het cluster in de resource groep die u in stap 2 hebt gemaakt. 
```powershell
# deploy the template files to the resource group created above
$templateFilePath = "C:\AzureDeploy-1.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath
```

### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Een nieuw type primair knoop punt toevoegen aan het cluster
> [!Note]
> De resources die in de volgende stappen zijn gemaakt, worden het nieuwe primaire knooppunt type in het cluster zodra de schaal bewerking is voltooid. Zorg ervoor dat u namen gebruikt die uniek zijn van het eerste subnet, het open bare IP-adres, het Load Balancer, de Schaalset voor virtuele machines en het knooppunt type. 

U kunt een sjabloon vinden met de volgende stappen: [service Fabric-nieuw knooppunt type cluster](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-2.json). De volgende stappen bevatten gedeeltelijke resource fragmenten die de wijzigingen in de nieuwe resources markeren.  

1. Maak een nieuw subnet in uw bestaande Virtual Network.
```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```
2. Maak een nieuwe open bare IP-resource met een unieke domeinnaam label. 
```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt2')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```
3. Maak een nieuwe Load Balancer resource die afhankelijk is van het open bare IP-adres dat hierboven is gemaakt. 
```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```
4. Maak een nieuwe Schaalset voor virtuele machines die gebruikmaakt van de nieuwe VM-SKU en de OS-SKU die u naar wens wilt schalen. 

Verwijzing van knooppunt type 
```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

VM-SKU
```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

SKU VAN BESTURINGS SYSTEEM 
```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

Het volgende code fragment is een voor beeld van een nieuwe resource voor de Schaalset van virtuele machines die wordt gebruikt voor het maken van een nieuw knooppunt type voor een Service Fabric cluster. U moet ervoor zorgen dat u aanvullende uitbrei dingen opneemt die vereist zijn voor uw werk belasting. 

```json
    {
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeType1Name')]",
      "location": "[variables('computeLocation')]",
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType1Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
      ],
      "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Automatic"
        },
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
              {
                "name": "[concat('ServiceFabricNodeVmExt_',variables('vmNodeType1Name'))]",
                "properties": {
                  "type": "ServiceFabricNode",
                  "autoUpgradeMinorVersion": true,
                  "protectedSettings": {
                    "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                    "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                  },
                  "publisher": "Microsoft.Azure.ServiceFabric",
                  "settings": {
                    "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                    "nodeTypeRef": "[variables('vmNodeType1Name')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "enableParallelJobs": true,
                    "nicPrefixOverride": "[variables('subnet1Prefix')]",
                    "certificate": {
                      "thumbprint": "[parameters('certificateThumbprint')]",
                      "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.0"
                }
              }
            ]
          },
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[concat(variables('nicName'), '-1')]",
                "properties": {
                  "ipConfigurations": [
                    {
                      "name": "[concat(variables('nicName'),'-',1)]",
                      "properties": {
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[variables('lbPoolID1')]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[variables('lbNatPoolID1')]"
                          }
                        ],
                        "subnet": {
                          "id": "[variables('subnet1Ref')]"
                        }
                      }
                    }
                  ],
                  "primary": true
                }
              }
            ]
          },
          "osProfile": {
            "adminPassword": "[parameters('adminPassword')]",
            "adminUsername": "[parameters('adminUsername')]",
            "computernamePrefix": "[variables('vmNodeType1Name')]",
            "secrets": [
              {
                "sourceVault": {
                  "id": "[parameters('sourceVaultValue')]"
                },
                "vaultCertificates": [
                  {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('certificateUrlValue')]"
                  }
                ]
              }
            ]
          },
          "storageProfile": {
            "imageReference": {
              "publisher": "[parameters('vmImagePublisher1')]",
              "offer": "[parameters('vmImageOffer1')]",
              "sku": "[parameters('vmImageSku1')]",
              "version": "[parameters('vmImageVersion1')]"
            },
            "osDisk": {
              "caching": "ReadOnly",
              "createOption": "FromImage",
              "managedDisk": {
                "storageAccountType": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      },
      "sku": {
        "name": "[parameters('vmNodeType1Size')]",
        "capacity": "[parameters('nt1InstanceCount')]",
        "tier": "Standard"
      },
      "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
      }
    },

```

5. Voeg een nieuw knooppunt type toe aan het cluster, dat verwijst naar de Schaalset voor virtuele machines die hierboven is gemaakt. De eigenschap **isPrimary** van dit type knoop punt moet worden ingesteld op True. 
```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```
6. Implementeer de bijgewerkte ARM-sjabloon. 
```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-2.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

Het Service Fabric cluster heeft nu twee knooppunt typen wanneer de implementatie is voltooid. 

### <a name="remove-the-existing-node-type"></a>Het bestaande knooppunt type verwijderen 
Zodra de implementatie van de resources is voltooid, kunt u beginnen met het uitschakelen van de knoop punten in het oorspronkelijke primaire knooppunt type. Wanneer de knoop punten zijn uitgeschakeld, worden de systeem services gemigreerd naar het nieuwe primaire knooppunt type dat in de bovenstaande stap is geïmplementeerd.

1. Stel de eigenschap primair knooppunt type in de Service Fabric cluster resource in op false. 
```json
{
    "name": "[variables('vmNodeType0Name')]",
    "applicationPorts": {
        "endPort": "[variables('nt0applicationEndPort')]",
        "startPort": "[variables('nt0applicationStartPort')]"
    },
    "clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
    "durabilityLevel": "Bronze",
    "ephemeralPorts": {
        "endPort": "[variables('nt0ephemeralEndPort')]",
        "startPort": "[variables('nt0ephemeralStartPort')]"
    },
    "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
    "isPrimary": false,
    "reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
    "vmInstanceCount": "[parameters('nt0InstanceCount')]"
}
```
2. Implementeer de sjabloon met de bijgewerkte eigenschap isPrimary voor het oorspronkelijke knooppunt type. U vindt hier een sjabloon waarvan de primaire vlag is ingesteld op False voor het oorspronkelijke knooppunt type: [service Fabric-primair knooppunt type onwaar](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-3.json).

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-3.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

3. Schakel de knoop punten in het knooppunt type 0. 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterConnectionEndpoint `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"


$nodeType = "nt1vm" # specify the name of node type
$nodes = Get-ServiceFabricNode 

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```
* Voor een Bronze duurzaamheid moet u wachten totdat alle knoop punten zijn uitgeschakeld.
* Voor de duurzaamheid van zilver en goud geldt dat sommige knoop punten in worden uitgeschakeld en dat de rest de status uitschakelen heeft. Controleer het tabblad Details van de knoop punten in het uitschakelen van de status, als deze allemaal vastzitten op het quorum voor infrastructuur service partities, dan is het veilig om door te gaan.

> [!Note]
> Het kan even duren voordat deze stap is voltooid. 

4. Stop de gegevens op het knooppunt type 0. 
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```
5. Toewijzing van knoop punten in de oorspronkelijke Schaalset voor virtuele machines ongedaan maken 
```powershell
$scaleSetName="nt1vm"
$scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```
> [!Note]
> Stap 6 en 7 zijn optioneel als u al een open bare standaard-SKU gebruikt en standaard SKU load balancer. In dit geval kunt u meerdere virtuele-machine schaal sets/knooppunt typen onder hetzelfde load balancer hebben. 

6. U kunt nu het oorspronkelijke IP-adres en de Load Balancer resources verwijderen. In deze stap werkt u ook de DNS-naam bij. 

```powershell
$lbname="LB-cluster-name-nt1vm"
$lbResourceType="Microsoft.Network/loadBalancers"
$ipResourceType="Microsoft.Network/publicIPAddresses"
$oldPublicIpName="PublicIP-LB-FE-nt1vm"
$newPublicIpName="PublicIP-LB-FE-nt2vm"

$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbname -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
``` 

7. Werk het beheer eindpunt op het cluster bij om naar het nieuwe IP-adres te verwijzen. 
```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```
8. Verwijder de knooppunt status van het knooppunt type 0.
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```
9. Verwijder de verwijzing naar het oorspronkelijke knooppunt type van de Service Fabric resource in de ARM-sjabloon. 
```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```
Alleen voor Silver-en hogere duurzaamheids clusters werkt u de cluster bron bij in de sjabloon en configureert u status beleid voor het negeren van de infra structuur:/de status van de systeem toepassing door applicationDeltaHealthPolicies toe te voegen onder de cluster resource-eigenschappen, zoals hieronder wordt vermeld. Het onderstaande beleid moet bestaande fouten negeren, maar geen nieuwe status fouten toestaan.
```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```
10. Verwijder alle andere resources met betrekking tot het oorspronkelijke knooppunt type uit de ARM-sjabloon. Zie [service Fabric-nieuw cluster knooppunt type](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-4.json) voor een sjabloon waarbij al deze oorspronkelijke resources zijn verwijderd.

11. Implementeer de gewijzigde Azure Resource Manager sjabloon. * * Deze stap neemt enige tijd in beslag, meestal Maxi maal twee uur. Met deze upgrade worden de instellingen gewijzigd in de InfrastructureService. Daarom is het opnieuw opstarten van een knoop punt vereist. In dit geval wordt forceRestart genegeerd. De para meter upgradeReplicaSetCheckTimeout geeft de maximale tijds duur op die Service Fabric wacht tot een partitie een veilige status heeft, als deze niet al een veilige status heeft. Zodra de controle van de veiligheid is geslaagd voor alle partities op een knoop punt, wordt Service Fabric door gegeven aan de upgrade op dat knoop punt. De waarde voor de para meter upgradeTimeout kan worden teruggebracht tot 6 uur, maar er moet een maximale beveiliging van 12 uur worden gebruikt.
Controleer vervolgens of de Service Fabric resource in de portal gereed wordt weer gegeven. 

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-4.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

Het primaire knooppunt type van het cluster is nu bijgewerkt. Controleer of de geïmplementeerde toepassingen goed functioneren en of de cluster status OK is.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [toevoegen van een knooppunt type aan een cluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Meer informatie over [schaal baarheid van toepassingen](service-fabric-concepts-scalability.md).
* [Een Azure-cluster in-of uitschalen](service-fabric-tutorial-scale-cluster.md).
* [Schaal een Azure-cluster programmatisch](service-fabric-cluster-programmatic-scaling.md) met behulp van de Fluent Azure Compute SDK.
* [Een zelfstandige cluster in-of uitschalen](service-fabric-cluster-windows-server-add-remove-nodes.md).
