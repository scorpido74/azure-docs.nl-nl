---
title: Azure internal Load Balancer verplaatsen naar een andere Azure-regio met Azure PowerShell
description: Azure Resource Manager-sjabloon gebruiken om Azure internal Load Balancer van het ene Azure-gebied naar het andere te verplaatsen met Azure PowerShell
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f8e431124155fe23853fe61e985fe4db522c3f77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644270"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>Azure internal Load Balancer verplaatsen naar een andere regio met PowerShell

Er zijn verschillende scenario's waarin u uw bestaande interne load balancer van de ene regio naar de andere wilt verplaatsen. U bijvoorbeeld een interne load balancer maken met dezelfde configuratie voor het testen. U ook een interne load balancer naar een andere regio verplaatsen als onderdeel van de planning voor noodherstel.

Azure interne load balancers kunnen niet van de ene regio naar de andere worden verplaatst. U echter wel een Azure Resource Manager-sjabloon gebruiken om de bestaande configuratie en het virtuele netwerk van een interne load balancer te exporteren.  U de resource vervolgens in een andere regio fasen door de load balancer en het virtuele netwerk naar een sjabloon te exporteren, de parameters te wijzigen die overeenkomen met het doelgebied en de sjablonen vervolgens naar de nieuwe regio te implementeren.  Zie [Brongroepen exporteren naar sjablonen voor](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) meer informatie over ResourceManager en sjablonen


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de interne load balancer van Azure zich in het Azure-gebied bevindt van waaruit u wilt verplaatsen.

- Azure internal load balancers kunnen niet tussen regio's worden verplaatst.  U moet de nieuwe load balancer koppelen aan resources in het doelgebied.

- Als u een interne configuratie van de load balancer wilt exporteren en een sjabloon wilt implementeren om een interne load balancer in een andere regio te maken, hebt u de rol Netwerkbijdrager of hoger nodig.
   
- Identificeer de indeling van de bronnetwerkindeling en alle bronnen die u momenteel gebruikt. Deze lay-out omvat, maar is niet beperkt tot load balancers, netwerkbeveiligingsgroepen, virtuele machines en virtuele netwerken.

- Controleer of u met uw Azure-abonnement interne load balancers maken in het doelgebied dat wordt gebruikt. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende resources heeft om de toevoeging van load balancers voor dit proces te ondersteunen.  Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen
In de volgende stappen wordt uitgelegd hoe u de interne load balancer voorbereidt op de verhuizing met behulp van een Resource Manager-sjabloon en de interne configuratie van de load balancer naar het doelgebied verplaatsen met Azure PowerShell.  Als onderdeel van dit proces moet de virtuele netwerkconfiguratie van de interne load balancer worden opgenomen en moet dit eerst worden gedaan voordat de interne load balancer wordt verplaatst.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>De virtuele netwerksjabloon exporteren en implementeren vanuit Azure PowerShell

1. Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) en volg de aanwijzingen op het scherm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  Verkrijg de resource-ID van het virtuele netwerk dat u naar de doelregio wilt verplaatsen en plaats deze in een variabele met [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporteer het virtuele bronnetwerk naar een .json-bestand naar de map waar u de opdracht [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)uitvoert:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Het gedownloade bestand wordt vernoemd naar de resourcegroep waaruit de resource is geëxporteerd.  Zoek het bestand dat is geëxporteerd vanuit de opdracht met ** \<de naam resourcegroep>.json** en open het in een editor naar keuze:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Als u de parameter van de naam van het virtuele netwerk wilt bewerken, wijzigt u de standaardwaarde van de **eigenschapwaarde** van de naam van het virtuele netwerk van de bron in de naam van uw virtuele doelnetwerk, moet u ervoor zorgen dat de naam tussen aanhalingstekens staat:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  Als u het doelgebied wilt bewerken waar het VNET wordt verplaatst, wijzigt u de **locatieeigenschap** onder resources:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
7. Als u regiolocatiegegevens wilt verkrijgen, u de Azure PowerShell-cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) gebruiken door de volgende opdracht uit te voeren:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  U ook andere parameters wijzigen in het ** \<bestand>.json-bestand voor resourcesgroep>.json** en zijn optioneel, afhankelijk van uw vereisten:

    * **Adresruimte** - De adresruimte van het VNET kan worden **resources** > gewijzigd voordat u het**gedeelte resources addressSpace** wijzigt en de eigenschap **adresvoorvoegsels wijzigt** in het bestand ** \<>.json-** bestand van de brongroepgroep:

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Subnet** - De subnetnaam en de subnetadresruimte kunnen worden gewijzigd of toegevoegd door de **subnettensectie** van de ** \<brongroepnaam>.json-bestand** te wijzigen. De naam van het subnet kan worden gewijzigd door de **eigenschap naam** te wijzigen. De subnetadresruimte kan worden gewijzigd door de eigenschap **adresvoorvoegsel** in het ** \<brongroepnaam->.json-bestand** te wijzigen:

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

         In het ** \<bestand>.json,** om het adresvoorvoegsel te wijzigen, moet het op twee plaatsen worden bewerkt, de sectie hierboven vermeld en de **sectie tekst** hieronder.  Wijzig de eigenschap **adresvoorvoegsel** dat overeenkomt met de bovenstaande eigenschap:

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

9.  Sla het ** \<brongroepnaam>.json-bestand** op.

