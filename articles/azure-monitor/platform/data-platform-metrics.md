---
title: Statistieken in Azure Monitor | Microsoft Documenten
description: Beschrijft statistieken in Azure Monitor die lichtgewicht bewakingsgegevens zijn die bijna realtime scenario's kunnen ondersteunen.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: a02b3df02d455db8a7dfd21f63d659f75a66e446
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457311"
---
# <a name="metrics-in-azure-monitor"></a>Metrische gegevens in Azure Monitor

> [!NOTE]
> Het Azure Monitor-gegevensplatform is gebaseerd op twee fundamentele gegevenstypen: metrische gegevens en logboeken. In dit artikel worden statistieken beschreven. Raadpleeg [Logboeken in Azure Monitor](data-platform-logs.md) voor een gedetailleerde beschrijving van logboeken en naar [azure monitor-gegevensplatform](data-platform.md) voor een vergelijking van de twee.

Statistieken in Azure Monitor zijn licht van gewicht en kunnen bijna realtime scenario's ondersteunen, waardoor ze bijzonder handig zijn voor het waarschuwen en snel detecteren van problemen. In dit artikel wordt beschreven hoe statistieken zijn gestructureerd, wat u ermee doen en worden verschillende gegevensbronnen geïdentificeerd die gegevens opslaan in statistieken.

## <a name="what-are-metrics"></a>Wat zijn statistieken?
Metrische gegevens zijn numerieke waarden die verschillende aspecten van een systeem beschrijven op een bepaald tijdstip. Statistieken worden op regelmatige tijdstippen verzameld en zijn handig om te waarschuwen omdat ze vaak kunnen worden bemonsterd en een waarschuwing snel kan worden geactiveerd met relatief eenvoudige logica.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Wat u doen met Azure Monitor Metrics?
In de volgende tabel worden de verschillende manieren weergegeven waarop u metrische gegevens gebruiken in Azure Monitor.

