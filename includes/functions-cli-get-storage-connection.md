---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673375"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>De Azure Storage ophalen connection string

U hebt eerder een Azure Storage-account gemaakt voor gebruik door de functie-app. De connection string voor dit account wordt veilig opgeslagen in de app-instellingen in Azure. Door de instelling in het bestand *Local. settings. json* te downloaden, kunt u deze verbinding schrijven naar een opslag wachtrij in hetzelfde account wanneer u de functie lokaal uitvoert. 

1. Voer de volgende opdracht uit vanuit de hoofdmap van het project en vervang `<app_name>` door de naam van de functie-app uit de vorige Snelstartgids. Met deze opdracht worden alle bestaande waarden in het bestand overschreven.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Open *Local. settings. json* en zoek de waarde met `AzureWebJobsStorage`de naam, het opslag account Connection String. U gebruikt de naam `AzureWebJobsStorage` en de Connection String in andere secties van dit artikel.

> [!IMPORTANT]
> Omdat *Local. settings. json* bevat geheimen die zijn gedownload van Azure, moet u dit bestand altijd uitsluiten van broncode beheer. Het *. gitignore* -bestand dat is gemaakt met een lokale functie project, sluit het bestand standaard uit.