---
title: Een virtueel Azure-netwerk verplaatsen naar een andere Azure-regio met Azure PowerShell
description: Verplaats een Virtueel Azure-netwerk van de ene Azure-regio naar de andere met behulp van een Resource Manager-sjabloon en Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: dc316e5bbb88359ff8b1e8a4fc35a56541a577f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646707"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Een virtueel Azure-netwerk verplaatsen naar een andere regio met Azure PowerShell

Er zijn verschillende scenario's voor het verplaatsen van een bestaand Virtueel Azure-netwerk van de ene regio naar de andere. U bijvoorbeeld een virtueel netwerk maken met dezelfde configuratie voor testen en beschikbaarheid als uw bestaande virtuele netwerk. Of u wilt een virtueel productienetwerk verplaatsen naar een andere regio als onderdeel van uw planning voor noodherstel.

U een Azure Resource Manager-sjabloon gebruiken om de verplaatsing van het virtuele netwerk naar een andere regio te voltooien. Dit doe je door het virtuele netwerk naar een sjabloon te exporteren, de parameters te wijzigen die overeenkomen met het doelgebied en vervolgens de sjabloon naar het nieuwe gebied te implementeren. Zie [Resourcegroepen exporteren naar sjablonen voor](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)meer informatie over resourcebeheersjablonen.


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat uw virtuele netwerk zich in de Azure-regio bevindt waarvan u wilt overstappen.

- Als u een virtueel netwerk wilt exporteren en een sjabloon wilt implementeren om een virtueel netwerk in een andere regio te maken, moet u de rol Netwerkbijdrager of hoger hebben.

- Virtuele netwerkpeeringen worden niet opnieuw gemaakt en ze mislukken als ze nog steeds aanwezig zijn in de sjabloon. Voordat u de sjabloon exporteert, moet u virtuele netwerkpeers verwijderen. U ze vervolgens herstellen na de virtuele netwerkverplaatsing.
    
- Identificeer de indeling van de bronnetwerkindeling en alle bronnen die u momenteel gebruikt. Deze lay-out omvat, maar is niet beperkt tot load balancers, network security groups (NSGs) en openbare IP's.

- Controleer of u met uw Azure-abonnement virtuele netwerken maken in het doelgebied. Neem contact op met ondersteuning om het vereiste quotum in te schakelen.

- Zorg ervoor dat uw abonnement voldoende middelen heeft om de toevoeging van virtuele netwerken voor dit proces te ondersteunen. Zie [Azure-abonnement- en servicelimieten, quota en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits) voor meer informatie.


## <a name="prepare-for-the-move"></a>Bereid je voor op de verhuizing
In deze sectie bereidt u het virtuele netwerk voor op de verhuizing met behulp van een resourcemanagersjabloon. Vervolgens verplaatst u het virtuele netwerk naar het doelgebied met Azure PowerShell-opdrachten.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ga als volgt te werk om het virtuele netwerk te exporteren en het virtuele doelnetwerk te implementeren met PowerShell:

1. Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) en volg vervolgens de aanwijzingen op het scherm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Verkrijg de resource-ID van het virtuele netwerk dat u naar het doelgebied wilt verplaatsen en plaats deze vervolgens in een variabele met [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Exporteer het virtuele bronnetwerk naar een .json-bestand in de map waarin u de opdracht [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)uitvoert:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. Het gedownloade bestand heeft dezelfde naam als de resourcegroep waaruit de resource is geëxporteerd. Zoek het * \<bestand resourcegroep-naam>.json,* dat u met de opdracht hebt geëxporteerd, en open het vervolgens in uw editor:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. Als u de parameter van de naam van het virtuele netwerk wilt bewerken, wijzigt u de eigenschap **defaultValue** van de naam van het virtuele netwerk van de bron in de naam van uw virtuele doelnetwerk. Zorg ervoor dat u de naam tussen aanhalingstekens insluit.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. Als u het doelgebied wilt bewerken waar het virtuele netwerk wordt verplaatst, wijzigt u de **locatieeigenschap** onder resources:

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
  
1. Als u regiolocatiegegevens wilt verkrijgen, u de Azure PowerShell-cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) gebruiken door de volgende opdracht uit te voeren:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. (Optioneel) U ook andere parameters wijzigen in het * \<brongroepnaam->.json-bestand,* afhankelijk van uw vereisten:

    * **Adresruimte:** Voordat u het bestand opslaat, u de adresruimte van het virtuele netwerk wijzigen door de sectie **resources** > **addressSpace** te wijzigen en de eigenschap **adresvoorvoegsels te** wijzigen:

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

    * **Subnet**: U de subnetnaam en de subnetadresruimte wijzigen of toevoegen door de **subnettensectie** van het bestand te wijzigen. U de naam van het subnet wijzigen door de **eigenschap naam** te wijzigen. En u de subnetadresruimte wijzigen door de eigenschap **adresvoorvoegsel te** wijzigen:

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

        Als u het adresvoorvoegsel wilt wijzigen, bewerkt u het bestand op twee plaatsen: in de code in de vorige sectie en in het **tekstgedeelte** van de volgende code. Wijzig de eigenschap **adresVoorvoegsel** in de volgende code om overeen te komen met de eigenschap **adresVoorvoegsel** in de code in de vorige sectie.

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

1. Sla het * \<brongroepnaam>.json-bestand* op.

1. Maak een resourcegroep in de doelregio voor het doelvirtuele netwerk dat moet worden geïmplementeerd met [Nieuw-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Implementeer het * \<>.json-bestand* met bewerkte brongroep in de resourcegroep die u in de vorige stap hebt gemaakt met [Nieuwe AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. Als u wilt controleren of de resources in de doelregio zijn gemaakt, gebruikt u [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) en [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>De virtuele netwerk- of brongroep verwijderen 

Nadat u het virtuele netwerk hebt geïmplementeerd om het virtuele netwerk in de doelregio opnieuw te starten of te verwijderen, verwijdert u de brongroep die u in de doelregio hebt gemaakt en wordt het verplaatste virtuele netwerk verwijderd. 

Als u de brongroep wilt verwijderen, gebruikt u [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>Opruimen

Voer een van de volgende handelingen uit om uw wijzigingen te voltooien en de virtuele netwerkverplaatsing te voltooien:

* Verwijder de brongroep met [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Verwijder het virtuele bronnetwerk met [Remove-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0)  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie verplaatste u een virtueel netwerk van de ene regio naar de andere met behulp van PowerShell en vervolgens de onnodige bronbronnen opschonen. Zie voor meer informatie over het verplaatsen van resources tussen regio's en disaster recovery in Azure:

- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-virtuele machines verplaatsen naar een andere regio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
