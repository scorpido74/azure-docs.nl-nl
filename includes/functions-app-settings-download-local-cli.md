---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "72329546"
---
U hebt al een functie-app in azure gemaakt, samen met het vereiste opslag account. De connection string voor dit account wordt veilig opgeslagen in de app-instellingen in Azure. In dit artikel schrijft u berichten naar een opslag wachtrij in hetzelfde account. Als u verbinding wilt maken met uw opslag account wanneer u de functie lokaal uitvoert, moet u de app-instellingen downloaden naar het bestand local. settings. json. 

Voer in de hoofdmap van het project de volgende Azure Functions Core Tools opdracht uit om instellingen te downloaden naar local. settings. json, `<APP_NAME>` vervangen door de naam van uw functie-app uit het vorige artikel:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Mogelijk moet u zich aanmelden bij uw Azure-account.

> [!IMPORTANT]  
> Met deze opdracht worden alle bestaande instellingen overschreven door waarden uit uw functie-app in Azure.  
>
> Omdat deze geheimen bevat, wordt het bestand local. settings. json nooit gepubliceerd en moet het worden uitgesloten van broncode beheer.  
> 

U hebt de waarde `AzureWebJobsStorage`nodig die het opslag account Connection String. U gebruikt deze verbinding om te controleren of de uitvoer binding werkt zoals verwacht.
