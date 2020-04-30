---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78262020"
---
### <a name="set-the-storage-account-connection"></a>De verbinding voor het opslag account instellen

Open het bestand local. settings. json en kopieer de waarde van `AzureWebJobsStorage`, het opslag account Connection String. Stel de `AZURE_STORAGE_CONNECTION_STRING` omgevings variabele in op de Connection String met behulp van deze bash-opdracht:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Wanneer u de connection string instelt in de `AZURE_STORAGE_CONNECTION_STRING` omgevings variabele, hebt u toegang tot uw opslag account zonder elke keer verificatie te hoeven opgeven.