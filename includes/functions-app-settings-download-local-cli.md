---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 801613f4e3fb1e754856d716c6815895ea5da3aa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839145"
---
U hebt al een functie-app in azure gemaakt, samen met het vereiste opslag account. De connection string voor dit account wordt veilig opgeslagen in de app-instellingen in Azure. In dit artikel schrijft u berichten naar een opslag wachtrij in hetzelfde account. Als u verbinding wilt maken met uw opslag account wanneer u de functie lokaal uitvoert, moet u de app-instellingen downloaden naar het bestand local. settings. json. Voer de volgende Azure Functions Core Tools opdracht uit om de instellingen te downloaden naar local. settings. json, waarbij u `<APP_NAME>` vervangt door de naam van uw functie-app uit het vorige artikel:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Mogelijk moet u zich aanmelden bij uw Azure-account.

> [!IMPORTANT]  
> Omdat deze geheimen bevat, wordt het bestand local. settings. json nooit gepubliceerd en moet het worden uitgesloten van broncode beheer.

U hebt de waarde `AzureWebJobsStorage` nodig. Dit is het opslag account connection string. U gebruikt deze verbinding om te controleren of de uitvoer binding werkt zoals verwacht.
