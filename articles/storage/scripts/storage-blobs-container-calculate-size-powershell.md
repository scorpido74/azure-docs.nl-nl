---
title: Grootte van een BLOB-container berekenen met Power shell
titleSuffix: Azure Storage
description: Bereken de grootte van een container in Azure Blob Storage door de grootte van alle blobs in de container bij elkaar op te tellen.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: 890da96ac1d97a2eb84f5296c74a02824d657273
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895041"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>De grootte van een BLOB-container berekenen met Power shell

Met dit script wordt de grootte van een container in Azure Blob Storage berekend door de grootte van de blobs in de container op te tellen.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Dit PowerShell-script geeft een geschatte grootte van de container en dient niet te worden gebruikt voor factureringsberekeningen. Zie [De grootte van een Blob Storage-container voor facturering berekenen](../scripts/storage-blobs-container-calculate-billing-size-powershell.md) voor een script waarmee u de grootte van de container voor facturering kunt berekenen. 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Gebruik de volgende opdracht om de resourcegroep, container en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om de grootte van de Blob Storage-container te berekenen. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Hiermee haalt u een opgegeven opslagaccount of alle opslagaccounts in een resourcegroep of het abonnement op. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Hiermee geeft u een lijst met blobs in een container weer. |

## <a name="next-steps"></a>Volgende stappen

Zie [De grootte van een Blob Storage-container voor facturering berekenen](../scripts/storage-blobs-container-calculate-billing-size-powershell.md) voor een script waarmee u de grootte van de container voor facturering kunt berekenen.

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

Meer PowerShell-voorbeeldscripts voor Storage vindt u in de [PowerShell-voorbeelden voor Azure Storage](../blobs/storage-samples-blobs-powershell.md).
