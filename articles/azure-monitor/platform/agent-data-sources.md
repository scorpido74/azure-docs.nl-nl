---
title: Agentgegevensbronnen configureren in Azure Monitor | Microsoft Documenten
description: Gegevensbronnen definiëren de logboekgegevens die Azure Monitor verzamelt van agents en andere verbonden bronnen.  In dit artikel wordt het concept beschreven van de manier waarop Azure Monitor gegevensbronnen gebruikt, worden de details uitgelegd van het configureren ervan en wordt een overzicht gegeven van de verschillende beschikbare gegevensbronnen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aec3fe2386ce916c556f6da295a8554fff140259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249098"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Agentgegevensbronnen in Azure Monitor
De gegevens die Azure Monitor verzamelt van agents, worden gedefinieerd door de gegevensbronnen die u configureert.  De gegevens van agents worden opgeslagen als [loggegevens](data-platform-logs.md) met een set records.  Elke gegevensbron maakt records van een bepaald type, waarbij elk type zijn eigen set eigenschappen heeft.

![Logboekgegevensverzameling](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Samenvatting van gegevensbronnen
In de volgende tabel worden de agentgegevensbronnen weergegeven die momenteel beschikbaar zijn in Azure Monitor.  Elk heeft een link naar een apart artikel met details voor die gegevensbron.   Het biedt ook informatie over hun methode en frequentie van het verzamelen. 


| Gegevensbron | Platform | Log analyse agent | Operations Manager-agent | Azure Storage | Operations Manager vereist? | Operations Manager agent gegevens verzonden via management groep | Verzamelingsfrequentie |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Aangepaste logboeken](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | bij aankomst |
| [Aangepaste logboeken](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | bij aankomst |
| [IIS-logboeken](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |afhankelijk van de rolloverinstelling Logboekbestand |
| [Prestatiemeteritems](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |zoals gepland, minimaal 10 seconden |
| [Prestatiemeteritems](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |zoals gepland, minimaal 10 seconden |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |vanuit Azure-opslag: 10 minuten; van agent: bij aankomst |
| [Logboeken voor Windows-gebeurtenissen](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | bij aankomst |


## <a name="configuring-data-sources"></a>Gegevensbronnen configureren
U configureert gegevensbronnen in het menu **Gegevens** in **Geavanceerde instellingen** voor de werkruimte.  Elke configuratie wordt geleverd aan alle aangesloten bronnen in uw werkruimte.  U momenteel geen agents uitsluiten van deze configuratie.

![Windows-gebeurtenissen configureren](media/agent-data-sources/configure-events.png)

1. Selecteer in de Azure-portal de optie **Logboekanalysewerkruimten** > uw werkruimte > **Geavanceerde instellingen.**
2. Selecteer **Gegevens**.
3. Klik op de gegevensbron die u wilt configureren.
4. Volg de koppeling naar de documentatie voor elke gegevensbron in de bovenstaande tabel voor meer informatie over de configuratie ervan.


## <a name="data-collection"></a>Gegevensverzameling
Gegevensbronconfiguraties worden binnen enkele minuten geleverd aan agents die rechtstreeks zijn verbonden met Azure Monitor.  De opgegeven gegevens worden verzameld van de agent en rechtstreeks geleverd aan Azure Monitor met intervallen die specifiek zijn voor elke gegevensbron.  Zie de documentatie voor elke gegevensbron voor deze specificaties.

Voor System Center Operations Manager-agents in een verbonden beheergroep worden gegevensbronconfiguraties vertaald in beheerpakketten en standaard elke 5 minuten aan de beheergroep geleverd.  De agent downloadt het managementpack als alle andere en verzamelt de opgegeven gegevens. Afhankelijk van de gegevensbron worden de gegevens verzonden naar een beheerserver die de gegevens doorstuurt naar de Azure Monitor, of de agent stuurt de gegevens naar Azure Monitor zonder de beheerserver te doorlopen. Zie [Gegevensverzamelingsgegevens voor bewakingsoplossingen in Azure](../insights/solutions-inventory.md) voor meer informatie.  U meer informatie lezen over het aansluiten van Operations Manager en Azure Monitor en het wijzigen van de frequentie die de configuratie wordt geleverd bij [Integratie configureren met System Center Operations Manager.](om-agents.md)

Als de agent geen verbinding kan maken met Azure Monitor of Operations Manager, blijft deze gegevens verzamelen die het zal leveren wanneer deze een verbinding tot een verbinding maakt.  Gegevens kunnen verloren gaan als de hoeveelheid gegevens de maximale cachegrootte voor de client bereikt of als de agent niet in staat is om binnen 24 uur een verbinding tot stand te brengen.

## <a name="log-records"></a>Logboekrecords
Alle logboekgegevens die door Azure Monitor worden verzameld, worden in de werkruimte opgeslagen als records.  Records die door verschillende gegevensbronnen worden verzameld, hebben hun eigen set eigenschappen en worden geïdentificeerd door de eigenschap **Type.**  Zie de documentatie voor elke gegevensbron en oplossing voor meer informatie over elk recordtype.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [bewakingsoplossingen](../insights/solutions.md) die functionaliteit toevoegen aan Azure Monitor en ook gegevens verzamelen in de werkruimte.
* Meer informatie over [logboekquery's](../log-query/log-query-overview.md) om de gegevens te analyseren die zijn verzameld uit gegevensbronnen en bewakingsoplossingen.  
* Configureer [waarschuwingen](alerts-overview.md) om u proactief op de hoogte te stellen van kritieke gegevens die zijn verzameld uit gegevensbronnen en bewakingsoplossingen.
