---
title: Azure externe load balancer verplaatsen naar een andere Azure-regio met Azure PowerShell
description: Gebruik azure resource beheersjabloon om Azure external Load Balancer van het ene Azure-gebied naar het andere te verplaatsen met Azure PowerShell.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: a24eb4608e7630d5b613751fa2120361eccd7672
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644814"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Azure externe load balancer verplaatsen naar een ander gebied met Azure PowerShell

Er zijn verschillende scenario's waarin u uw bestaande externe load balancer van de ene regio naar de andere wilt verplaatsen. U bijvoorbeeld een externe load balancer maken met dezelfde configuratie voor het testen. U ook een externe load balancer naar een andere regio verplaatsen als onderdeel van de planning voor noodherstel.

Azure externe lastenafwegingen kunnen niet van de ene regio naar de andere worden verplaatst. U echter wel een Azure Resource Manager-sjabloon gebruiken om de bestaande configuratie en het openbare IP-adres van een externe load balancer te exporteren.  U de resource vervolgens in een andere regio faseren door de load balancer en het openbare IP-adres naar een sjabloon te exporteren, de parameters te wijzigen die overeenkomen met het doelgebied en de sjablonen vervolgens te implementeren in de nieuwe regio.  Zie [Brongroepen exporteren naar sjablonen voor](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) meer informatie over ResourceManager en sjablonen


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de externe load balancer van Azure zich in het Azure-gebied bevindt van waaruit u wilt verplaatsen.

- Azure externe load balancers kunnen niet worden verplaatst tussen regio's.  U moet de nieuwe load balancer koppelen aan resources in het doelgebied.

- Als u een externe configuratie van de load balancer wilt exporteren en een sjabloon wilt implementeren om een externe load balancer in een andere regio te maken, hebt u de rol Netwerkinzender of hoger nodig.
   
- Identificeer de indeling van de bronnetwerkindeling en alle bronnen die u momenteel gebruikt. Deze lay-out omvat, maar is niet beperkt tot load balancers, netwerkbeveiligingsgroepen, openbare IP's en virtuele netwerken.

- Controleer of u met uw Azure-abonnement externe load balancers maken in het doelgebied dat wordt gebruikt. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende resources heeft om de toevoeging van load balancers voor dit proces te ondersteunen.  Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen
In de volgende stappen wordt uitgelegd hoe u de externe load balancer voorbereidt op de verhuizing met behulp van een Resource Manager-sjabloon en de externe configuratie van de load balancer naar het doelgebied verplaatsen met Azure PowerShell.  Als onderdeel van dit proces moet de openbare IP-configuratie van de externe load balancer worden opgenomen en moet ik eerst worden gedaan voordat u de externe load balancer verplaatst.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>De openbare IP-sjabloon exporteren en implementeren vanuit Azure PowerShell

1. Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) en volg de aanwijzingen op het scherm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. Verkrijg de resource-ID van het openbare IP dat u naar het doelgebied wilt verplaatsen en plaats deze in een variabele met [Get-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporteer het bron-openbaar IP-adres naar een .json-bestand naar de map waar u de opdracht [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)uitvoert:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Het gedownloade bestand wordt vernoemd naar de resourcegroep waaruit de resource is geëxporteerd.  Zoek het bestand dat is geëxporteerd vanuit de opdracht met ** \<de naam resourcegroep>.json** en open het in een editor naar keuze:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Als u de parameter van de openbare IP-naam wilt bewerken, wijzigt u de standaardwaarde van de **eigenschapstandaardWaarde** van de bronnaam openbare IP-naam in de naam van uw doel-openbaar IP-adres, moet u ervoor zorgen dat de naam tussen aanhalingstekens staat:
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. Als u het doelgebied wilt bewerken waar het openbare IP-adres wordt verplaatst, wijzigt u de **locatieeigenschap** onder resources:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. Als u regiolocatiegegevens wilt verkrijgen, u de Azure PowerShell-cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) gebruiken door de volgende opdracht uit te voeren:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. U desgevraagd ook andere parameters in de sjabloon wijzigen en zijn optioneel, afhankelijk van uw vereisten:

    * **Sku** - U de sku van het openbare IP-adres in de configuratie wijzigen van standaard naar basis- of basisstandaard door de eigenschap **sku-naam** > **name** in het ** \<bestand resourcegroep->.json** te wijzigen:

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         Zie [Een openbaar IP-adres maken, wijzigen of verwijderen](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)voor meer informatie over de verschillen tussen basis- en standaardsku public ips.

    * **Openbare IP-toewijzingsmethode** en **niet-actieve time-out** - U beide opties in de sjabloon wijzigen door de eigenschap **publicIPAllocationMethod** te wijzigen van **Dynamisch** naar **Statisch** of **Statisch** naar **Dynamisch**. De niet-actieve time-out kan worden gewijzigd door de eigenschap **idleTimeoutInMinutes** te wijzigen in het gewenste bedrag.  De standaardinstelling is **4**:

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        Zie [Een openbaar IP-adres maken, wijzigen of verwijderen](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)voor meer informatie over de toewijzingsmethoden en de inactieve time-outwaarden.


