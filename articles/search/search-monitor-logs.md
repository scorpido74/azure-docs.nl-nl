---
title: Loggegevens verzamelen
titleSuffix: Azure Cognitive Search
description: Verzamel en analyseer logboekgegevens door een diagnostische instelling in te schakelen en gebruik vervolgens de Kusto Query Language om de resultaten te verkennen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 86e869bc08552ea11728c508486a4784eccf4042
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462349"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Loggegevens verzamelen en analyseren voor Azure Cognitive Search

Diagnostische of operationele logboeken bieden inzicht in de gedetailleerde bewerkingen van Azure Cognitive Search en zijn handig voor het bewaken van service- en werkbelastingprocessen. Intern bestaan er voor een korte periode logboeken op de backend, voldoende voor onderzoek en analyse als u een ondersteuningsticket indient. Als u echter zelfregie wilt over operationele gegevens, moet u een diagnostische instelling configureren om aan te geven waar logboekinformatie wordt verzameld.

Het instellen van logboeken is handig voor diagnostiek en het behoud van de operationele geschiedenis. Nadat u logboekregistratie hebt ingeschakeld, u query's uitvoeren of rapporten maken voor gestructureerde analyse.

In de volgende tabel worden opties opgesomd voor het verzamelen en voortduren van gegevens.

