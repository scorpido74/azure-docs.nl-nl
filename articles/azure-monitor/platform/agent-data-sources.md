---
title: Agent gegevens bronnen configureren in Azure Monitor | Microsoft Docs
description: Gegevens bronnen definiëren de logboek gegevens die Azure Monitor verzameld van agents en andere verbonden bronnen.  In dit artikel wordt beschreven hoe Azure Monitor gegevens bronnen gebruikt, wordt uitgelegd hoe u deze kunt configureren en wordt een overzicht gegeven van de verschillende gegevens bronnen die beschikbaar zijn.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aec3fe2386ce916c556f6da295a8554fff140259
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249098"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Gegevens bronnen van agents in Azure Monitor
De gegevens die Azure Monitor verzameld van agents, worden gedefinieerd door de gegevens bronnen die u configureert.  De gegevens van agents worden opgeslagen als [logboek gegevens](data-platform-logs.md) met een set records.  Elke gegevens bron maakt records van een bepaald type met elk type met een eigen set eigenschappen.

![Gegevens verzameling vastleggen](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Samen vatting van gegevens bronnen
De volgende tabel geeft een lijst van de agent gegevens bronnen die momenteel beschikbaar zijn in Azure Monitor.  Elk heeft een koppeling naar een afzonderlijk artikel met Details voor die gegevens bron.   Het bevat ook informatie over de methode en frequentie van de verzameling. 


| Gegevensbron | Platform | Log Analytics-agent | Operations Manager-agent | Azure-opslag | Operations Manager vereist? | Operations Manager agent gegevens die via een beheer groep zijn verzonden | Verzamelingsfrequentie |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Aangepaste logboeken](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | bij aankomst |
| [Aangepaste logboeken](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | bij aankomst |
| [IIS-logboeken](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |is afhankelijk van de instelling van het logboek bestand voor de rollover |
| [Prestatiemeteritems](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |zoals gepland, mini maal 10 seconden |
| [Prestatiemeteritems](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |zoals gepland, mini maal 10 seconden |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |uit Azure Storage: 10 minuten; van agent: op aankomst |
| [Windows-gebeurtenis logboeken](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | bij aankomst |


## <a name="configuring-data-sources"></a>Gegevens bronnen configureren
U configureert gegevens bronnen vanuit het menu **Data** in **Geavanceerde instellingen** voor de werk ruimte.  Elke configuratie wordt aan alle verbonden bronnen in uw werk ruimte geleverd.  U kunt momenteel geen agents uitsluiten van deze configuratie.

![Windows-gebeurtenissen configureren](media/agent-data-sources/configure-events.png)

1. Selecteer in de Azure Portal **log Analytics werk ruimten** > uw werk ruimte > **Geavanceerde instellingen**.
2. Selecteer **gegevens**.
3. Klik op de gegevens bron die u wilt configureren.
4. Volg de koppeling naar de documentatie voor elke gegevens bron in de bovenstaande tabel voor meer informatie over de configuratie.


## <a name="data-collection"></a>Gegevensverzameling
Gegevens bron configuraties worden geleverd aan agents die rechtstreeks zijn verbonden met Azure Monitor binnen een paar minuten.  De opgegeven gegevens worden verzameld van de agent en direct bezorgd bij Azure Monitor met intervallen die specifiek zijn voor elke gegevens bron.  Zie de documentatie voor elke gegevens bron voor deze specifieke informatie.

Voor System Center Operations Manager agents in een verbonden beheer groep worden de gegevens bron configuraties omgezet in Management Packs en standaard elke vijf minuten aan de beheer groep geleverd.  De agent downloadt de management pack zoals de andere en verzamelt de opgegeven gegevens. Afhankelijk van de gegevens bron, worden de gegevens verzonden naar een beheer server die de gegevens doorstuurt naar de Azure Monitor, of de agent verzendt de gegevens naar Azure Monitor zonder via de beheer server te gaan. Zie [Details over het verzamelen van gegevens voor het controleren van oplossingen in azure](../insights/solutions-inventory.md) voor meer informatie.  U vindt meer informatie over het verbinden van Operations Manager en Azure Monitor en het wijzigen van de frequentie die de configuratie wordt geleverd bij het [configureren van de integratie met System Center Operations Manager](om-agents.md).

Als de agent geen verbinding kan maken met Azure Monitor of Operations Manager, worden er gegevens verzameld die worden geleverd bij het tot stand brengen van een verbinding.  Gegevens kunnen verloren gaan als de hoeveelheid gegevens de maximale cache grootte voor de client bereikt, of als de agent binnen 24 uur geen verbinding kan maken.

## <a name="log-records"></a>Logboek records
Alle door Azure Monitor verzamelde logboek gegevens worden opgeslagen in de werk ruimte als records.  Records die door verschillende gegevens bronnen worden verzameld, hebben hun eigen set eigenschappen en worden geïdentificeerd aan de hand van de eigenschap **type** .  Zie de documentatie voor elke gegevens bron en oplossing voor meer informatie over elk record type.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [controleren van oplossingen](../insights/solutions.md) voor het toevoegen van functionaliteit aan Azure monitor en het verzamelen van gegevens in de werk ruimte.
* Meer informatie over [logboek query's](../log-query/log-query-overview.md) voor het analyseren van de verzamelde gegevens van gegevens bronnen en bewakings oplossingen.  
* [Waarschuwingen](alerts-overview.md) configureren om u proactief te informeren over essentiële gegevens die worden verzameld uit gegevens bronnen en bewakings oplossingen.
