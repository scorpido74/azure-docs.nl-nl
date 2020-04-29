---
title: Azure PowerShell-voorbeeldscript - Containers verwijderen op basis van voorvoegsel | Microsoft Docs
description: Verwijder Azure Storage-blobcontainers op basis van het voorvoegsel van een containernaam.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: 73c2f221d2ac0b3ceb76e3db0fdc6379a6dd330a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80060880"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Containers verwijderen op basis van het containernaamvoorvoegsel

Met dit script worden containers in Azure Blob-opslag verwijderd op basis van een voorvoegsel in de containernaam.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep, de resterende containers en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt containers te verwijderen op basis van het voorvoegsel van de containernaam. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Hiermee haalt u een opgegeven opslagaccount of alle opslagaccounts in een resourcegroep of het abonnement op. |
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | Hiermee worden de opslagcontainers weergegeven die zijn gekoppeld aan een opslagaccount. |
| [Remove-AzStorageContainer](/powershell/module/az.storage/Remove-AzStorageContainer) | Hiermee verwijdert u de opgegeven opslagcontainer. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

Meer PowerShell-voorbeeldscripts voor Storage vindt u in de [PowerShell-voorbeelden voor Azure Blob Storage](../blobs/storage-samples-blobs-powershell.md).
