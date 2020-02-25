---
title: Prestaties van Azure Data Explorer, status & gebruik met metrische gegevens controleren
description: Meer informatie over het gebruik van Azure Data Explorer metrieken voor het bewaken van de prestaties, de status en het gebruik van het cluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 1319b8cd6ac8a0eb83381c24bcde9996458e47a7
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560301"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Prestaties, status en gebruik van Azure Data Explorer controleren met metrische gegevens

Azure Data Explorer is een snelle, volledig beheerde service voor gegevensanalyses waarmee grote hoeveelheden gegevens van toepassingen, websites, IoT-apparaten en dergelijke in real-time kunnen worden geanalyseerd. Als u Azure Data Explorer wilt gebruiken, maakt u eerst een cluster. Daarna maakt u een of meer databases in het cluster. De volgende stap is het opnemen (laden) van gegevens in een database, zodat u er query's op kunt uitvoeren. Azure Data Explorer metrische gegevens geven belang rijke indica toren voor de status en prestaties van de cluster bronnen. Gebruik de metrische gegevens die in dit artikel worden beschreven om de status en prestaties van Azure Data Explorer cluster in uw specifieke scenario als zelfstandige metrische gegevens te controleren. U kunt metrische gegevens ook gebruiken als basis voor operationele [Azure-Dash boards](/azure/azure-portal/azure-portal-dashboards) en [Azure-waarschuwingen](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u er nog geen hebt, kunt u een [gratis Azure-account](https://azure.microsoft.com/free/)maken.
* Een [cluster en data base](create-cluster-database-portal.md).

## <a name="using-metrics"></a>Metrische gegevens gebruiken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Selecteer in uw Azure Data Explorer-cluster **metrische gegevens** om het deel venster metrieken te openen en de analyse van het cluster te starten.
    ![metrische gegevens](media/using-metrics/select-metrics.png)selecteren.
