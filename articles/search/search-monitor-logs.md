---
title: Logboek gegevens verzamelen
titleSuffix: Azure Cognitive Search
description: Verzamelen en analyseren van logboek gegevens door een diagnostische instelling in te scha kelen en vervolgens de Kusto-query taal te gebruiken om de resultaten te verkennen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 192591dedb0b5519fdcecde8c8683be87237c828
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82127818"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Logboek gegevens voor Azure Cognitive Search verzamelen en analyseren

Diagnostische of operationele logboeken bieden inzicht in de gedetailleerde bewerkingen van Azure Cognitive Search en zijn nuttig voor het bewaken van service-en workload-processen. Intern bestaan er voor een korte periode een logboek op de back-end, voldoende voor onderzoek en analyse als u een ondersteunings ticket hebt. Als u echter zelf richting wilt hebben over operationele gegevens, moet u een diagnostische instelling configureren om op te geven waar logboek gegevens worden verzameld.

Het instellen van Logboeken is handig voor diagnostische gegevens en het behouden van de operationele geschiedenis. Nadat u logboek registratie hebt ingeschakeld, kunt u query's uitvoeren of rapporten maken voor gestructureerde analyse.

In de volgende tabel worden de opties voor het verzamelen en persistent maken van gegevens opgesomd.

