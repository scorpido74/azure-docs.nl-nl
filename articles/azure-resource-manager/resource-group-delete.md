---
title: 'Verwijderen van de resourcegroep en resources: Azure Resource Manager'
description: Hierin wordt beschreven hoe u resource groepen en-resources verwijdert. Hierin wordt beschreven hoe Azure Resource Manager het verwijderen van resources ordent bij het verwijderen van een resource groep. Beschrijft de responscodes en hoe Resource Manager worden verwerkt om te bepalen of de verwijdering is geslaagd.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 75cdeb88a68dece59d6b037592f7212fa895e821
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991689"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager resource groep en verwijderen van resources

In dit artikel wordt uitgelegd hoe u resource groepen en-resources kunt verwijderen. Hierin wordt beschreven hoe Azure Resource Manager het verwijderen van resources ordent bij het verwijderen van een resource groep.

## <a name="delete-resource-group"></a>Resourcegroep verwijderen

Gebruik een van de volgende methoden om de resource groep te verwijderen.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name <resource-group-name>
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name <resource-group-name>
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in de [Portal](https://portal.azure.com)de resource groep die u wilt verwijderen.

1. Selecteer **Resourcegroep verwijderen**.

   ![Resourcegroep verwijderen](./media/resource-group-delete/delete-group.png)

1. Om het verwijderen te bevestigen, typt u de naam van de resource groep

---

## <a name="delete-resource"></a>Bron verwijderen

Gebruik een van de volgende methoden om een resource te verwijderen.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in de [Portal](https://portal.azure.com)de resource die u wilt verwijderen.

1. Selecteer **Verwijderen**. Op de volgende scherm afbeelding ziet u de beheer opties voor een virtuele machine.

   ![Bron verwijderen](./media/resource-group-delete/delete-resource.png)

1. Bevestig de verwijdering als u daarom wordt gevraagd.

---

## <a name="how-order-of-deletion-is-determined"></a>Hoe volg orde van verwijderen wordt bepaald

Wanneer u een resourcegroep verwijdert, Resource Manager de bepaalt de volgorde om resources te verwijderen. Hierbij de volgende volgorde:

1. Alle resources in de onderliggende (geneste) worden verwijderd.

2. Resources die andere resources beheren, worden vervolgens verwijderd. Een resource kan hebben de `managedBy` eigenschap is ingesteld om aan te geven dat een andere resource deze beheert. Als deze eigenschap is ingesteld, worden de resource die wordt beheerd van de andere resource wordt verwijderd voordat de andere resources.

3. De resterende resources zijn verwijderd na de vorige twee categorieën.

Nadat de volgorde is bepaald, problemen met Resource Manager een verwijderbewerking voor elke resource. Wacht voor eventuele afhankelijkheden om te voltooien voordat u doorgaat.

Voor synchrone bewerkingen kan zijn de verwachte reactie van geslaagde-codes:

* 200
* 204
* 404

Voor asynchrone bewerkingen is de verwachte reactie van geslaagde 202. Resource Manager houdt de location-header of de header van de azure-asynchrone bewerking om te bepalen van de status van de asynchrone bewerking.
  
### <a name="errors"></a>Fouten

Wanneer een delete-bewerking wordt een fout geretourneerd, probeert de aanroep voor het verwijderen door Resource Manager opnieuw. Nieuwe pogingen worden uitgevoerd voor de 5xx, 429 en statuscodes 408. Standaard is de periode voor opnieuw proberen 15 minuten.

## <a name="after-deletion"></a>Na verwijdering

Resource Manager geeft een GET-aanroep op elke bron die deze probeert te verwijderen. 404 wordt het antwoord van deze aanroep GET verwacht. Bij het Resource Manager haalt een 404-fout, partitielezer de verwijdering is voltooid. Resource Manager wordt de resource uit de cache verwijderd.

Als de GET-aanroep voor de resource een 200 of 201 retourneert, stelt Resource Manager de resource.

### <a name="errors"></a>Fouten

Als de GET-bewerking een fout retourneert, pogingen Resource Manager de GET voor de volgende foutcode:

* Minder dan 100
* 408
* 429
* Groter is dan 500

Resource Manager een failover voor andere foutcodes, het verwijderen van de resource.

## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over concepten van Resource Manager, [overzicht van Azure Resource Manager](resource-group-overview.md).
* Zie voor verwijdering opdrachten, [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete), en [REST-API](/rest/api/resources/resourcegroups/delete).