1. In het deel venster metrieken: ![deel venster metrieken](media/using-metrics/metrics-pane.png)
    1. Als u een metrische grafiek wilt maken, selecteert u **metrische** naam en relevante **aggregatie** per metriek. De **resource** -en **metrische naam ruimte** -kiezers zijn vooraf geselecteerd voor uw Azure Data Explorer-cluster. Zie [ondersteunde statistieken voor Azure Data Explorer](#supported-azure-data-explorer-metrics)voor meer informatie over de verschillende metrische gegevens.
    1. Selecteer **metrische gegevens toevoegen** om meerdere metrische gegevens weer te geven die in dezelfde grafiek zijn uitgezet.
    1. Selecteer **+ nieuwe grafiek** om meerdere grafieken in één weer gave te zien.
    1. Gebruik de tijd kiezer om het tijds bereik te wijzigen (standaard: afgelopen 24 uur).
    1. Gebruik [ **filter toevoegen** en **pas splitsen toe** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) voor metrische gegevens met dimensies.
    1. Selecteer **vastmaken aan dash board** om de grafiek configuratie toe te voegen aan de Dash boards zodat u het opnieuw kunt bekijken.
    1. Stel een **nieuwe waarschuwings regel** in om uw metrische gegevens te visualiseren met de ingestelde criteria. De nieuwe waarschuwings regel bevat uw doel resource, metrische gegevens, opsplitsen en filter dimensies uit uw diagram. Wijzig deze instellingen in het [deel venster waarschuwings regel maken](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Aanvullende informatie over het gebruik van de [Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started).

## <a name="supported-azure-data-explorer-metrics"></a>Ondersteunde metrische gegevens voor Azure Data Explorer

De ondersteunde gegevens van Azure Data Explorer worden in verschillende categorieën onderverdeeld op basis van het gebruik. 

### <a name="cluster-health-metrics"></a>Metrische gegevens van cluster status

De metrische gegevens van de cluster status volgen de algemene status van het cluster. Dit omvat het gebruik en de reactie snelheid van resources en opname.

**Gegevens** | **Teleenheid** | **Aggregatie** | **Beschrijving van metrische gegevens** | **Hoogte** |
|---|---|---|---|---|
| Cache gebruik | Procent | Avg, Max, Min | Het percentage toegewezen cache resources dat momenteel door het cluster wordt gebruikt. Cache is de grootte van SSD die is toegewezen voor gebruikers activiteit volgens het gedefinieerde cache beleid. Een gemiddeld cache gebruik van 80% of minder is een duurzame status voor een cluster. Als het gemiddelde cache gebruik hoger is dan 80%, moet het cluster worden [geschaald](manage-cluster-vertical-scaling.md) naar een prijs categorie geoptimaliseerd voor opslag of worden [uitgeschaald](manage-cluster-horizontal-scaling.md) naar meer exemplaren. U kunt het cache beleid ook aanpassen (minder dagen in de cache). Als het cache gebruik meer dan 100% is, is de grootte van de gegevens die in de cache moeten worden opgeslagen volgens het cache beleid groter dan de totale grootte van de cache op het cluster. | None |
| CPU | Procent | Avg, Max, Min | Het percentage toegewezen reken resources dat momenteel wordt gebruikt door machines in het cluster. Een gemiddeld CPU van 80% of minder is duurzaam voor een cluster. De maximale waarde van CPU is 100%, wat betekent dat er geen extra reken resources zijn om gegevens te verwerken. Wanneer een cluster niet goed presteert, controleert u de maximum waarde van de CPU om te bepalen of er specifieke Cpu's zijn die worden geblokkeerd. | None |
| Opname gebruik | Procent | Avg, Max, Min | Het percentage werkelijke bronnen dat wordt gebruikt om gegevens op te nemen uit het totale aantal toegewezen resources, in het capaciteits beleid, om opname uit te voeren. Het standaard capaciteits beleid is niet meer dan 512 gelijktijdige opname bewerkingen of 75% van de cluster bronnen die zijn belegd in opname. Het gemiddelde opname gebruik van 80% of minder is een duurzame status voor een cluster. De maximale waarde voor opname gebruik is 100%. Dit betekent dat alle mogelijkheden voor het opnemen van het cluster worden gebruikt en dat een opname wachtrij kan resulteren. | None |
| Actief houden | Count | Gemiddeld | Houdt de reactie tijd bij van het cluster. Een volledig reagerende cluster retourneert waarde 1 en een geblokkeerd of niet-verbonden cluster retourneert 0. |
| Totaal aantal vertraagde opdrachten | Count | Gem, Max, min, som | Het aantal vertraagde (geweigerde) opdrachten in het cluster, omdat het Maxi maal toegestane aantal gelijktijdige (parallelle) opdrachten is bereikt. | None |
| Totaal aantal gebieden | Count | Gem, Max, min, som | Het totale aantal gegevens gebieden in het cluster. Wijzigingen in deze metriek kunnen aanzienlijke wijzigingen in de gegevens structuur en hoge belasting van het cluster impliceren, omdat het samen voegen van gegevens gebieden een CPU-zware activiteit is. | None |
| | | | |

### <a name="export-health-and-performance-metrics"></a>Metrische gegevens over de status en prestaties van de export

Met metrische gegevens over de status en prestaties van exporteren worden de algemene status en prestaties bijgehouden van export bewerkingen, zoals de achterstand, de resultaten, het aantal records en het gebruik.

**Gegevens** | **Teleenheid** | **Aggregatie** | **Beschrijving van metrische gegevens** | **Hoogte** |
|---|---|---|---|---|
Doorlopend export aantal geëxporteerde records    | Count | Sum | Het aantal geëxporteerde records in alle doorlopende export taken. | None |
Maximale achterstand voor continue export |    Count   | Max.   | De achterstand (in minuten) die wordt gerapporteerd door de doorlopende export taken in het cluster. | None |
Aantal doorlopend exporteren in behandeling | Count | Max.   | Het aantal in behandeling zijnde doorlopende export taken. Deze taken zijn klaar om te worden uitgevoerd, maar in een wachtrij te wachten, mogelijk vanwege onvoldoende capaciteit. 
Resultaat doorlopend exporteren    | Count |   Count   | Het resultaat van het mislukken/slagen van elke continue export uitvoering. | ContinuousExportName |
Gebruik exporteren |    Procent | Max.   | Gebruikte export capaciteit, van de totale export capaciteit in het cluster (tussen 0 en 100). | None |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>Meet gegevens over de status en prestaties van opname

Met metrische gegevens over de status van opname en prestaties worden de algemene status en prestaties van opname bewerkingen, zoals latentie, resultaten en volume, bijgehouden.

**Gegevens** | **Teleenheid** | **Aggregatie** | **Beschrijving van metrische gegevens** | **Hoogte** |
|---|---|---|---|---|
| Verwerkte gebeurtenissen (voor gebeurtenis/IoT-hubs) | Count | Max, min, Sum | Totaal aantal gebeurtenissen dat is gelezen vanuit Event hubs en verwerkt door het cluster. De gebeurtenissen worden gesplitst in gebeurtenissen die zijn afgewezen en gebeurtenissen die door de Cluster-Engine worden geaccepteerd. | EventStatus |
| Opname latentie | Waarna | Avg, Max, Min | Latentie van opgenomen gegevens, vanaf het moment dat de gegevens in het cluster worden ontvangen totdat deze klaar is voor de query. De latentie van de opname vertraging is afhankelijk van het opname scenario. | None |
| Opname resultaat | Count | Count | Totaal aantal opname bewerkingen dat is mislukt en geslaagd. Gebruik **splitsen Toep assen** om buckets van geslaagde en mislukte resultaten te maken en de dimensies (**waarde** > **status**) te analyseren.| IngestionResultDetails |
| Opname volume (in MB) | Count | Max, Sum | De totale grootte van de gegevens die zijn opgenomen in het cluster (in MB) vóór compressie. | Database |
| | | | |  

### <a name="query-performance"></a>Queryprestaties

Met metrische gegevens van query prestaties worden de query duur en het totale aantal gelijktijdige of vertraagde query's bijgehouden.

**Gegevens** | **Teleenheid** | **Aggregatie** | **Beschrijving van metrische gegevens** | **Hoogte** |
|---|---|---|---|---|
| Queryduur | Milliseconden | Gem, min, maximum, Sum | Totale tijd tot de query resultaten zijn ontvangen (geen netwerk latentie inbegrepen). | QueryStatus |
| Totaal aantal gelijktijdige query's | Count | Gem, Max, min, som | Het aantal query's dat parallel in het cluster wordt uitgevoerd. Deze metrische gegevens zijn een goede manier om de belasting van het cluster te schatten. | None |
| Totaal aantal vertraagde query's | Count | Gem, Max, min, som | Het aantal vertraagde (geweigerde) query's in het cluster. Het maximum aantal gelijktijdige (parallelle) query's dat is toegestaan, wordt gedefinieerd in het beleid voor gelijktijdige query's. | None |
| | | | |

### <a name="streaming-ingest-metrics"></a>Metrische gegevens van stroom opname

Met metrische gegevens over het streamen wordt tracering van streaminggegevens bijgehouden, en de frequentie, duur en resultaten van de aanvraag.

**Gegevens** | **Teleenheid** | **Aggregatie** | **Beschrijving van metrische gegevens** | **Hoogte** |
|---|---|---|---|---|
Gegevens frequentie van streaming opname |    Count   | RateRequestsPerSecond | Het totale volume van de gegevens die zijn opgenomen in het cluster. | None |
Opname duur van streaming   | Milliseconden  | Avg, Max, Min | Totale duur van alle aanvragen voor streaming-opname. | None |
Aanvraag frequentie voor streaming-opname   | Count | Aantal, Gem, Max, min, som | Totaal aantal aanvragen voor streaming-opname. | None |
Resultaat van streaming-opname | Count | Gemiddeld   | Totaal aantal streaming-opname aanvragen per resultaat type. | Resultaat |
| | | | |

Meer informatie over [ondersteunde metrische gegevens voor Azure Data Explorer cluster](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters).


## <a name="next-steps"></a>Volgende stappen

* [Zelf studie: gegevens opnemen en controleren in azure Data Explorer](/azure/data-explorer/ingest-data-no-code)
* [Azure Data Explorer opname bewerkingen bewaken met Diagnostische logboeken](/azure/data-explorer/using-diagnostic-logs)
* [Snelstart: query’s uitvoeren op gegevens in Azure Data Explorer](web-query-data.md)
