---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 89e2e64910e33d43c107ee88137de718d020d7d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839034"
---
Open het bestand local. settings. json en kopieer de waarde van `AzureWebJobsStorage`, het opslag account connection string. Stel de `AZURE_STORAGE_CONNECTION_STRING`-omgevings variabele in op de connection string met behulp van deze bash-opdracht:

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Wanneer u de connection string instelt in de omgevings variabele `AZURE_STORAGE_CONNECTION_STRING`, hebt u toegang tot uw opslag account zonder dat elke keer verificatie hoeft te worden opgegeven.