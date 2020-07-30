---
title: PowerShell-script om een resourcevergrendeling te maken voor een Azure Cosmos Gremlin API-database en -grafiek
description: Een resourcevergrendeling te maken voor een Azure Cosmos Gremlin API-database en -grafiek
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: 32b5a02064ec987250f640ab6ea63515bac8385f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87039940"
---
# <a name="create-a-resource-lock-for-azure-cosmos-gremlin-api-database-and-graph-using-azure-powershell"></a>Een resourcevergrendeling voor een Azure Cosmos Gremlin API-database en -grafiek maken met behulp van Azure PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

> [!IMPORTANT]
> Resourcevergrendeling werkt niet voor wijzigingen die zijn aangebracht door gebruikers die verbinding maken met behulp van een Gremlin SDK of Azure Portal, tenzij het Cosmos DB-account eerst is vergrendeld met de eigenschap `disableKeyBasedMetadataWriteAccess` ingeschakeld. Zie [Wijzigingen aan SDK's voorkomen](../../../role-based-access-control.md#prevent-sdk-changes) voor meer informatie over het inschakelen van deze eigenschap.

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Na het uitvoeren van het voorbeeldscript kan de volgende opdracht worden gebruikt om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
|**Azure-resource**| |
| [New-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock) | Hiermee maakt u een resourcevergrendeling. |
| [Get-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcelock) | Hiermee wordt een resourcevergrendeling opgehaald of worden resourcevergrendelingen weergegeven. |
| [Remove-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcelock) | Hiermee wordt een resourcevergrendeling verwijderd. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Meer Azure Cosmos DB PowerShell-voorbeeldscripts vindt u in [Azure Cosmos DB PowerShell-scripts](../../../powershell-samples.md).