---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673375"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>De tekenreeks azure storage-verbinding ophalen

Eerder hebt u een Azure Storage-account gemaakt voor gebruik door de functie-app. De verbindingstekenreeks voor dit account wordt veilig opgeslagen in app-instellingen in Azure. Als u de instelling downloadt naar het bestand *local.settings.json,* u die verbinding schrijven naar een opslagwachtrij in hetzelfde account gebruiken wanneer u de functie lokaal uitvoert. 

1. Voer vanuit de hoofdmap van het project `<app_name>` de volgende opdracht uit, vervangen door de naam van uw functie-app van de vorige quickstart. Met deze opdracht worden alle bestaande waarden in het bestand overschreven.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Open *local.settings.json* en zoek `AzureWebJobsStorage`de waarde met de naam , de tekenreeks Opslagaccountverbinding. U gebruikt `AzureWebJobsStorage` de naam en de verbindingstekenreeks in andere secties van dit artikel.

> [!IMPORTANT]
> Omdat *local.settings.json* geheimen bevat die zijn gedownload van Azure, sluit u dit bestand altijd uit van bronbeheer. Het *.gitignore-bestand* dat is gemaakt met een project voor lokale functies, sluit het bestand standaard uit.