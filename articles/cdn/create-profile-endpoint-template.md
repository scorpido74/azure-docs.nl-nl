---
title: 'Quickstart: Een profiel en eindpunt maken - Resource Manager-sjabloon'
titleSuffix: Azure Content Delivery Network
description: Meer informatie over het maken van een Azure Content Delivery Network-profiel en eindpunt met een Resource Manager-sjabloon
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/25/2020
ms.author: allensu
ms.openlocfilehash: aca3c9c52260d3ea2ab8def37566377fa67e712d
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705925"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---arm-template"></a>Quickstart: Een Azure CDN-profiel en -eindpunt maken - ARM-sjabloon

Ga aan de slag met Azure Content Delivery Network (CDN) door een Azure Resource Manager-sjabloon (ARM-sjabloon) te gebruiken. Met de sjabloon wordt een profiel en een eindpunt geïmplementeerd.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-cdn-with-custom-origin/).

Deze sjabloon is geconfigureerd voor het maken van een:

* Profiel
* Eindpunt

:::code language="json" source="~/quickstart-templates/101-cdn-with-custom-origin/azuredeploy.json":::

Er is één Azure-resource gedefinieerd in de sjabloon:

* **[Microsoft.Cdn/profiles](https://docs.microsoft.com/azure/templates/microsoft.cdn/profiles)**

## <a name="deploy-the-template"></a>De sjabloon implementeren

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
read -p "Enter the location (i.e. eastus): " location
resourceGroupName="myResourceGroupCDN"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az group deployment create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json"

$resourceGroupName = "myResourceGroupCDN"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

### <a name="portal"></a>Portal

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Selecteer **Resourcegroepen** in het linkerdeelvenster.

3. Selecteer de resourcegroep die u in de vorige sectie hebt gemaakt. De standaardnaam van de resourcegroep is **myResourceGroupCDN**

4. Controleer of de volgende resources zijn gemaakt in de resourcegroep:

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Azure CDN-resourcegroep" border="true":::

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="azure-cli"></a>Azure CLI

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep en alle gerelateerde resources daarin te verwijderen wanneer u ze niet meer nodig hebt.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

### <a name="powershell"></a>PowerShell

U kunt de opdracht [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) gebruiken om de resourcegroep en alle gerelateerde resources daarin te verwijderen wanneer u ze niet meer nodig hebt.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

### <a name="portal"></a>Portal

U kunt de resourcegroep, het CDN-profiel en alle gerelateerde resources verwijderen wanneer u ze niet meer nodig hebt. Selecteer de resourcegroep **myResourceGroupCDN** die het CDN-profiel en het eindpunt bevat en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u het volgende gemaakt:

* CDN-profiel
* Eindpunt

Als u meer wilt weten over Azure CDN en Azure Resource Manager, vindt u meer informatie in de onderstaande artikelen.

* Een [overzicht van Azure CDN](cdn-overview.md) lezen
* Meer informatie over [Azure Resource Manager](../azure-resource-manager/management/overview.md)