---
title: Aandachtspunten voor de opslag van Azure Functions
description: Meer informatie over de opslag vereisten van Azure Functions en over het versleutelen van opgeslagen gegevens.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 324516240d09a5443908cbffec514e4caba2b604
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648789"
---
# <a name="storage-considerations-for-azure-functions"></a>Aandachtspunten voor de opslag van Azure Functions

Azure Functions moet een Azure Storage-account zijn wanneer u een exemplaar van een functie-app maakt. De volgende opslag Services kunnen worden gebruikt door uw functie-app:


|Opslag service  | Functie gebruik  |
|---------|---------|
| [Azure Blob-opslag](../storage/blobs/storage-blobs-introduction.md)     | Status-en functie sleutels van bindingen onderhouden.  <br/>Wordt ook gebruikt door [taak hubs in Durable functions](durable/durable-functions-task-hubs.md). |
| [Azure Files](../storage/files/storage-files-introduction.md)  | Bestands share gebruikt om de code van uw functie-app op te slaan en uit te voeren in een [verbruiks abonnement](functions-scale.md#consumption-plan). |
| [Azure-wachtrij opslag](../storage/queues/storage-queues-introduction.md)     | Wordt gebruikt door [taak hubs in Durable functions](durable/durable-functions-task-hubs.md).   |
| [Azure-tabel opslag](../storage/tables/table-storage-overview.md)  |  Wordt gebruikt door [taak hubs in Durable functions](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> Als u gebruikmaakt van het hostingabonnement Consumption worden uw functiecode en uw bindingsconfiguratiebestanden opgeslagen in het belangrijkste opslagaccount in Azure File Storage. Wanneer u het belangrijkste opslagaccount verwijdert, wordt de inhoud verwijderd en kan deze niet worden hersteld.

## <a name="storage-account-requirements"></a>Vereisten voor een opslagaccount

Wanneer u een functie-app maakt, moet u een Azure Storage-account voor algemeen gebruik maken of koppelen dat ondersteuning biedt voor blob-, wachtrij-en tabel opslag. Dit komt omdat functies afhankelijk zijn van Azure Storage voor bewerkingen, zoals het beheren van triggers en de uitvoering van logboek functies. Sommige opslag accounts bieden geen ondersteuning voor wacht rijen en tabellen. Deze accounts omvatten alleen Blob-opslag accounts, Azure Premium Storage en opslag accounts voor algemeen gebruik met ZRS-replicatie. Deze niet-ondersteunde accounts worden van de Blade opslag account gefilterd bij het maken van een functie-app.

Zie [Introductie van de Azure Storage-services](../storage/common/storage-introduction.md#core-storage-services) voor meer informatie over opslagaccounttypen. 

Hoewel u een bestaand opslag account kunt gebruiken met uw functie-app, moet u ervoor zorgen dat deze aan deze vereisten voldoet. Opslag accounts die zijn gemaakt als onderdeel van de stroom voor het maken van de functie-app, worden gegarandeerd voldoen aan deze vereisten voor opslag accounts.  

## <a name="storage-account-guidance"></a>Richt lijnen voor opslag accounts

Voor elke functie-app moet een opslag account worden gebruikt. Als dat account wordt verwijderd, wordt de functie-app niet uitgevoerd. Zie problemen [met opslag problemen oplossen voor informatie over het](functions-recover-storage-account.md)oplossen van problemen met opslag. De volgende aanvullende overwegingen zijn van toepassing op het opslag account dat wordt gebruikt door functie-apps.

### <a name="storage-account-connection-setting"></a>Instelling voor verbinding met opslag account

De verbinding van het opslag account wordt onderhouden in de [toepassings instelling AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

Het connection string van het opslag account moet worden bijgewerkt wanneer u de opslag sleutels opnieuw genereert. [Lees hier meer over opslag sleutel beheer](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

### <a name="shared-storage-accounts"></a>Gedeelde opslag accounts

Het is mogelijk dat meerdere functie-apps hetzelfde opslag account delen zonder problemen. In Visual Studio kunt u bijvoorbeeld meerdere apps ontwikkelen met behulp van de Azure Storage-emulator. In dit geval fungeert de emulator als één opslag account. Hetzelfde opslag account dat wordt gebruikt door de functie-app kan ook worden gebruikt om uw toepassings gegevens op te slaan. Deze benadering is echter niet altijd een goed idee in een productie omgeving.

### <a name="optimize-storage-performance"></a>Opslagprestaties optimaliseren

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Versleuteling van opslag gegevens

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="mount-file-shares-linux"></a>Bestands shares koppelen (Linux)

U kunt bestaande Azure Files-shares koppelen aan uw Linux-functie-apps. Door een share te koppelen aan uw Linux-functie-app, kunt u gebruikmaken van bestaande machine learning modellen of andere gegevens in uw functies. U kunt de [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) opdracht gebruiken om een bestaande share te koppelen aan uw Linux-functie-app. 

In deze opdracht `share-name` is de naam van de bestaande Azure Files share en `custom-id` kan elke wille keurige teken reeks zijn die een unieke definitie vormt van de share wanneer deze wordt gekoppeld aan de functie-app. Het `mount-path` is ook het pad van waaruit de share wordt geopend in uw functie-app. `mount-path`moet de indeling `/dir-name` hebben en kan niet beginnen met `/home` .

Zie de scripts in [een python-functie-app maken en een Azure Files share koppelen](scripts/functions-cli-mount-files-storage-linux.md)voor een volledig voor beeld. 

Momenteel wordt slechts een `storage-type` van `AzureFiles` ondersteund. U kunt Maxi maal vijf shares koppelen aan een bepaalde functie-app. Het koppelen van een bestands share kan de koude start tijd verhogen met ten minste 200 300ms of zelfs meer wanneer het opslag account zich in een andere regio bevindt.

De gekoppelde share is beschikbaar voor uw functie code op de `mount-path` opgegeven locatie. Wanneer bijvoorbeeld `mount-path` is `/path/to/mount` , hebt u toegang tot de doel directory per bestands systeem-api's, zoals in het volgende python-voor beeld:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure Functions hosting opties.

> [!div class="nextstepaction"]
> [Schaal en hosting van Azure Functions](functions-scale.md)


