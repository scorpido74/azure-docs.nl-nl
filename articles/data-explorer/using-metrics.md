---
title: De prestaties van Azure Data Explorer, status & gebruik met statistieken bewaken
description: Meer informatie over het gebruik van Azure Data Explorer-statistieken om de prestaties, status en het gebruik van het cluster te controleren.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 1319b8cd6ac8a0eb83381c24bcde9996458e47a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560301"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Prestaties, status en gebruik van Azure Data Explorer bewaken met statistieken

Azure Data Explorer is een snelle, volledig beheerde service voor gegevensanalyses waarmee grote hoeveelheden gegevens van toepassingen, websites, IoT-apparaten en dergelijke in real-time kunnen worden geanalyseerd. Als u Azure Data Explorer wilt gebruiken, maakt u eerst een cluster. Daarna maakt u een of meer databases in het cluster. De volgende stap is het opnemen (laden) van gegevens in een database, zodat u er query's op kunt uitvoeren. Azure Data Explorer-statistieken bieden belangrijke indicatoren met betrekking tot de status en prestaties van de clusterbronnen. Gebruik de statistieken die in dit artikel worden beschreven om de clusterstatus en prestaties van Azure Data Explorer in uw specifieke scenario te controleren als zelfstandige statistieken. U ook statistieken gebruiken als basis voor operationele [Azure-dashboards](/azure/azure-portal/azure-portal-dashboards) en [Azure-waarschuwingen.](/azure/azure-monitor/platform/alerts-metric-overview)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u er geen hebt, u een [gratis Azure-account](https://azure.microsoft.com/free/)maken.
* Een [cluster en database](create-cluster-database-portal.md).

## <a name="using-metrics"></a>Statistieken gebruiken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Selecteer in uw Azure Data Explorer-cluster **de optie Metriek** om het weergavevenster te openen en te beginnen met de analyse van uw cluster.
    ![Selecteer](media/using-metrics/select-metrics.png)Statistieken .
