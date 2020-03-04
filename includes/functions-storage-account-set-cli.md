---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262020"
---
### <a name="set-the-storage-account-connection"></a>De verbinding voor het opslag account instellen

Open het bestand local. settings. json en kopieer de waarde van `AzureWebJobsStorage`. Dit is het opslag account connection string. Stel de `AZURE_STORAGE_CONNECTION_STRING`-omgevings variabele in op de connection string met behulp van deze bash-opdracht:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Wanneer u de connection string instelt in de omgevings variabele `AZURE_STORAGE_CONNECTION_STRING`, hebt u toegang tot uw opslag account zonder dat elke keer verificatie moet worden opgegeven.