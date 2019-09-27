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
ms.openlocfilehash: d0c93d941047413c5056b3718f57b360357affbd
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327140"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-search"></a>Resource verbruik en query activiteit in Azure Search bewaken

U kunt op de pagina overzicht van uw Azure Search-service systeem gegevens weer geven over resource gebruik, metrische gegevens opvragen en hoeveel quota beschikbaar zijn voor het maken van meer indexen, Indexeer functies en gegevens bronnen. U kunt ook de portal gebruiken om log Analytics te configureren of een andere resource die wordt gebruikt voor permanente gegevens verzameling. 

Het instellen van Logboeken is handig voor zelf diagnose en het behouden van de operationele geschiedenis. Intern bestaan er voor een korte periode een logboek op de back-end, voldoende voor onderzoek en analyse als u een ondersteunings ticket hebt. Als u de controle over en toegang wilt hebben tot logboek gegevens, moet u een van de oplossingen instellen die in dit artikel worden beschreven.

In dit artikel vindt u meer informatie over uw bewakings opties, het inschakelen van logboek registratie en logboek opslag, en het weer geven van de logboek inhoud.

## <a name="metrics-at-a-glance"></a>Metrische gegevens in één oogopslag

De bewerkings-en **bewakings** secties die in het overzichts venster zijn **ingebouwd, worden** opgenomen in het Resource verbruik en de metrische gegevens van de query uitvoering Deze informatie wordt beschikbaar zodra u de service gaat gebruiken, zonder dat de configuratie is vereist. Deze pagina wordt om de paar minuten vernieuwd. Als u besluit [om te bepalen welke laag moet worden gebruikt voor werk belastingen voor de productie](search-sku-tier.md), of als u [het aantal actieve replica's en partities wilt aanpassen](search-capacity-planning.md), kunt u aan de hand van deze maat regelen zien hoe snel resources worden verbruikt. en hoe goed de huidige configuratie de bestaande belasting verwerkt.

Op het tabblad **gebruik** ziet u de beschik baarheid van resources ten opzichte van huidige [limieten](search-limits-quotas-capacity.md). De volgende afbeelding is voor de gratis service, die wordt afgelimiteerd bij 3 objecten van elk type en 50 MB aan opslag. Een Basic-of Standard-Service heeft hogere limieten en als u het aantal partities verhoogt, wordt de maximale opslag proportioneel.

![Gebruiks status relatief ten opzichte van de effectief limieten](./media/search-monitor-usage/usage-tab.png
 "Usage-status ten opzichte van de effectief limieten @ no__t

## <a name="queries-per-second-qps-and-other-metrics"></a>Query's per seconde (QPS) en andere metrische gegevens

Het tabblad **bewaking** toont zwevende gemiddelden voor metrische gegevens, zoals zoek *query's per seconde* (qps), geaggregeerd per minuut. 
*Zoek latentie* is de hoeveelheid tijd die de zoek service nodig heeft om Zoek query's, geaggregeerd per minuut, te verwerken. *Percentage van vertraagde Zoek query's* (niet weer gegeven) is het percentage Zoek query's dat is beperkt, ook samengevoegd per minuut.

Deze getallen zijn van benadering en zijn bedoeld om u een algemeen idee te geven van de manier waarop uw systeem bezig is met het verwerken van aanvragen. De werkelijke QPS kan hoger of lager zijn dan het getal dat u in de portal hebt gerapporteerd.

Query's per seconde voor activiteiten van een ![activiteit]per(./media/search-monitor-usage/monitoring-tab.png "tweede activiteit")

## <a name="activity-logs"></a>Activiteitenlogboeken

In het **activiteiten logboek** worden gegevens verzameld van Azure Resource Manager. Voor beelden van informatie in het activiteiten logboek zijn het maken of verwijderen van een service, het bijwerken van een resource groep, het controleren op Beschik baarheid van namen of het verkrijgen van een service toegangs sleutel voor het afhandelen van een aanvraag. 

U kunt het **activiteiten logboek** openen vanuit het navigatie deel venster, of vanuit meldingen in de bovenste venster opdracht balk, of via de pagina **problemen vaststellen en oplossen** .

Voor taken in de service, zoals het maken van een index of het verwijderen van een gegevens bron, ziet u algemene meldingen, zoals ' beheer sleutel ophalen ' voor elke aanvraag, maar niet de specifieke actie zelf. Voor dit niveau van informatie moet u een oplossing voor bewaking van invoeg toepassingen inschakelen.

## <a name="add-on-monitoring-solutions"></a>Bewakings oplossingen voor invoeg toepassingen

Azure Search slaat geen gegevens buiten de beheerde objecten op, wat betekent dat logboek gegevens extern moeten worden opgeslagen. U kunt de onderstaande resources configureren als u logboek gegevens persistent wilt maken. 

De volgende tabel vergelijkt de opties voor het opslaan van Logboeken en het toevoegen van uitgebreide bewaking van service bewerkingen en het uitvoeren van query's op werk belastingen via Application Insights.

| Resource | Gebruikt voor |
|----------|----------|
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | Geregistreerde gebeurtenissen en query gegevens, op basis van de onderstaande schema's, worden gecorreleerd met gebruikers gebeurtenissen in uw app. Dit is de enige oplossing waarmee gebruikers acties of signalen worden gebruikt voor het toewijzen van gebeurtenissen van door de gebruiker geïnitieerde Zoek opdrachten, in tegens telling tot het filteren van aanvragen die worden verzonden door de toepassings code. Als u deze aanpak wilt gebruiken, kopieert u de instrumentatie code naar uw bron bestanden om de aanvraag informatie naar Application Insights te routeren. Zie voor meer informatie [Search Traffic Analytics](search-traffic-analytics.md)(Engelstalig). |
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

   ![](./media/search-monitor-usage/enable-monitoring.png "Bewaking") inschakelen inschakelen

3. Kies de gegevens die u wilt exporteren: Logboeken, metrische gegevens of beide. U kunt het kopiëren naar een opslag account, het naar een Event Hub verzenden of het exporteren naar Azure Monitor-Logboeken.

   Voor archivering naar Blob Storage moet alleen het opslag account bestaan. Containers en blobs worden zo gemaakt als nodig wanneer logboek gegevens worden geëxporteerd.

   ![Blob Storage-archief configureren](./media/search-monitor-usage/configure-blob-storage-archive.png "Blob Storage-archief")

4. Sla het profiel op.

5. Test logboek registratie door het maken of verwijderen van objecten (logboek gebeurtenissen maken) en door het verzenden van query's (metrische gegevens worden gegenereerd). 

Logboek registratie is ingeschakeld wanneer u het profiel opslaat. Containers worden alleen gemaakt als er een activiteit is die moet worden geregistreerd of gemeten. Wanneer de gegevens naar een opslag account worden gekopieerd, worden de gegevens ingedeeld als JSON en in twee containers geplaatst:

* Insights-logs-operationlogs: voor zoeken in logboeken over webverkeer
* Insights-metrics-pt1m: voor metrische gegevens

**It duurt één uur voordat de containers in de Blob-opslag worden weer gegeven. Er is één blob, per uur, per container.**

U kunt [Visual Studio code](#download-and-open-in-visual-studio-code) of een andere JSON-editor gebruiken om de bestanden weer te geven. 

### <a name="example-path"></a>Voor beeld van pad

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>Logboek-schema
Blobs met uw verkeers logboeken van de zoek service zijn gestructureerd zoals beschreven in deze sectie. Elke BLOB heeft één hoofd object met de naam **records** die een matrix met logboek objecten bevatten. Elke BLOB bevat records voor alle bewerkingen die plaatsvonden tijdens hetzelfde uur.

| Name | Type | Voorbeeld | Opmerkingen |
| --- | --- | --- | --- |
| time |datetime |"2018-12-07T00:00:43.6872559 Z" |Tijdstempel van de bewerking |
| resourceId |string |' / SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>STANDAARD-RESOURCEGROUPS-PROVIDERS /<br/> MICROSOFT. ZOEKEN/SEARCHSERVICES/SEARCHSERVICE" |Uw ResourceId |
| operationName |string |"Query.Search" |De naam van de bewerking |
| operationVersion |string |"2019-05-06" |De api-versie die wordt gebruikt |
| category |string |"OperationLogs" |constante |
| resultType |string |"Geslaagd" |Mogelijke waarden: Geslaagd of mislukt |
| resultSignature |int |200 |HTTP-resultaatcode |
| durationMS |int |50 |Duur van de bewerking in milliseconden |
| properties |object |Zie de volgende tabel |Object met de bewerking-specifieke gegevens |

**Eigenschappen van schema**

| Name | Type | Voorbeeld | Opmerkingen |
| --- | --- | --- | --- |
| Description |string |'/Indexes('content')/docs ophalen' |Eindpunt van de bewerking |
| Query’s uitvoeren |string |"?search=AzureSearch&$count=true&api-version=2019-05-06" |De queryparameters |
| Documenten |int |42 |Aantal verwerkte documenten |
| Index |string |"testindex" |Naam van de index die is gekoppeld aan de bewerking |

## <a name="metrics-schema"></a>Schema van de metrische gegevens

Metrische gegevens worden vastgelegd voor query aanvragen.

| Name | Type | Voorbeeld | Opmerkingen |
| --- | --- | --- | --- |
| resourceId |string |' / SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>STANDAARD-RESOURCEGROUPS-PROVIDERS /<br/>MICROSOFT. ZOEKEN/SEARCHSERVICES/SEARCHSERVICE" |de resource-id |
| MetricName |string |"Latentie" |de naam van de metrische gegevens |
| time |datetime |"2018-12-07T00:00:43.6872559 Z" |tijdstempel van de bewerking |
| gemiddeld |int |64 |De gemiddelde waarde van de onbewerkte voorbeelden in de metrische tijdsinterval |
| minimum |int |37 |De minimumwaarde van de onbewerkte voorbeelden in de metrische tijdsinterval |
| maximum |int |78 |De maximale waarde van de onbewerkte voorbeelden in de metrische tijdsinterval |
| totaal |int |258 |De totale waarde van de onbewerkte voorbeelden in de metrische tijdsinterval |
| count |int |4 |Het aantal onbewerkte voorbeelden die worden gebruikt voor het genereren van de metrische gegevens |
| timegrain |string |"PT1M" |Het tijdsinterval van de metrische gegevens in de ISO 8601 |

Alle metrische gegevens worden gerapporteerd in één minuut intervallen. Elke metriek wordt de minimale, maximale en gemiddelde waarden per minuut.

Voor de metriek SearchQueriesPerSecond is minimaal de laagste waarde voor zoekquery's per seconde dat is geregistreerd gedurende die minuut. Dit geldt ook voor de maximale waarde. Gemiddelde, wordt de statistische functie voor de hele minuut.
Denk aan dit scenario gedurende één minuut: één seconde van hoog laden dat wil zeggen het maximum voor SearchQueriesPerSecond, gevolgd door 58 seconden gemiddelde belasting, en tot slot een tweede met slechts één query die is de minimale.

ThrottledSearchQueriesPercentage, minimum, maximum, gemiddelde en totale, een hebben dezelfde waarde: het percentage van zoekquery's die zijn beperkt, van het totale aantal zoekquery's gedurende één minuut.

## <a name="download-and-open-in-visual-studio-code"></a>Downloaden en openen in Visual Studio code

U kunt een JSON-editor gebruiken om het logboek bestand weer te geven. Als u er geen hebt, raden we u aan [Visual Studio code](https://code.visualstudio.com/download)aan te bevelen.

1. Open uw opslag account in Azure Portal. 

2. Klik in het navigatie deel venster aan de linkerkant op **blobs**. U ziet **inzichten-logs-operationlogs** en **Insights-metrische gegevens-pt1m**. Deze containers worden gemaakt door Azure Search wanneer de logboek gegevens worden geëxporteerd naar de Blob-opslag.

3. Klik op omlaag in de maphiërarchie totdat het JSON-bestand is bereikt.  Gebruik het context menu om het bestand te downloaden.

Zodra het bestand is gedownload, opent u het in een JSON-editor om de inhoud weer te geven.

## <a name="use-system-apis"></a>Systeem-Api's gebruiken
Zowel de Azure Search REST API als de .NET SDK bieden programmatische toegang tot metrische gegevens van services, index-en indexerings informatie en document tellingen.

* [Statistieken van services ophalen](/rest/api/searchservice/get-service-statistics)
* [Indexstatistieken ophalen](/rest/api/searchservice/get-index-statistics)
* [Aantal documenten](/rest/api/searchservice/count-documents)
* [Status van de indexeerfunctie ophalen](/rest/api/searchservice/get-indexer-status)

Zie de documentatie om in te schakelen met behulp van PowerShell of Azure CLI, [hier](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="next-steps"></a>Volgende stappen

[Beheer uw zoek service op Microsoft Azure](search-manage.md) voor meer informatie over Service beheer en [prestaties en optimalisatie](search-performance-optimization.md) voor afstemmings richtlijnen.