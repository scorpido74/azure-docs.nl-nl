---
title: PowerShell-script voor het bijwerken van de regio's van een Azure Cosmos-account
description: Voorbeeld van Azure PowerShell-script voor het bijwerken van de regio's van een Azure Cosmos-account
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/01/2020
ms.author: mjbrown
ms.openlocfilehash: 8bd5bba0f98a872ce31836217aef5605df3b0c41
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488177"
---
# <a name="update-an-azure-cosmos-accounts-regions-using-powershell"></a>Regio's van een Azure Cosmos-account bijwerken met behulp van PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Voorbeeldscript

> [!NOTE]
> Het is niet mogelijk om in dezelfde bewerking regio's te wijzigen en andere eigenschappen van een Cosmos-account aan te passen. Dit moet worden gedaan met twee afzonderlijke bewerkingen.
> [!NOTE]
> In dit voorbeeld wordt een SQL (Core) API-account gebruikt. Als u dit voorbeeld voor andere API's wilt gebruiken, kopieert u de gerelateerde eigenschappen en past u deze toe op uw API-specifieke script.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update-region.ps1 "Update Azure Cosmos account regions")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Na het uitvoeren van het voorbeeldscript kan de volgende opdracht worden gebruikt om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Hiermee geeft u de Cosmos DB-accounts weer of een bepaald Cosmos DB-account. |
| [New-AzCosmosDBLocationObject](/powershell/module/az.cosmosdb/new-azcosmosdblocationobject) | Hiermee wordt een object van het type PSLocation gemaakt dat kan worden gebruikt als een parameter voor Update-AzCosmosDBAccountRegion. |
| [Update-AzCosmosDBAccountRegion](/powershell/module/az.cosmosdb/update-azcosmosdbaccountregion) | Regio’s van een Cosmos DB-account bijwerken. |
|**Azure-resourcegroepen**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.