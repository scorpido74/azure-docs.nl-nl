---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 07780c94960c581ce69e4fdd45b346c75ab8b098
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329577"
---
### <a name="set-the-storage-account-connection"></a>De verbinding voor het opslag account instellen

Open het bestand local. settings. json en kopieer de waarde van `AzureWebJobsStorage`, het opslag account connection string. Stel de `AZURE_STORAGE_CONNECTION_STRING`-omgevings variabele in op de connection string met behulp van deze bash-opdracht:

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Wanneer u de connection string instelt in de omgevings variabele `AZURE_STORAGE_CONNECTION_STRING`, hebt u toegang tot uw opslag account zonder dat elke keer verificatie hoeft te worden opgegeven.