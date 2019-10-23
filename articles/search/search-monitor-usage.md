---
title: De metrische gegevens van het resource gebruik en de query voor een zoek service bewaken-Azure Search
description: Schakel logboek registratie in, haal de metrische gegevens van de query activiteit, het resource gebruik en andere systeemgegevens van een Azure Search-service op.
author: HeidiSteen
manager: nitinme
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: heidist
ms.openlocfilehash: fe8061f8e99742f9dc5c1181235c4203aaad82ca
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72331218"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-search"></a>Resource verbruik en query activiteit in Azure Search bewaken

U kunt op de pagina overzicht van uw Azure Search-service systeem gegevens weer geven over resource gebruik, metrische gegevens opvragen en hoeveel quota beschikbaar zijn voor het maken van meer indexen, Indexeer functies en gegevens bronnen. U kunt ook de portal gebruiken om log Analytics te configureren of een andere resource die wordt gebruikt voor permanente gegevens verzameling. 

Het instellen van Logboeken is handig voor zelf diagnose en het behouden van de operationele geschiedenis. Intern bestaan er voor een korte periode een logboek op de back-end, voldoende voor onderzoek en analyse als u een ondersteunings ticket hebt. Als u de controle over en toegang wilt hebben tot logboek gegevens, moet u een van de oplossingen instellen die in dit artikel worden beschreven.

In dit artikel vindt u meer informatie over uw bewakings opties, het inschakelen van logboek registratie en logboek opslag, en het weer geven van de logboek inhoud.

## <a name="metrics-at-a-glance"></a>Metrische gegevens in één oogopslag

