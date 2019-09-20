---
title: Azure PowerShell script-een Azure Cosmos-account bijwerken
description: Azure PowerShell script voorbeeld-een Azure Cosmos-account bijwerken of regio's wijzigen
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: 7d57264daf4036d1823d3da27c248bdca1d7466d
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154742"
---
# <a name="update-an-azure-cosmos-account-or-add-modify-regions-using-powershell"></a>Een Azure Cosmos-account bijwerken of wijzigings regio's toevoegen met Power shell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Voorbeeldscript

> [!NOTE]
> U kunt regio's niet wijzigen en andere Cosmos-account eigenschappen wijzigen in dezelfde bewerking. Deze moeten worden uitgevoerd als twee afzonderlijke bewerkingen.
> [!NOTE]
> Dit voor beeld laat zien hoe u een SQL (core) API-account gebruikt. Als u dit voor beeld voor andere Api's wilt gebruiken, kopieert u de gerelateerde eigenschappen en past u deze toe op uw API-specifiek script.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update.ps1 "Add a region to an Azure Cosmos account")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Na het uitvoeren van het voorbeeldscript kan de volgende opdracht worden gebruikt om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
|**Azure-resources**| |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Een resource maken. |
| [Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) | Een resource bijwerken. |
|**Azure-resource groepen**| |
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Meer Azure Cosmos DB PowerShell-voorbeeldscripts vindt u in [Azure Cosmos DB PowerShell-scripts](../../../powershell-samples.md).
