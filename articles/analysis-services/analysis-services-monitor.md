---
title: Serverstatistieken van Azure Analysis Services-server controleren | Microsoft Documenten
description: Lees hoe Analysis Services Azure Metrics Explorer, een gratis tool in de portal, gebruikt om de prestaties en status van uw servers te controleren.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: aaa3a6d128fe7dd466f6f60ab515f05fa38ba63b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252803"
---
# <a name="monitor-server-metrics"></a>Metrische servergegevens bewaken

Analysis Services biedt statistieken in Azure Metrics Explorer, een gratis hulpprogramma in de portal, om u te helpen de prestaties en status van uw servers te controleren. Controleer bijvoorbeeld het geheugen en cpu-gebruik, het aantal clientverbindingen en het verbruik van querybronnen. Analysis Services gebruikt hetzelfde bewakingsframework als de meeste andere Azure-services. Zie [Aan de slag met Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md)voor meer informatie.

Als u meer diepgaande diagnostiek wilt uitvoeren, prestaties wilt bijhouden en trends wilt identificeren in meerdere serviceresources in een resourcegroep of -abonnement, gebruikt u [Azure Monitor](../azure-monitor/overview.md). Azure Monitor (service) kan resulteren in een factureerbare service.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Statistieken voor een Analysis Services-server controleren