10. Een resourcegroep maken in de doelregio voor de doelstelling VNET die moet worden geïmplementeerd met [Nieuwe-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Het gebewerkte ** \<brongroepnaam>.json-bestand** implementeren in de resourcegroep die in de vorige stap is gemaakt met [Nieuwe AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. Gebruik [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) en [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)om te controleren of de resources in de doelregio zijn gemaakt:
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>De sjabloon voor interne lastenbalans exporteren en implementeren vanuit Azure PowerShell

1. Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) en volg de aanwijzingen op het scherm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Verkrijg de resource-ID van de interne load balancer die u naar het doelgebied wilt verplaatsen en plaats deze in een variabele met [Get-AzLoadBalancer:](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporteer de configuratie van de broninterne load balancer naar een .json-bestand naar de map waar u de opdracht [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)uitvoert:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. Het gedownloade bestand wordt vernoemd naar de resourcegroep waaruit de resource is geëxporteerd.  Zoek het bestand dat is geëxporteerd vanuit de opdracht met ** \<de naam resourcegroep>.json** en open het in een editor naar keuze:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Als u de parameter van de naam van de interne load balancer wilt bewerken, wijzigt u de standaardwaarde van de eigenschap **standaardWaarde** van de naam van de broninterne load balancer in de naam van uw doelinterne load balancer, moet u ervoor zorgen dat de naam tussen aanhalingstekens staat:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```
 
6. Als u de waarde wilt bewerken van het hierboven verplaatste virtuele doelnetwerk, moet u eerst de bron-id verkrijgen en deze vervolgens kopiëren en plakken in het ** \<bestand>.json met brongroep.**  Gebruik [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)om de ID te verkrijgen:
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Typ de variabele en druk op enter om de resource-id weer te geven.  Markeer het ID-pad en kopieer het naar het klembord:

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  Plak in het **defaultValue** ** \<bestand resourcegroepnaam>.json** de **resource-id** van de variabele in plaats van de standaardwaarde in de tweede parameter voor de virtuele netwerk-id van het doel, ervoor te zorgen dat u het pad in aanhalingstekens omsluit:
   
    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

8. Als u het doelgebied wilt bewerken waar de configuratie van de interne loadbalancer wordt verplaatst, wijzigt u de **locatieeigenschap** onder **resources** in het ** \<bestand resourcegroepnaam>.json:**

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. Als u regiolocatiegegevens wilt verkrijgen, u de Azure PowerShell-cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) gebruiken door de volgende opdracht uit te voeren:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. U desgevraagd ook andere parameters in de sjabloon wijzigen en zijn optioneel, afhankelijk van uw vereisten:
    
    * **Sku** - U de sku van de interne load balancer in de configuratie wijzigen van standaard naar basis- of standaard door de eigenschap **sku-naam** > **name** in het ** \<bestand>.json** van de brongroep te wijzigen:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Zie overzicht van [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) voor meer informatie over de verschillen tussen basis- en standaardsku-loadbalancers

    * **Regels voor taakverdeling** - U regels voor taakverdeling toevoegen of verwijderen in de configuratie door items toe te voegen of te verwijderen aan het gedeelte **loadBalancingRules** van het ** \<bestand>.json** van de resourcegroep:

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Zie Wat is Azure Load [Balancer voor](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) meer informatie over regels voor het balanceren van de belasting?

    * **Probes** - U een sonde voor de load balancer in de configuratie toevoegen of verwijderen door items toe te voegen of te verwijderen aan het **gedeelte met sondes** van het ** \<bestand resourcegroep->.json:**

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Zie [Statusssen](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview) van Load Balancer voor meer informatie over statussen van Azure Load Balancer.

    * **Binnenkomende NAT-regels** - U binnenkomende NAT-regels voor de load balancer toevoegen of verwijderen door vermeldingen toe te voegen aan het gedeelte **inboundNatRules** van het ** \<bestand>.json van de brongroep:**

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Als u de toevoeging of verwijdering van een binnenkomende NAT-regel wilt voltooien, moet de regel aanwezig zijn of worden verwijderd als een **eigenschap type** aan het einde van het ** \<bestand>.json-bestand met brongroepgroep:**

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Zie Wat is Azure Load Balancer voor meer informatie over binnenkomende [NAT-regels?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
    
13. Sla het ** \<brongroepnaam>.json-bestand** op.
    
10. Maak of een resourcegroep in de doelregio voor de doelinterne load balancer die moet worden geïmplementeerd met [Nieuwe-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). De bestaande resourcegroep van bovenaf kan ook worden hergebruikt als onderdeel van dit proces:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Het gebewerkte ** \<brongroepnaam>.json-bestand** implementeren in de resourcegroep die in de vorige stap is gemaakt met [Nieuwe AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Als u wilt controleren of de resources in de doelregio zijn gemaakt, gebruikt u [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) en [Get-AzLoadBalancer:](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Verwijderen 

Als u na de implementatie opnieuw wilt beginnen of de virtuele netwerk- en loadbalancer in het doel wilt verwijderen, verwijdert u de brongroep die in het doel is gemaakt en wordt het verplaatste virtuele netwerk en de load balancer verwijderd.  Als u de brongroep wilt verwijderen, gebruikt u [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Opruimen

Als u de wijzigingen wilt vastleggen en de verplaatsing van de NSG wilt voltooien, verwijdert u de bron NSG of resourcegroep, gebruikt u [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) of [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) en [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een interne laadbakvan Azure van de ene regio naar de andere verplaatst en de bronbronnen opgeschoond.  Zie voor meer informatie over het verplaatsen van resources tussen regio's en disaster recovery in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Virtuele Azure-machines verplaatsen naar een andere regio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
