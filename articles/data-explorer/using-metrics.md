---
title: Prestaties, status en gebruik van Azure Data Explorer controleren met metrische gegevens
description: Meer informatie over het gebruik van Azure Data Explorer metrieken voor het bewaken van de prestaties, de status en het gebruik van het cluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: f5b47a5ae9d13711233d0e4852ec487af7344622
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173793"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Prestaties, status en gebruik van Azure Data Explorer controleren met metrische gegevens

Azure Data Explorer is een snelle, volledig beheerde service voor gegevensanalyses waarmee grote hoeveelheden gegevens van toepassingen, websites, IoT-apparaten en dergelijke in real-time kunnen worden geanalyseerd. Als u Azure Data Explorer wilt gebruiken, maakt u eerst een cluster. Daarna maakt u een of meer databases in het cluster. De volgende stap is het opnemen (laden) van gegevens in een database, zodat u er query's op kunt uitvoeren. Azure Data Explorer metrische gegevens geven belang rijke indica toren voor de status en prestaties van de cluster bronnen. Gebruik de metrische gegevens die in dit artikel worden beschreven om de status en prestaties van Azure Data Explorer cluster in uw specifieke scenario als zelfstandige metrische gegevens te controleren. U kunt metrische gegevens ook gebruiken als basis voor operationele [Azure-Dash boards](/azure/azure-portal/azure-portal-dashboards) en [Azure-waarschuwingen](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/).

* Een [cluster en data base](create-cluster-database-portal.md)maken.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure Portal](https://portal.azure.com/).

## <a name="using-metrics"></a>Metrische gegevens gebruiken

Selecteer in uw Azure Data Explorer-cluster **metrische gegevens** om het deel venster metrieken te openen en de analyse van het cluster te starten.

![Metrische gegevens selecteren](media/using-metrics/select-metrics.png)

In het deel venster metrische gegevens:

![Deel venster metrische gegevens](media/using-metrics/metrics-pane.png)

1. Als u een metrische grafiek wilt maken, selecteert u **metrische** naam en relevante **aggregatie** per metriek, zoals hieronder wordt beschreven. De **resource** -en **metrische naam ruimte** -kiezers zijn vooraf geselecteerd voor uw Azure Data Explorer-cluster.

    **Gegevens** | **Teleenheid** | **Aggregatie** | **Beschrijving van metrische gegevens**
    |---|---|---|---|
    | Cache gebruik | Procent | Avg, Max, Min | Het percentage toegewezen cache resources dat momenteel door het cluster wordt gebruikt. Cache verwijst naar de grootte van SSD toegewezen voor gebruikers activiteit volgens het gedefinieerde cache beleid. Een gemiddeld cache gebruik van 80% of minder is een duurzame status voor een cluster. Als het gemiddelde cache gebruik hoger is dan 80%, moet het cluster worden [geschaald](manage-cluster-vertical-scaling.md) naar een prijs categorie geoptimaliseerd voor opslag of worden [uitgeschaald](manage-cluster-horizontal-scaling.md) naar meer exemplaren. U kunt het cache beleid ook aanpassen (minder dagen in de cache). Als het cache gebruik meer dan 100% is, is de grootte van de gegevens die in de cache moeten worden opgeslagen volgens het cache beleid groter dan de totale grootte van de cache op het cluster. |
    | CPU | Procent | Avg, Max, Min | Het percentage toegewezen reken resources dat momenteel wordt gebruikt door machines in het cluster. Een gemiddeld CPU van 80% of minder is duurzaam voor een cluster. De maximale waarde van CPU is 100%, wat betekent dat er geen extra reken resources zijn om gegevens te verwerken. Wanneer een cluster niet goed presteert, controleert u de maximum waarde van de CPU om te bepalen of er specifieke Cpu's zijn die worden geblokkeerd. |
    | Verwerkte gebeurtenissen (voor Event Hubs) | Count | Max, min, Sum | Totaal aantal gebeurtenissen dat is gelezen vanuit Event hubs en verwerkt door het cluster. De gebeurtenissen worden gesplitst in gebeurtenissen die zijn afgewezen en gebeurtenissen die door de Cluster-Engine worden geaccepteerd. |
    | Opname latentie | Seconden | Avg, Max, Min | Latentie van opgenomen gegevens, vanaf het moment dat de gegevens in het cluster worden ontvangen totdat deze klaar is voor de query. De latentie van de opname vertraging is afhankelijk van het opname scenario. |
    | Opname resultaat | Count | Count | Totaal aantal opname bewerkingen dat is mislukt en geslaagd. Gebruik **splitsen Toep assen** om buckets van geslaagde en mislukte resultaten te maken en de dimensies (**waarde** > **status**) te analyseren.|
    | Opname gebruik | Procent | Avg, Max, Min | Het percentage werkelijke bronnen dat wordt gebruikt om gegevens op te nemen uit het totale aantal toegewezen resources, in het capaciteits beleid, om opname uit te voeren. Het standaard capaciteits beleid is niet meer dan 512 gelijktijdige opname bewerkingen of 75% van de cluster bronnen die zijn belegd in opname. Het gemiddelde opname gebruik van 80% of minder is een duurzame status voor een cluster. De maximale waarde voor opname gebruik is 100%. Dit betekent dat alle mogelijkheden voor het opnemen van het cluster worden gebruikt en dat een opname wachtrij kan resulteren. |
    | Opname volume (in MB) | Count | Max, min, Sum | De totale grootte van de gegevens die zijn opgenomen in het cluster (in MB) vóór compressie. |
    | Actief houden | Count | Gemiddeld | Houdt de reactie tijd bij van het cluster. Een volledig reagerende cluster retourneert waarde 1 en een geblokkeerd of niet-verbonden cluster retourneert 0. |
    | Queryduur | Seconden | Count, Avg, Min, Max, Sum | Totale tijd tot de query resultaten zijn ontvangen (geen netwerk latentie inbegrepen). |
    | | | |

    Aanvullende informatie over [ondersteunde metrische gegevens van Azure Data Explorer cluster](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)

2. Selecteer de knop **metriek toevoegen** om meerdere metrische gegevens weer te geven die in dezelfde grafiek zijn getekend.
3. Selecteer de knop **+ nieuwe grafiek** om meerdere grafieken in één weer gave te zien.
4. Gebruik de tijd kiezer om het tijds bereik te wijzigen (standaard: afgelopen 24 uur).
5. Gebruik [ **filter toevoegen** en **pas splitsen toe** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) voor metrische gegevens met dimensies.
6. Selecteer **vastmaken aan dash board** om de grafiek configuratie toe te voegen aan de Dash boards zodat u het opnieuw kunt bekijken.
7. Stel een **nieuwe waarschuwings regel** in om uw metrische gegevens te visualiseren met de ingestelde criteria. De nieuwe waarschuwings regel bevat uw doel resource, metrische gegevens, opsplitsen en filter dimensies uit uw diagram. Wijzig deze instellingen in het [deel venster waarschuwings regel maken](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Aanvullende informatie over het gebruik van de [Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started).


## <a name="next-steps"></a>Volgende stappen

* [Zelf studie: gegevens opnemen en controleren in azure Data Explorer](/azure/data-explorer/ingest-data-no-code)
* [Azure Data Explorer opname bewerkingen bewaken met Diagnostische logboeken](/azure/data-explorer/using-diagnostic-logs)
* [Snelstart: query’s uitvoeren op gegevens in Azure Data Explorer](web-query-data.md)