1. Selecteer in Azure-portal de optie **Metrische gegevens**.

    ![Bewaken in Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. Selecteer **in Metric**de statistieken die u in uw grafiek wilt opnemen. 

    ![Monitordiagram](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Serverstatistieken

Gebruik deze tabel om te bepalen welke statistieken het beste zijn voor uw bewakingsscenario. Alleen statistieken van dezelfde eenheid kunnen op dezelfde grafiek worden weergegeven.

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|
|---|---|---|---|---|
|CommandPoolJobQueueLengte|Wachtrijlengte van taakgroep|Count|Average|Aantal taken in de wachtrij van de command thread pool.|
|Huidige verbindingen|Verbinding: huidige verbindingen|Count|Average|Huidig aantal clientverbindingen tot stand gebracht.|
|CurrentUserSessions|Huidige gebruikerssessies|Count|Average|Huidig aantal gebruikerssessies ingesteld.|
|mashup_engine_memory_metric|M-enginegeheugen|Bytes|Average|Geheugengebruik door mashup-engineprocessen|
|mashup_engine_qpu_metric|M Engine QPU|Count|Average|QPU-gebruik door mashup-motorprocessen|
|memory_metric|Geheugen|Bytes|Average|Geheugen. Bereik 0-25 GB voor S1, 0-50 GB voor S2 en 0-100 GB voor S4|
|memory_thrashing_metric|Geheugenthrashing|Percentage|Average|Gemiddelde geheugen geseling.|
|Schonerestroomprijs|Geheugen: Schonere huidige prijs|Count|Average|Huidige prijs van het geheugen, $/byte/time, genormaliseerd tot 1000.|
|CleanerMemoryNonshrinkable CleanerMemory|Geheugen: Schoner geheugen niet krimpend|Bytes|Average|Hoeveelheid geheugen, in bytes, niet onderhevig aan zuivering door de achtergrond reiniger.|
|CleanerMemoryKrimpbaar|Geheugen: Schoner geheugen krimpbaar|Bytes|Average|Hoeveelheid geheugen, in bytes, onder voorbehoud van zuivering door de achtergrond reiniger.|
|MemoryLimitHard|Geheugen: Geheugenlimiet hard|Bytes|Average|Limiet voor hard geheugen, uit configuratiebestand.|
|MemoryLimitHigh|Geheugen: geheugenlimiet hoog|Bytes|Average|Hoge geheugenlimiet, van configuratiebestand.|
|MemoryLimitLow|Geheugen: geheugenlimiet laag|Bytes|Average|Lage geheugenlimiet, van configuratiebestand.|
|MemoryLimitVertiPaq|Geheugen: Geheugenlimiet VertiPaq|Bytes|Average|In-memory limit, van configuratiebestand.|
|Geheugengebruik|Geheugen: geheugengebruik|Bytes|Average|Geheugengebruik van het serverproces zoals gebruikt bij het berekenen van een schonere geheugenprijs. Gelijk aan tegenproces\PrivateBytes plus de grootte van geheugentoegewezen gegevens, waarbij elk geheugen wordt genegeerd, dat in kaart is gebracht of toegewezen is toegewezen door de in-memory analytics-engine (VertiPaq) die hoger is dan de geheugenlimiet van de engine.|
|private_bytes_metric|Privébytes |Bytes|Average|De totale hoeveelheid geheugen die het analysis services-engineproces en mashup-containerprocessen hebben toegewezen, zonder geheugen dat wordt gedeeld met andere processen.|
|virtual_bytes_metric|Virtuele bytes |Bytes|Average|De huidige grootte van de virtuele adresruimte die analysis services-engineproces en Mashup-containerprocessen gebruiken.|
|mashup_engine_private_bytes_metric|M Engine Private Bytes |Bytes|Average|De totale hoeveelheid geheugen Mashup container processen hebben toegewezen, met inbegrip van geheugen gedeeld met andere processen.|
|mashup_engine_virtual_bytes_metric|Virtuele bytes van M Engine |Bytes|Average|De huidige grootte van de virtuele adresruimte die Mashup-containerprocessen gebruikt.|
|Quota|Geheugen: Quotum|Bytes|Average|Huidig geheugenquotum, in bytes. Geheugenquotum wordt ook wel geheugensubsidie of geheugenreservering genoemd.|
|Quotageblokkeerd|Geheugen: Quotum geblokkeerd|Count|Average|Huidig aantal quotaaanvragen dat wordt geblokkeerd totdat andere geheugenquota zijn vrijgemaakt.|
|VertiPaqNonpaged|Geheugen: VertiPaq Nonpaged|Bytes|Average|Bytes geheugen die zijn vergrendeld in de werkset voor gebruik door de geheugenengine.|
|VertiPaqPaged|Geheugen: VertiPaq Paged|Bytes|Average|Bytes van het opgepiepte geheugen dat wordt gebruikt voor gegevens in het geheugen.|
|ProcessingpoolJobQueueLength|Wachtrijlengte van de groeptaak verwerken|Count|Average|Aantal niet-I/O-taken in de wachtrij van de verwerkingsthreadgroep.|
|Rijenomgebouwde persec|Verwerking: rijen die per seconde worden geconverteerd|CountPerSeconde|Average|Snelheid van rijen die tijdens de verwerking worden omgezet.|
|RowsReadPersec|Verwerking: Rijen per seconde gelezen|CountPerSeconde|Average|Snelheid van rijen die worden gelezen uit alle relationele databases.|
|RijengeschrevenPersec|Verwerking: rijen geschreven per seconde|CountPerSeconde|Average|Snelheid van rijen geschreven tijdens de verwerking.|
|qpu_metric|QPU (QPU)|Count|Average|QPU. Bereik 0-100 voor S1, 0-200 voor S2 en 0-400 voor S4|
|QuerypoolBusyThreads|Drukke threads voor querygroep|Count|Average|Aantal drukke threads in de querythreadgroep.|
|Succesvolle verbindingenPerSec|Succesvolle verbindingen per seconde|CountPerSeconde|Average|Snelheid van succesvolle verbindingsvoltooiingen.|
|CommandPoolBusyThreads|Threads: Drukke threads voor het uitvoeren van opdrachten|Count|Average|Aantal drukke threads in de command thread pool.|
|CommandPoolIdleThreads|Threads: Niet-actieve threads van de opdrachtgroep|Count|Average|Aantal niet-actieve threads in de command thread pool.|
|LongParsingBusyThreads|Threads: Lange ontwering drukke threads|Count|Average|Aantal drukke threads in de lange parsing thread pool.|
|LongParsingIdleThreads|Threads: Lange ontwering idle threads|Count|Average|Aantal niet-actieve threads in de lange parsing thread pool.|
|LongParsingJobQueueLength|Threads: Lange ontwering taakwachtrijlengte|Count|Average|Aantal taken in de wachtrij van de lange parsingthreadpool.|
|ProcessingpoolIOJobQueueLength|Threads: De wachtrijlengte van de groep I/O verwerken|Count|Average|Aantal I/O-taken in de wachtrij van de verwerkingsthreadgroep.|
|ProcessingpoolBusyIOJobThreads|Threads: Verwerking van de gedrukke I/O-taakthreads|Count|Average|Aantal threads met I/O-taken in de verwerkingsthreadgroep.|
|ProcessingpoolBusyNonIOThreads|Threads: Verwerking van druk niet-I/O-threads|Count|Average|Aantal threads waarop niet-I/O-taken worden uitgevoerd in de verwerkingsthreadgroep.|
|VerwerkingPoolIdleIOJobThreads|Threads: I/O-taakthreads verwerken|Count|Average|Aantal niet-actieve threads voor I/O-taken in de verwerkingsthreadgroep.|
|VerwerkingPoolIdleNonIOThreads|Threads: Niet-I/O-threads verwerken|Count|Average|Aantal niet-actieve threads in de verwerkingsthreadpool die is gewijd aan niet-I/O-taken.|
|QueryPoolIdleThreads|Threads: Niet-actieve threads van de querygroep|Count|Average|Aantal niet-actieve threads voor I/O-taken in de verwerkingsthreadgroep.|
|QuerypoolJobQueueLengte|Threads: wachtrijlengte van de querygroeptaak|Count|Average|Aantal taken in de wachtrij van de querythreadgroep.|
|ShortParsingBusyThreads|Threads: Korte parsing drukke threads|Count|Average|Aantal drukke threads in de korte parsing thread pool.|
|ShortParsingIdleThreads|Threads: Korte ontwering idle threads|Count|Average|Aantal niet-actieve threads in de korte parsing thread pool.|
|ShortParsingJobQueueLength ShortParsingJobQueueLength ShortParsingJobQueueLength ShortPar|Threads: Korte afstand van de taakwachtrijlengte|Count|Average|Aantal taken in de wachtrij van de korte parsing thread pool.|
|TotalConnection-fouten|Totale verbindingsfouten|Count|Average|Totaal mislukte verbindingspogingen.|
|TotalConnectionRequests|Totaal aantal verbindingsaanvragen|Count|Average|Totaal aantal verbindingsaanvragen. |

## <a name="next-steps"></a>Volgende stappen
[Overzicht van Azure Monitor](../azure-monitor/overview.md)      
[Aan de slag met Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md)      
[Statistieken in Azure Monitor REST API](/rest/api/monitor/metrics)
