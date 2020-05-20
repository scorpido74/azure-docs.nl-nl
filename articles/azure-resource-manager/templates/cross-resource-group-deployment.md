---
title: '& resource groep voor het implementeren van resources voor meerdere abonnementen'
description: Laat zien hoe u tijdens de implementatie meer dan één Azure-abonnement en-resource groep kunt bereiken.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 2ef68dcb933075833c323d973b023cdaee61bd2f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650637"
---
# <a name="deploy-azure-resources-across-subscriptions-or-resource-groups"></a>Azure-resources implementeren in abonnementen of resource groepen

Met Resource Manager kunt u in één implementatie naar meer dan één resource groep implementeren. U gebruikt geneste sjablonen om resource groepen op te geven die verschillen van de resource groep in de implementatie bewerking. De resource groepen kunnen bestaan in verschillende abonnementen.

> [!NOTE]
> U kunt implementeren naar **800-resource groepen** in één implementatie. Deze beperking betekent meestal dat u kunt implementeren in één resource groep die is opgegeven voor de bovenliggende sjabloon en Maxi maal 799 resource groepen in geneste of gekoppelde implementaties. Als uw bovenliggende sjabloon alleen geneste of gekoppelde sjablonen bevat en zelf geen resources implementeert, kunt u Maxi maal 800 resource groepen toevoegen in geneste of gekoppelde implementaties.

## <a name="specify-subscription-and-resource-group"></a>Abonnement en resource groep opgeven

Gebruik een [geneste of gekoppelde sjabloon](linked-templates.md)om een resource groep te richten die afwijkt van die van de bovenliggende sjabloon. Geef binnen het type implementatie resource waarden op voor de abonnements-ID en de resource groep waarop u wilt dat de geneste sjabloon wordt geïmplementeerd.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

Als u de abonnements-ID of de resource groep niet opgeeft, worden het abonnement en de resource groep van de bovenliggende sjabloon gebruikt. Alle resource groepen moeten bestaan voordat u de implementatie uitvoert.

Het account dat de sjabloon implementeert, moet gemachtigd zijn om te implementeren naar de opgegeven abonnements-ID. Als het opgegeven abonnement bestaat in een andere Azure Active Directory Tenant, moet u [gast gebruikers toevoegen vanuit een andere map](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

In het volgende voor beeld worden twee opslag accounts geïmplementeerd. Het eerste opslag account wordt geïmplementeerd in de resource groep die is opgegeven in de implementatie bewerking. Het tweede opslag account wordt geïmplementeerd naar de resource groep die is opgegeven in de `secondResourceGroup` `secondSubscriptionID` para meters en:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Als u `resourceGroup` de naam instelt van een resource groep die niet bestaat, mislukt de implementatie.

Als u de voor gaande sjabloon wilt testen en de resultaten wilt weer geven, gebruikt u Power shell of Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u twee opslag accounts wilt implementeren voor twee resource groepen in **hetzelfde abonnement**, gebruikt u:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Als u twee opslag accounts wilt implementeren op **twee abonnementen**, gebruikt u:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u twee opslag accounts wilt implementeren voor twee resource groepen in **hetzelfde abonnement**, gebruikt u:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Als u twee opslag accounts wilt implementeren op **twee abonnementen**, gebruikt u:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="use-functions"></a>Functies gebruiken

De functies [resourceGroup ()](template-functions-resource.md#resourcegroup) en [Subscription ()](template-functions-resource.md#subscription) worden op verschillende manieren opgelost, afhankelijk van hoe u de sjabloon opgeeft. Wanneer u een koppeling naar een externe sjabloon maakt, worden de functies altijd omgezet in het bereik voor die sjabloon. Wanneer u een sjabloon in een bovenliggende sjabloon nest, gebruikt u de `expressionEvaluationOptions` eigenschap om op te geven of de functies worden omgezet in de resource groep en het abonnement voor de bovenliggende sjabloon of de geneste sjabloon. Stel de eigenschap in op `inner` om om te zetten in het bereik voor de geneste sjabloon. Stel de eigenschap in op `outer` om om te zetten naar het bereik van de bovenliggende sjabloon.

In de volgende tabel wordt aangegeven of de functies worden omgezet naar de bovenliggende of Inge sloten resource groep en het-abonnement.

| Sjabloon type | Bereik | Oplossing |
| ------------- | ----- | ---------- |
| nest        | Outer (standaard) | Bovenliggende resource groep |
| nest        | wend | Subresourcegroep |
| Btrieve        | N.v.t.   | Subresourcegroep |

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) ziet u:

* geneste sjabloon met het standaard bereik (buitenste)
* geneste sjabloon met binnenste bereik
* gekoppelde sjabloon

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

Als u de voor gaande sjabloon wilt testen en de resultaten wilt weer geven, gebruikt u Power shell of Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

De uitvoer van het vorige voor beeld is:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

De uitvoer van het vorige voor beeld is:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht in de structuur en syntaxis van Azure Resource Manager sjablonen](template-syntax.md)voor meer informatie over het definiëren van para meters in uw sjabloon.
* Zie [problemen met algemene Azure-implementatie fouten oplossen met Azure Resource Manager](common-deployment-errors.md)voor tips over het oplossen van veelvoorkomende implementatie fouten.
* Zie voor meer informatie over het implementeren van een sjabloon waarvoor een SAS-token is vereist een [persoonlijke sjabloon implementeren met SAS-token](secure-template-with-sas-token.md).
