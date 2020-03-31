---
title: Telemetrie voor analyse van zoekverkeer
titleSuffix: Azure Cognitive Search
description: Schakel analyse van zoekverkeer in voor Azure Cognitive Search, verzamel telemetrie en door gebruikers geïnitieerde gebeurtenissen met Behulp van Application Insights en analyseer bevindingen in een Power BI-rapport.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: db8c1781061f038cc90310fcd00c220fa6f5d1a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258206"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Telemetriegegevens verzamelen voor analyse van zoekverkeer

Analyse van zoekverkeer is een patroon voor het verzamelen van telemetrie over gebruikersinteracties met uw Azure Cognitive Search-toepassing, zoals door gebruikers geïnitieerde klikgebeurtenissen en toetsenbordingangen. Met behulp van deze informatie u de effectiviteit van uw zoekoplossing bepalen, inclusief populaire zoektermen, klikfrequentie en welke queryinvoernulresultaten nul resultaten opleveren.

Dit patroon is afhankelijk van [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (een functie van [Azure Monitor)](https://docs.microsoft.com/azure/azure-monitor/)om gebruikersgegevens te verzamelen. Het vereist dat u instrumentatie toevoegt aan uw clientcode, zoals beschreven in dit artikel. Ten slotte hebt u een rapportagemechanisme nodig om de gegevens te analyseren. We raden Power BI aan, maar u het application dashboard of een tool gebruiken die verbinding maakt met Application Insights.

> [!NOTE]
> Het patroon dat in dit artikel wordt beschreven, is voor geavanceerde scenario's en clickstream-gegevens die worden gegenereerd door code die u aan uw client toevoegt. Servicelogs zijn daarentegen eenvoudig in te stellen, bieden een reeks statistieken en kunnen worden uitgevoerd in de portal zonder dat er code nodig is. Het inschakelen van diagnostische logboekregistratie wordt aanbevolen voor alle scenario's. Zie [Loggegevens verzamelen en analyseren](search-monitor-logs.md)voor meer informatie .

## <a name="identify-relevant-search-data"></a>Relevante zoekgegevens identificeren

Om nuttige statistieken voor analyse van het zoekverkeer te hebben, is het noodzakelijk om bepaalde signalen van de gebruikers van uw zoektoepassing te registreren. Deze signalen betekenen inhoud waarin gebruikers geïnteresseerd zijn en die zij relevant achten. Voor analyse van zoekverkeer:

+ Door gebruikers gegenereerde zoekgebeurtenissen: alleen zoekopdrachten die door een gebruiker zijn gestart, zijn interessant. Zoekverzoeken die worden gebruikt om facetten, aanvullende inhoud of interne informatie in te vullen, zijn niet belangrijk en ze scheeftrekken en bias uw resultaten.

+ Door gebruikers gegenereerde klikgebeurtenissen: Op een pagina met zoekresultaten betekent een klikgebeurtenis over het algemeen dat een document een relevant resultaat is voor een specifieke zoekopdracht.

Door zoek- en klikgebeurtenissen te koppelen aan een correlatie-ID, krijgt u een beter inzicht in hoe goed de zoekfunctionaliteit van uw toepassing presteert.

## <a name="add-search-traffic-analytics"></a>Analyse van zoekverkeer toevoegen

Op de [portalpagina](https://portal.azure.com) voor uw Azure Cognitive Search-service bevat de pagina Search Traffic Analytics een spiekbriefje voor het volgen van dit telemetriepatroon. Op deze pagina u een Application Insights-bron selecteren of maken, de instrumentatiesleutel ophalen, fragmenten kopiëren die u voor uw oplossing aanpassen en een Power BI-rapport downloaden dat is opgebouwd over het schema dat in het patroon wordt weergegeven.

![Pagina Traffic Analytics zoeken in de portal](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Pagina Traffic Analytics zoeken in de portal")

## <a name="1---set-up-application-insights"></a>1 - Applicatieinzichten instellen

Selecteer een bestaande Application Insights-bron of [maak er een](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) als u er nog geen hebt. Als u de pagina Search Traffic Analytics gebruikt, u de instrumentatiesleutel kopiëren die uw toepassing nodig heeft om verbinding te maken met Application Insights.

Zodra u een Application Insights-bron hebt, u [instructies volgen voor ondersteunde talen en platforms](https://docs.microsoft.com/azure/azure-monitor/app/platforms) om uw app te registreren. Registratie is gewoon het toevoegen van de instrumentatiesleutel van Application Insights aan uw code, die het opzetten van de koppeling. U de sleutel vinden in de portal of op de pagina Search Traffic Analytics wanneer u een bestaande bron selecteert.

Een snelkoppeling die werkt voor sommige Visual Studio-projecttypen wordt weergegeven in de volgende stappen. Het maakt een bron en registreert uw app in slechts een paar klikken.

1. Open uw oplossing voor Visual Studio en ASP.NET ontwikkeling en selecteer **Project** > **Add Application Insights Telemetry.**

1. Klik op **Aan de slag**.

1. Registreer uw app door een Microsoft-account, Azure-abonnement en een Application Insights-bron op te geven (een nieuwe bron is de standaardbron). Klik **op Registreren**.

Op dit moment is uw toepassing ingesteld voor toepassingsbewaking, wat betekent dat alle paginabelastingen worden bijgehouden met standaardstatistieken. Zie [Telemetrie aan de serverzijde van Application Insights inschakelen](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio)voor meer informatie over de vorige stappen.

## <a name="2---add-instrumentation"></a>2 - Instrumentatie toevoegen

Met deze stap u uw eigen zoektoepassing gebruiken met behulp van de Application Insights-bron die u in de bovenstaande stap hebt gemaakt. Er zijn vier stappen voor dit proces, te beginnen met het maken van een telemetrieclient.

### <a name="step-1-create-a-telemetry-client"></a>Stap 1: Een telemetrieclient maken

Maak een object dat gebeurtenissen naar Application Insights verzendt. U instrumentatie toevoegen aan uw server-side application code of client-side code die wordt uitgevoerd in een browser, hier uitgedrukt als C# en JavaScript varianten (voor andere talen, zie de volledige lijst van [ondersteunde platforms en frameworks](https://docs.microsoft.com/azure/application-insights/app-insights-platforms). Kies de aanpak die u de gewenste diepte van informatie geeft.

Telemetrie aan de serverzijde legt metrische gegevens vast op de toepassingslaag, bijvoorbeeld in toepassingen die als webservice in de cloud worden uitgevoerd, of als on-premises app op een bedrijfsnetwerk. Telemetrie aan de serverzijde legt zoek- en klikgebeurtenissen, de positie van een document in de resultaten en querygegevens vast, maar uw gegevensverzameling wordt beperkt tot alle informatie die op die laag beschikbaar is.

Op de client hebt u mogelijk extra code die query-invoer manipuleert, navigatie toevoegt of context bevat (bijvoorbeeld query's die zijn gestart vanaf een startpagina versus een productpagina). Als u uw oplossing beschrijft, u kiezen voor instrumentatie aan de clientzijde, zodat uw telemetrie de extra details weergeeft. Hoe dit extra detail wordt verzameld gaat verder dan het bereik van dit patroon, maar u [Application Insights voor webpagina's voor](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data) meer richting bekijken. 

**C# gebruiken**

Voor C#wordt de **Instrumentatiesleutel** gevonden in de toepassingsconfiguratie, zoals appsettings.json als uw project ASP.NET is. Raadpleeg de registratie-instructies als u niet zeker bent van de sleutellocatie.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
    {
        _telemetryClient = telemetry;
    }
```

**JavaScript gebruiken**

```javascript
<script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
({
instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
});
window.appInsights=appInsights;
</script>
```

### <a name="step-2-request-a-search-id-for-correlation"></a>Stap 2: Een zoek-id aanvragen voor correlatie

Om zoekverzoeken te correleren met klikken, is het noodzakelijk om een correlatie-ID te hebben die deze twee verschillende gebeurtenissen met elkaar in verband brengt. Azure Cognitive Search biedt u een zoek-id wanneer u deze aanvraagt met een HTTP-header.

Als u de zoek-id hebt, u correlatie maken van de statistieken die worden uitgezonden door Azure Cognitive Search voor het verzoek zelf, met de aangepaste statistieken die u registreert bij Application Insights.  

**C# gebruiken**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues)){
    searchId = headerValues.FirstOrDefault();
}
```

**JavaScript gebruiken (REST-API's aanroepen)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Stap 3: Zoekgebeurtenissen registreren

Elke keer dat een zoekaanvraag door een gebruiker wordt uitgegeven, moet u dat als zoekgebeurtenis registreren met het volgende schema op een aangepaste toepassingsgebeurtenis. Vergeet niet om alleen door gebruikers gegenereerde zoekopdrachten te registreren.

+ **SearchServiceName**: (tekenreeks) zoekservicenaam
+ **SearchId**: (guid) unieke id van de zoekopdracht (komt in het zoekantwoord)
+ **IndexName**: (tekenreeks) zoekservice-index die moet worden opgevraagd
+ **QueryTermen**: (tekenreeks) zoektermen die door de gebruiker zijn ingevoerd
+ **ResultCount**: (int) aantal documenten die zijn geretourneerd (komt in het zoekantwoord)
+ **ScoreProfiel**: (tekenreeks) naam van het gebruikte scoreprofiel, indien aanwezig

> [!NOTE]
> Vraag het aantal door de gebruiker gegenereerde query's aan door $count=true toe te voegen aan uw zoekopdracht. Zie [Documenten zoeken (REST) voor](/rest/api/searchservice/search-documents#counttrue--false)meer informatie.
>

**C# gebruiken**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
_telemetryClient.TrackEvent("Search", properties);
```

**JavaScript gebruiken**

```javascript
appInsights.trackEvent("Search", {
SearchServiceName: <service name>,
SearchId: <search id>,
IndexName: <index name>,
QueryTerms: <search terms>,
ResultCount: <results count>,
ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>Stap 4: Klik op gebeurtenissen log

Elke keer dat een gebruiker op een document klikt, is dat een signaal dat moet worden geregistreerd voor zoekanalysedoeleinden. Gebruik aangepaste gebeurtenissen met Application Insights om deze gebeurtenissen met het volgende schema te registreren:

+ **ServiceName**: (tekenreeks) naam van de zoekservice
+ **SearchId**: (guid) unieke id van de gerelateerde zoekopdracht
+ **DocId**: (tekenreeks) document-id
+ **Positie**: (int) rang van het document op de pagina met zoekresultaten

> [!NOTE]
> Positie verwijst naar de hoofdvolgorde in uw aanvraag. U bent vrij om dit nummer in te stellen, zolang het altijd hetzelfde is, om te kunnen vergelijken.
>

**C# gebruiken**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
_telemetryClient.TrackEvent("Click", properties);
```

**JavaScript gebruiken**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3 - Analyseren in Power BI

Nadat u uw app hebt geinstrumenteerd en hebt geverifieerd dat uw toepassing correct is verbonden met Application Insights, downloadt u een vooraf gedefinieerde rapportsjabloon om gegevens in Power BI-bureaublad te analyseren. Het rapport bevat vooraf gedefinieerde grafieken en tabellen die nuttig zijn voor het analyseren van de extra gegevens die zijn vastgelegd voor analyse van het zoekverkeer.

1. Klik in het linkernavigatiedeelvenster azure cognitive search onder **Instellingen**op **Verkeer-analyse zoeken**.

1. Klik op de pagina **Verkeeranalyse zoeken** in stap 3 op **Power BI-bureaublad ophalen** om Power BI te installeren.

   ![Power BI-rapporten ophalen](./media/search-traffic-analytics/get-use-power-bi.png "Power BI-rapporten ophalen")

1. Klik op dezelfde pagina op **Power BI-rapport downloaden**.

1. Het rapport wordt geopend in Power BI Desktop en u wordt gevraagd verbinding te maken met Application Insights en referenties op te geven. U verbindingsgegevens vinden op de Azure-portalpagina's voor uw Application Insights-bron. Geef voor referenties dezelfde gebruikersnaam en wachtwoord op als voor het aanmelden van portalen.

   ![Verbinding maken met Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Verbinding maken met Application Insights")

1. Klik **op Laden**.

Het rapport bevat grafieken en tabellen die u helpen beter geïnformeerde beslissingen te nemen om uw zoekprestaties en relevantie te verbeteren.

Statistieken omvatten de volgende items:

+ Zoekvolume en populairste term-documentparen: termen die resulteren in hetzelfde document waarop is geklikt, geordend met klikken.
+ Zoekopdrachten zonder klikken: termen voor topquery's die geen klikken registreren

In de volgende schermafbeelding ziet u hoe een ingebouwd rapport eruit zou kunnen zien als u alle schema-elementen hebt gebruikt.

![Power BI-dashboard voor Azure Cognitive Search](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Power BI-dashboard voor Azure Cognitive Search")

## <a name="next-steps"></a>Volgende stappen

Instrument uw zoekapplicatie om krachtige en inzichtelijke gegevens over uw zoekservice te krijgen.

U meer informatie vinden over [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) en de [prijspagina](https://azure.microsoft.com/pricing/details/application-insights/) bezoeken voor meer informatie over hun verschillende servicelagen.

Meer informatie over het maken van geweldige rapporten. Zie [Aan de slag met Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) voor meer informatie.