1. In het deelvenster ![Statistieken: deelvenster Statistieken](media/using-metrics/metrics-pane.png)
    1. Als u een metrische grafiek wilt maken, selecteert u **Metrische** naam en relevante **aggregatie** per statistiek. De pickers **Resource** en **Metric Namespace** zijn vooraf geselecteerd voor uw Azure Data Explorer-cluster. Zie ondersteunde Azure Data [Explorer-statistieken](#supported-azure-data-explorer-metrics)voor meer informatie over verschillende statistieken.
    1. Selecteer **Statistiek toevoegen** om meerdere metrische gegevens te zien die in dezelfde grafiek zijn uitgezet.
    1. Selecteer **+ Nieuwe grafiek** om meerdere grafieken in één weergave weer te geven.
    1. Gebruik de tijdkiezer om het tijdsbereik te wijzigen (standaard: afgelopen 24 uur).
    1. Gebruik [ **Filter toevoegen** en **Splitsen toepassen** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) voor metrische gegevens met afmetingen.
    1. Selecteer **Vastmaken aan het dashboard** om uw grafiekconfiguratie toe te voegen aan de dashboards, zodat u deze opnieuw bekijken.
    1. Stel **een nieuwe waarschuwingsregel** in om uw statistieken te visualiseren aan de hand van de ingestelde criteria. De nieuwe waarschuwingsregel bevat uw doelbron, metrische, splitsings- en filterdimensies uit uw grafiek. Wijzig deze instellingen in het [deelvenster Voor het maken van waarschuwingsregels](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Aanvullende informatie over het gebruik van de [Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started).

## <a name="supported-azure-data-explorer-metrics"></a>Ondersteunde Azure Data Explorer-statistieken

De ondersteunde Azure Data Explorer-statistieken zijn onderverdeeld in verschillende categorieën op basis van het gebruik. 

### <a name="cluster-health-metrics"></a>Statistieken voor clusterstatus

De statistieken van de clusterstatus houden de algemene status van het cluster bij. Dit omvat het gebruik en de responsiviteit van resources en inname.

**Gegevens** | **Eenheid** | **Aggregatie** | **Metrische beschrijving** | **Dimensies** |
|---|---|---|---|---|
| Cachegebruik | Percentage | Avg, Max, Min | Percentage toegewezen cacheresources dat momenteel door het cluster wordt gebruikt. Cache is de grootte van SSD die is toegewezen voor gebruikersactiviteit volgens het gedefinieerde cachebeleid. Een gemiddeld cachegebruik van 80% of minder is een duurzame status voor een cluster. Als het gemiddelde cachegebruik hoger is dan 80%, moet het cluster worden [opgeschaald](manage-cluster-vertical-scaling.md) naar een opslaggeoptimaliseerde prijscategorie of [worden geschaald](manage-cluster-horizontal-scaling.md) naar meer exemplaren. U ook het cachebeleid aanpassen (minder dagen in de cache). Als het cachegebruik meer dan 100% bedraagt, is de grootte van gegevens die in de cache moeten worden opgeslagen, volgens het cachebeleid groter dan de totale grootte van de cache op het cluster. | Geen |
| CPU | Percentage | Avg, Max, Min | Percentage toegewezen rekenresources dat momenteel wordt gebruikt door machines in het cluster. Een gemiddelde CPU van 80% of minder is duurzaam voor een cluster. De maximale waarde van CPU is 100%, wat betekent dat er geen extra rekenbronnen zijn om gegevens te verwerken. Wanneer een cluster niet goed presteert, controleert u de maximale waarde van de CPU om te bepalen of er specifieke CPU's zijn die zijn geblokkeerd. | Geen |
| Innamegebruik | Percentage | Avg, Max, Min | Percentage van de werkelijke resources die worden gebruikt om gegevens in te nemen uit de totale resources die in het capaciteitsbeleid zijn toegewezen om opname uit te voeren. Het standaardcapaciteitsbeleid is niet meer dan 512 gelijktijdige opnamebewerkingen of 75% van de clusterresources die zijn geïnvesteerd in inname. Een gemiddelde opnamebenutting van 80% of minder is een duurzame status voor een cluster. Maximale waarde van opnamegebruik is 100%, wat betekent dat alle clusteropnamecapaciteit wordt gebruikt en er een innamewachtrij kan ontstaan. | Geen |
| In leven blijven | Count | Gem. | Houdt de responsiviteit van het cluster bij. Een volledig responsief cluster retourneert waarde 1 en een geblokkeerd of losgekoppeld cluster retourneert 0. |
| Totaal aantal opdrachten met een beperkt bedrag | Count | Avg, Max, Min, Som | Het aantal aangeslagen (afgewezen) opdrachten in het cluster, omdat het maximaal toegestane aantal gelijktijdige (parallelle) opdrachten is bereikt. | Geen |
| Totaal aantal inomvang | Count | Avg, Max, Min, Som | Totaal aantal gegevensomvang in het cluster. Wijzigingen in deze statistiek kunnen enorme wijzigingen in de gegevensstructuur en hoge belasting op het cluster impliceren, omdat het samenvoegen van gegevensomvang een cpu-zware activiteit is. | Geen |
| | | | |

### <a name="export-health-and-performance-metrics"></a>Gezondheids- en prestatiestatistieken exporteren

Exportstatus- en prestatiestatistieken houden de algemene status en prestaties van exportbewerkingen bij, zoals vertraging, resultaten, aantal records en gebruik.

**Gegevens** | **Eenheid** | **Aggregatie** | **Metrische beschrijving** | **Dimensies** |
|---|---|---|---|---|
Continu exportaantal geëxporteerde records    | Count | Sum | Het aantal geëxporteerde records in alle continue exporttaken. | Geen |
Continue export max lateness |    Count   | Max.   | De te late (in minuten) gerapporteerd door de continue export taken in het cluster. | Geen |
Aantal continue export in behandeling | Count | Max.   | Het aantal lopende continue exporttaken. Deze taken zijn klaar om uit te voeren, maar wachten in een wachtrij, mogelijk als gevolg van onvoldoende capaciteit). 
Continu exportresultaat    | Count |   Count   | Het resultaat van mislukking/succes van elke continue exportrun. | ContinuExportName |
Exportgebruik |    Percentage | Max.   | Gebruikte exportcapaciteit, van de totale exportcapaciteit in het cluster (tussen 0 en 100). | Geen |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>Inname-status- en prestatiestatistieken

Innamestatus- en prestatiestatistieken houden de algemene status en prestaties bij van innamebewerkingen zoals latentie, resultaten en volume.

**Gegevens** | **Eenheid** | **Aggregatie** | **Metrische beschrijving** | **Dimensies** |
|---|---|---|---|---|
| Gebeurtenissen verwerkt (voor gebeurtenis/IoT-hubs) | Count | Max, Min, Som | Totaal aantal gebeurtenissen dat is gelezen uit gebeurtenishubs en door het cluster wordt verwerkt. De gebeurtenissen worden opgesplitst in gebeurtenissen die zijn afgewezen en gebeurtenissen die worden geaccepteerd door de clusterengine. | EventStatus |
| Innamelatentie | Seconden | Avg, Max, Min | Latentie van gegevens die zijn ingenomen, vanaf het moment dat de gegevens in het cluster zijn ontvangen totdat deze klaar zijn voor query's. De innamelatentieperiode is afhankelijk van het opnamescenario. | Geen |
| Innameresultaat | Count | Count | Totaal aantal innamebewerkingen dat is mislukt en is geslaagd. Gebruik **splitsen toepassen** om slagbakken te maken en resultaten te mislukken en de afmetingen **(waardestatus)** > **Status**te analyseren.| IngestionResultDetails |
| Innamevolume (in MB) | Count | Max. | De totale grootte van de gegevens die vóór compressie aan het cluster (in MB) zijn ingenomen. | Database |
| | | | |  

### <a name="query-performance"></a>Queryprestaties

Queryprestatiestatistieken houden de duur van de query en het totale aantal gelijktijdige of gewurgde query's bij.

**Gegevens** | **Eenheid** | **Aggregatie** | **Metrische beschrijving** | **Dimensies** |
|---|---|---|---|---|
| Queryduur | Milliseconden | Avg, Min, Max, Som | Totale tijd totdat queryresultaten zijn ontvangen (bevat geen netwerklatentie). | Querystatus |
| Totaal aantal gelijktijdige query's | Count | Avg, Max, Min, Som | Het aantal query's dat parallel in het cluster wordt uitgevoerd. Deze statistiek is een goede manier om de belasting op het cluster te schatten. | Geen |
| Totaal aantal aanzet tot beperkingen | Count | Avg, Max, Min, Som | Het aantal geweigerde (afgewezen) query's in het cluster. Het maximum aantal gelijktijdige (parallelle) query's dat is toegestaan, wordt gedefinieerd in het beleid voor gelijktijdige query's. | Geen |
| | | | |

### <a name="streaming-ingest-metrics"></a>Streaming ingest metrics

Streaming-statistieken houden streaminggegevens en de aanvraagsnelheid, duur en resultaten bij.

**Gegevens** | **Eenheid** | **Aggregatie** | **Metrische beschrijving** | **Dimensies** |
|---|---|---|---|---|
Gegevenssnelheid streamen |    Count   | RateRequestsPerSeconde | Totaal volume van de gegevens ingenomen naar het cluster. | Geen |
Streaming Ingest Duur   | Milliseconden  | Avg, Max, Min | Totale duur van alle streaming opnameverzoeken. | Geen |
Percentage streamingaanvragen   | Count | Telling, Avg, Max, Min, Som | Totaal aantal streaming aanvragen. | Geen |
Streaming Ingest Resultaat | Count | Gem.   | Totaal aantal streamingaanvragen per resultaattype. | Resultaat |
| | | | |

Aanvullende informatie over [ondersteunde Azure Data Explorer-clusterstatistieken](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters).


## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Gegevens over het innemen en querybewaking in Azure Data Explorer](/azure/data-explorer/ingest-data-no-code)
* [Innamebewerkingen van Azure Data Explorer controleren met diagnostische logboeken](/azure/data-explorer/using-diagnostic-logs)
* [Quickstart: Gegevens doorzoeken in Azure Data Explorer](web-query-data.md)
