---
title: Voorbereiden op indelingswijziging in Azure Monitor-bronlogboeken
description: Azure-bronlogboeken zijn verplaatst naar het gebruik van toevoegende blobs op 1 november 2018.
author: johnkemnetz
services: monitoring
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 001dfbc78c0027249143e933684523d47af383d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096777"
---
# <a name="prepare-for-format-change-to-azure-monitor-platform-logs-archived-to-a-storage-account"></a>Voorbereiden op indelingswijziging in Azure Monitor-platformlogboeken die zijn gearchiveerd naar een opslagaccount

> [!WARNING]
> Als u [Azure-bronlogboeken of -statistieken naar een opslagaccount](resource-logs-collect-storage.md) verzendt met diagnostische instellingen of [activiteitslogboeken naar een opslagaccount met logboekprofielen,](resource-logs-collect-storage.md)is de indeling van de gegevens in het opslagaccount op 1 november 2018 gewijzigd in JSON-lijnen. In de onderstaande instructies wordt de impact beschreven en hoe u uw tooling bijwerken om met de nieuwe indeling om te gaan.
>

## <a name="what-changed"></a>Wat is er veranderd?

Azure Monitor biedt een mogelijkheid waarmee u bronlogboeken en activiteitslogboeken verzenden naar een Azure-opslagaccount, naamruimte voor gebeurtenishubs of naar een Logboekanalysewerkruimte in Azure Monitor. Om een probleem met de systeemprestaties op te lossen, is op **1 november 2018 om 12:00 middernacht UTC** de indeling van logboekgegevens die naar blobopslag worden verzonden gewijzigd. Als u tooling hebt die gegevens uit blob-opslag leest, moet u uw hulpprogramma's bijwerken om inzicht te krijgen in de nieuwe gegevensindeling.

* Op donderdag 1 november 2018 om 12:00 middernacht UTC is het blobformaat gewijzigd in [JSON Lines](http://jsonlines.org/). Dit betekent dat elke record wordt afgebakend door een nieuwe regel, zonder externe records array en geen komma's tussen JSON records.
* De blob-indeling is gewijzigd voor alle diagnostische instellingen voor alle abonnementen tegelijk. De eerste PT1H.json bestand uitgezonden voor 1 november gebruikt deze nieuwe indeling. De blob- en containernamen blijven hetzelfde.
* Het instellen van een diagnostische instelling tussen vóór 1 november bleef gegevens in de huidige indeling uitzenden tot 1 november.
* Deze wijziging vond in één keer plaats in alle openbare cloudregio's. De wijziging vindt nog niet plaats in Microsoft Azure dat wordt beheerd door clouds van 21Vianet, Azure Germany of Azure Government.
* Deze wijziging heeft gevolgen voor de volgende gegevenstypen:
  * [Azure-bronlogboeken](archive-diagnostic-logs.md) [(zie lijst met bronnen hier)](diagnostic-logs-schema.md)
  * [Azure-resourcestatistieken die worden geëxporteerd door diagnostische instellingen](diagnostic-settings.md)
  * [Azure Activity-logboekgegevens die worden geëxporteerd door logboekprofielen](activity-log-collect.md)
* Deze wijziging heeft geen invloed op:
  * Netwerkstroomlogboeken
  * Azure-servicelogboeken zijn nog niet beschikbaar via Azure Monitor (bijvoorbeeld Azure App Service-bronlogboeken, opslaganalyselogboeken)
  * Routering van Azure-bronlogboeken en activiteitslogboeken naar andere bestemmingen (gebeurtenishubs, logboekanalyse)

### <a name="how-to-see-if-you-are-impacted"></a>Hoe om te zien of u wordt beïnvloed

U wordt alleen beïnvloed door deze wijziging als u:
1. Zijn logboekgegevens naar een Azure-opslagaccount verzenden met behulp van een diagnostische instelling en
2. Hebben tooling die afhankelijk is van de JSON structuur van deze logs in opslag.
 
Als u wilt bepalen of u diagnostische instellingen hebt die gegevens naar een Azure-opslagaccount verzenden, u naar het gedeelte **Monitor** van de portal navigeren, op **Diagnostische instellingen**klikken en eventuele resources identificeren die **zijn** ingesteld op **Ingeschakeld:**

![Blade van Diagnostische instellingen azure-monitor](media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Als diagnostische status is ingesteld op ingeschakeld, hebt u een actieve diagnostische instelling voor die resource. Klik op de bron om te zien of diagnostische instellingen gegevens naar een opslagaccount verzenden:

![Opslagaccount ingeschakeld](media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Als er resources zijn die gegevens naar een opslagaccount verzenden met behulp van deze diagnostische instellingen voor bronnen, wordt de indeling van de gegevens in dat opslagaccount beïnvloed door deze wijziging. Tenzij u aangepaste tooling hebt die buiten deze opslagaccounts werkt, heeft de indelingswijziging geen invloed op u.

### <a name="details-of-the-format-change"></a>Details van de indelingswijziging

De huidige indeling van het PT1H.json-bestand in Azure blob-opslag maakt gebruik van een JSON-array met records. Hier is nu een voorbeeld van een KeyVault-logboekbestand:

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

De nieuwe indeling maakt gebruik [van JSON-regels](http://jsonlines.org/), waarbij elke gebeurtenis een regel is en het teken newline een nieuwe gebeurtenis aangeeft. Hier is wat de bovenstaande steekproef eruit zal zien in de PT1H.json bestand na de wijziging:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Met deze nieuwe indeling kan Azure Monitor logboekbestanden pushen met [behulp van toevoegende blobs,](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)die efficiënter zijn voor het continu toevoegen van nieuwe gebeurtenisgegevens.

## <a name="how-to-update"></a>Bijwerken

U hoeft alleen updates te maken als u aangepaste tooling hebt die deze logboekbestanden inneemt voor verdere verwerking. Als u gebruik maakt van een externe loganalyse of SIEM-tool, raden we u aan [om gebeurtenishubs te gebruiken om deze gegevens in plaats daarvan in te nemen.](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) Event hubs integratie is eenvoudiger in termen van het verwerken van logs van veel services en bookmarking locatie in een bepaald logboek.

Aangepaste gereedschappen moeten worden bijgewerkt om zowel de huidige indeling als de hierboven beschreven JSON-lijnen-indeling te verwerken. Dit zorgt ervoor dat wanneer gegevens in de nieuwe indeling worden weergegeven, uw hulpprogramma's niet worden afgebroken.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het archiveren van bronbronlogboeken naar een opslagaccount](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* Meer informatie over [het archiveren van activiteitslogboekgegevens naar een opslagaccount](./../../azure-monitor/platform/archive-activity-log.md)