| Resource | Gebruikt voor |
|----------|----------|
| [Verzenden naar logboekanalysewerkruimte](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Gebeurtenissen en statistieken worden verzonden naar een werkruimte log analytics, die kan worden opgevraagd in de portal om gedetailleerde informatie terug te sturen. Zie [Aan de slag met Azure Monitor-logboeken](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) voor een inleiding |
| [Archiveren met Blob-opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Gebeurtenissen en statistieken worden gearchiveerd naar een Blob-container en opgeslagen in JSON-bestanden. Logs kunnen vrij gedetailleerd zijn (per uur / minuut), handig voor het onderzoeken van een specifiek incident, maar niet voor open-ended onderzoek. Gebruik een JSON-editor om een onbewerkt logboekbestand of Power BI weer te geven om logboekgegevens samen te voegen en te visualiseren.|
| [Streamen naar gebeurtenishub](https://docs.microsoft.com/azure/event-hubs/) | Gebeurtenissen en statistieken worden gestreamd naar een Azure Event Hubs-service. Kies dit als een alternatieve service voor het verzamelen van gegevens voor zeer grote logboeken. |

Zowel Azure Monitor-logboeken als Blob-opslag zijn beschikbaar als een gratis service, zodat u deze gratis uitproberen voor de levensduur van uw Azure-abonnement. Application Insights is gratis aan te melden en te gebruiken zolang de grootte van toepassingsgegevens onder bepaalde limieten valt (zie de [prijspagina](https://azure.microsoft.com/pricing/details/monitor/) voor meer informatie).

## <a name="prerequisites"></a>Vereisten

Als u Log Analytics of Azure Storage gebruikt, u vooraf resources maken.

+ [Een werkruimte voor logboekanalyse maken](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [Een opslagaccount maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>Gegevensverzameling inschakelen

Diagnostische instellingen geven aan hoe geregistreerde gebeurtenissen en statistieken worden verzameld.

1. Selecteer **Diagnostische instellingen** onder **Controle**.

   ![Diagnostische instellingen](./media/search-monitor-usage/diagnostic-settings.png "Diagnostische instellingen")

1. Selecteren **+ Diagnostische instelling toevoegen**

1. Schakel **Logboekanalyse**in, selecteer uw werkruimte en selecteer **OperationLogs** en **AllMetrics**.

   ![Gegevensverzameling configureren](./media/search-monitor-usage/configure-storage.png "Gegevensverzameling configureren")

1. Sla de instelling op.

1. Nadat logboekregistratie is ingeschakeld, gebruikt u uw zoekservice om logboeken en statistieken te genereren. Het zal even duren voordat geregistreerde gebeurtenissen en statistieken beschikbaar komen.

Voor Log Analytics duurt het enkele minuten voordat gegevens beschikbaar zijn, waarna u Kusto-query's uitvoeren om gegevens terug te sturen. Zie [Queryaanvragen controleren](search-monitor-logs.md)voor meer informatie .

Voor Blob-opslag duurt het een uur voordat de containers worden weergegeven in blobopslag. Er is één blob, per uur, per container. Containers worden alleen gemaakt wanneer er een activiteit is om in te loggen of te meten. Wanneer de gegevens naar een opslagaccount worden gekopieerd, worden de gegevens opgemaakt als JSON en in twee containers geplaatst:

+ insights-logs-operationlogs: voor logboeken voor zoekverkeer
+ insights-metrics-pt1m: voor metrics

## <a name="query-log-information"></a>Querylogboekgegevens

In diagnostische logboeken bevatten twee tabellen logboeken en metrische gegevens voor Azure Cognitive Search: **AzureDiagnostics** en **AzureMetrics**.

1. Selecteer **Logboeken** **onder Controle**.

1. Voer **AzureMetrics** in het queryvenster in. Voer deze eenvoudige query uit om kennis te maken met de gegevens die in deze tabel worden verzameld. Schuif over de tabel om statistieken en waarden weer te geven. Let op het aantal recordgegevens bovenaan en als uw service al een tijdje statistieken verzamelt, u het tijdsinterval aanpassen om een beheerbare gegevensset te krijgen.

   ![Tabel AzureMetrics](./media/search-monitor-usage/azuremetrics-table.png "Tabel AzureMetrics")

1. Voer de volgende query in om een tabelvormige resultaatset terug te sturen.

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Herhaal de vorige stappen, te beginnen met **AzureDiagnostics** om alle kolommen terug te sturen voor informatieve doeleinden, gevolgd door een meer selectieve query die interessantere informatie extraheert.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![Tabel AzureDiagnostics](./media/search-monitor-usage/azurediagnostics-table.png "Tabel AzureDiagnostics")

## <a name="log-schema"></a>Logboekschema

Gegevensstructuren die azure cognitive search-logboekgegevens bevatten, voldoen aan het onderstaande schema. 

Voor Blob-opslag heeft elke blob één hoofdobject dat **records** wordt genoemd met een array van logboekobjecten. Elke blob bevat records voor alle bewerkingen die tijdens hetzelfde uur plaatsvonden.

De volgende tabel is een gedeeltelijke lijst met velden die vaak voorkomen bij diagnostische logboekregistratie.

| Name | Type | Voorbeeld | Opmerkingen |
| --- | --- | --- | --- |
| timeGenerated timeGenerated timeGenerated timeGenerated |datum/tijd |"2018-12-07T00:00:43.6872559Z" |Tijdstempel van de bewerking |
| resourceId |tekenreeks |"/ABONNEMENTEN/1111111-1111-1111-1111-1111-11111111111/<br/>RESOURCEGROEPEN/STANDAARD/PROVIDERS/<br/> Microsoft. SEARCH/SEARCHSERVICES/SEARCHSERVICE" |Uw ResourceId |
| operationName |tekenreeks |"Query.Search" |De naam van de bewerking |
| operationVersion |tekenreeks |"2019-05-06" |De gebruikte api-versie |
| category |tekenreeks |"OperationLogs" |Constante |
| resultType |tekenreeks |"Succes" |Mogelijke waarden: Succes of Falen |
| resultSignature |int |200 |HTTP-resultaatcode |
| duurMS |int |50 |Duur van de bewerking in milliseconden |
| properties |object |zie de volgende tabel |Object dat bedrijfsspecifieke gegevens bevat |

### <a name="properties-schema"></a>Eigenschappenschema

De onderstaande eigenschappen zijn specifiek voor Azure Cognitive Search.

| Name | Type | Voorbeeld | Opmerkingen |
| --- | --- | --- | --- |
| Description_s |tekenreeks |"GET /indexen('inhoud')/docs" |Eindpunt van de bewerking |
| Documents_d |int |42 |Aantal verwerkte documenten |
| IndexName_s |tekenreeks |"test-index" |Naam van de index die aan de bewerking is gekoppeld |
| Query_s |tekenreeks |"?search=AzureSearch&$count=true&api-version=2019-05-06" |De queryparameters |

## <a name="metrics-schema"></a>Schema voor metrische gegevens

Statistieken worden vastgelegd voor queryaanvragen en gemeten in intervallen van één minuut. Elke statistiek legt minimum-, maximum- en gemiddelde waarden per minuut bloot. Zie [Queryaanvragen controleren](search-monitor-queries.md)voor meer informatie .

| Name | Type | Voorbeeld | Opmerkingen |
| --- | --- | --- | --- |
| resourceId |tekenreeks |"/ABONNEMENTEN/1111111-1111-1111-1111-1111-11111111111/<br/>RESOURCEGROEPEN/STANDAARD/PROVIDERS/<br/>Microsoft. SEARCH/SEARCHSERVICES/SEARCHSERVICE" |uw resource-id |
| metricName |tekenreeks |"Latentie" |de naam van de statistiek |
| tijd |datum/tijd |"2018-12-07T00:00:43.6872559Z" |tijdstempel van de bewerking |
| gemiddeld |int |64 |De gemiddelde waarde van de ruwe monsters in het metrische tijdsinterval, eenheden in seconden of percentage, afhankelijk van de statistiek. |
| minimum |int |37 |De minimumwaarde van de ruwe monsters in het metrische tijdsinterval, eenheden in seconden. |
| maximum |int |78 |De maximale waarde van de ruwe monsters in het metrische tijdsinterval, eenheden in seconden.  |
| totaal |int |258 |De totale waarde van de ruwe monsters in het metrische tijdsinterval, eenheden in seconden.  |
| count |int |4 |Het aantal metrische gegevens dat binnen een minuut wordt uitgestraald van een knooppunt naar het logboek.  |
| tijdskorrel |tekenreeks |"PT1M" |De tijdskorrel van de statistiek in ISO 8601. |

Het is gebruikelijk dat query's in milliseconden worden uitgevoerd, dus alleen query's die als seconden worden gemeten, worden weergegeven in metrische gegevens zoals QPS.

Voor de statistiek **Zoekopdrachten per seconde** is minimaal de laagste waarde voor zoekopdrachten per seconde die in die minuut zijn geregistreerd. Hetzelfde geldt voor de maximale waarde. Gemiddeld, is het aggregaat over de hele minuut. Binnen een minuut hebt u bijvoorbeeld een patroon als dit: een seconde hoge belasting die het maximum is voor SearchQueriesPerSeconde, gevolgd door 58 seconden gemiddelde belasting en ten slotte één seconde met slechts één query, wat het minimum is.

Voor **percentage zoekopdrachten met throttled**, minimum, maximum, gemiddelde en totaal, hebben allemaal dezelfde waarde: het percentage zoekopdrachten dat is beperkt, van het totale aantal zoekopdrachten gedurende één minuut.

## <a name="view-raw-log-files"></a>Onbewerkte logboekbestanden weergeven

Blob-opslag wordt gebruikt voor het archiveren van logboekbestanden. U elke JSON-editor gebruiken om het logboekbestand te bekijken. Als je er geen hebt, raden we [Visual Studio Code](https://code.visualstudio.com/download)aan.

1. Open uw Opslagaccount in Azure-portal. 

2. Klik in het linkernavigatiedeelvenster op **Blobs**. U ziet **insights-logs-operationlogs** en **insights-metrics-pt1m.** Deze containers worden gemaakt door Azure Cognitive Search wanneer de logboekgegevens worden geëxporteerd naar Blob-opslag.

3. Klik op de maphiërarchie totdat u het JSon-bestand hebt bereikt.  Gebruik het contextmenu om het bestand te downloaden.

Zodra het bestand is gedownload, opent u het in een JSON-editor om de inhoud te bekijken.

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, bekijkt u de basisprincipes van het controleren van de zoekservice om meer te weten te komen over het volledige scala aan toezichtsmogelijkheden.

> [!div class="nextstepaction"]
> [Bewerkingen en activiteiten in Azure Cognitive Search controleren](search-monitor-usage.md)