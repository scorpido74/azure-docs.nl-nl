---
title: Logboek gegevens verzamelen
titleSuffix: Azure Cognitive Search
description: Verzamelen en analyseren van logboek gegevens door een diagnostische instelling in te scha kelen en vervolgens de Kusto-query taal te gebruiken om de resultaten te verkennen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 2849dc94f1c45dda3da09120adebba6e004eb96b
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211176"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Logboek gegevens voor Azure Cognitive Search verzamelen en analyseren

Diagnostische of operationele logboeken bieden inzicht in de gedetailleerde bewerkingen van Azure Cognitive Search en zijn nuttig voor het bewaken van service-en workload-processen. Intern bestaan er voor een korte periode een logboek op de back-end, voldoende voor onderzoek en analyse als u een ondersteunings ticket hebt. Als u echter zelf richting wilt hebben over operationele gegevens, moet u een diagnostische instelling configureren om op te geven waar logboek gegevens worden verzameld. 

Het instellen van Logboeken is handig voor diagnostische gegevens en het behouden van de operationele geschiedenis. Nadat u logboek registratie hebt ingeschakeld, kunt u query's uitvoeren of rapporten maken voor gestructureerde analyse.

In de volgende tabel worden de opties voor het verzamelen en persistent maken van gegevens opgesomd.