9. Sla het ** \<brongroepnaam>.json-bestand** op.

10. Maak een resourcegroep in de doelregio voor het doel-ip dat moet worden geïmplementeerd met [Nieuwe-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Het gebewerkte ** \<brongroepnaam>.json-bestand** implementeren in de resourcegroep die in de vorige stap is gemaakt met [Nieuwe AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Gebruik [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) en [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)om te controleren of de resources in de doelregio zijn gemaakt:
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>De externe sjabloon voor load balancer exporteren en implementeren vanuit Azure PowerShell

1. Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) en volg de aanwijzingen op het scherm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Verkrijg de resource-ID van de externe load balancer die u naar het doelgebied wilt verplaatsen en plaats deze in een variabele met [Get-AzLoadBalancer:](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporteer de configuratie van de externe lastenbalancer naar een .json-bestand naar de map waar u de opdracht [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)uitvoert:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. Het gedownloade bestand wordt vernoemd naar de resourcegroep waaruit de resource is geëxporteerd.  Zoek het bestand dat is geëxporteerd vanuit de opdracht met ** \<de naam resourcegroep>.json** en open het in een editor naar keuze:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Als u de parameter van de naam van de externe load balancer wilt bewerken, wijzigt u de standaardwaarde van de **eigenschapstandaardWaarde** van de naam van de externe loadbalancer van de bron in de naam van uw externe laadbalansr van het doel, moet u ervoor zorgen dat de naam tussen aanhalingstekens staat:

    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "loadBalancers_myLoadbalancer_ext_name": {
        "defaultValue": "<target-external-lb-name>",
        "type": "String"
            },
        "publicIPAddresses_myPubIP_in_externalid": {
        "defaultValue": "<target-publicIP-resource-ID>",
        "type": "String"
            },

    ```

6.  Als u de waarde wilt bewerken van het hierboven verplaatste doel-ip-adres, moet u eerst de bron-id verkrijgen en deze vervolgens kopiëren en plakken in het ** \<brongroepnaam>.json-bestand.**  Gebruik [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)om de ID te verkrijgen:

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Typ de variabele en druk op enter om de resource-id weer te geven.  Markeer het ID-pad en kopieer het naar het klembord:

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  Plak in het **defaultValue** ** \<bestand resourcegroepnaam>.json** de **resource-id** van de variabele in plaats van de standaardwaarde in de tweede parameter voor de openbare IP-externe ID, ervoor te zorgen dat u het pad in aanhalingstekens omsluit:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "loadBalancers_myLoadbalancer_ext_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
            },
            "publicIPAddresses_myPubIP_in_externalid": {
            "defaultValue": "<target-publicIP-resource-ID>",
            "type": "String"
            },

    ```

8.  Als u uitgaande NAT- en uitgaande regels hebt geconfigureerd voor de load balancer, is in dit bestand een derde vermelding aanwezig voor de externe id voor het uitgaande openbare IP-adres.  Herhaal de bovenstaande stappen in het **doelgebied** om de id voor de uitgaande openbare iP te verkrijgen en plak die vermelding in het ** \<bestand resourcegroep-naam>.json:**

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "defaultValue": "<target-external-lb-name>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "defaultValue": "<target-publicIP-resource-ID>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                "type": "String"
            }
        },
    ```

10. Als u het doelgebied wilt bewerken waar de configuratie van de externe load balancer wordt verplaatst, wijzigt u de **locatieeigenschap** onder **resources** in het ** \<bestand resourcegroepnaam>.json:**

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
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
    
    * **Sku** - U de sku van de externe load balancer in de configuratie wijzigen van standaard naar basis- of standaard door de eigenschap **sku-naam** > **name** in het ** \<bestand resourcegroep-naam>.json** te wijzigen:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
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

    * **Uitgaande regels** : u uitgaande regels in de configuratie toevoegen of verwijderen door de eigenschap **outboundRules** te bewerken in het ** \<bestand>.json-** bestand voor resourcegroepgroepen:

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Zie [Uitgaande regels voor Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview) voor meer informatie over uitgaande regels

13. Sla het ** \<brongroepnaam>.json-bestand** op.
    
10. Maak of een resourcegroep in de doelregio voor de externe laadbalans die moet worden geïmplementeerd met [Nieuwe AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). De bestaande resourcegroep van bovenaf kan ook worden hergebruikt als onderdeel van dit proces:
    
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

Als u na de implementatie opnieuw wilt beginnen of de openbare IP- en loadbalancer in het doel wilt verwijderen, verwijdert u de brongroep die in het doel is gemaakt en worden de verplaatste openbare IP- en loadbalancer verwijderd.  Als u de brongroep wilt verwijderen, gebruikt u [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Opruimen

Als u de wijzigingen wilt vastleggen en de verplaatsing van de NSG wilt voltooien, verwijdert u de bron NSG of resourcegroep, gebruikt [u Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) of [Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0) en [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure-netwerkbeveiligingsgroep van de ene regio naar de andere verplaatst en de bronbronnen opgeschoond.  Zie voor meer informatie over het verplaatsen van resources tussen regio's en disaster recovery in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Virtuele Azure-machines verplaatsen naar een andere regio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
