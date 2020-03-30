---
title: Azure-netwerkbeveiligingsgroep (NSG) verplaatsen naar een andere Azure-regio met Azure PowerShell
description: Gebruik de sjabloon Azure Resource Manager om azure-netwerkbeveiligingsgroep van de ene Azure-regio naar de andere te verplaatsen met Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 0cbd8f61cb1b4cb8eae6b30625fb3039ff75adde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641465"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Azure-netwerkbeveiligingsgroep (NSG) verplaatsen naar een andere regio met Azure PowerShell

Er zijn verschillende scenario's waarin u uw bestaande NSGs van de ene regio naar de andere wilt verplaatsen. U bijvoorbeeld een NSG maken met dezelfde configuratie- en beveiligingsregels voor het testen. U ook een NSG verplaatsen naar een andere regio als onderdeel van de planning voor noodherstel.

Azure-beveiligingsgroepen kunnen niet van de ene regio naar de andere worden verplaatst. U echter wel een Azure Resource Manager-sjabloon gebruiken om de bestaande configuratie- en beveiligingsregels van een NSG te exporteren.  U de resource vervolgens in een andere regio fasen door de NSG naar een sjabloon te exporteren, de parameters te wijzigen die overeenkomen met het doelgebied en de sjabloon vervolgens te implementeren in de nieuwe regio.  Zie [Brongroepen exporteren naar sjablonen voor](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)meer informatie over Resourcemanager en sjablonen.


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de Azure-netwerkbeveiligingsgroep zich in het Azure-gebied bevindt van waaruit u wilt verplaatsen.

- Azure-netwerkbeveiligingsgroepen kunnen niet tussen regio's worden verplaatst.  U moet de nieuwe NSG koppelen aan resources in de doelregio.

- Als u een NSG-configuratie wilt exporteren en een sjabloon wilt implementeren om een NSG in een andere regio te maken, hebt u de rol Netwerkbijdrager of hoger nodig.
   
- Identificeer de indeling van de bronnetwerkindeling en alle bronnen die u momenteel gebruikt. Deze lay-out omvat, maar is niet beperkt tot load balancers, openbare IP's en virtuele netwerken.

- Controleer of u met uw Azure-abonnement NSG's maken in het doelgebied dat wordt gebruikt. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende middelen heeft om de toevoeging van NSG's voor dit proces te ondersteunen.  Zie [Azure-abonnement en -servicelimieten, quotums en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen
In de volgende stappen wordt uitgelegd hoe u de netwerkbeveiligingsgroep voorbereidt op de verplaatsing van de configuratie- en beveiligingsregel met behulp van een Resource Manager-sjabloon en de NSG-configuratie- en beveiligingsregels met Azure PowerShell naar het doelgebied verplaatsen.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>De sjabloon exporteren en implementeren vanuit een script

1. Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) en volg de aanwijzingen op het scherm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Verkrijg de resource-ID van de NSG die u naar de doelregio wilt verplaatsen en plaats deze in een variabele met [Get-AzNetworkSecurityGroup:](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporteer de bron NSG naar een .json-bestand naar de map waar u de opdracht [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)uitvoert:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. Het gedownloade bestand wordt vernoemd naar de resourcegroep waaruit de resource is geëxporteerd.  Zoek het bestand dat is geëxporteerd vanuit de opdracht met ** \<de naam resourcegroep>.json** en open het in een editor naar keuze:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Als u de parameter van de NSG-naam wilt bewerken, wijzigt u de standaardwaarde van de **eigenschapsnaam** VAN de bron NSG-naam in de naam van uw doel NSG, moet u ervoor zorgen dat de naam tussen aanhalingstekens staat:
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. Als u het doelgebied wilt bewerken waar de NSG-configuratie- en beveiligingsregels worden verplaatst, wijzigt u de **locatieeigenschap** onder **resources:**

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. Als u regiolocatiegegevens wilt verkrijgen, u de Azure PowerShell-cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) gebruiken door de volgende opdracht uit te voeren:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. U ook andere parameters wijzigen in de naam van de ** \<brongroep>.json** als u dat wilt, en zijn optioneel, afhankelijk van uw vereisten:

    * **Beveiligingsregels** : u bewerken welke regels worden geïmplementeerd in de doel-NSG door regels toe te voegen of te verwijderen aan de sectie **securityRules** in het ** \<bestand>.json-** bestand met brongroepnaam:

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        Als u de toevoeging of verwijdering van de regels in de doel-NSG wilt voltooien, moet u ook de aangepaste regeltypen bewerken aan het einde van het ** \<brongroepnaam->.json-bestand** in de indeling van het onderstaande voorbeeld:

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. Sla het ** \<brongroepnaam>.json-bestand** op.

10. Maak een resourcegroep in de doelregio voor de beoogde NSG die moet worden geïmplementeerd met [Nieuwe-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Het gebewerkte ** \<brongroepnaam>.json-bestand** implementeren in de resourcegroep die in de vorige stap is gemaakt met [Nieuwe AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Gebruik [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) en [Get-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0)om te controleren of de resources in de doelregio zijn gemaakt:
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Verwijderen 

Als u na de implementatie opnieuw wilt beginnen of de NSG in het doel wilt verwijderen, verwijdert u de brongroep die in het doel is gemaakt en wordt de verplaatste NSG verwijderd.  Als u de brongroep wilt verwijderen, gebruikt u [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Opruimen

Als u de wijzigingen wilt doorvoeren en de verplaatsing van de NSG wilt voltooien, verwijdert u de bron NSG of resourcegroep, gebruikt [u Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) of [Remove-AzNetworkSecurityGroup:](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworksecuritygroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure-netwerkbeveiligingsgroep van de ene regio naar de andere verplaatst en de bronbronnen opgeschoond.  Zie voor meer informatie over het verplaatsen van resources tussen regio's en disaster recovery in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Virtuele Azure-machines verplaatsen naar een andere regio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
