---
title: PowerShell-script om een resourcevergrendeling te maken voor een Azure Cosmos MongoDB API-database en -verzameling
description: Een resourcevergrendeling maken voor een Azure Cosmos MongoDB API-database en -verzameling
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: b9b743e9f293455c09557348c53cc76b1f904b0a
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489129"
---
# <a name="create-a-resource-lock-for-azure-cosmos-mongodb-api-database-and-collection-using-azure-powershell"></a>Met Azure PowerShell een resourcevergrendeling maken voor een Azure Cosmos MongoDB API-database en -verzameling

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

> [!IMPORTANT]
> Resourcevergrendeling werkt niet voor wijzigingen die zijn aangebracht door gebruikers die verbinding maken met behulp van een MongoDB-SDK, Mongoshell, hulpprogramma's of Azure Portal, tenzij het Cosmos DB-account eerst is vergrendeld met de eigenschap `disableKeyBasedMetadataWriteAccess` ingeschakeld. Zie [Wijzigingen aan SDK's voorkomen](../../../role-based-access-control.md#prevent-sdk-changes) voor meer informatie over het inschakelen van deze eigenschap.

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-lock.ps1 "Create, list, and remove resource locks")]

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
| [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) | Hiermee maakt u een resourcevergrendeling. |
| [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock) | Hiermee wordt een resourcevergrendeling opgehaald of worden resourcevergrendelingen weergegeven. |
| [Remove-AzResourceLock](/powershell/module/az.resources/remove-azresourcelock) | Hiermee wordt een resourcevergrendeling verwijderd. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.