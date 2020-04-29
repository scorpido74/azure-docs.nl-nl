---
title: Toegangs sleutels voor opslag accounts draaien met Power shell
titleSuffix: Azure Storage
description: Maak een Azure Storage-account en haal een van de toegangssleutels van het account op en draai deze.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: 52ebed3de093f15d8188ee5fec49d75d5a4a206d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80060814"
---
# <a name="rotate-storage-account-access-keys-with-powershell"></a>Toegangs sleutels voor opslag accounts draaien met Power shell

Met dit script wordt een Azure Storage-account gemaakt, worden de primaire toegangssleutels van het nieuwe opslagaccount weergegeven en wordt de sleutel vernieuwd (gedraaid).

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep, het opslagaccount en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om het opslagaccount te maken en een van de toegangssleutels op te halen en te draaien. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzLocation](/powershell/module/az.resources/get-azlocation) | Hiermee haalt u alle locaties en de ondersteunde resourceproviders voor elke locatie op. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een Azure-resourcegroep. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Hiermee maakt u een opslagaccount. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Hiermee worden de toegangssleutels voor Azure opslagaccounts weergegeven. |
| [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) | Hiermee genereert u een toegangssleutel voor een Azure Storage-account. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

Meer PowerShell-voorbeeldscripts voor Storage vindt u in de [PowerShell-voorbeelden voor Azure Blob Storage](../blobs/storage-samples-blobs-powershell.md).
