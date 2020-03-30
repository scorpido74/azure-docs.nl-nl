---
title: Azure Public IP verplaatsen naar een andere Azure-regio met Azure PowerShell
description: Gebruik azure resource beheersjabloon om Azure Public IP van de ene Azure-regio naar de andere te verplaatsen met Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: c55b6011381d385fed7c7b8175ff02ec9be66fdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641550"
---
# <a name="move-azure-public-ip-to-another-region-using-azure-powershell"></a>Azure Public IP verplaatsen naar een andere regio met Azure PowerShell

Er zijn verschillende scenario's waarin u uw bestaande Azure Public IP's van de ene regio naar de andere wilt verplaatsen. U bijvoorbeeld een openbaar IP maken met dezelfde configuratie en sku voor het testen. U ook een openbaar IP-adres verplaatsen naar een andere regio als onderdeel van de planning voor noodherstel.

Azure Public IP's zijn regiospecifiek en kunnen niet van de ene regio naar de andere worden verplaatst. U echter wel een Azure Resource Manager-sjabloon gebruiken om de bestaande configuratie van een openbaar IP-adres te exporteren.  U de resource vervolgens in een andere regio faseren door het openbare IP-adres naar een sjabloon te exporteren, de parameters te wijzigen die overeenkomen met het doelgebied en de sjabloon vervolgens te implementeren in de nieuwe regio.  Zie [Brongroepen exporteren naar sjablonen voor](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) meer informatie over ResourceManager en sjablonen


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat het Azure Public IP zich in de Azure-regio bevindt van waaruit u wilt verplaatsen.

- Azure Public IP's kunnen niet tussen regio's worden verplaatst.  U moet het nieuwe openbare ip koppelen aan resources in de doelregio.

- Als u een openbare IP-configuratie wilt exporteren en een sjabloon wilt implementeren om een openbaar IP-adres in een andere regio te maken, hebt u de rol Netwerkinzender of hoger nodig.
   
- Identificeer de indeling van de bronnetwerkindeling en alle bronnen die u momenteel gebruikt. Deze lay-out omvat, maar is niet beperkt tot load balancers, network security groups (NSGs) en virtuele netwerken.

- Controleer of u met uw Azure-abonnement openbare IP's maken in het doelgebied dat wordt gebruikt. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende middelen heeft om de toevoeging van openbare IP's voor dit proces te ondersteunen.  Zie [Azure-abonnement en -servicelimieten, quotums en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen
In de volgende stappen wordt uitgelegd hoe u het openbare IP-adres voorbereiden op de configuratieverplaatsing met behulp van een Resource Manager-sjabloon en de openbare IP-configuratie naar het doelgebied verplaatsen met Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>De sjabloon exporteren en implementeren vanuit een script

1. Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) en volg de aanwijzingen op het scherm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Verkrijg de resource-ID van het openbare IP dat u naar het doelgebied wilt verplaatsen en plaats deze in een variabele met [Get-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporteer het virtuele bronnetwerk naar een .json-bestand naar de map waar u de opdracht [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)uitvoert:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Het gedownloade bestand wordt vernoemd naar de resourcegroep waaruit de resource is geëxporteerd.  Zoek het bestand dat is geëxporteerd vanuit de opdracht met ** \<de naam resourcegroep>.json** en open het in een editor naar keuze:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
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
## <a name="discard"></a>Verwijderen 

Als u na de implementatie opnieuw wilt beginnen of het openbare ip-adres in het doel wilt verwijderen, verwijdert u de brongroep die in het doel is gemaakt en wordt het verplaatste openbare IP verwijderd.  Als u de brongroep wilt verwijderen, gebruikt u [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Opruimen

Als u de wijzigingen wilt doorvoeren en de verplaatsing van het virtuele netwerk wilt voltooien, verwijdert u het virtuele netwerk of de brongroep, gebruikt [u Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) of [Remove-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure Public IP verplaatst van de ene regio naar de andere en de bronbronnen opgeschoond.  Zie voor meer informatie over het verplaatsen van resources tussen regio's en disaster recovery in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Virtuele Azure-machines verplaatsen naar een andere regio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
