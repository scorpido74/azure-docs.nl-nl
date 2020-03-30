---
title: Brongroep en resources verwijderen
description: Beschrijft hoe u resourcegroepen en resources verwijderen. Hierin wordt beschreven hoe Azure Resource Manager het verwijderen van resources beveelt wanneer een brongroep wordt verwijderd. Het beschrijft de antwoordcodes en hoe Resource Manager hiermee omgaat om te bepalen of de verwijdering is geslaagd.
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18
ms.openlocfilehash: db56cf0897cd90f1e6e51199032d0d9712530f1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274019"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager-brongroep en resourceverwijderen

In dit artikel ziet u hoe u brongroepen en resources verwijdert. Hierin wordt beschreven hoe Azure Resource Manager het verwijderen van resources beveelt wanneer u een brongroep verwijdert.

## <a name="how-order-of-deletion-is-determined"></a>Hoe de volgorde van verwijdering wordt bepaald

Wanneer u een resourcegroep verwijdert, bepaalt Resource Manager de volgorde om resources te verwijderen. Het gebruikt de volgende volgorde:

1. Alle onderliggende (geneste) bronnen worden verwijderd.

2. Bronnen die andere bronnen beheren, worden vervolgens verwijderd. Een resource kan `managedBy` de eigenschap hebben ingesteld om aan te geven dat een andere resource deze beheert. Wanneer deze eigenschap is ingesteld, wordt de resource die de andere resource beheert, verwijderd vóór de andere resources.

3. De resterende bronnen worden verwijderd na de vorige twee categorieën.

Nadat de volgorde is bepaald, geeft Resource Manager een delete-bewerking uit voor elke resource. Het wacht tot eventuele afhankelijkheden te voltooien voordat u verder gaat.

Voor synchrone bewerkingen zijn de verwachte succesvolle antwoordcodes:

* 200
* 204
* 404

Voor asynchrone bewerkingen is de verwachte succesvolle respons 202. Resourcemanager houdt de locatiekop of de azure-async-bewerkingskop bij om de status van de asynchrone verwijderingsbewerking te bepalen.
  
### <a name="deletion-errors"></a>Verwijderingsfouten

Wanneer een verwijderbewerking een fout retourneert, probeert Resource Manager de aanroep Verwijderen opnieuw in. Er worden opnieuw pogingen ingeroepen voor de statuscodes 5xx, 429 en 408. Standaard is de periode voor opnieuw proberen 15 minuten.

## <a name="after-deletion"></a>Na verwijdering

Resource manager geeft een GET-aanroep uit op elke resource die het heeft geprobeerd te verwijderen. De reactie van deze GET-oproep zal naar verwachting 404 zijn. Wanneer Resource Manager een 404 krijgt, wordt de verwijdering als voltooid. Resource Manager verwijdert de bron uit de cache.

Als de GET-aanroep op de resource echter een 200 of 201 retourneert, maakt Resource Manager de resource opnieuw.

Als de bewerking GET een fout retourneert, probeert Resource Manager de GET opnieuw in voor de volgende foutcode:

* Minder dan 100
* 408
* 429
* Meer dan 500

Voor andere foutcodes kan de resource niet worden verwijderd door Resource Manager.

## <a name="delete-resource-group"></a>Resourcegroep verwijderen

Gebruik een van de volgende methoden om de brongroep te verwijderen.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in de [portal](https://portal.azure.com)de brongroep die u wilt verwijderen.

1. Selecteer **Resourcegroep verwijderen**.

   ![Resourcegroep verwijderen](./media/delete-resource-group/delete-group.png)

1. Als u de verwijdering wilt bevestigen, typt u de naam van de resourcegroep

---

## <a name="delete-resource"></a>Bron verwijderen

Gebruik een van de volgende methoden om een bron te verwijderen.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in de [portal](https://portal.azure.com)de bron die u wilt verwijderen.

1. Selecteer **Verwijderen**. De volgende schermafbeelding toont de beheeropties voor een virtuele machine.

   ![Bron verwijderen](./media/delete-resource-group/delete-resource.png)

1. Bevestig de verwijdering als u daarom wordt gevraagd.

---


## <a name="next-steps"></a>Volgende stappen

* Zie Overzicht van [Azure Resource Manager](overview.md)voor het begrijpen van Resource Manager-concepten.
* Zie [PowerShell,](/powershell/module/az.resources/Remove-AzResourceGroup) [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete)en [REST API](/rest/api/resources/resourcegroups/delete)voor verwijderingsopdrachten.
