---
title: Power shell-script voor het wijzigen van de failover-prioriteit voor een Azure Cosmos-account met één hoofd
description: Azure PowerShell script-voor beeld-failover-prioriteit wijzigen of failover activeren voor een Azure Cosmos DB account met één Master
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: a81938675e72d9ec3a18c920121951e38580b91e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80366123"
---
# <a name="change-failover-priority-or-trigger-failover-for-an-azure-cosmos-db-single-master-account-using-powershell"></a>Failover-prioriteit wijzigen of failover activeren voor een Azure Cosmos DB Single-Master-account met behulp van Power shell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Voorbeeldscript

> [!NOTE]
> Elke wijziging in een regio waarbij `failoverPriority=0` een hand matige failover wordt geactiveerd en alleen kan worden uitgevoerd voor een account dat is geconfigureerd voor hand matige failover. Wijzigingen in alle andere regio's veranderen eenvoudigweg de failover-prioriteit voor een Cosmos-account.
> [!NOTE]
> Dit voor beeld laat zien hoe u een SQL (core) API-account gebruikt. Als u dit voor beeld voor andere Api's wilt gebruiken, kopieert u de gerelateerde eigenschappen en past u deze toe op uw API-specifiek script

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-failover-priority-update.ps1 "Update failover priority for an Azure Cosmos account or trigger a manual failover")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Na het uitvoeren van het voorbeeldscript kan de volgende opdracht worden gebruikt om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Een lijst met Cosmos DB accounts of een opgegeven Cosmos DB-account. |
| [Update-AzCosmosDBAccountFailoverPriority](https://docs.microsoft.com/powershell/module/az.cosmosdb/update-azcosmosdbaccountfailoverpriority) | De prioriteits volgorde van de failover van een Cosmos DB account regio's bijwerken. |
|**Azure-resource groepen**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Meer Azure Cosmos DB PowerShell-voorbeeldscripts vindt u in [Azure Cosmos DB PowerShell-scripts](../../../powershell-samples.md).
