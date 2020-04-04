---
title: Azure Storage-analyses gebruiken om logboeken en metrische gegevens te verzamelen | Microsoft Documenten
description: Met Storage Analytics u statistiekengegevens voor alle opslagservices bijhouden en logboeken verzamelen voor blob-, wachtrij- en tabelopslag.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 4ad9f13bcdf36b67400adb62d58ee260ff256bb3
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637148"
---
# <a name="storage-analytics"></a>Storage Analytics

Azure Storage Analytics voert logboekregistratie uit en biedt metrische gegevens voor een opslagaccount. U deze gegevens gebruiken om aanvragen te traceren, gebruikstrends te analyseren en problemen met uw opslagaccount te diagnosticeren.

Als u Storage Analytics wilt gebruiken, moet u deze afzonderlijk inschakelen voor elke service die u wilt controleren. U het inschakelen via de [Azure-portal.](https://portal.azure.com) Zie Een [opslagaccount controleren in de Azure-portal](storage-monitor-storage-account.md)voor meer informatie. U Storage Analytics ook programmatisch inschakelen via de REST API of de clientbibliotheek. Gebruik de [eigenschappen van Blob-service](/rest/api/storageservices/set-blob-service-properties)instellen , [Serviceeigenschappen voor wachtrijservice instellen,](/rest/api/storageservices/set-queue-service-properties) [Eigenschappen van tabelservice instellen](/rest/api/storageservices/set-table-service-properties)en Eigenschappen van [bestandsservice instellen](/rest/api/storageservices/Get-File-Service-Properties) om Storage Analytics voor elke service in te schakelen.

De geaggregeerde gegevens worden opgeslagen in een bekende blob (voor logboekregistratie) en in bekende tabellen (voor metrische gegevens), die toegankelijk kunnen zijn via de Blob-service en Tabelservice-API's.

Storage Analytics heeft een limiet van 20 TB voor de hoeveelheid opgeslagen gegevens die onafhankelijk is van de totale limiet voor uw opslagaccount. Zie [Schaalbaarheids- en prestatiedoelen voor standaardopslagaccounts voor](scalability-targets-standard-account.md)meer informatie over opslagaccountlimieten.

Zie [Microsoft Azure Storage controleren, diagnosticeren en oplossen](storage-monitoring-diagnosing-troubleshooting.md)van Problemen met Azure Storage voor een uitgebreide handleiding voor het gebruik van Storage Analytics en andere hulpprogramma's voor het identificeren, diagnosticeren en oplossen van azure storage.

## <a name="billing-for-storage-analytics"></a>Facturering voor opslaganalyse

Alle metrische gegevens worden geschreven door de services van een opslagaccount. Als gevolg hiervan is elke schrijfbewerking uitgevoerd door Storage Analytics factureerbaar. Bovendien is de hoeveelheid opslag die wordt gebruikt door metrische gegevens ook factureerbaar.

De volgende acties van Storage Analytics zijn factureerbaar:

* Verzoeken om blobs te maken voor logboekregistratie.
* Verzoeken om tabelentiteiten voor statistieken te maken.

Als u een beleid voor gegevensbewaring hebt geconfigureerd, worden er geen kosten in rekening gebracht voor het verwijderen van transacties wanneer Storage Analytics oude logboekregistratie- en metrische gegevens verwijdert. Het verwijderen van transacties van een klant is echter factureerbaar. Zie Een beleid voor het bewaren van gegevens voor [opslaganalyse instellen](https://msdn.microsoft.com/library/azure/hh343263.aspx)voor meer informatie over bewaarbeleid voor opslaganalyse .

### <a name="understanding-billable-requests"></a>Inzicht in factureerbare aanvragen

Elk verzoek aan de opslagservice van een account is factureerbaar of niet-factureerbaar. Storage Analytics registreert elke afzonderlijke aanvraag die aan een service wordt gedaan, inclusief een statusbericht dat aangeeft hoe de aanvraag is afgehandeld. Op dezelfde manier slaat Storage Analytics statistieken op voor zowel een service als de API-bewerkingen van die service, inclusief de percentages en het aantal statusberichten. Samen kunnen deze functies u helpen uw factureerbare verzoeken te analyseren, verbeteringen aan te brengen in uw toepassing en problemen met aanvragen voor uw services te diagnosticeren. Zie [Inzicht in Azure Storage Billing - Bandbreedte, Transacties en Capaciteit](https://docs.microsoft.com/archive/blogs/windowsazurestorage/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity)voor meer informatie over facturering.

Wanneer u storageanalytics-gegevens bekijkt, u de tabellen in het onderwerp [Aangemelde bewerkingen en statusberichten](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) van Storage Analytics gebruiken om te bepalen welke aanvragen factureerbaar zijn. Vervolgens u uw logboeken en metrische gegevens vergelijken met de statusberichten om te zien of er kosten in rekening zijn gebracht voor een bepaald verzoek. U de tabellen in het vorige onderwerp ook gebruiken om de beschikbaarheid voor een opslagservice of afzonderlijke API-bewerking te onderzoeken.

## <a name="next-steps"></a>Volgende stappen
* [Een Storage-account bewaken in de Azure-portal](storage-monitor-storage-account.md)
* [Statistieken over opslaganalyses](storage-analytics-metrics.md)
* [Logboekregistratie van Opslaganalyse](storage-analytics-logging.md)
