---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "72329546"
---
U hebt al een functie-app in Azure gemaakt, samen met het vereiste opslagaccount. De verbindingstekenreeks voor dit account wordt veilig opgeslagen in app-instellingen in Azure. In dit artikel schrijft u berichten naar een opslagwachtrij in hetzelfde account. Als u verbinding wilt maken met uw opslagaccount wanneer u de functie lokaal uitvoert, moet u app-instellingen downloaden naar het bestand local.settings.json. 

Voer vanuit de hoofdmap van het project de volgende opdracht Azure Functions Core Tools `<APP_NAME>` uit om instellingen te downloaden naar local.settings.json, vervangen door de naam van uw functie-app uit het vorige artikel:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Mogelijk moet u zich aanmelden bij uw Azure-account.

> [!IMPORTANT]  
> Met deze opdracht worden bestaande instellingen overschrijft met waarden uit uw functie-app in Azure.  
>
> Omdat het geheimen bevat, wordt het bestand local.settings.json nooit gepubliceerd en moet het worden uitgesloten van bronbeheer.  
> 

U hebt `AzureWebJobsStorage`de waarde nodig , de tekenreeks opslagaccountverbinding. U gebruikt deze verbinding om te controleren of de uitvoerbinding werkt zoals verwacht.