|  |  |
|:---|:---|
| Analyseren | Gebruik [metrics explorer](metrics-charts.md) om verzamelde statistieken in een grafiek te analyseren en statistieken uit verschillende bronnen te vergelijken. |
| Visualiseren | Maak een grafiek van metrics explorer vast aan een [Azure-dashboard](../learn/tutorial-app-dashboards.md).<br>Maak een [werkmap](../app/usage-workbooks.md) om te combineren met meerdere sets gegevens in een interactief rapport. Exporteer de resultaten van een query naar [Grafana](grafana-plugin.md) om gebruik te maken van de dashboarding en te combineren met andere gegevensbronnen. |
| Waarschuwing | Configureer een [metrische waarschuwingsregel](alerts-metric.md) die een melding verzendt of [automatische actie](action-groups.md) onderneemt wanneer de metrische waarde een drempelwaarde overschrijdt. |
| Automatiseren |  Gebruik [Autoscale](autoscale-overview.md) om resources te verhogen of te verlagen op basis van een metrische waarde die een drempelwaarde overschrijdt. |
| Exporteren | [Routeer metrische gegevens naar logboeken](resource-logs-collect-storage.md) om gegevens in Azure Monitor Metrics samen met gegevens in Azure Monitor Logs te analyseren en metrische waarden langer dan 93 dagen op te slaan.<br>Metstatistieken streamen naar een [gebeurtenishub](stream-monitoring-data-event-hubs.md) om ze door te sturen naar externe systemen. |
| Ophalen | Metrische waarden openen vanaf een opdrachtregel met [PowerShell-cmdlets](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Metrische waarden openen vanuit aangepaste toepassing met [rest-API](rest-api-walkthrough.md).<br>Metrische waarden openen vanaf een opdrachtregel met [CLI](/cli/azure/monitor/metrics). |
| Archiveren | [Archiveer](..//learn/tutorial-archive-data.md) de prestatie- of statusgeschiedenis van uw resource voor nalevings-, controle- of offlinerapportagedoeleinden. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Hoe worden gegevens in Azure Monitor Metrics gestructureerd?
Gegevens die worden verzameld door Azure Monitor Metrics worden opgeslagen in een database uit de tijdreeksdie is geoptimaliseerd voor het analyseren van tijdstempelgegevens. Elke set metrische waarden is een tijdreeks met de volgende eigenschappen:

* De tijd dat de waarde werd verzameld
* De resource waar de waarde aan is gekoppeld
* Een naamruimte die fungeert als een categorie voor de statistiek
* Een metrische naam
* De waarde zelf
* Sommige statistieken kunnen meerdere dimensies hebben, zoals beschreven in [multidimensionale statistieken](#multi-dimensional-metrics). Aangepaste statistieken kunnen maximaal 10 dimensies hebben.

## <a name="multi-dimensional-metrics"></a>Multidimensionale statistieken
Een van de uitdagingen voor metrische gegevens is dat het vaak beperkte informatie heeft om context te bieden voor verzamelde waarden. Azure Monitor pakt deze uitdaging aan met multidimensionale statistieken. Afmetingen van een statistiek zijn naam-waardeparen die extra gegevens bevatten om de metrische waarde te beschrijven. Een metrische _beschikbare schijfruimte_ kan bijvoorbeeld een dimensie hebben met de naam _Drive_ met waarden _C:_, _D:_, waarmee de beschikbare schijfruimte op alle stations of voor elk station afzonderlijk kan worden bekeken.

In het onderstaande voorbeeld worden twee gegevenssets weergegeven voor een hypothetische statistiek genaamd _Netwerkdoorvoer_. De eerste gegevensset heeft geen afmetingen. De tweede gegevensset toont de waarden met twee dimensies, _IP-adres_ en _richting:_

### <a name="network-throughput"></a>Netwerkdoorvoer

| Tijdstempel     | Metrische waarde |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1.331,8 Kbps |
| 8/9/2017 8:15 | 1,141,4 Kbps |
| 8/9/2017 8:16 | 1.110,2 Kbps |

Deze niet-dimensionale statistiek kan alleen antwoord geven op een fundamentele vraag als "wat was mijn netwerkdoorvoer op een bepaald moment?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Netwerkdoorvoer + twee dimensies ("IP" en "Richting")

| Tijdstempel     | Dimensie "IP"   | Dimensie "Richting" | Metrische waarde|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168,5,2" | Richting="Verzenden"    | 646,5 Kbps |
| 8/9/2017 8:14 | IP="192.168,5,2" | Richting="Ontvangen" | 420,1 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Richting="Verzenden"    | 150,0 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Richting="Ontvangen" | 115,2 Kbps |
| 8/9/2017 8:15 | IP="192.168,5,2" | Richting="Verzenden"    | 515,2 Kbps |
| 8/9/2017 8:15 | IP="192.168,5,2" | Richting="Ontvangen" | 371,1 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Richting="Verzenden"    | 155,0 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Richting="Ontvangen" | 100,1 Kbps |

Deze statistiek kan vragen beantwoorden zoals "wat was de netwerkdoorvoer voor elk IP-adres?", en "hoeveel gegevens zijn verzonden versus ontvangen?" Multidimensionale statistieken dragen extra analytische en diagnostische waarde in vergelijking met niet-dimensionale statistieken.

## <a name="interacting-with-azure-monitor-metrics"></a>Interactie met Azure Monitor Metrics
Gebruik [Metrics Explorer](metrics-charts.md) om de gegevens in uw metrische database interactief te analyseren en de waarden van meerdere statistieken in de loop van de tijd in kaart te brengen. U de grafieken vastmaken aan een dashboard om ze te bekijken met andere visualisaties. U ook statistieken ophalen met behulp van de [REST API voor Azure-bewaking.](rest-api-walkthrough.md)

![Metrics Explorer](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Bronnen van Azure Monitor-statistieken
Er zijn drie fundamentele bronnen van statistieken verzameld door Azure Monitor. Zodra deze statistieken zijn verzameld in de Azure Monitor-metrische database, kunnen ze samen worden geëvalueerd, ongeacht hun bron.

**Platformstatistieken** worden gemaakt door Azure-bronnen en geven u inzicht in hun status en prestaties. Elk type resource maakt een [afzonderlijke set metrische gegevens](metrics-supported.md) zonder dat er een configuratie nodig is. Platformstatistieken worden verzameld uit Azure-resources op frequentie van één minuut, tenzij anders is aangegeven in de definitie van de statistiek. 

**GastOS-statistieken** worden verzameld van het gastbesturingssysteem van een virtuele machine. Gast-OS-statistieken inschakelen voor virtuele Windows-machines met [Windows Diagnostic Extension (WAD)](../platform/diagnostics-extension-overview.md) en voor virtuele Linux-machines met [InfluxData Telegraf Agent](https://www.influxdata.com/time-series-platform/telegraf/).

**Toepassingsstatistieken** worden gemaakt door Application Insights voor uw bewaakte toepassingen en helpen u prestatieproblemen op te sporen en trends bij te houden in de manier waarop uw toepassing wordt gebruikt. Dit omvat waarden als _serverresponstijd_ en _browseruitzonderingen._

**Aangepaste statistieken** zijn statistieken die u definieert naast de standaardstatistieken die automatisch beschikbaar zijn. U [aangepaste statistieken definiëren in uw toepassing](../app/api-custom-events-metrics.md) die wordt gecontroleerd door Application Insights of aangepaste statistieken voor een Azure-service maken met behulp van de aangepaste metrische [api.](metrics-store-custom-rest-api.md)

## <a name="retention-of-metrics"></a>Behoud van statistieken
Voor de meeste bronnen in Azure worden statistieken 93 dagen bewaard. Er zijn enkele uitzonderingen:

**Statistieken gastbesturingssysteem**
-   **Klassieke gast OS metrics**. Dit zijn prestatiemeteritems die zijn verzameld door de [Windows Diagnostic Extension (WAD)](../platform/diagnostics-extension-overview.md) of de [Linux Diagnostic Extension (LAD)](../../virtual-machines/extensions/diagnostics-linux.md) en worden doorgestuurd naar een Azure-opslagaccount. Bewaar voor deze statistieken is 14 dagen.
-   **Gaststatistieken van besturingssysteem die naar Azure Monitor Metrics worden verzonden**. Dit zijn prestatiemeteritems die zijn verzameld door de [Windows Diagnostic Extension (WAD)](diagnostics-extension-overview.md) en naar de [Azure Monitor-gegevenssink](diagnostics-extension-overview.md#data-destinations)of via de [InfluxData Telegraf Agent](https://www.influxdata.com/time-series-platform/telegraf/) op Linux-machines worden verzonden. Bewaar voor deze statistieken is 93 dagen.
-   **GastOS-statistieken verzameld door log Analytics-agent**. Dit zijn prestatiemeteritems die door de log analytics-agent zijn verzameld en naar een werkruimte van Log Analytics zijn verzonden. Bewaar voor deze statistieken is 31 dagen en kan tot 2 jaar worden verlengd.

**Op application Insights log gebaseerde statistieken**. 
- Achter de scène vertalen [op logboeken gebaseerde statistieken](../app/pre-aggregated-metrics-log-metrics.md) zich in logquery's. Hun retentie komt overeen met het behoud van gebeurtenissen in onderliggende logboeken. Voor resources voor Application Insights worden logboeken 90 dagen bewaard.


> [!NOTE]
> U [platformstatistieken voor Azure Monitor-resources naar een Log Analytics-werkruimte verzenden](resource-logs-collect-storage.md) voor trending op lange termijn.





## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [Azure Monitor-gegevensplatform](data-platform.md).
- Meer informatie over [logboekgegevens in Azure Monitor](data-platform-logs.md).
- Meer informatie over de [bewakingsgegevens die beschikbaar zijn](data-sources.md) voor verschillende bronnen in Azure.
