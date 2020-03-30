---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78262020"
---
### <a name="set-the-storage-account-connection"></a>De opslagaccountverbinding instellen

Open het bestand local.settings.json en `AzureWebJobsStorage`kopieer de waarde van de tekenreeks Opslagaccountverbinding. Stel `AZURE_STORAGE_CONNECTION_STRING` de omgevingsvariabele in op de verbindingstekenreeks met deze opdracht Bash:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Wanneer u de verbindingstekenreeks `AZURE_STORAGE_CONNECTION_STRING` instelt in de omgevingsvariabele, hebt u toegang tot uw opslagaccount zonder dat u telkens verificatie hoeft te bieden.