De bewerkings-en **bewakings** secties die in het overzichts venster zijn **ingebouwd, worden** opgenomen in het Resource verbruik en de metrische gegevens van de query uitvoering Deze informatie wordt beschikbaar zodra u de service gaat gebruiken, zonder dat de configuratie is vereist. Deze pagina wordt om de paar minuten vernieuwd. Als u besluit [om te bepalen welke laag moet worden gebruikt voor werk belastingen voor de productie](search-sku-tier.md), of als u [het aantal actieve replica's en partities wilt aanpassen](search-capacity-planning.md), kunt u aan de hand van deze maat regelen zien hoe snel resources worden verbruikt. en hoe goed de huidige configuratie de bestaande belasting verwerkt.

Op het tabblad **gebruik** ziet u de beschik baarheid van resources ten opzichte van huidige [limieten](search-limits-quotas-capacity.md). De volgende afbeelding is voor de gratis service, die wordt afgelimiteerd bij 3 objecten van elk type en 50 MB aan opslag. Een Basic-of Standard-Service heeft hogere limieten en als u het aantal partities verhoogt, wordt de maximale opslag proportioneel.

![Status van gebruik ten opzichte van de doel limieten](./media/search-monitor-usage/usage-tab.png
 "Status van gebruik ten opzichte van de doel limieten")

## <a name="queries-per-second-qps-and-other-metrics"></a>Query's per seconde (QPS) en andere metrische gegevens

Het tabblad **bewaking** toont zwevende gemiddelden voor metrische gegevens, zoals zoek *query's per seconde* (qps), geaggregeerd per minuut. 
*Zoek latentie* is de hoeveelheid tijd die de zoek service nodig heeft om Zoek query's, geaggregeerd per minuut, te verwerken. *Percentage van vertraagde Zoek query's* (niet weer gegeven) is het percentage Zoek query's dat is beperkt, ook samengevoegd per minuut.

Deze getallen zijn van benadering en zijn bedoeld om u een algemeen idee te geven van de manier waarop uw systeem bezig is met het verwerken van aanvragen. De werkelijke QPS kan hoger of lager zijn dan het getal dat u in de portal hebt gerapporteerd.

![Query's per seconde-activiteit](./media/search-monitor-usage/monitoring-tab.png "Query's per seconde-activiteit")

## <a name="activity-logs"></a>Activiteitenlogboeken

In het **activiteiten logboek** worden gegevens verzameld van Azure Resource Manager. Voor beelden van informatie in het activiteiten logboek zijn het maken of verwijderen van een service, het bijwerken van een resource groep, het controleren op Beschik baarheid van namen of het verkrijgen van een service toegangs sleutel voor het afhandelen van een aanvraag. 

U kunt het **activiteiten logboek** openen vanuit het navigatie deel venster, of vanuit meldingen in de bovenste venster opdracht balk, of via de pagina **problemen vaststellen en oplossen** .

Voor taken in de service, zoals het maken van een index of het verwijderen van een gegevens bron, ziet u algemene meldingen, zoals ' beheer sleutel ophalen ' voor elke aanvraag, maar niet de specifieke actie zelf. Voor dit niveau van informatie moet u een oplossing voor bewaking van invoeg toepassingen inschakelen.

## <a name="add-on-monitoring-solutions"></a>Bewakings oplossingen voor invoeg toepassingen

Azure Search slaat geen gegevens buiten de beheerde objecten op, wat betekent dat logboek gegevens extern moeten worden opgeslagen. U kunt de onderstaande resources configureren als u logboek gegevens persistent wilt maken. 

De volgende tabel vergelijkt de opties voor het opslaan van Logboeken en het toevoegen van uitgebreide bewaking van service bewerkingen en het uitvoeren van query's op werk belastingen via Application Insights.

| Bron | Gebruikt voor |
|----------|----------|
| [Azure Monitor-logboeken](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | Vastgelegde gebeurtenissen en query gegevens, op basis van de onderstaande schema's. Gebeurtenissen worden geregistreerd in een Log Analytics-werk ruimte. U kunt query's uitvoeren op een werk ruimte om gedetailleerde informatie uit het logboek te retour neren. Zie [aan de slag met Azure monitor-logboeken](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) voor meer informatie. |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Vastgelegde gebeurtenissen en query gegevens, op basis van de onderstaande schema's. Gebeurtenissen worden geregistreerd in een BLOB-container en opgeslagen in JSON-bestanden. Een JSON-editor gebruiken om de inhoud van een bestand weer te geven.|
| [Event Hub](https://docs.microsoft.com/azure/event-hubs/) | Vastgelegde gebeurtenissen en query gegevens, op basis van de schema's die in dit artikel worden beschreven. Kies deze optie als een alternatieve service voor het verzamelen van gegevens voor zeer grote logboeken. |

Zowel de Azure Monitor-Logboeken als de Blob-opslag zijn beschikbaar als gratis service, zodat u ze gratis kunt uitproberen voor de levens duur van uw Azure-abonnement. Application Insights is gratis aan te melden en te gebruiken zolang de grootte van de toepassings gegevens onder bepaalde limieten ligt (Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/monitor/) voor meer informatie).

In de volgende sectie wordt stapsgewijs uitgelegd hoe u Azure Blob Storage inschakelt en gebruikt om logboek gegevens te verzamelen en te openen die zijn gemaakt door Azure Search bewerkingen.

## <a name="enable-logging"></a>Logboekregistratie inschakelen

Logboek registratie voor indexerings-en query-workloads is standaard uitgeschakeld en is afhankelijk van oplossingen voor het vastleggen van de infra structuur en de lange termijn externe opslag. De enige persistente gegevens in Azure Search zijn de objecten die worden gemaakt en beheerd, waardoor logboeken ergens anders moeten worden opgeslagen.

In deze sectie leert u hoe u Blob Storage kunt gebruiken voor het opslaan van vastgelegde gebeurtenissen en metrische gegevens.

1. [Maak een opslag account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) als u er nog geen hebt. U kunt deze in dezelfde resource groep plaatsen als Azure Search om het later eenvoudig op te schonen als u alle resources die in deze oefening worden gebruikt wilt verwijderen.

   Uw opslag account moet zich in dezelfde regio bevinden als Azure Search.

2. Open de overzichts pagina van de zoek service. Schuif in het linkerdeel venster omlaag naar **bewaking** en klik op **bewaking inschakelen**.

   ![Bewaking inschakelen](./media/search-monitor-usage/enable-monitoring.png "Bewaking inschakelen")

3. Kies de gegevens die u wilt exporteren: Logboeken, metrieken of beide. U kunt het kopiëren naar een opslag account, het naar een Event Hub verzenden of het exporteren naar Azure Monitor-Logboeken.

   Voor archivering naar Blob Storage moet alleen het opslag account bestaan. Containers en blobs worden zo gemaakt als nodig wanneer logboek gegevens worden geëxporteerd.

   ![Blob Storage-archief configureren](./media/search-monitor-usage/configure-blob-storage-archive.png "Blob Storage-archief configureren")

4. Sla het profiel op.

5. Test logboek registratie door het maken of verwijderen van objecten (logboek gebeurtenissen maken) en door het verzenden van query's (metrische gegevens worden gegenereerd). 

Logboek registratie is ingeschakeld wanneer u het profiel opslaat. Containers worden alleen gemaakt als er een activiteit is die moet worden geregistreerd of gemeten. Wanneer de gegevens naar een opslag account worden gekopieerd, worden de gegevens ingedeeld als JSON en in twee containers geplaatst:

* Insights-logboeken-operationlogs: voor logboeken met Zoek opdrachten
* inzichten-metrische gegevens-pt1m: voor metrische gegevens

**Het duurt één uur voordat de containers worden weer gegeven in de Blob-opslag. Er is één blob, per uur, per container.**

U kunt [Visual Studio code](#download-and-open-in-visual-studio-code) of een andere JSON-editor gebruiken om de bestanden weer te geven. 

### <a name="example-path"></a>Voor beeld van pad

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>Logboek schema
Blobs met uw verkeers logboeken van de zoek service zijn gestructureerd zoals beschreven in deze sectie. Elke BLOB heeft één hoofd object met de naam **records** die een matrix met logboek objecten bevatten. Elke BLOB bevat records voor alle bewerkingen die plaatsvonden tijdens hetzelfde uur.

| Naam | Type | Voorbeeld | Opmerkingen |
| --- | --- | --- | --- |
| tijd |datum/tijd |"2018-12-07T00:00:43.6872559 Z" |Tijds tempel van de bewerking |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/STANDAARD/PROVIDERS/<br/> Micro soft. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |Uw ResourceId |
| operationName |string |Query. Search |De naam van de bewerking |
| operationVersion |string |"2019-05-06" |De API-versie die wordt gebruikt |
| category |string |"OperationLogs" |bedrag |
| resultType |string |Geleverd |Mogelijke waarden: geslaagd of mislukt |
| resultSignature |int |200 |HTTP-resultaat code |
| durationMS |int |50 |De duur van de bewerking in milliseconden |
| properties |object |Raadpleeg de volgende tabel |Object met toepassingsspecifieke gegevens |

**Eigenschappen schema**

| Naam | Type | Voorbeeld | Opmerkingen |
| --- | --- | --- | --- |
| Beschrijving |string |' GET/Indexes (' content ')/docs ' |Het eind punt van de bewerking |
| Query |string |"? Search = AzureSearch & $count = True & API-Version = 2019-05-06" |De query parameters |
| Documenten |int |42 |Aantal verwerkte documenten |
| indexName |string |"testindex" |De naam van de index die is gekoppeld aan de bewerking |

## <a name="metrics-schema"></a>Schema voor metrische gegevens

Metrische gegevens worden vastgelegd voor query aanvragen.

| Naam | Type | Voorbeeld | Opmerkingen |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/STANDAARD/PROVIDERS/<br/>Micro soft. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |de resource-ID |
| metricName |string |Periode |de naam van de metriek |
| tijd |datum/tijd |"2018-12-07T00:00:43.6872559 Z" |de tijds tempel van de bewerking |
| evenredig |int |64 |De gemiddelde waarde van de onbewerkte voor beelden in het tijds interval voor metrische gegevens |
| Maal |int |37 |De minimale waarde van de onbewerkte voor beelden in het tijds interval voor metrische gegevens |
| Gehalte |int |78 |De maximum waarde van de onbewerkte voor beelden in het tijds interval voor metrische gegevens |
| eind |int |258 |De totale waarde van de onbewerkte voor beelden in het tijds interval voor metrische gegevens |
| count |int |4 |Het aantal onbewerkte voor beelden dat wordt gebruikt voor het genereren van de metriek |
| timegrain |string |"PT1M" |De tijd korrel van de metriek in ISO 8601 |

Alle metrische gegevens worden binnen een interval van één minuut gerapporteerd. Bij elke metriek worden minimum-, maximum-en gemiddelde waarden per minuut weer gegeven.

Voor de metriek SearchQueriesPerSecond is minimum de laagste waarde voor zoek query's per seconde die tijdens die minuut zijn geregistreerd. Hetzelfde geldt voor de maximum waarde. Gemiddelde, is de aggregatie over de hele minuut.
Denk na over dit scenario gedurende één minuut: één seconde van een hoge belasting die het maximum voor SearchQueriesPerSecond is, gevolgd door 58 seconden gemiddeld laden, en ten slotte één seconde met slechts één query, wat het minimum is.

Voor ThrottledSearchQueriesPercentage, minimum, maximum, Average en Total hebben allemaal dezelfde waarde: het percentage Zoek query's dat is beperkt tot het totale aantal Zoek query's gedurende één minuut.

## <a name="download-and-open-in-visual-studio-code"></a>Downloaden en openen in Visual Studio code

U kunt een JSON-editor gebruiken om het logboek bestand weer te geven. Als u er geen hebt, raden we u aan [Visual Studio code](https://code.visualstudio.com/download)aan te bevelen.

1. Open uw opslag account in Azure Portal. 

2. Klik in het navigatie deel venster aan de linkerkant op **blobs**. U ziet **inzichten-logs-operationlogs** en **Insights-metrische gegevens-pt1m**. Deze containers worden gemaakt door Azure Search wanneer de logboek gegevens worden geëxporteerd naar de Blob-opslag.

3. Klik op omlaag in de maphiërarchie totdat het JSON-bestand is bereikt.  Gebruik het context menu om het bestand te downloaden.

Zodra het bestand is gedownload, opent u het in een JSON-editor om de inhoud weer te geven.

## <a name="use-system-apis"></a>Systeem-Api's gebruiken
Zowel de Azure Search REST API als de .NET SDK bieden programmatische toegang tot metrische gegevens van services, index-en indexerings informatie en document tellingen.

* [Statistieken van services ophalen](/rest/api/searchservice/get-service-statistics)
* [Index statistieken ophalen](/rest/api/searchservice/get-index-statistics)
* [Documenten tellen](/rest/api/searchservice/count-documents)
* [De Indexeer functie-status ophalen](/rest/api/searchservice/get-indexer-status)

Als u Power shell of de Azure CLI wilt inschakelen, raadpleegt u de documentatie [hier](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="next-steps"></a>Volgende stappen

[Beheer uw zoek service op Microsoft Azure](search-manage.md) voor meer informatie over Service beheer en [prestaties en optimalisatie](search-performance-optimization.md) voor afstemmings richtlijnen.
