---
title: Metrische gegevens van Azure Analysis Services server bewaken | Microsoft Docs
description: Meer informatie over het bewaken van Analysis Services server-metrische gegevens in Azure Portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7c7aaf911930f83775f66c47377bc68edb059519
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70958973"
---
# <a name="monitor-server-metrics"></a>Metrische servergegevens bewaken

Analysis Services biedt metrische gegevens in azure Metrics Explorer, een gratis hulp programma in de portal, waarmee u de prestaties en de status van uw servers kunt bewaken. Bewaak bijvoorbeeld het geheugen-en CPU-gebruik, het aantal client verbindingen en het Resource verbruik van de query. Analysis Services gebruikt hetzelfde bewakings raamwerk als de meeste andere Azure-Services. Zie aan de slag [met Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md)voor meer informatie.

Als u meer uitgebreide diagnostische gegevens wilt uitvoeren, de prestaties wilt bijhouden en trends wilt identificeren voor meerdere service resources in een resource groep of abonnement, gebruikt u [Azure monitor](../azure-monitor/overview.md). Azure Monitor (Service) kan resulteren in een factureer bare service.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Meet gegevens voor een Analysis Services server bewaken

1. Selecteer in Azure Portal **metrische gegevens**.

    ![Bewaken in Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. Selecteer in **beschik bare metrische**gegevens de metrische gegevens die in uw grafiek moeten worden meegenomen. 

    ![Bewaak diagram](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Metrische Server gegevens

Gebruik deze tabel om te bepalen welke metrische gegevens het meest geschikt zijn voor uw bewakings scenario. In hetzelfde diagram kunnen alleen metrische gegevens van dezelfde eenheid worden weer gegeven.

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Description|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Wachtrij lengte van de opdracht pool taak|Count|Average|Aantal taken in de wachtrij van de opdracht thread pool.|
|CurrentConnections|Verbinding: Huidige verbindingen|Count|Average|Het huidige aantal client verbindingen dat tot stand is gebracht.|
|CurrentUserSessions|Huidige gebruikers sessies|Count|Average|Het huidige aantal gebruikers sessies dat tot stand is gebracht.|
|mashup_engine_memory_metric|M-engine geheugen|Bytes|Average|Geheugen gebruik door mashup-engine processen|
|mashup_engine_qpu_metric|M-engine QPU|Count|Average|QPU-gebruik door mashup-engine processen|
|memory_metric|Geheugen|Bytes|Average|Geheugenmetabase. Bereik 0-25 GB voor S1, 0-50 GB voor S2 en 0-100 GB voor S4|
|memory_thrashing_metric|Geheugen overbelasting|Percent|Average|Gemiddeld geheugen overbelasting.|
|CleanerCurrentPrice|Geheugen: Huidige prijs verduidelijken|Count|Average|Huidige prijs van geheugen, $/byte/tijd), genormaliseerd naar 1000.|
|CleanerMemoryNonshrinkable|Geheugen: Ruim geheugen dat niet kan worden verkleind|Bytes|Average|Hoeveelheid geheugen (in bytes) die niet wordt verwijderd door de achtergrond opschoning.|
|CleanerMemoryShrinkable|Geheugen: Verkleinbaar geheugen|Bytes|Average|De hoeveelheid geheugen (in bytes) die wordt verwijderd door de achtergrond opschoning.|
|MemoryLimitHard|Geheugen: Geheugen limiet hard|Bytes|Average|Vaste geheugen limiet, van configuratie bestand.|
|MemoryLimitHigh|Geheugen: Hoge geheugen limiet|Bytes|Average|Hoge geheugen limiet, van configuratie bestand.|
|MemoryLimitLow|Geheugen: Geheugen limiet laag|Bytes|Average|Limiet voor weinig geheugen, van configuratie bestand.|
|MemoryLimitVertiPaq|Geheugen: Geheugen limiet VertiPaq|Bytes|Average|In-Memory limiet, van configuratie bestand.|
|MemoryUsage|Geheugen: Geheugengebruik|Bytes|Average|Geheugen gebruik van het Server proces zoals gebruikt bij het berekenen van de prijs voor het schonen van geheugen. Gelijk aan Counter Process\PrivateBytes plus de grootte van gegevens die zijn toegewezen door het geheugen, waarbij elk geheugen wordt genegeerd dat is toegewezen aan of toegewezen door de in-Memory Analytics Engine (VertiPaq) die de geheugen limiet van het engine overschrijdt.|
|private_bytes_metric|Privébytes |Bytes|Average|De totale hoeveelheid geheugen die het Analysis Services engine proces en de mashup-container processen hebben toegewezen, exclusief geheugen gedeeld met andere processen.|
|virtual_bytes_metric|Virtuele bytes |Bytes|Average|De huidige grootte van de virtuele adres ruimte die Analysis Services engine proces en mashup-container processen gebruiken.|
|mashup_engine_private_bytes_metric|M-engine-eigen bytes |Bytes|Average|De totale hoeveelheid geheugen-mashup-container processen zijn toegewezen, exclusief geheugen gedeeld met andere processen.|
|mashup_engine_virtual_bytes_metric|M-engine virtuele bytes |Bytes|Average|De huidige grootte van de mashup-container processen van de virtuele adres ruimte wordt gebruikt.|
|Quota|Geheugen: Quota|Bytes|Average|Huidig geheugen quotum, in bytes. Geheugen quota wordt ook wel een geheugen toekenning of geheugen reservering genoemd.|
|QuotaBlocked|Geheugen: Quota geblokkeerd|Count|Average|Het huidige aantal quotum aanvragen dat is geblokkeerd totdat andere geheugen quota zijn vrijgemaakt.|
|VertiPaqNonpaged|Geheugen: VertiPaq Nonpaged|Bytes|Average|Geheugen bytes vergrendeld in de werkset voor gebruik door de in-Memory engine.|
|VertiPaqPaged|Geheugen: VertiPaq Paged|Bytes|Average|Bytes van wisselbaar geheugen die in gebruik zijn voor in-Memory gegevens.|
|ProcessingPoolJobQueueLength|Wachtrij lengte van de pool taak wordt verwerkt|Count|Average|Het aantal niet-I/O-taken in de wachtrij van de verwer kende thread pool.|
|RowsConvertedPerSec|Bezig Geconverteerde rijen per seconde|CountPerSecond|Average|Het aantal rijen dat tijdens de verwerking is geconverteerd.|
|RowsReadPerSec|Bezig Gelezen rijen per seconde|CountPerSecond|Average|Het aantal rijen dat van alle relationele data bases is gelezen.|
|RowsWrittenPerSec|Bezig Aantal geschreven rijen per seconde|CountPerSecond|Average|Het aantal rijen dat tijdens de verwerking is geschreven.|
|qpu_metric|QPU|Count|Average|QPU. Bereik 0-100 voor S1, 0-200 voor S2 en 0-400 voor S4|
|QueryPoolBusyThreads|Query's pool bezette threads|Count|Average|Het aantal actieve threads in de query thread pool.|
|SuccessfullConnectionsPerSec|Geslaagde verbindingen per seconde|CountPerSecond|Average|Snelheid van geslaagde verbindings voltooiingen.|
|CommandPoolBusyThreads|Lijnen Bezette threads van opdracht pool|Count|Average|Het aantal actieve threads in de opdracht thread pool.|
|CommandPoolIdleThreads|Lijnen Niet-actieve threads van opdracht pool|Count|Average|Het aantal niet-actieve threads in de opdracht thread pool.|
|LongParsingBusyThreads|Lijnen Lange threads voor het parseren|Count|Average|Het aantal actieve threads in de thread pool voor lang parseren.|
|LongParsingIdleThreads|Lijnen Lang niet-actieve threads parseren|Count|Average|Het aantal niet-actieve threads in de thread pool voor lang parseren.|
|LongParsingJobQueueLength|Lijnen Lengte van taak wachtrij voor lang parseren|Count|Average|Aantal taken in de wachtrij van de thread pool voor lang parseren.|
|ProcessingPoolIOJobQueueLength|Lijnen Wachtrij lengte van I/O-taak verwerking van groep|Count|Average|Het aantal I/O-taken in de wachtrij van de verwer kende thread pool.|
|ProcessingPoolBusyIOJobThreads|Lijnen Bezig met verwerken van werk groep-I/O-taak threads|Count|Average|Het aantal threads waarmee I/O-taken worden uitgevoerd in de verwer kende thread pool.|
|ProcessingPoolBusyNonIOThreads|Lijnen Bezig met verwerken van niet-I/O-threads van groep|Count|Average|Het aantal threads met niet-I/O-taken in de verwer kende thread pool.|
|ProcessingPoolIdleIOJobThreads|Lijnen Niet-actieve I/O-taak threads van groep verwerken|Count|Average|Het aantal niet-actieve threads voor I/O-taken in de verwer kende thread pool.|
|ProcessingPoolIdleNonIOThreads|Lijnen Niet-I/O-threads van de groep worden verwerkt|Count|Average|Het aantal niet-actieve threads in de verwer kende thread pool dat is gereserveerd voor niet-I/O-taken.|
|QueryPoolIdleThreads|Lijnen Niet-actieve threads van query pool|Count|Average|Het aantal niet-actieve threads voor I/O-taken in de verwer kende thread pool.|
|QueryPoolJobQueueLength|Lijnen Wachtrij lengte van de taak pool voor query's|Count|Average|Aantal taken in de wachtrij van de query thread pool.|
|ShortParsingBusyThreads|Lijnen Kortere threads voor het parseren|Count|Average|Het aantal actieve threads in de thread pool voor kort parseren.|
|ShortParsingIdleThreads|Lijnen Niet-actieve threads voor kort parseren|Count|Average|Het aantal niet-actieve threads in de thread pool voor kort parseren.|
|ShortParsingJobQueueLength|Lijnen Korte lengte van taak wachtrij voor parseren|Count|Average|Aantal taken in de wachtrij van de thread pool voor kort parseren.|
|TotalConnectionFailures|Totaal aantal verbindings fouten|Count|Average|Totaal aantal mislukte Verbindings pogingen.|
|TotalConnectionRequests|Totaal aantal verbindings aanvragen|Count|Average|Totaal aantal verbindings aanvragen. |

## <a name="next-steps"></a>Volgende stappen
[Overzicht van Azure Monitor](../azure-monitor/overview.md)      
[Aan de slag met Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md)      
[Metrische gegevens in Azure Monitor REST API](/rest/api/monitor/metrics)
