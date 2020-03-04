---
title: Een Service Fabric-cluster schalen in Azure
description: In deze zelf studie leert u hoe u een Service Fabric cluster kunt schalen in azure out and in en hoe u overgebleven resources opschoont.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: f1b813576a94541cdc2ab0a67fea71b6f49696c5
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251798"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Zelfstudie: Een Service Fabric-cluster schalen

Deze zelf studie is deel drie van een reeks en laat zien hoe u uw bestaande cluster uit en in kunt schalen. Aan het einde van deze zelfstudie weet u hoe u een cluster kunt schalen en eventuele resterende resources kunt opschonen.  Lees [service Fabric clusters schalen](service-fabric-cluster-scaling.md)voor meer informatie over het schalen van een cluster dat wordt uitgevoerd in Azure.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Knoop punten toevoegen en verwijderen (uitschalen en schalen in)
> * Knooppunt typen toevoegen en verwijderen (uitschalen en schalen in)
> * Knooppunt resources verg Roten (omhoog schalen)

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

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installeer [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) of [Azure cli](/cli/azure/install-azure-cli).
* Een beveiligd [Windows-cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) maken in Azure

## <a name="important-considerations-and-guidelines"></a>Belang rijke overwegingen en richt lijnen

Werk belastingen van toepassingen veranderen na verloop van tijd, uw bestaande services hebben meer (of minder) resources nodig?  [Knoop punten toevoegen aan of verwijderen](#add-nodes-to-or-remove-nodes-from-a-node-type) uit een knooppunt type om cluster bronnen te verg Roten of verkleinen.

Wilt u meer dan 100 knoop punten toevoegen aan uw cluster?  Een enkele Service Fabric knooppunt type/schaalset mag niet meer dan 100 knoop punten/Vm's bevatten.  Als u een cluster wilt schalen dat groter is dan 100 knoop punten, [voegt u extra knooppunt typen toe](#add-nodes-to-or-remove-nodes-from-a-node-type).

Heeft uw toepassing meerdere services en moeten ze openbaar of Internet zijn gericht?  Typische toepassingen bevatten een front-end Gateway Service die invoer ontvangt van een client en een of meer back-end-services die communiceren met de front-end-services. In dit geval raden we u aan [ten minste twee knooppunt typen](#add-nodes-to-or-remove-nodes-from-a-node-type) aan het cluster toe te voegen.  

Hebben uw Services verschillende vereisten voor de infra structuur, zoals meer RAM-geheugen of hogere CPU-cycli? Uw toepassing bevat bijvoorbeeld een front-end-service en een back-end-service. De front-end-service kan worden uitgevoerd op kleinere Vm's (VM-grootten zoals D2) die poorten hebben geopend op internet. De back-end-service is echter reken intensief en moet worden uitgevoerd op grotere Vm's (met VM-grootten zoals D4, D6, D15) die niet op internet zijn gericht. In dit geval raden we u aan [twee of meer knooppunt typen](#add-nodes-to-or-remove-nodes-from-a-node-type) aan uw cluster toe te voegen. Hierdoor kan elk knooppunt type verschillende eigenschappen hebben, zoals Internet verbinding of VM-grootte. Het aantal virtuele machines kan ook afzonderlijk worden geschaald.

Houd bij het schalen van een Azure-cluster de volgende richt lijnen in acht:

* Een enkele Service Fabric knooppunt type/schaalset mag niet meer dan 100 knoop punten/Vm's bevatten.  Als u een cluster wilt schalen dat groter is dan 100 knoop punten, voegt u extra knooppunt typen toe.
* Primaire knooppunt typen die werk belastingen uitvoeren, moeten een [duurzaamheids niveau][durability] van goud of zilver hebben en moeten altijd vijf of meer knoop punten hebben.
* Niet-primaire knooppunt typen waarvoor stateful productie workloads worden uitgevoerd, moeten altijd vijf of meer knoop punten hebben.
* Niet-primaire knooppunt typen waarvoor stateless productie werkbelastingen worden uitgevoerd, moeten altijd twee of meer knoop punten hebben.
* Elk knooppunt type van het [duurzaamheids niveau][durability] goud of zilver moet altijd vijf of meer knoop punten bevatten.
* Bij het schalen van (knoop punten verwijderen uit) van een primair knooppunt type moet u nooit het aantal exemplaren verminderen tot minder dan wat het [betrouwbaarheids niveau][reliability] vereist.

Lees voor meer informatie de [richt lijnen voor cluster capaciteit](service-fabric-cluster-capacity.md).

## <a name="export-the-template-for-the-resource-group"></a>De sjabloon voor de resourcegroep exporteren

Nadat u een beveiligd [Windows-cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) hebt gemaakt en de resource groep hebt ingesteld, exporteert u de Resource Manager-sjabloon voor de resource groep. Als u de sjabloon exporteert, kunt u toekomstige implementaties van het cluster en de bijbehorende resources automatiseren, omdat de sjabloon alle volledige infra structuur bevat.  Lees [Azure Resource Manager resource groepen beheren met behulp van de Azure Portal](/azure/azure-resource-manager/manage-resource-groups-portal)voor meer informatie over het exporteren van sjablonen.

1. Ga in het [Azure Portal](https://portal.azure.com)naar de resource groep met het cluster (**sfclustertutorialgroup**als u deze zelf studie volgt). 

2. Selecteer in het linkerdeel venster **implementaties**of selecteer de koppeling onder **implementaties**. 

3. Selecteer de meest recente geslaagde implementatie uit de lijst.

4. Selecteer in het linkerdeel venster **sjabloon** en selecteer vervolgens **downloaden** om de sjabloon te exporteren als een zip-bestand.  Sla de sjabloon en de para meters op de lokale computer op.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Knoop punten toevoegen aan of verwijderen uit een knooppunt type

Als u het aantal knoop punten in het cluster wilt schalen of horizon taal wilt schalen, wijzigt u in en uit. Wanneer u in-of uitschaalt, voegt u meer exemplaren van virtuele machines toe aan de schaalset. Deze instanties worden de knooppunten die door Service Fabric worden gebruikt. Service Fabric weet wanneer er meer exemplaren aan de schaalset zijn toegevoegd (door uitschaling) en reageert daar automatisch op. U kunt het cluster op elk gewenst moment schalen, zelfs wanneer werk belastingen op het cluster worden uitgevoerd.

### <a name="update-the-template"></a>De sjabloon bijwerken

[Exporteer een sjabloon en parameter bestand](#export-the-template-for-the-resource-group) uit de resource groep voor de meest recente implementatie.  Open het bestand *para meters. json* .  Als u het cluster in deze zelf studie hebt geïmplementeerd met behulp van de [voorbeeld sjabloon][template] , zijn er drie knooppunt typen in het cluster en drie para meters die het aantal knoop punten voor elk knooppunt type instellen: *nt0InstanceCount*, *nt1InstanceCount*en *nt2InstanceCount*.  De para meter *nt1InstanceCount* stelt bijvoorbeeld het aantal exemplaren voor het tweede knooppunt type in en stelt het aantal virtuele machines in de gekoppelde schaalset voor virtuele machines in.

Dus door de waarde van de *nt1InstanceCount* te wijzigen, wijzigt u het aantal knoop punten in het tweede knooppunt type.  Houd er rekening mee dat u een knooppunt type niet naar meer dan 100 knoop punten kunt schalen.  Niet-primaire knooppunt typen waarvoor stateful productie workloads worden uitgevoerd, moeten altijd vijf of meer knoop punten hebben. Niet-primaire knooppunt typen waarvoor stateless productie werkbelastingen worden uitgevoerd, moeten altijd twee of meer knoop punten hebben.

Als u schaalt in, knoop punten verwijdert uit, een knooppunt type van Bronze [duurzaamheids niveau][durability] , moet u [de status van deze knoop punten hand matig verwijderen](service-fabric-cluster-scale-up-down.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set).  Voor Silver-en Gold-duurzaamheids lagen worden deze stappen automatisch uitgevoerd door het platform.

### <a name="deploy-the-updated-template"></a>De bijgewerkte sjabloon implementeren
Sla de wijzigingen op in de *sjabloon. json* en de *para meters. json* -bestanden.  Voer de volgende opdracht uit om de bijgewerkte sjabloon te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Of de volgende Azure CLI-opdracht:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Een knooppunt type toevoegen aan het cluster

Elk knooppunt type dat is gedefinieerd in een Service Fabric cluster dat in azure wordt uitgevoerd, wordt ingesteld als een [afzonderlijke schaalset voor virtuele machines](service-fabric-cluster-nodetypes.md). Elk knooppunt type kan vervolgens afzonderlijk worden beheerd. U kunt elk knooppunt type omhoog of omlaag schalen, verschillende sets poorten openen en verschillende capaciteits metrieken gebruiken. U kunt ook de SKU van het besturings systeem dat wordt uitgevoerd op elk cluster knooppunt onafhankelijk wijzigen, maar houd er rekening mee dat u geen mengeling van Windows en Linux kunt uitvoeren in het voorbeeld cluster. Een type/schaalset met één knoop punt kan niet meer dan 100 knoop punten bevatten.  U kunt een cluster horizon taal schalen naar meer dan 100 knoop punten door extra typen knoop punten/schaal sets toe te voegen. U kunt het cluster op elk gewenst moment schalen, zelfs wanneer werk belastingen op het cluster worden uitgevoerd.

### <a name="update-the-template"></a>De sjabloon bijwerken

[Exporteer een sjabloon en parameter bestand](#export-the-template-for-the-resource-group) uit de resource groep voor de meest recente implementatie.  Open het bestand *para meters. json* .  Als u het cluster hebt geïmplementeerd met behulp van de [voorbeeld sjabloon][template] in deze zelf studie, zijn er drie knooppunt typen in het cluster.  In deze sectie voegt u een vierde knooppunt type toe door een resource manager-sjabloon bij te werken en te implementeren. 

Naast het nieuwe knooppunt type, voegt u ook de gekoppelde virtuele-machine schaalset (die wordt uitgevoerd in een afzonderlijk subnet van het virtuele netwerk) en de netwerk beveiligings groep toe.  U kunt ervoor kiezen om een nieuw of bestaand openbaar IP-adres en Azure load balancer-resources toe te voegen voor de nieuwe schaalset.  Het nieuwe knooppunt type heeft een [duurzaamheids niveau][durability] van zilver en grootte van "Standard_D2_V2".

Voeg in het bestand *Template. json* de volgende nieuwe para meters toe:
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

Voeg in het bestand *Template. json* de volgende nieuwe variabelen toe:
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

Voeg in het bestand *Template. json* een nieuw subnet toe aan de bron van het virtuele netwerk:
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

Voeg in het bestand *Template. json* een nieuw openbaar IP-adres en Load Balancer resources toe:
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

Voeg in het bestand *Template. json* een nieuwe netwerk beveiligings groep en resources voor virtuele-machine schaal sets toe.  De eigenschap NodeTypeRef in de eigenschappen van de Service Fabric-extensie van de virtuele-machine schaalset wijst het opgegeven knooppunt type toe aan de schaalset.

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

Werk in het bestand *Template. json* de cluster bron bij en voeg een nieuw knooppunt type toe:
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

Voeg in het bestand *para meters. json* de volgende nieuwe para meters en waarden toe:
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>De bijgewerkte sjabloon implementeren
Sla de wijzigingen op in de *sjabloon. json* en de *para meters. json* -bestanden.  Voer de volgende opdracht uit om de bijgewerkte sjabloon te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Of de volgende Azure CLI-opdracht:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Een knooppunt type verwijderen uit het cluster
Nadat u een Service Fabric cluster hebt gemaakt, kunt u horizon taal een cluster schalen door een knooppunt type (virtuele-machine schaalset) en alle knoop punten ervan te verwijderen. U kunt het cluster op elk gewenst moment schalen, zelfs wanneer werk belastingen op het cluster worden uitgevoerd. Naarmate het cluster wordt geschaald, worden uw toepassingen ook automatisch geschaald.

> [!WARNING]
> Het gebruik van Remove-AzServiceFabricNodeType om een knooppunt type van een productie cluster te verwijderen, wordt niet aanbevolen om regel matig te worden gebruikt. Het is een gevaarlijke opdracht, omdat hiermee de bron van de virtuele-machine schaalset wordt verwijderd achter het knooppunt type. 

Als u het knooppunt type wilt verwijderen, voert u de cmdlet [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) uit.  Het knooppunt type moet zilver of goud [duurzaamheids niveau][durability] de cmdlet verwijdert de schaalset die is gekoppeld aan het knooppunt type en neemt enige tijd in beslag.  Voer vervolgens de cmdlet [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) uit op elk van de knoop punten die u wilt verwijderen. Hiermee wordt de knooppunt status verwijderd en worden de knoop punten uit het cluster verwijderd. Als er services worden uitgevoerd op de knoop punten, worden de services voor het eerst naar een ander knoop punt verplaatst. Als cluster beheer geen knoop punt voor de replica/service kan vinden, wordt de bewerking uitgesteld/geblokkeerd.

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

## <a name="increase-node-resources"></a>Knooppunt resources verg Roten 
Nadat u een Service Fabric cluster hebt gemaakt, kunt u het type van een cluster knooppunt verticaal schalen (de resources van de knoop punten wijzigen) of het besturings systeem van het knooppunt type Vm's bijwerken.  

> [!WARNING]
> We raden u aan de VM-SKU van een schaalset/knooppunt type alleen te wijzigen als deze wordt uitgevoerd in een zilver duurzaamheid of hoger. Het wijzigen van de VM-SKU-grootte is een in-place infrastructuur bewerking voor het uitvoeren van gegevens. Zonder enige mogelijkheid om deze wijziging te vertragen of te bewaken, is het mogelijk dat de bewerking gegevens verlies kan veroorzaken voor stateful Services of andere onvoorziene operationele problemen veroorzaakt, zelfs voor stateless workloads.

> [!WARNING]
> We raden u aan de VM-SKU van het primaire knooppunt type niet te wijzigen, wat een gevaarlijke bewerking is en niet wordt ondersteund.  Als u meer cluster capaciteit nodig hebt, kunt u meer VM-exemplaren of extra knooppunt typen toevoegen.  Als dat niet mogelijk is, kunt u een nieuw cluster maken en de [toepassings status](service-fabric-reliable-services-backup-restore.md) (indien van toepassing) herstellen van het oude cluster.  Als dat niet mogelijk is, kunt u [de VM-SKU van het primaire knooppunt type wijzigen](service-fabric-scale-up-node-type.md).

### <a name="update-the-template"></a>De sjabloon bijwerken

[Exporteer een sjabloon en parameter bestand](#export-the-template-for-the-resource-group) uit de resource groep voor de meest recente implementatie.  Open het bestand *para meters. json* .  Als u het cluster hebt geïmplementeerd met behulp van de [voorbeeld sjabloon][template] in deze zelf studie, zijn er drie knooppunt typen in het cluster.  

De grootte van de virtuele machines in het tweede knooppunt type wordt ingesteld in de para meter *vmNodeType1Size* .  Wijzig de waarde van de para meter *vmNodeType1Size* van Standard_D2_V2 naar [Standard_D3_V2](../virtual-machines/dv2-dsv2-series.md), waarmee de resources van elk VM-exemplaar worden verdubbeld.

De VM-SKU voor alle drie de knooppunt typen wordt ingesteld in de para meter *vmImageSku* .  Het wijzigen van de VM-SKU van een knooppunt type moet voorzichtig zijn en wordt niet aanbevolen voor het primaire knooppunt type.

### <a name="deploy-the-updated-template"></a>De bijgewerkte sjabloon implementeren
Sla de wijzigingen op in de *sjabloon. json* en de *para meters. json* -bestanden.  Voer de volgende opdracht uit om de bijgewerkte sjabloon te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Of de volgende Azure CLI-opdracht:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Knoop punten toevoegen en verwijderen (uitschalen en schalen in)
> * Knooppunt typen toevoegen en verwijderen (uitschalen en schalen in)
> * Knooppunt resources verg Roten (omhoog schalen)

Daarna gaat u verder met de volgende zelfstudie, waarin u leert hoe u een upgrade uitvoert van de runtime van een cluster.
> [!div class="nextstepaction"]
> [De runtime van een cluster upgraden](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json

> * Knooppunt typen toevoegen en verwijderen (uitschalen en schalen in)
> * Knooppunt resources verg Roten (omhoog schalen)

Daarna gaat u verder met de volgende zelfstudie, waarin u leert hoe u een upgrade uitvoert van de runtime van een cluster.
> [!div class="nextstepaction"]
> [De runtime van een cluster upgraden](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
