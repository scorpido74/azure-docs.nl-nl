---
title: Opslagoverwegingen voor Azure-functies
description: Meer informatie over de opslagvereisten van Azure-functies en over het versleutelen van opgeslagen gegevens.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3bacc93ad6c1851d9165e8efb7d27b427050e6f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276580"
---
# <a name="storage-considerations-for-azure-functions"></a>Opslagoverwegingen voor Azure-functies

Azure Functions vereist een Azure Storage-account wanneer u een functie-app-exemplaar maakt. De volgende opslagservices kunnen door uw functie-app worden gebruikt:


|Opslagservice  | Functiesgebruik  |
|---------|---------|
| [Azure Blob-opslag](../storage/blobs/storage-blobs-introduction.md)     | Bindingenstatus- en functietoetsen behouden.  <br/>Ook gebruikt door [taakhubs in duurzame functies.](durable/durable-functions-task-hubs.md) |
| [Azure-bestanden](../storage/files/storage-files-introduction.md)  | Bestandsshare wordt gebruikt om uw functie-appcode op te slaan en uit te voeren in een [verbruiksplan.](functions-scale.md#consumption-plan) |
| [Azure-wachtrijopslag](../storage/queues/storage-queues-introduction.md)     | Gebruikt door [taakhubs in duurzame functies](durable/durable-functions-task-hubs.md).   |
| [Azure Table storage](../storage/tables/table-storage-overview.md)  |  Gebruikt door [taakhubs in duurzame functies](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> Als u gebruikmaakt van het hostingabonnement Consumption worden uw functiecode en uw bindingsconfiguratiebestanden opgeslagen in het belangrijkste opslagaccount in Azure File Storage. Wanneer u het belangrijkste opslagaccount verwijdert, wordt de inhoud verwijderd en kan deze niet worden hersteld.

## <a name="storage-account-requirements"></a>Vereisten voor een opslagaccount

Wanneer u een functie-app maakt, moet u een Azure Storage-account voor algemene doeleinden maken of koppelen dat blob-, wachtrij- en tabelopslag ondersteunt. Dit komt omdat Functions afhankelijk is van Azure Storage voor bewerkingen zoals het beheren van triggers en het uitvoeren van logboeken. Sommige opslagaccounts ondersteunen geen wachtrijen en tabellen. Deze accounts omvatten opslagaccounts met blob, alleen Azure Premium-opslag en opslagaccounts voor algemene doeleinden met ZRS-replicatie. Deze niet-ondersteunde accounts worden uit het blade van het opslagaccount gefilterd bij het maken van een functie-app.

Zie [Introductie van de Azure Storage-services](../storage/common/storage-introduction.md#azure-storage-services) voor meer informatie over opslagaccounttypen. 

Hoewel u een bestaand opslagaccount gebruiken met uw functie-app, moet u ervoor zorgen dat deze aan deze vereisten voldoet. Opslagaccounts die zijn gemaakt als onderdeel van de doormaakstroom van de functie-app, voldoen gegarandeerd aan deze vereisten voor opslagaccounts.  

## <a name="storage-account-guidance"></a>Richtlijnen voor opslagaccount

Elke functie-app vereist een opslagaccount om te werken. Als dat account wordt verwijderd, wordt de functie-app niet uitgevoerd. Zie Problemen met betrekking [tot opslag oplossen](functions-recover-storage-account.md)als u opslaggerelateerde problemen wilt oplossen. De volgende aanvullende overwegingen zijn van toepassing op het opslagaccount dat wordt gebruikt door functie-apps.

### <a name="storage-account-connection-setting"></a>Instelling voor opslagaccountverbinding

De opslagaccountverbinding wordt onderhouden in de [azurewebjobsstorage-toepassingsinstelling](./functions-app-settings.md#azurewebjobsstorage). 

De tekenreeks voor de verbinding met het opslagaccount moet worden bijgewerkt wanneer u opslagsleutels regenereert. [Lees hier meer over storage key management](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

### <a name="shared-storage-accounts"></a>Gedeelde opslagaccounts

Het is mogelijk voor meerdere functie-apps om hetzelfde opslagaccount te delen zonder problemen. In Visual Studio u bijvoorbeeld meerdere apps ontwikkelen met behulp van de Azure Storage Emulator. In dit geval werkt de emulator als één opslagaccount. Hetzelfde opslagaccount dat door uw functie-app wordt gebruikt, kan ook worden gebruikt om uw toepassingsgegevens op te slaan. Deze aanpak is echter niet altijd een goed idee in een productieomgeving.

### <a name="optimize-storage-performance"></a>Opslagprestaties optimaliseren

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Versleuteling van opslaggegevens

Azure Storage versleutelt alle gegevens in een opslagaccount in rust. Zie [Azure Storage-versleuteling voor gegevens in rust](../storage/common/storage-service-encryption.md).

Standaard worden gegevens versleuteld met door Microsoft beheerde sleutels. Voor extra controle over versleutelingssleutels u door de klant beheerde sleutels leveren die u gebruiken voor versleuteling van blob- en bestandsgegevens. Deze sleutels moeten aanwezig zijn in Azure Key Vault voor functies om toegang te krijgen tot het opslagaccount. Zie Door de klant beheerde sleutels configureren met Azure Key Vault voor meer informatie [met de Azure-portal.](../storage/common/storage-encryption-keys-portal.md)  

## <a name="mount-file-shares-linux"></a>Bestandsshares monteren (Linux)

U bestaande Azure Files-shares aan uw Linux-functie-apps monteren. Door een share te monteren op uw Linux-functie-app, u gebruikmaken van bestaande machine learning-modellen of andere gegevens in uw functies. U [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) de opdracht gebruiken om een bestaand aandeel te monteren op uw Linux-functie-app. 

In deze `share-name` opdracht is de naam van het `custom-id` bestaande Azure-bestandenaandeel en kan elke tekenreeks zijn die het aandeel op unieke wijze definieert wanneer het is gemonteerd op de functie-app. Ook `mount-path` is het pad van waaruit het aandeel wordt geopend in uw functie-app. `mount-path`moet in het `/dir-name`formaat, en het `/home`kan niet beginnen met .

Zie bijvoorbeeld de scripts in [de functie-app Een Python maken en een Azure-bestandenshare monteren.](scripts/functions-cli-mount-files-storage-linux.md) 

Momenteel wordt `storage-type` alleen `AzureFiles` een van ondersteund. U slechts vijf shares monteren op een bepaalde functie-app. Het monteren van een bestandsshare kan de koude starttijd met ten minste 200-300 ms verhogen, of zelfs meer wanneer de opslagrekening zich in een andere regio bevindt.

Het gemonteerde aandeel is beschikbaar voor `mount-path` uw functiecode op de opgegeven. Wanneer `mount-path` is `/path/to/mount`dat bijvoorbeeld, u de doelmap openen op API's van bestandssysteem, zoals in het volgende Python-voorbeeld:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over azure-functies hostingopties.

> [!div class="nextstepaction"]
> [Schaal en hosting van Azure Functions](functions-scale.md)