| Resource | Gebruikt voor |
|----------|----------|
| [Verzenden naar Log Analytics werk ruimte](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Vastgelegde gebeurtenissen en query gegevens, op basis van de onderstaande schema's. Gebeurtenissen worden geregistreerd in een Log Analytics-werk ruimte. Met Log Analytics kunt u query's uitvoeren om gedetailleerde informatie te retour neren. Zie [aan de slag met Azure monitor-logboeken](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) voor meer informatie. |
| [Archiveren met Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Vastgelegde gebeurtenissen en query gegevens, op basis van de onderstaande schema's. Gebeurtenissen worden geregistreerd in een BLOB-container en opgeslagen in JSON-bestanden. Logboeken kunnen behoorlijk nauw keurig zijn (per uur per minuut), wat nuttig is voor het opnieuw doorzoeken van een specifiek incident, maar niet voor open-end onderzoek. Een JSON-editor gebruiken om een logboek bestand weer te geven.|
| [Streamen naar Event hub](https://docs.microsoft.com/azure/event-hubs/) | Vastgelegde gebeurtenissen en query gegevens, op basis van de schema's die in dit artikel worden beschreven. Kies deze optie als een alternatieve service voor het verzamelen van gegevens voor zeer grote logboeken. |

Zowel de Azure Monitor-Logboeken als de Blob-opslag zijn beschikbaar als gratis service, zodat u ze gratis kunt uitproberen voor de levens duur van uw Azure-abonnement. Application Insights is gratis aan te melden en te gebruiken zolang de grootte van de toepassings gegevens onder bepaalde limieten ligt (Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/monitor/) voor meer informatie).

## <a name="prerequisites"></a>Vereisten

Als u Log Analytics of Azure Storage gebruikt, kunt u vooraf resources maken.

+ [Een log Analytics-werk ruimte maken](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [Maak een opslag account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) als u een logboek archief nodig hebt.

## <a name="create-a-log"></a>Een logboek maken

Diagnostische instellingen definiëren het verzamelen van gegevens. Een instelling bepaalt hoe en wat wordt verzameld. 

1. Onder **bewaking**selecteert u **Diagnostische instellingen**.

   ![Diagnostische instellingen](./media/search-monitor-usage/diagnostic-settings.png "Diagnostische instellingen")

1. Selecteer **+ Diagnostische instelling toevoegen**

1. Kies de gegevens die u wilt exporteren: Logboeken, metrische gegevens of beide. U kunt gegevens verzamelen in een opslag account, een log Analytics-werk ruimte of streamen naar Event hub.

   Log Analytics wordt aanbevolen omdat u een query kunt uitvoeren op de werk ruimte in de portal.

   Als u ook Blob Storage gebruikt, worden containers en blobs als vereist gemaakt wanneer logboek gegevens worden geëxporteerd.

   ![Gegevens verzameling configureren](./media/search-monitor-usage/configure-storage.png "Gegevens verzameling configureren")

1. Sla de instelling op.

1. Test door het maken of verwijderen van objecten (logboek gebeurtenissen maken) en door het verzenden van query's (metrische gegevens worden gegenereerd). 

In Blob Storage worden containers alleen gemaakt als er een activiteit is die moet worden geregistreerd of gemeten. Wanneer de gegevens naar een opslag account worden gekopieerd, worden de gegevens ingedeeld als JSON en in twee containers geplaatst:

* Insights-logs-operationlogs: voor zoeken in logboeken over webverkeer
* Insights-metrics-pt1m: voor metrische gegevens

**Het duurt één uur voordat de containers worden weer gegeven in de Blob-opslag. Er is één blob, per uur, per container.**

Logboeken worden gearchiveerd voor elk uur dat de activiteit plaatsvindt. Het volgende pad is een voor beeld van een logboek bestand dat is gemaakt op 12 2020 januari om 9:00 uur waarbij elke `/` een map is: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2020/m=01/d=12/h=09/m=00/name=PT1H.json`

## <a name="log-schema"></a>Logboek-schema

Gegevens structuren die Azure Cognitive Search-logboek gegevens bevatten, voldoen aan het onderstaande schema. 

Voor Blob Storage bevat elke BLOB één hoofd object met de naam **records** die een matrix met logboek objecten bevatten. Elke BLOB bevat records voor alle bewerkingen die plaatsvonden tijdens hetzelfde uur.

De volgende tabel bevat een gedeeltelijke lijst met velden die worden gebruikt voor diagnostische logboek registratie.

| Naam | Type | Voorbeeld | Opmerkingen |
| --- | --- | --- | --- |
| timeGenerated |datum/tijd |"2018-12-07T00:00:43.6872559 Z" |Tijdstempel van de bewerking |
| resourceId |tekenreeks |' / SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>STANDAARD-RESOURCEGROUPS-PROVIDERS /<br/> MICROSOFT. ZOEKEN/SEARCHSERVICES/SEARCHSERVICE" |Uw ResourceId |
| operationName |tekenreeks |"Query.Search" |De naam van de bewerking |
| operationVersion |tekenreeks |"2019-05-06" |De api-versie die wordt gebruikt |
| category |tekenreeks |"OperationLogs" |constante |
| resultType |tekenreeks |"Geslaagd" |Mogelijke waarden: slagen of mislukken |
| resultSignature |int |200 |HTTP-resultaatcode |
| durationMS |int |50 |Duur van de bewerking in milliseconden |
| properties |object |Zie de volgende tabel |Object met de bewerking-specifieke gegevens |

### <a name="properties-schema"></a>Eigenschappen schema

De onderstaande eigenschappen zijn specifiek voor Azure Cognitive Search.

| Naam | Type | Voorbeeld | Opmerkingen |
| --- | --- | --- | --- |
| Description_s |tekenreeks |'/Indexes('content')/docs ophalen' |Eindpunt van de bewerking |
| Documents_d |int |42 |Aantal verwerkte documenten |
| IndexName_s |tekenreeks |"test-index" |Naam van de index die is gekoppeld aan de bewerking |
| Query_s |tekenreeks |"?search=AzureSearch&$count=true&api-version=2019-05-06" |De queryparameters |

## <a name="metrics-schema"></a>Schema van de metrische gegevens

Metrische gegevens worden vastgelegd voor query aanvragen en gemeten in een interval van één minuut. Elke metriek wordt de minimale, maximale en gemiddelde waarden per minuut. Zie [query aanvragen bewaken](search-monitor-queries.md)voor meer informatie.

| Naam | Type | Voorbeeld | Opmerkingen |
| --- | --- | --- | --- |
| resourceId |tekenreeks |' / SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>STANDAARD-RESOURCEGROUPS-PROVIDERS /<br/>MICROSOFT. ZOEKEN/SEARCHSERVICES/SEARCHSERVICE" |de resource-ID |
| MetricName |tekenreeks |"Latentie" |de naam van de metrische gegevens |
| tijd |datum/tijd |"2018-12-07T00:00:43.6872559 Z" |tijdstempel van de bewerking |
| gemiddeld |int |64 |De gemiddelde waarde van de onbewerkte voor beelden in het tijds interval voor metrische gegevens, eenheden in seconden of percentage, afhankelijk van de metriek. |
| minimum |int |37 |De minimale waarde van de onbewerkte voor beelden in het tijds interval voor metrische gegevens, eenheden in seconden. |
| maximum |int |78 |De maximum waarde van de onbewerkte voor beelden in de metrische tijds interval, eenheden in seconden.  |
| totaal |int |258 |De totale waarde van de onbewerkte voor beelden in het tijds interval voor metrische gegevens, eenheden in seconden.  |
| count |int |4 |Het aantal metrische gegevens dat vanaf een knoop punt wordt verzonden naar het logboek binnen het interval van één minuut.  |
| timegrain |tekenreeks |"PT1M" |De tijd korrel van de metriek in ISO 8601. |

Het is gebruikelijk om query's uit te voeren in milliseconden, zodat alleen query's die als seconden worden gemeten, worden weer gegeven in metriek zoals QPS.

Voor de metrische **Zoek Query's per seconde** is minimum de laagste waarde voor zoek query's per seconde die tijdens die minuut zijn geregistreerd. Dit geldt ook voor de maximale waarde. Gemiddelde, wordt de statistische functie voor de hele minuut. U kunt bijvoorbeeld binnen één minuut een patroon hebben dat er ongeveer als volgt uitziet: één seconde van een hoge belasting die het maximum voor SearchQueriesPerSecond is, gevolgd door 58 seconden gemiddelde belasting, en ten slotte één seconde met slechts één query, wat het minimum is.

Voor het **beperkende Zoek Query's percentage**, minimum, maximum, gemiddelde en totaal heeft dezelfde waarde: het percentage Zoek query's dat is beperkt tot het totale aantal Zoek query's gedurende één minuut.

## <a name="view-log-files"></a>Logboek bestanden weer geven

Blob-opslag wordt gebruikt voor het archiveren van logboek bestanden. U kunt een JSON-editor gebruiken om het logboek bestand weer te geven. Als u er geen hebt, raden we u aan [Visual Studio code](https://code.visualstudio.com/download)aan te bevelen.

1. Open uw opslag account in Azure Portal. 

2. Klik in het navigatie deel venster aan de linkerkant op **blobs**. U ziet **inzichten-logs-operationlogs** en **Insights-metrische gegevens-pt1m**. Deze containers worden gemaakt door Azure Cognitive Search wanneer de logboek gegevens worden geëxporteerd naar de Blob-opslag.

3. Klik op omlaag in de maphiërarchie totdat het JSON-bestand is bereikt.  Gebruik het context menu om het bestand te downloaden.

Zodra het bestand is gedownload, opent u het in een JSON-editor om de inhoud weer te geven.

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, raadpleegt u de grond beginselen van de controle van de zoek service voor meer informatie over het volledige aanbod van de mogelijkheden voor toezicht.

> [!div class="nextstepaction"]
> [Bewerkingen en activiteiten bewaken in azure Cognitive Search](search-monitor-usage.md)