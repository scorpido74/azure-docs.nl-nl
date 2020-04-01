---
title: Een Service Fabric-cluster schalen in Azure
description: In deze zelfstudie leert u hoe u een cluster servicestructuur in Azure schalen en hoe u overgebleven resources opschonen.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: f1b813576a94541cdc2ab0a67fea71b6f49696c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78251798"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Zelfstudie: Een Service Fabric-cluster schalen

Deze zelfstudie is deel drie van een reeks en laat u zien hoe u uw bestaande cluster schalen. Aan het einde van deze zelfstudie weet u hoe u een cluster kunt schalen en eventuele resterende resources kunt opschonen.  Lees [Clusters van schalen van servicefabric](service-fabric-cluster-scaling.md)voor meer informatie over het schalen van een cluster dat wordt uitgevoerd in Azure.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Knooppunten toevoegen en verwijderen (uitschalen en inschalen)
> * Knooppunttypen toevoegen en verwijderen (uitschalen en schalen in)
> * Knooppuntresources vergroten (opschalen)

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * Een beveiligd [Windows-cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) maken in Azure met behulp van een sjabloon
> * [Een cluster bewaken](service-fabric-tutorial-monitor-cluster.md)
> * Een cluster in- of uitschalen
> * [De runtime van een cluster upgraden](service-fabric-tutorial-upgrade-cluster.md)
> * [Een cluster verwijderen](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installeer [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) of [Azure CLI](/cli/azure/install-azure-cli).
* Een beveiligd [Windows-cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) maken in Azure

## <a name="important-considerations-and-guidelines"></a>Belangrijke overwegingen en richtlijnen

Toepassingsworkloads veranderen in de loop van de tijd, hebben uw bestaande services meer (of minder) resources nodig?  [Voeg knooppunten](#add-nodes-to-or-remove-nodes-from-a-node-type) toe of verwijder knooppunten uit een knooppunttype om clusterbronnen te vergroten of te verkleinen.

Moet u meer dan 100 knooppunten aan uw cluster toevoegen?  Een enkel servicestructuurknooppunttype/schaalset mag niet meer dan 100 knooppunten/VM's bevatten.  Als u een cluster groter wilt maken dan 100 knooppunten, [voegt u extra knooppunttypen toe.](#add-nodes-to-or-remove-nodes-from-a-node-type)

Heeft uw applicatie meerdere diensten, en heeft een van hen nodig om openbaar of internet geconfronteerd?  Typische toepassingen bevatten een front-end gatewayservice die invoer ontvangt van een client en een of meer back-endservices die communiceren met de front-endservices. In dit geval raden we u aan [ten minste twee knooppunttypen aan](#add-nodes-to-or-remove-nodes-from-a-node-type) het cluster toe te voegen.  

Hebben uw services verschillende infrastructuurbehoeften, zoals meer RAM-geheugen of hogere CPU-cycli? Uw toepassing bevat bijvoorbeeld een front-endservice en een back-endservice. De front-end service kan worden uitgevoerd op kleinere VM's (VM-formaten zoals D2) die poorten open voor het internet hebben. De back-end service is echter rekenintensief en moet draaien op grotere VM's (met VM-formaten zoals D4, D6, D15) die niet op internet staan. In dit geval raden we u aan [twee of meer knooppunttypen aan](#add-nodes-to-or-remove-nodes-from-a-node-type) uw cluster toe te voegen. Hierdoor kan elk knooppunttype verschillende eigenschappen hebben, zoals internetverbinding of VM-grootte. Het aantal VM's kan ook onafhankelijk van elkaar worden geschaald.

Houd bij het schalen van een Azure-cluster rekening met de volgende richtlijnen:

* Een enkel servicestructuurknooppunttype/schaalset mag niet meer dan 100 knooppunten/VM's bevatten.  Als u een cluster wilt schalen met meer dan 100 knooppunten, voegt u extra knooppunttypen toe.
* Primaire knooppunttypen met productieworkloads moeten een [duurzaamheidsniveau][durability] van Goud of Zilver hebben en altijd vijf of meer knooppunten hebben.
* Niet-primaire knooppunttypen met stateful productieworkloads moeten altijd vijf of meer knooppunten hebben.
* Niet-primaire knooppunttypen met statusloze productieworkloads moeten altijd twee of meer knooppunten hebben.
* Elk knooppunt type [duurzaamheid niveau][durability] van Goud of Zilver moet altijd vijf of meer knooppunten.
* Als u een primair knooppunttype inschaalt (verwijderd uit) mag het aantal exemplaren nooit lager zijn dan wat het [betrouwbaarheidsniveau][reliability] vereist.

Lees voor meer informatie [clustercapaciteitsrichtlijnen](service-fabric-cluster-capacity.md).

## <a name="export-the-template-for-the-resource-group"></a>De sjabloon voor de resourcegroep exporteren

Nadat u een beveiligd [Windows-cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) hebt gemaakt en uw resourcegroep hebt ingesteld, exporteert u de sjabloon Resourcebeheer voor de resourcegroep. Als u de sjabloon exporteert, u toekomstige implementaties van het cluster en de bijbehorende resources automatiseren omdat de sjabloon alle volledige infrastructuur bevat.  Lees [Azure Resource Manager-brongroepen beheren met behulp van de Azure-portal](/azure/azure-resource-manager/manage-resource-groups-portal)voor meer informatie over het exporteren van sjablonen.

1. Ga in de [Azure-portal](https://portal.azure.com)naar de brongroep met het cluster **(sfclustertutorialgroup**, als u deze zelfstudie volgt). 

2. Selecteer in het linkerdeelvenster **Implementaties**of selecteer de koppeling onder **Implementaties**. 

3. Selecteer de meest recente succesvolle implementatie in de lijst.

4. Selecteer In het linkerdeelvenster **Sjabloon** en selecteer vervolgens **Downloaden** om de sjabloon als ZIP-bestand te exporteren.  Sla de sjabloon en parameters op uw lokale computer op.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Knooppunten toevoegen aan of verwijderen van knooppunten uit een knooppunttype

Als u in- en uitschalen of horizontaal schalen, verandert het aantal knooppunten in het cluster. Wanneer u in of uitschaalt, voegt u meer virtuele machine-exemplaren toe aan de schaalset. Deze instanties worden de knooppunten die door Service Fabric worden gebruikt. Service Fabric weet wanneer er meer exemplaren aan de schaalset zijn toegevoegd (door uitschaling) en reageert daar automatisch op. U het cluster op elk gewenst moment schalen, zelfs wanneer workloads op het cluster worden uitgevoerd.

### <a name="update-the-template"></a>De sjabloon bijwerken

[Exporteer een sjabloon- en parametersbestand](#export-the-template-for-the-resource-group) uit de resourcegroep voor de meest recente implementatie.  Open het *bestand parameters.json.*  Als u het cluster hebt geïmplementeerd met behulp van de [voorbeeldsjabloon][template] in deze zelfstudie, zijn er drie knooppunttypen in het cluster en drie parameters die het aantal knooppunten voor elk knooppunttype instellen: *nt0InstanceCount*, *nt1InstanceCount*en *nt2InstanceCount*.  De parameter *nt1InstanceCount* stelt bijvoorbeeld het aantal instantieinname in voor het tweede knooppunttype en stelt het aantal VM's in de bijbehorende virtuele machineschaalset in.

Door de waarde van de *nt1InstanceCount bij te werken,* wijzigt u dus het aantal knooppunten in het tweede knooppunttype.  Vergeet niet dat u een knooppunttype niet schalen naar meer dan 100 knooppunten.  Niet-primaire knooppunttypen met stateful productieworkloads moeten altijd vijf of meer knooppunten hebben. Niet-primaire knooppunttypen met statusloze productieworkloads moeten altijd twee of meer knooppunten hebben.

Als u inschaalt, knooppunten verwijdert uit, een knooppunttype [bronzeduurzaamheidsniveau,][durability] moet u [handmatig de status van die knooppunten verwijderen.](service-fabric-cluster-scale-up-down.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set)  Voor silver en gold duurzaamheidslaag worden deze stappen automatisch uitgevoerd door het platform.

### <a name="deploy-the-updated-template"></a>De bijgewerkte sjabloon implementeren
Sla eventuele wijzigingen op in de *bestanden template.json* en *parameters.json.*  Voer de volgende opdracht uit om de bijgewerkte sjabloon te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Of de volgende Azure CLI-opdracht:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Een knooppunttype toevoegen aan het cluster

Elk knooppunttype dat is gedefinieerd in een Cluster Servicefabric dat in Azure wordt uitgevoerd, is ingesteld als een [afzonderlijke virtuele machineschaalset.](service-fabric-cluster-nodetypes.md) Elk knooppunttype kan vervolgens afzonderlijk worden beheerd. U elk knooppunttype onafhankelijk naar boven of omlaag schalen, verschillende sets poorten openen en verschillende capaciteitsstatistieken gebruiken. U ook zelfstandig de OS-SKU wijzigen die op elk clusterknooppunt wordt uitgevoerd, maar houd er rekening mee dat u geen mix van Windows en Linux hebben die in het voorbeeldcluster wordt uitgevoerd. Een type/schaalset met één knooppunt mag niet meer dan 100 knooppunten bevatten.  U een cluster horizontaal schalen naar meer dan 100 knooppunten door extra node-typen/schaalsets toe te voegen. U het cluster op elk gewenst moment schalen, zelfs wanneer workloads op het cluster worden uitgevoerd.

### <a name="update-the-template"></a>De sjabloon bijwerken

[Exporteer een sjabloon- en parametersbestand](#export-the-template-for-the-resource-group) uit de resourcegroep voor de meest recente implementatie.  Open het *bestand parameters.json.*  Als u het cluster hebt geïmplementeerd met behulp van de [voorbeeldsjabloon][template] in deze zelfstudie, zijn er drie knooppunttypen in het cluster.  In deze sectie voegt u een vierde knooppunttype toe door een resourcemanagersjabloon bij te werken en te implementeren. 

Naast het nieuwe knooppunttype voegt u ook de bijbehorende virtuele machineschaalset (die wordt uitgevoerd in een afzonderlijk subnet van het virtuele netwerk) en de netwerkbeveiligingsgroep toe.  U ervoor kiezen om nieuwe of bestaande openbare IP-adres- en Azure-load balancerresources toe te voegen voor de nieuwe schaalset.  Het nieuwe knooppunt type heeft een [duurzaamheidsniveau][durability] van Zilver en grootte van "Standard_D2_V2".

Voeg in het *bestand template.json* de volgende nieuwe parameters toe:
```json
"nt3InstanceCount": {
    "defaultValue": 5,
    "type": "Int",
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType3Size": {
    "defaultValue": "Standard_D2_V2",
    "type": "String"
},
```

Voeg in het *bestand template.json* de volgende nieuwe variabelen toe:
```json
"lbID3": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
"lbIPConfig3": "[concat(variables('lbID3'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
"lbPoolID3": "[concat(variables('lbID3'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
"lbProbeID3": "[concat(variables('lbID3'),'/probes/FabricGatewayProbe')]",
"lbHttpProbeID3": "[concat(variables('lbID3'),'/probes/FabricHttpGatewayProbe')]",
"lbNatPoolID3": "[concat(variables('lbID3'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
"vmNodeType3Name": "[toLower(concat('NT4', variables('vmName')))]",
"vmStorageAccountName3": "[toLower(concat(uniqueString(resourceGroup().id), '1', '3' ))]",
"nt3applicationStartPort": "20000",
"nt3applicationEndPort": "30000",
"nt3ephemeralStartPort": "49152",
"nt3ephemeralEndPort": "65534",
"nt3fabricTcpGatewayPort": "19000",
"nt3fabricHttpGatewayPort": "19080",
"nt3reverseProxyEndpointPort": "19081",
"subnet3Name": "Subnet-3",
"subnet3Prefix": "10.0.3.0/24",
"subnet3Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet3Name'))]",
```

Voeg in het *bestand template.json* een nieuw subnet toe aan de virtuele netwerkbron:
```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            ...
            {
                "name": "[variables('subnet3Name')]",
                "properties": {
                    "addressPrefix": "[variables('subnet3Prefix')]",
                    "networkSecurityGroup": {
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', variables('subnet3Name')))]"
                    }
                }
            }
        ]
    },
    "dependsOn": [
        ...
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', variables('subnet3Name')))]"
    ]
},
```

Voeg in het *bestand template.json* nieuwe bronnen voor openbare IP-adres en load balancer toe:
```json
{
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "dnsSettings": {
            "domainNameLabel": "[concat(variables('dnsName'),'-','nt4')]"
        },
        "publicIPAllocationMethod": "Dynamic"
    }
},
        {
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "frontendIPConfigurations": [
            {
                "name": "LoadBalancerIPConfig",
                "properties": {
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
                    }
                }
            }
        ],
        "backendAddressPools": [
            {
                "name": "LoadBalancerBEAddressPool",
                "properties": {}
            }
        ],
        "loadBalancingRules": [
            {
                "name": "LBRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "LBHttpRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbHttpProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule1",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort1')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort1')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe1')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule2",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort2')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort2')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe2')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            {
                "name": "FabricGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricTcpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "FabricHttpGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricHttpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe1",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort1')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe2",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort2')]",
                    "protocol": "tcp"
                }
            }
        ],
        "inboundNatPools": [
            {
                "name": "LoadBalancerBEAddressNatPool",
                "properties": {
                    "backendPort": "3389",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPortRangeEnd": "4500",
                    "frontendPortRangeStart": "3389",
                    "protocol": "tcp"
                }
            }
        ]
    },
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
    ]
},
```

Voeg in het *bestand template.json* nieuwe netwerkbeveiligingsgroep en resources voor virtuele machineschaalinstelitems toe.  De eigenschap NodeTypeRef binnen de extensieeigenschappen ServiceFabric van de virtuele machineschaalset brengt het opgegeven knooppunttype in kaart aan de schaalset.

```json
{
    "type": "Microsoft.Network/networkSecurityGroups",
    "name": "[concat('nsg', variables('subnet3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[resourceGroup().location]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "securityRules": [
            {
                "name": "allowSvcFabSMB",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "445",
                    "direction": "Inbound",
                    "priority": 3950,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow SMB traffic within the net, used by fabric to move packages around"
                }
            },
            {
                "name": "allowSvcFabCluser",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "1025-1027",
                    "direction": "Inbound",
                    "priority": 3920,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow ports within vnet that are used by the fabric to talk between nodes"
                }
            },
            {
                "name": "allowSvcFabEphemeral",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3ephemeralStartPort'), '-', variables('nt3ephemeralEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3930,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow fabric ephemeral ports within the vnet"
                }
            },
            {
                "name": "allowSvcFabPortal",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricHttpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3900,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster web portal"
                }
            },
            {
                "name": "allowSvcFabClient",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricTcpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3910,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used by the fabric client (includes powershell)"
                }
            },
            {
                "name": "allowSvcFabApplication",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3applicationStartPort'), '-', variables('nt3applicationEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3940,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow fabric application ports within the vnet"
                }
            },
            {
                "name": "blockAll",
                "properties": {
                    "access": "Deny",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "*",
                    "direction": "Inbound",
                    "priority": 4095,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "block all traffic except what we've explicitly allowed"
                }
            },
            {
                "name": "allowVNetRDP",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "3389",
                    "direction": "Inbound",
                    "priority": 3960,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow RDP within the net"
                }
            },
            {
                "name": "allowSvcFabReverseProxy",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3reverseProxyEndpointPort')]",
                    "direction": "Inbound",
                    "priority": 3980,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster using reverse proxy"
                }
            },
            {
                "name": "allowAppPort1",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort1')]",
                    "direction": "Inbound",
                    "priority": 2001,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 1"
                }
            },
            {
                "name": "allowAppPort2",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort2')]",
                    "direction": "Inbound",
                    "priority": 2002,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 2"
                }
            }
        ]
    }
},
{
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "sku": {
        "name": "[parameters('vmNodeType3Size')]",
        "capacity": "[parameters('nt3InstanceCount')]",
        "tier": "Standard"
    },
    "name": "[variables('vmNodeType3Name')]",
    "apiVersion": "2018-10-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
            "mode": "Automatic"
        },
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                    {
                        "name": "[concat(variables('vmNodeType3Name'),'OMS')]",
                        "properties": {
                            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                            "type": "MicrosoftMonitoringAgent",
                            "typeHandlerVersion": "1.0",
                            "autoUpgradeMinorVersion": true,
                            "settings": {
                                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                            },
                            "protectedSettings": {
                                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                            }
                        }
                    },
                    {
                        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType3Name')]",
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
                                "nodeTypeRef": "[variables('vmNodeType3Name')]",
                                "dataPath": "D:\\SvcFab",
                                "durabilityLevel": "Silver",
                                "enableParallelJobs": true,
                                "nicPrefixOverride": "[variables('subnet3Prefix')]",
                                "certificate": {
                                    "thumbprint": "[parameters('certificateThumbprint')]",
                                    "x509StoreName": "[parameters('certificateStoreValue')]"
                                }
                            },
                            "typeHandlerVersion": "1.0"
                        }
                    },
                    {
                        "name": "[concat('VMDiagnosticsVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "IaaSDiagnostics",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "storageAccountName": "[variables('applicationDiagnosticsStorageAccountName')]",
                                "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                "storageAccountEndPoint": "https://core.windows.net/"
                            },
                            "publisher": "Microsoft.Azure.Diagnostics",
                            "settings": {
                                "WadCfg": {
                                    "DiagnosticMonitorConfiguration": {
                                        "overallQuotaInMB": "50000",
                                        "EtwProviders": {
                                            "EtwEventSourceProviderConfiguration": [
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Actors",
                                                    "scheduledTransferKeywordFilter": "1",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableActorEventTable"
                                                    }
                                                },
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Services",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                    }
                                                }
                                            ],
                                            "EtwManifestProviderConfiguration": [
                                                {
                                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                    "scheduledTransferLogLevelFilter": "Information",
                                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricSystemEventTable"
                                                    }
                                                }
                                            ]
                                        }
                                    }
                                },
                                "StorageAccount": "[variables('applicationDiagnosticsStorageAccountName')]"
                            },
                            "typeHandlerVersion": "1.5"
                        }
                    },
                    {
                        "name": "[concat('VMIaaSAntimalware','_vmNodeType3Name')]",
                        "properties": {
                            "publisher": "Microsoft.Azure.Security",
                            "type": "IaaSAntimalware",
                            "typeHandlerVersion": "1.5",
                            "settings": {
                                "AntimalwareEnabled": "true",
                                "Exclusions": {
                                    "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                                    "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
                                },
                                "RealtimeProtectionEnabled": "true",
                                "ScheduledScanSettings": {
                                    "isEnabled": "true",
                                    "scanType": "Quick",
                                    "day": "7",
                                    "time": "120"
                                }
                            },
                            "protectedSettings": null
                        }
                    }
                ]
            },
            "networkProfile": {
                "networkInterfaceConfigurations": [
                    {
                        "name": "[concat(variables('nicName'), '-2')]",
                        "properties": {
                            "ipConfigurations": [
                                {
                                    "name": "[concat(variables('nicName'),'-',2)]",
                                    "properties": {
                                        "loadBalancerBackendAddressPools": [
                                            {
                                                "id": "[variables('lbPoolID3')]"
                                            }
                                        ],
                                        "loadBalancerInboundNatPools": [
                                            {
                                                "id": "[variables('lbNatPoolID3')]"
                                            }
                                        ],
                                        "subnet": {
                                            "id": "[variables('subnet3Ref')]"
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
                "computernamePrefix": "[variables('vmNodeType3Name')]",
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
                    "publisher": "[parameters('vmImagePublisher')]",
                    "offer": "[parameters('vmImageOffer')]",
                    "sku": "[parameters('vmImageSku')]",
                    "version": "[parameters('vmImageVersion')]"
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
    "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
    ]
},
```

Werk in het *bestand template.json* de clusterbron bij en voeg een nieuw knooppunttype toe:
```json
{
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "apiVersion": "2018-02-01",
    "location": "[parameters('clusterLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "nodeTypes": [
            ...
            {
                "name": "[variables('vmNodeType3Name')]",
                "applicationPorts": {
                    "endPort": "[variables('nt3applicationEndPort')]",
                    "startPort": "[variables('nt3applicationStartPort')]"
                },
                "clientConnectionEndpointPort": "[variables('nt3fabricTcpGatewayPort')]",
                "durabilityLevel": "Silver",
                "ephemeralPorts": {
                    "endPort": "[variables('nt3ephemeralEndPort')]",
                    "startPort": "[variables('nt3ephemeralStartPort')]"
                },
                "httpGatewayEndpointPort": "[variables('nt3fabricHttpGatewayPort')]",
                "isPrimary": false,
                "reverseProxyEndpointPort": "[variables('nt3reverseProxyEndpointPort')]",
                "vmInstanceCount": "[parameters('nt3InstanceCount')]"
            }
        ],    
    }
}                
```

Voeg in het bestand *parameters.json* de volgende nieuwe parameters en waarden toe:
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>De bijgewerkte sjabloon implementeren
Sla eventuele wijzigingen op in de *bestanden template.json* en *parameters.json.*  Voer de volgende opdracht uit om de bijgewerkte sjabloon te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Of de volgende Azure CLI-opdracht:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Een knooppunttype uit het cluster verwijderen
Nadat u een cluster servicestructuur hebt gemaakt, u een cluster horizontaal schalen door een knooppunttype (virtuele machineschaalset) en alle knooppunten te verwijderen. U het cluster op elk gewenst moment schalen, zelfs wanneer workloads op het cluster worden uitgevoerd. Naarmate het cluster schaalt, schalen uw toepassingen ook automatisch.

> [!WARNING]
> Het gebruik van Remove-AzServiceFabricNodeType om een knooppunttype uit een productiecluster te verwijderen, wordt aanbevolen om regelmatig te worden gebruikt. Het is een gevaarlijke opdracht als het verwijdert de virtuele machine schaal set bron achter het knooppunt type. 

Als u het knooppunttype wilt verwijderen, voert u de cmdlet [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) uit.  Het knooppunttype moet zilver- of [goudduurzaamheidsniveau][durability] zijn De cmdlet verwijdert de schaalset die is gekoppeld aan het knooppunttype en neemt enige tijd in beslag.  Voer vervolgens de cmdlet [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) uit op elk van de knooppunten om te verwijderen, waardoor de knooppuntstatus wordt verwijderd en de knooppunten uit het cluster worden verwijderd. Als er services op de knooppunten zijn, worden de services eerst verplaatst naar een ander knooppunt. Als de clusterbeheerder geen knooppunt voor de replica/service kan vinden, wordt de bewerking vertraagd/geblokkeerd.

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="increase-node-resources"></a>Knooppuntresources vergroten 
Nadat u een cluster servicestructuur hebt gemaakt, u een clusterknooppunttype verticaal schalen (de bronnen van de knooppunten wijzigen) of het besturingssysteem van het knooppunttype VM's upgraden.  

> [!WARNING]
> We raden u aan de VM SKU van een schaalset/knooppunttype niet te wijzigen, tenzij deze wordt uitgevoerd op Silver-duurzaamheid of hoger. Het wijzigen van de VM SKU-grootte is een gegevensdestructieve infrastructuurbewerking. Zonder enige mogelijkheid om deze wijziging uit te stellen of te controleren, is het mogelijk dat de bewerking gegevensverlies kan veroorzaken voor stateful services of andere onvoorziene operationele problemen kan veroorzaken, zelfs voor stateloze workloads.

> [!WARNING]
> We raden u aan de VM SKU van het primaire knooppunttype, dat een gevaarlijke bewerking is en niet ondersteund, niet te wijzigen.  Als u meer clustercapaciteit nodig hebt, u meer VM-exemplaren of extra knooppunttypen toevoegen.  Als dat niet mogelijk is, u een nieuw cluster maken en [de toepassingsstatus](service-fabric-reliable-services-backup-restore.md) (indien van toepassing) herstellen vanuit uw oude cluster.  Als dat niet mogelijk is, u [de VM SKU van het primaire knooppunttype wijzigen.](service-fabric-scale-up-node-type.md)

### <a name="update-the-template"></a>De sjabloon bijwerken

[Exporteer een sjabloon- en parametersbestand](#export-the-template-for-the-resource-group) uit de resourcegroep voor de meest recente implementatie.  Open het *bestand parameters.json.*  Als u het cluster hebt geïmplementeerd met behulp van de [voorbeeldsjabloon][template] in deze zelfstudie, zijn er drie knooppunttypen in het cluster.  

De grootte van de VM's in het tweede knooppunttype is ingesteld in de parameter *vmNodeType1Size.*  Wijzig de *parameterwaarde vmNodeType1Size* van Standard_D2_V2 naar [Standard_D3_V2,](../virtual-machines/dv2-dsv2-series.md)waardoor de resources van elke VM-instantie worden verdubbeld.

De VM SKU voor alle drie de knooppunttypen is ingesteld in de parameter *vmImageSku.*  Nogmaals, het wijzigen van de VM SKU van een knooppunttype moet met de nodige voorzichtigheid worden benaderd en wordt niet aanbevolen voor het primaire knooppunttype.

### <a name="deploy-the-updated-template"></a>De bijgewerkte sjabloon implementeren
Sla eventuele wijzigingen op in de *bestanden template.json* en *parameters.json.*  Voer de volgende opdracht uit om de bijgewerkte sjabloon te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Of de volgende Azure CLI-opdracht:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Knooppunten toevoegen en verwijderen (uitschalen en inschalen)
> * Knooppunttypen toevoegen en verwijderen (uitschalen en schalen in)
> * Knooppuntresources vergroten (opschalen)

Daarna gaat u verder met de volgende zelfstudie, waarin u leert hoe u een upgrade uitvoert van de runtime van een cluster.
> [!div class="nextstepaction"]
> [De runtime van een cluster upgraden](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json

> * Knooppunttypen toevoegen en verwijderen (uitschalen en schalen in)
> * Knooppuntresources vergroten (opschalen)

Daarna gaat u verder met de volgende zelfstudie, waarin u leert hoe u een upgrade uitvoert van de runtime van een cluster.
> [!div class="nextstepaction"]
> [De runtime van een cluster upgraden](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
