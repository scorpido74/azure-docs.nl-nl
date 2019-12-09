---
title: Rapport over analyse gegevens van zoek verkeer
titleSuffix: Azure Cognitive Search
description: Schakel zoek verkeer Analytics in voor Azure Cognitive Search, verzamel telemetrie en door de gebruiker geïnitieerde gebeurtenissen met behulp van Application Insights en analyseer vervolgens de resultaten in een Power BI rapport.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: b9b0ba85aed4d63fe6bb939c9ed3b99d3e789397
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932565"
---
# <a name="implement-search-traffic-analytics-in-azure-cognitive-search"></a>Zoek Traffic Analytics in azure Cognitive Search implementeren

Zoek verkeer analyse is een patroon voor het implementeren van een feedback-lus voor uw zoek service. Doel is het verzamelen van telemetrie voor door de gebruiker geïnitieerde Klik gebeurtenissen en toetsenbord invoer. Met behulp van deze informatie kunt u de effectiviteit van uw zoek oplossing bepalen, waaronder populaire zoek termen, wissel frequentie en de resultaten van de query invoer nul als resultaat.

Dit patroon neemt afhankelijk van [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (een functie van [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/)) voor het verzamelen van gebruikers gegevens. U moet ook instrumentatie toevoegen aan de client code, zoals wordt beschreven in dit artikel. Ten slotte hebt u een rapportage mechanisme nodig om de gegevens te analyseren. We raden Power BI aan, maar u kunt elk hulp programma gebruiken dat verbinding maakt met Application Insights.

> [!NOTE]
> Het patroon dat in dit artikel wordt beschreven, is bedoeld voor geavanceerde scenario's en clickstream gegevens die door uw client worden gegenereerd. U kunt ook rapporteren over de logboek gegevens die zijn gegenereerd door uw zoek service. Zie [Resource verbruik en query activiteit bewaken](search-monitor-usage.md)voor meer informatie over service logboek rapporten.

## <a name="identify-relevant-search-data"></a>Relevante Zoek gegevens identificeren

Als u wilt beschikken over nuttige Zoek gegevens, is het nood zakelijk om sommige signalen van de gebruikers van de zoek toepassing te registreren. Deze signalen duiden inhoud aan waar gebruikers geïnteresseerd zijn en die relevant zijn voor hun behoeften.

Er zijn twee signalen zoeken Traffic Analytics nodig:

+ Door de gebruiker gegenereerde Zoek gebeurtenissen: alleen Zoek opdrachten die door een gebruiker zijn gestart, zijn interessant. Zoek aanvragen die worden gebruikt voor het vullen van facetten, extra inhoud of interne informatie, zijn niet belang rijk en ze hellen en verlichten uw resultaten.

+ Door de gebruiker gegenereerde Klik gebeurtenissen: door op in dit document te klikken, verwijzen we naar een gebruiker die een bepaald Zoek resultaat van een zoek query heeft geselecteerd. Een klik in het algemeen betekent dat een document een relevant resultaat is voor een specifieke zoek query.

Als u een koppeling zoekt en op gebeurtenissen met een correlatie-ID klikt, kunt u het gedrag van gebruikers in uw toepassing analyseren. Deze zoek inzichten zijn niet mogelijk met alleen logboeken voor zoek verkeer.

## <a name="add-search-traffic-analytics"></a>Analyse van zoek verkeer toevoegen

De signalen die in de voor gaande sectie worden vermeld, moeten worden verzameld uit de zoek toepassing, omdat de gebruiker ermee werkt. Application Insights is een uitbreid bare bewakings oplossing, beschikbaar voor meerdere platforms, met flexibele instrumentatie opties. Met het gebruik van Application Insights kunt u profiteren van de Power BI Zoek rapporten die zijn gemaakt door Azure Cognitive Search om de analyse van gegevens gemakkelijker te maken.

Op de pagina [Portal](https://portal.azure.com) voor uw Azure Cognitive Search-service bevat de pagina Zoek Traffic Analytics een cheat blad voor het volgen van dit telemetrie-patroon. U kunt ook een Application Insights resource selecteren of maken en de benodigde gegevens op één plek weer geven.

![Traffic Analytics instructies zoeken][1]

## <a name="1---select-a-resource"></a>1: een resource selecteren

U moet een Application Insights resource selecteren die u wilt gebruiken of u kunt er een maken als u er nog geen hebt. U kunt een resource gebruiken die al in gebruik is om de vereiste aangepaste gebeurtenissen te registreren.

Wanneer u een nieuwe Application Insights resource maakt, zijn alle toepassings typen geldig voor dit scenario. Selecteer de versie die het beste past bij het platform dat u gebruikt.

U hebt de instrumentatie sleutel nodig voor het maken van de telemetrie-client voor uw toepassing. U kunt deze downloaden via het dash board van de Application Insights portal of u kunt het downloaden van de pagina Zoek Traffic Analytics en het exemplaar selecteren dat u wilt gebruiken.

## <a name="2---add-instrumentation"></a>2: instrumentatie toevoegen

In deze stap gaat u uw eigen zoek toepassing gebruiken met behulp van de Application Insights resource die u in de bovenstaande stap hebt gemaakt. Er zijn vier stappen voor dit proces:

**Stap 1: een telemetrie-client maken**

Dit is het object dat gebeurtenissen naar de Application Insights resource verzendt.

*C#*

    private TelemetryClient telemetryClient = new TelemetryClient();
    telemetryClient.InstrumentationKey = "<YOUR INSTRUMENTATION KEY>";

*JavaScript*

    <script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
    ({
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
    });
    window.appInsights=appInsights;
    </script>

Voor andere talen en platformen, zie de volledige [lijst](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

**Stap 2: een zoek-ID aanvragen voor correlatie**

Voor het correleren van zoek aanvragen met klikken moet u een correlatie-ID hebben die deze twee afzonderlijke gebeurtenissen verbindt. Azure Cognitive Search biedt u een zoek-ID wanneer u deze aanvraagt met een koptekst:

*C#*

    // This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
    var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
    var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
    IEnumerable<string> headerValues;
    string searchId = string.Empty;
    if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out headerValues)){
     searchId = headerValues.FirstOrDefault();
    }

*JavaScript*

    request.setRequestHeader("x-ms-azs-return-searchid", "true");
    request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
    var searchId = request.getResponseHeader('x-ms-azs-searchid');

**Stap 3: gebeurtenissen in een logboek zoeken**

Telkens wanneer een zoek opdracht wordt uitgegeven door een gebruiker, moet u zich als een zoek gebeurtenis aanmelden met het volgende schema voor een Application Insights aangepaste gebeurtenis:

**SearchServiceName**: (teken reeks) zoek service naam **SearchId**: (GUID) unieke id van de zoek query (komt in het zoek antwoord) **index**naam: (teken reeks) zoek service-index die moet worden opgevraagd **QueryTerms**: (teken reeks) zoek termen die zijn ingevoerd door de gebruiker **ResultCount**: (int) aantal documenten dat is geretourneerd (in de zoek respons) **ScoringProfile**: (teken reeks) naam van het gebruikte Score profiel

> [!NOTE]
> Aantal aanvragen voor door de gebruiker gegenereerde query's door $count = True toe te voegen aan uw zoek query. Meer informatie vindt u [hier](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)
>

> [!NOTE]
> Vergeet niet alleen zoek query's te registreren die door gebruikers worden gegenereerd.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
    telemetryClient.TrackEvent("Search", properties);

*JavaScript*

    appInsights.trackEvent("Search", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    IndexName: <index name>,
    QueryTerms: <search terms>,
    ResultCount: <results count>,
    ScoringProfile: <scoring profile used>
    });

