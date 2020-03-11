---
title: 'Snelstartgids: een NAT-gateway maken-Resource Manager-sjabloon'
titleSuffix: Azure Virtual Network NAT
description: In deze Quick start ziet u hoe u een NAT-gateway maakt met behulp van de sjabloon Azure Resource Manager.
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
ms.openlocfilehash: 0f6a303bfa42538bf56efa7e9f669588c8bff66c
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082724"
---
# <a name="quickstart-create-a-nat-gateway---resource-manager-template"></a>Snelstartgids: een NAT-gateway maken-Resource Manager-sjabloon

Ga aan de slag met Virtual Network NAT met behulp van een Azure Resource Manager sjabloon.  Met deze sjabloon implementeert u een virtueel netwerk, een NAT-gateway en een virtuele Ubuntu-machine. De virtuele Ubuntu-machine wordt geïmplementeerd op een subnet dat is gekoppeld aan de NAT-gateway.

>[!NOTE] 
>Azure Virtual Network NAT is op dit moment beschikbaar als open bare preview en beschikbaar in een beperkt aantal [regio's](https://azure.microsoft.com/global-infrastructure/regions/). Deze preview is beschikbaar zonder service level agreement en wordt niet aanbevolen voor productie werkbelastingen. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-nat-gateway-and-supporting-resources"></a>Een NAT-gateway en ondersteunende bronnen maken

Deze sjabloon is geconfigureerd voor het maken van een 

* Virtueel netwerk 
* NAT-gateway resource
* Virtuele machine Ubuntu

De Ubuntu-VM wordt geïmplementeerd op een subnet dat is gekoppeld aan de NAT-gateway bron.

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json) start-sjablonen

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json" range="1-335" highlight="256-282":::

Er zijn negen Azure-resources gedefinieerd in de sjabloon:

**Micro soft. Network**

* **[Micro soft. Network/natGateways](https://docs.microsoft.com/azure/templates/microsoft.network/natgateways)** : Hiermee wordt een NAT-gateway bron gemaakt.

* **[Micro soft. Network/networkSecurityGroups](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)** : Hiermee wordt een netwerk beveiligings groep gemaakt.

    * **[Micro soft. Network/networkSecurityGroups/securityRules](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups/securityrules)** : Hiermee maakt u een beveiligings regel.

* **[Micro soft. Network/publicIPAddresses](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)** : maakt een openbaar IP-adres.

* **[Micro soft. Network/publicIPPrefixes](https://docs.microsoft.com/azure/templates/microsoft.network/publicipprefixes)** : maakt een openbaar IP-voor voegsel.

* **[Micro soft. Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)** : Hiermee maakt u een virtueel netwerk.

    * **[Micro soft. Network/virtualNetworks/subnets](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks/subnets)** : Hiermee maakt u een subnet van een virtueel netwerk.

* **[Micro soft. Network/networkinterfaces](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)** : Hiermee maakt u een netwerk interface.

**Micro soft. compute**

* **[Micro soft. Compute/informatie](https://docs.microsoft.com/azure/templates/Microsoft.Compute/virtualMachines)** : Hiermee maakt u een virtuele machine.

### <a name="deploy-the-template"></a>De sjabloon implementeren

**Azure CLI**

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

**Azure-portal**

[![Implementeren in Azure](./media/quick-create-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Selecteer **resource groepen** in het linkerdeel venster.

3. Selecteer de resource groep die u in de vorige sectie hebt gemaakt. De standaard naam van de resource groep is **myResourceGroupNAT**

4. Controleer of de volgende resources zijn gemaakt in de resource groep:

    ![NAT-resource groep Virtual Network](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Resources opschonen

**Azure CLI**

U kunt de opdracht [AZ Group delete](/cli/azure/group#az-group-delete) gebruiken om de resource groep en alle resources in te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

U kunt de opdracht [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) gebruiken om de resource groep en alle resources in te verwijderen wanneer u deze niet meer nodig hebt.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Azure-portal**

Als u deze niet meer nodig hebt, verwijdert u de resource groep, de NAT-gateway en alle gerelateerde resources. Selecteer de **myResourceGroupNAT** van de resource groep die de NAT-gateway bevat en selecteer vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u het volgende gemaakt:

* NAT-gateway resource
* Virtueel netwerk
* Virtuele machine Ubuntu

De virtuele machine wordt geïmplementeerd naar een subnet van een virtueel netwerk dat is gekoppeld aan de NAT-gateway. 

Ga door naar de volgende artikelen voor meer informatie over Virtual Network NAT en Azure Resource Manager.

* Een [overzicht van Virtual Network NAT](nat-overview.md) lezen
* Meer informatie over de [NAT-gateway resource](nat-gateway-resource.md)
* Meer informatie over [Azure Resource Manager](../azure-resource-manager/management/overview.md)
