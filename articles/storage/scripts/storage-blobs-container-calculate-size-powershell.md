---
title: Grootte van een blob-container berekenen met PowerShell
titleSuffix: Azure Storage
description: Bereken de grootte van een container in Azure Blob Storage door de grootte van alle blobs in de container bij elkaar op te tellen.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: de275bcca1644750532809b35ae85d954d3cac6d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076902"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>Grootte van een blob-container berekenen met PowerShell

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

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).

Meer PowerShell-voorbeeldscripts voor Storage vindt u in de [PowerShell-voorbeelden voor Azure Storage](../blobs/storage-samples-blobs-powershell.md).