| Resource | Gebruikt voor |
|----------|----------|
| [Verzenden naar Log Analytics werk ruimte](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Gebeurtenissen en metrische gegevens worden verzonden naar een Log Analytics-werk ruimte, die in de portal kan worden opgevraagd om gedetailleerde informatie te retour neren. Zie [aan de slag met Azure monitor-logboeken](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) voor een inleiding |
| [Archiveren met Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Gebeurtenissen en metrische gegevens worden gearchiveerd naar een BLOB-container en opgeslagen in JSON-bestanden. Logboeken kunnen behoorlijk nauw keurig zijn (per uur per minuut), wat nuttig is voor het opnieuw doorzoeken van een specifiek incident, maar niet voor open-end onderzoek. Gebruik een JSON-editor om een onbewerkte logboek bestand te bekijken of Power BI om logboek gegevens samen te voegen en te visualiseren.|
| [Streamen naar Event hub](https://docs.microsoft.com/azure/event-hubs/) | Gebeurtenissen en metrische gegevens worden gestreamd naar een Azure Event Hubs-service. Kies deze optie als een alternatieve service voor het verzamelen van gegevens voor zeer grote logboeken. |

Zowel de Azure Monitor-Logboeken als de Blob-opslag zijn beschikbaar als gratis service, zodat u ze gratis kunt uitproberen voor de levens duur van uw Azure-abonnement. Application Insights is gratis aan te melden en te gebruiken zolang de grootte van de toepassings gegevens onder bepaalde limieten ligt (Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/monitor/) voor meer informatie).

## <a name="prerequisites"></a>Vereisten

Als u Log Analytics of Azure Storage gebruikt, kunt u vooraf resources maken.

+ [Een log Analytics-werk ruimte maken](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [Create a storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>Gegevensverzameling inschakelen

Diagnostische instellingen bepalen hoe vastgelegde gebeurtenissen en metrische gegevens worden verzameld.

1. Selecteer **Diagnostische instellingen** onder **Controle**.

   ![Diagnostische instellingen](./media/search-monitor-usage/diagnostic-settings.png "Diagnostische instellingen")

1. Selecteer **+ Diagnostische instelling toevoegen**

1. Controleer **log Analytics**, selecteer uw werk ruimte en selecteer **OperationLogs** en **AllMetrics**.

   ![Gegevens verzameling configureren](./media/search-monitor-usage/configure-storage.png "Gegevens verzameling configureren")

1. Sla de instelling op.

1. Nadat logboek registratie is ingeschakeld, gebruikt u uw zoek service om logboeken en metrische gegevens te genereren. Het duurt enige tijd voordat geregistreerde gebeurtenissen en metrische gegevens beschikbaar komen.

Voor Log Analytics kan het enkele minuten duren voordat er gegevens beschikbaar zijn, waarna u Kusto-query's kunt uitvoeren om gegevens te retour neren. Zie [query aanvragen bewaken](search-monitor-logs.md)voor meer informatie.

Voor Blob-opslag duurt het één uur voordat de containers worden weer gegeven in de Blob-opslag. Er is één blob, per uur, per container. Containers worden alleen gemaakt als er een activiteit is die moet worden geregistreerd of gemeten. Wanneer de gegevens naar een opslag account worden gekopieerd, worden de gegevens ingedeeld als JSON en in twee containers geplaatst:

+ Insights-logboeken-operationlogs: voor logboeken met Zoek opdrachten
+ inzichten-metrische gegevens-pt1m: voor metrische gegevens

## <a name="query-log-information"></a>Informatie over het query logboek

Twee tabellen bevatten logboeken en metrische gegevens voor Azure Cognitive Search: **AzureDiagnostics** en **AzureMetrics**.

1. Onder **bewaking**selecteert u **Logboeken**.

1. Voer **AzureMetrics** in het query venster in. Voer deze eenvoudige query uit om meer te weten te komen over de gegevens die in deze tabel worden verzameld. Schuif over de tabel om de metrische gegevens en waarden weer te geven. U ziet het aantal records bovenaan en als uw service metrische gegevens heeft verzameld, kunt u het tijds interval aanpassen om een beheer bare gegevensset te verkrijgen.

   ![AzureMetrics-tabel](./media/search-monitor-usage/azuremetrics-table.png "AzureMetrics-tabel")

1. Voer de volgende query in om een resultatenset in tabel vorm te retour neren.

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Herhaal de vorige stappen, beginnend met **AzureDiagnostics** om alle kolommen voor informatieve doel einden te retour neren, gevolgd door een meer selectieve query waarmee interessante informatie wordt geëxtraheerd.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![AzureDiagnostics-tabel](./media/search-monitor-usage/azurediagnostics-table.png "AzureDiagnostics-tabel")

## <a name="log-schema"></a>Logboek schema

Gegevens structuren die Azure Cognitive Search-logboek gegevens bevatten, voldoen aan het onderstaande schema. 

Voor Blob Storage bevat elke BLOB één hoofd object met de naam **records** die een matrix met logboek objecten bevatten. Elke BLOB bevat records voor alle bewerkingen die plaatsvonden tijdens hetzelfde uur.

De volgende tabel bevat een gedeeltelijke lijst met velden die worden gebruikt voor het registreren van bronnen.

| Naam | Type | Voorbeeld | Opmerkingen |
| --- | --- | --- | --- |
| timeGenerated |datum/tijd |"2018-12-07T00:00:43.6872559 Z" |Tijds tempel van de bewerking |
| resourceId |tekenreeks |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/STANDAARD/PROVIDERS/<br/> Micro soft. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |Uw ResourceId |
| operationName |tekenreeks |Query. Search |De naam van de bewerking |
| operationVersion |tekenreeks |"2019-05-06" |De API-versie die wordt gebruikt |
| category |tekenreeks |"OperationLogs" |bedrag |
| resultType |tekenreeks |Geleverd |Mogelijke waarden: geslaagd of mislukt |
| resultSignature |int |200 |HTTP-resultaat code |
| durationMS |int |50 |De duur van de bewerking in milliseconden |
| properties |object |Raadpleeg de volgende tabel |Object met toepassingsspecifieke gegevens |

### <a name="properties-schema"></a>Eigenschappen schema

De onderstaande eigenschappen zijn specifiek voor Azure Cognitive Search.

| Naam | Type | Voorbeeld | Opmerkingen |
| --- | --- | --- | --- |
| Description_s |tekenreeks |' GET/Indexes (' content ')/docs ' |Het eind punt van de bewerking |
| Documents_d |int |42 |Aantal verwerkte documenten |
| IndexName_s |tekenreeks |"test-index" |De naam van de index die is gekoppeld aan de bewerking |
| Query_s |tekenreeks |"? Search = AzureSearch&$count = True&API-Version = 2019-05-06" |De query parameters |

## <a name="metrics-schema"></a>Schema voor metrische gegevens

Metrische gegevens worden vastgelegd voor query aanvragen en gemeten in een interval van één minuut. Bij elke metriek worden minimum-, maximum-en gemiddelde waarden per minuut weer gegeven. Zie [query aanvragen bewaken](search-monitor-queries.md)voor meer informatie.

| Naam | Type | Voorbeeld | Opmerkingen |
| --- | --- | --- | --- |
| resourceId |tekenreeks |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/STANDAARD/PROVIDERS/<br/>Micro soft. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |de resource-ID |
| metricName |tekenreeks |Periode |de naam van de metriek |
| tijd |datum/tijd |"2018-12-07T00:00:43.6872559 Z" |de tijds tempel van de bewerking |
| gemiddeld |int |64 |De gemiddelde waarde van de onbewerkte voor beelden in het tijds interval voor metrische gegevens, eenheden in seconden of percentage, afhankelijk van de metriek. |
| minimum |int |37 |De minimale waarde van de onbewerkte voor beelden in het tijds interval voor metrische gegevens, eenheden in seconden. |
| maximum |int |78 |De maximum waarde van de onbewerkte voor beelden in de metrische tijds interval, eenheden in seconden.  |
| totaal |int |258 |De totale waarde van de onbewerkte voor beelden in het tijds interval voor metrische gegevens, eenheden in seconden.  |
| count |int |4 |Het aantal metrische gegevens dat vanaf een knoop punt wordt verzonden naar het logboek binnen het interval van één minuut.  |
| timegrain |tekenreeks |"PT1M" |De tijd korrel van de metriek in ISO 8601. |

Het is gebruikelijk om query's uit te voeren in milliseconden, zodat alleen query's die als seconden worden gemeten, worden weer gegeven in metriek zoals QPS.

Voor de metrische **Zoek Query's per seconde** is minimum de laagste waarde voor zoek query's per seconde die tijdens die minuut zijn geregistreerd. Hetzelfde geldt voor de maximum waarde. Gemiddelde, is de aggregatie over de hele minuut. U kunt bijvoorbeeld binnen één minuut een patroon hebben dat er ongeveer als volgt uitziet: één seconde van een hoge belasting die het maximum voor SearchQueriesPerSecond is, gevolgd door 58 seconden gemiddelde belasting, en ten slotte één seconde met slechts één query, wat het minimum is.

Voor het **beperkende Zoek Query's percentage**, minimum, maximum, gemiddelde en totaal heeft dezelfde waarde: het percentage Zoek query's dat is beperkt tot het totale aantal Zoek query's gedurende één minuut.

## <a name="view-raw-log-files"></a>Onbewerkte logboek bestanden weer geven

Blob-opslag wordt gebruikt voor het archiveren van logboek bestanden. U kunt een JSON-editor gebruiken om het logboek bestand weer te geven. Als u er geen hebt, raden we u aan [Visual Studio code](https://code.visualstudio.com/download)aan te bevelen.

1. Open uw opslag account in Azure Portal. 

2. Klik in het navigatie deel venster aan de linkerkant op **blobs**. U ziet **inzichten-logs-operationlogs** en **Insights-metrische gegevens-pt1m**. Deze containers worden gemaakt door Azure Cognitive Search wanneer de logboek gegevens worden geëxporteerd naar de Blob-opslag.

3. Klik op omlaag in de maphiërarchie totdat het JSON-bestand is bereikt.  Gebruik het context menu om het bestand te downloaden.

Zodra het bestand is gedownload, opent u het in een JSON-editor om de inhoud weer te geven.

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, raadpleegt u de grond beginselen van de controle van de zoek service voor meer informatie over het volledige aanbod van de mogelijkheden voor toezicht.

> [!div class="nextstepaction"]
> [Bewerkingen en activiteiten bewaken in azure Cognitive Search](search-monitor-usage.md)