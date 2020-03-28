---
title: Azure CLI-voorbeeldscript - Blobcontainergrootte berekenen | Microsoft Docs
description: Bereken de grootte van een container in Azure Blob Storage door de grootte van de blobs in de container op te tellen.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 06/28/2017
ms.author: tamram
ms.openlocfilehash: 83ec8c7b3bf5ba9d23d50d8fa1bce563dc56c135
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067113"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>De grootte van een Blob Storage-container berekenen

Met dit script wordt de grootte van een container in Azure Blob Storage berekend door de grootte van de blobs in de container op te tellen.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Dit CLI-script geeft een geschatte grootte van de container en dient niet te worden gebruikt voor factureringsberekeningen.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep, container en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om de grootte van de Blob Storage-container te berekenen. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az storage blob upload](/cli/azure/storage/account) | Hiermee worden lokale bestanden geüpload naar een Azure Blob Storage-container. |
| [az storage blob list](/cli/azure/storage/account/keys) | Hiermee wordt een lijst gemaakt van de blobs in een Azure Blob Storage-container. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Meer CLI-voorbeeldscripts voor Storage vindt u in de [Azure CLI-voorbeelden voor Azure Blob Storage](../blobs/storage-samples-blobs-cli.md).