**Stap 4: gebeurtenissen op logboeken registreren**

Telkens wanneer een gebruiker op een document klikt, is dit een signaal dat moet worden vastgelegd voor de analyse van de zoek opdracht. Gebruik Application Insights aangepaste gebeurtenissen om deze gebeurtenissen te registreren met het volgende schema:

**ServiceName**: (teken reeks) zoek service naam **SearchId**: (GUID) unieke id van de gerelateerde zoek query **documenten**: (teken reeks) **positie**van document-id: (int) rang van het document in de pagina met zoek resultaten

> [!NOTE]
> De positie verwijst naar de Cardinal-volg orde in uw toepassing. U kunt dit nummer instellen, zolang het altijd hetzelfde is, om een vergelijking mogelijk te maken.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
    telemetryClient.TrackEvent("Click", properties);

*JavaScript*

    appInsights.trackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

## <a name="3---analyze-in-power-bi"></a>3-in Power BI analyseren

Nadat u uw app hebt geinstrumenteerd en hebt gecontroleerd of uw toepassing correct is verbonden met Application Insights, kunt u een vooraf gedefinieerde sjabloon gebruiken die door Azure Cognitive Search is gemaakt voor Power BI bureau blad. 

Azure cognitieve Zoek opdrachten bieden een bewaking [Power bi inhouds pakket](https://app.powerbi.com/getdata/services/azure-search) , zodat u logboek gegevens kunt analyseren. Het inhouds pakket voegt vooraf gedefinieerde grafieken en tabellen toe die nuttig zijn voor het analyseren van de aanvullende gegevens die zijn vastgelegd voor de analyse van het zoek verkeer. Zie voor meer informatie de [help-pagina](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/). 

1. Klik in het linkerdeel venster van het dash board van Azure Cognitive Search onder **instellingen**op **verkeer analyse zoeken**.

2. Klik op de pagina **Traffic Analytics zoeken** in stap 3 op **Power BI Desktop ophalen** om Power bi te installeren.

   ![Power BI-rapporten ophalen](./media/search-traffic-analytics/get-use-power-bi.png "Power BI-rapporten ophalen")

2. Klik op dezelfde pagina op **Power bi rapport downloaden**.

3. Het rapport wordt geopend in Power BI Desktop en u wordt gevraagd verbinding te maken met Application Insights. U kunt deze informatie vinden op de Azure Portal pagina's voor uw Application Insights resource.

   ![Verbinding maken met Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Verbinding maken met Application Insights")

4. Klik op **Load**.

Het rapport bevat grafieken en tabellen waarmee u meer onderbouwde beslissingen kunt nemen om uw zoek prestaties en relevantie te verbeteren.

Metrische gegevens zijn opgenomen in de volgende items:

* Klik op tarieven (afdeling): de verhouding van gebruikers die op een specifiek document klikken en het totaal aantal Zoek opdrachten.
* Zoek opdrachten zonder klikken: voor waarden voor top query's die geen klikken registreren
* Meest geklikte documenten: de meeste geklikte documenten op ID in de afgelopen 24 uur, 7 dagen en 30 dagen.
* Populaire term-document paren: voor waarden die tot hetzelfde document hebben geklikt, gesorteerd door te klikken.
* Tijd om te klikken: klikken op tijdstip sinds de zoek query

De volgende scherm afbeelding toont de ingebouwde rapporten en grafieken voor het analyseren van analyse van zoek verkeer.

![Power BI dash board voor Azure Cognitive Search](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Power BI dash board voor Azure Cognitive Search")

## <a name="next-steps"></a>Volgende stappen
Instrumenteer uw zoek toepassing om krachtige en zicht bare gegevens over uw zoek service te krijgen.

U vindt meer informatie over [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) en gaat u naar de [pagina met prijzen](https://azure.microsoft.com/pricing/details/application-insights/) voor meer informatie over de verschillende service lagen.

Meer informatie over het maken van geweldige rapporten. Zie [aan de slag met Power bi Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) voor meer informatie.

<!--Image references-->
[1]: ./media/search-traffic-analytics/azuresearch-trafficanalytics.png
[2]: ./media/search-traffic-analytics/azuresearch-appinsightsdata.png
[3]: ./media/search-traffic-analytics/azuresearch-pbitemplate.png
