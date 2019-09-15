---
title: Azure Virtual Network verplaatsen naar een andere Azure-regio met behulp van Azure PowerShell
description: Gebruik Azure Resource Manager sjabloon om Azure Virtual Network van de ene Azure-regio naar de andere te verplaatsen met behulp van Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 0037419570ceffcd11dbc8dfe85b45d7ec0ee8ce
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997443"
---
# <a name="move-azure-virtual-network-to-another-region-using-azure-powershell"></a>Azure-Virtual Network verplaatsen naar een andere regio met behulp van Azure Power shell

Er zijn verschillende scenario's waarin u uw bestaande Azure Virtual Networks (VNETs) wilt verplaatsen van de ene regio naar een andere. U kunt bijvoorbeeld een virtueel netwerk maken met dezelfde configuratie voor testen en beschik baarheid van uw bestaande virtuele netwerk. Het is ook mogelijk dat u een virtueel netwerk voor productie naar een andere regio wilt verplaatsen als onderdeel van de planning voor nood herstel.

U kunt een Azure Resource Manager sjabloon gebruiken om de verplaatsing van het virtuele netwerk naar een andere regio te volt ooien. Hiervoor exporteert u het virtuele netwerk naar een sjabloon, wijzigt u de para meters zodat deze overeenkomen met de doel regio en implementeert u de sjabloon vervolgens in de nieuwe regio.  Zie [resource groepen exporteren naar sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) voor meer informatie over Resource Manager en sjablonen


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de Azure-Virtual Network zich bevindt in de Azure-regio van waaruit u wilt verplaatsen.

- Als u een virtueel netwerk wilt exporteren en een sjabloon wilt implementeren om een virtueel netwerk in een andere regio te maken, hebt u de rol netwerk bijdrager of hoger nodig.

- Peering van virtuele netwerken wordt niet opnieuw gemaakt en mislukt als deze nog in de sjabloon aanwezig zijn.  U moet alle peers van het virtuele netwerk verwijderen voordat u de sjabloon exporteert en vervolgens de peers na het verplaatsen van het virtuele netwerk opnieuw tot stand brengt.
    
- Identificeer de bron netwerk indeling en alle resources die u momenteel gebruikt. Deze indeling bevat, maar is niet beperkt tot load balancers, netwerk beveiligings groepen (Nsg's) en open bare Ip's.

- Controleer of u met uw Azure-abonnement virtuele netwerken kunt maken in de doel regio die wordt gebruikt. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende bronnen heeft ter ondersteuning van het toevoegen van virtuele netwerken voor dit proces.  Zie [Azure-abonnement en service limieten, quota's en beperkingen](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen
De volgende stappen laten zien hoe u het virtuele netwerk voorbereidt voor de verplaatsing met behulp van een resource manager-sjabloon en het virtuele netwerk naar de doel regio verplaatst met behulp van Azure PowerShell-opdrachten.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-and-deploy-the-target-virtual-network-with-powershell"></a>Het virtuele netwerk exporteren en het virtuele doel netwerk implementeren met Power shell

1. Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) en volg de instructies op het scherm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Vraag de abonnements-ID op waar u het virtuele doel netwerk wilt implementeren en plaats het in een variabele met [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0):
   
    ```azurepowershell-interactive
    Get-AzSubscription | format-table

    $Subscription = "MySubscriptionID"
    ```
3. Haal de bron-ID op van het virtuele netwerk dat u wilt verplaatsen naar de doel regio en plaats deze in een variabele met [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
4. Exporteer het virtuele bron netwerk naar een. JSON-bestand naar de map waar u de opdracht [export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)uitvoert:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

5. Het bestand dat u hebt gedownload krijgt de naam van de resource groep waaruit de resource is geëxporteerd.  Zoek het bestand dat is geëxporteerd uit de opdracht met de naam **< Resource-Group-name >. json** en open het in een editor naar keuze:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

6. Als u de para meter van de naam van het virtuele netwerk wilt bewerken, wijzigt u de eigenschap **DefaultValue** van de virtuele bron netwerk naam in de naam van het virtuele netwerk van het doel. Controleer of de naam tussen aanhalings tekens is:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

7.  Als u de doel regio wilt bewerken waar het VNET wordt verplaatst, wijzigt u de eigenschap **Location** onder resources:

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
  
8. Als u regio codes wilt ophalen, kunt u de Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) gebruiken door de volgende opdracht uit te voeren:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
9.  U kunt ook andere para meters wijzigen in het **< Resource-Group-name >. json-** bestand als u ervoor kiest en zijn optioneel, afhankelijk van uw vereisten:

    * **Adres ruimte** : de adres ruimte van het VNET kan worden gewijzigd voordat u opslaat door de sectie **resources** > **addressSpace** te wijzigen en de eigenschap **addressPrefixes** in de **< Resource-Group-name te wijzigen >. json** -bestand:
    
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

    * **Subnet** : de naam van het subnet en de adres ruimte van het subnet kunnen worden gewijzigd of toegevoegd aan door de sectie **subnetten** van het **< resource-group-name >. json-** bestand te wijzigen. De naam van het subnet kan worden gewijzigd door de eigenschap **name** te wijzigen. De adres ruimte van het subnet kan worden gewijzigd door de eigenschap **addressPrefix** in het **< Resource-group-name >. json-** bestand te wijzigen:
    
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
                    ],
    ```

    In het **< Resource-Group-name >. json** -bestand, om het adres voorvoegsel te wijzigen, moet het worden bewerkt op twee plaatsen, de hierboven vermelde sectie en de sectie **type** die hieronder wordt weer gegeven.  Wijzig de eigenschap **addressPrefix** zodat deze overeenkomt met het bovenstaande:
                
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

10. Sla de **< Resource-Group-name >. json** -bestand op.

11. Maak een resource groep in de doel regio voor het doel-VNET dat moet worden geïmplementeerd met [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
12. Implementeer het bewerkte **< Resource-Group-name >. json** -bestand in de resource groep die u in de vorige stap hebt gemaakt met behulp van [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

13. Als u wilt controleren of de resources zijn gemaakt in de doel regio, gebruikt u [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) en [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Verwijderen 

Als u na de implementatie wilt beginnen of het virtuele netwerk in het doel wilt verwijderen, verwijdert u de resource groep die is gemaakt in het doel en wordt het verplaatste virtuele netwerk verwijderd.  Gebruik [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)om de resource groep te verwijderen:

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Opruimen

Als u de wijzigingen wilt door voeren en de verplaatsing van het virtuele netwerk wilt volt ooien, verwijdert u het virtuele bron netwerk of de resource groep, gebruikt u [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) of [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Azure-Virtual Network verplaatst van de ene regio naar een andere en de bron resources opgeschoond.  Raadpleeg voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Virtuele Azure-machines verplaatsen naar een andere regio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
