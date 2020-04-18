---
title: 'Snelstart: een NAT-gateway maken - Sjabloon Resourcebeheer'
titleSuffix: Azure Virtual Network NAT
description: In deze snelstart ziet u hoe u een NAT-gateway maakt met behulp van de sjabloon Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway by using an Azure Resource Manager template so that I can provide outbound connectivity for my virtual machines.
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2020
ms.author: allensu
ms.custom: subject-armqs
ms.openlocfilehash: 3850f3f22e730e46f6d278b6cef0e17d357b126d
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618045"
---
# <a name="quickstart-create-a-nat-gateway---resource-manager-template"></a>Snelstart: een NAT-gateway maken - sjabloon Resourcemanager

Ga aan de slag met Virtual Network NAT met behulp van een Azure Resource Manager-sjabloon.  Deze sjabloon implementeert een virtueel netwerk, een NAT-gatewaybron en een virtuele ubuntu-machine. De virtuele ubuntu-machine wordt geïmplementeerd in een subnet dat is gekoppeld aan de NAT-gatewaybron.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-a-nat-gateway-and-supporting-resources"></a>Een NAT-gateway maken en resources ondersteunen

Deze sjabloon is geconfigureerd om een 

* Virtueel netwerk 
* NAT-gatewaybron
* Ubuntu virtuele machine

De Ubuntu VM wordt geïmplementeerd in een subnet dat is gekoppeld aan de NAT-gatewaybron.

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json" range="1-335" highlight="256-282":::

Negen Azure-resources worden gedefinieerd in de sjabloon:

**Microsoft.Network**

* **[Microsoft.Network/natGateways:](https://docs.microsoft.com/azure/templates/microsoft.network/natgateways)** hiermee wordt een NAT-gatewaybron gemaakt.

* **[Microsoft.Network/networkSecurityGroups](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)**: Hiermee maakt u een netwerkbeveiligingsgroep.

    * **[Microsoft.Network/networkSecurityGroups/securityRules:](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups/securityrules)** Maakt een beveiligingsregel.

* **[Microsoft.Network/publicIPAddresses:](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)** Hiermee maakt u een openbaar IP-adres.

* **[Microsoft.Network/publicIPPrefixes:](https://docs.microsoft.com/azure/templates/microsoft.network/publicipprefixes)** Maakt een openbaar IP-voorvoegsel.

* **[Microsoft.Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)**: Maakt een virtueel netwerk.

    * **[Microsoft.Network/virtualNetworks/subnetten:](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks/subnets)** Hiermee maakt u een virtueel netwerksubnet.

* **[Microsoft.Network/networkinterfaces](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)**: Hiermee maakt u een netwerkinterface.

**Microsoft.Compute**

* **[Microsoft.Compute/virtualMachines](https://docs.microsoft.com/azure/templates/Microsoft.Compute/virtualMachines)**: Maakt een virtuele machine.

### <a name="deploy-the-template"></a>De sjabloon implementeren

**Azure-CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupNAT"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az group deployment create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

**Azure PowerShell**

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. westcentralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

$resourceGroupName = "myResourceGroupNAT"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

**Azure Portal**

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Selecteer **Resourcegroepen** in het linkerdeelvenster.

3. Selecteer de resourcegroep die u in de vorige sectie hebt gemaakt. De standaardnaam van de resourcegroep is **myResourceGroupNAT**

4. Controleer of de volgende resources zijn gemaakt in de resourcegroep:

    ![Nat-brongroep voor virtuele netwerken](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Resources opschonen

**Azure-CLI**

Wanneer dit niet meer nodig is, u de opdracht verwijderen van de [AZ-groep](/cli/azure/group#az-group-delete) gebruiken om de brongroep en alle bronnen binnen te verwijderen.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

Wanneer dit niet meer nodig is, u de opdracht [Verwijderen-AzResourceGroep](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) gebruiken om de resourcegroep en alle bronnen binnen te verwijderen.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Azure Portal**

Verwijder de brongroep, NAT-gateway en alle gerelateerde bronnen wanneer dit niet meer nodig is. Selecteer de brongroep **myResourceGroupNAT** die de NAT-gateway bevat en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een:

* NAT-gatewaybron
* Virtueel netwerk
* Ubuntu virtuele machine

De virtuele machine wordt geïmplementeerd in een virtueel netwerksubnet dat is gekoppeld aan de NAT-gateway. 

Ga verder naar de onderstaande artikelen voor meer informatie over Nat voor virtueel netwerk NAT en Azure Resource Manager.

* Lees een [overzicht van Virtual Network NAT](nat-overview.md)
* Lees meer over de [NAT Gateway-bron](nat-gateway-resource.md)
* Meer informatie over [Azure Resource Manager](../azure-resource-manager/management/overview.md)
