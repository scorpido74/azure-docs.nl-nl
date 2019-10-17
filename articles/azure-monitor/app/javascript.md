---
title: Azure Application Insights voor JavaScript-web-apps | Microsoft Docs
description: Verzamel tellingen van het aantal paginaweergaven en sessies, webclientgegevens en gebruikspatronen. Detecteer uitzonderingen en prestatieproblemen in JavaScript-webpagina's.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b710d09-6ab4-4004-b26a-4fa840039500
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: mbullwin
ms.openlocfilehash: b49206c677e2f1b20c154ae0c9e358e8b2b0bbd8
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430208"
---
# <a name="application-insights-for-web-pages"></a>Application Insights voor webpagina’s

Krijg inzicht in de prestaties en het gebruik van uw webpagina's of app. Als u [Application Insights](app-insights-overview.md) toevoegt aan uw pagina script, krijgt u de beschikking over de pagina laden en Ajax-aanroepen, tellingen en Details van browser uitzonderingen en Ajax-fouten, evenals de aantallen gebruikers en sessies. Al deze gegevens kunnen worden gesegmenteerd op pagina, clientbesturingssysteem en browserversie, geografische locatie en andere dimensies. U kunt waarschuwingen instellen voor foutaantallen of het langzaam laden van de pagina. En door het invoegen van trace-aanroepen in JavaScript-code, kunt u bijhouden hoe de verschillende functies van uw webpaginatoepassing worden gebruikt.

Application Insights kan met elke webpagina worden gebruikt. Het enige wat u hiervoor hoeft te doen, is een klein stukje JavaScript toevoegen. Als uw webservice [Java](java-get-started.md) of [ASP.net](asp-net.md)is, kunt u de sdk's aan de server zijde in combi natie met de Java script-SDK aan de client zijde gebruiken om een end-to-end-goed idee te krijgen van de prestaties van uw app.

## <a name="adding-the-javascript-sdk"></a>De Java script-SDK toevoegen

1. Eerst hebt u een Application Insights resource nodig. Als u nog geen resource en instrumentatie sleutel hebt, volgt u de [instructies een nieuwe resource maken](create-new-resource.md).
2. Kopieer de instrumentatie sleutel van de resource waar u uw Java script-telemetrie wilt verzenden.
3. Voeg de Application Insights java script-SDK toe aan uw webpagina of app via een van de volgende twee opties:
    * [NPM-installatie](#npm-based-setup)
    * [Java script-fragment](#snippet-based-setup)

> [!IMPORTANT]
> U hoeft alleen een van de onderstaande methoden te gebruiken voor het toevoegen van de Application Insights java script SDK aan uw toepassing. Als u de installatie op basis van NPM gebruikt, gebruik dan niet de installatie op basis van het fragment. Hetzelfde geldt voor het omgekeerde scenario wanneer u de methode op basis van het fragment gebruikt. gebruik niet ook de op NPM gebaseerde installatie. 

### <a name="npm-based-setup"></a>installatie op basis van NPM

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
```

### <a name="snippet-based-setup"></a>Installatie op basis van een fragment

Als uw app geen gebruik maakt van NPM, kunt u uw webpagina's rechtstreeks met Application Insights door middel van het plakken van dit fragment boven aan de pagina's. Dit moet bij voor keur het eerste script in uw `<head>` sectie zijn, zodat alle mogelijke problemen met al uw afhankelijkheden kunnen worden gecontroleerd. Als u een razendsnelle server-app gebruikt, voegt u het fragment boven aan het bestand `_Host.cshtml` toe in de sectie `<head>`.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Telemetrie verzenden naar de Azure Portal

Standaard verzamelt de Application Insights java script SDK een aantal telemetriegegevens die nuttig zijn bij het bepalen van de status van uw toepassing en de onderliggende gebruikers ervaring. Deze omvatten:

- Niet- **onderschepte uitzonde ringen** in uw app, inclusief informatie over
    - Stack tracering
    - Details van uitzonde ring en bericht dat de fout vergezelt
    - Regel & kolom aantal fouten
    - URL waar de fout is opgetreden
- Aanvragen voor **netwerk afhankelijkheden** die zijn gemaakt door uw app **xhr** en **Fetch** (de verzameling ophalen is standaard uitgeschakeld), bevatten informatie over
    - URL van afhankelijkheids bron
    - Opdracht & methode voor het aanvragen van de afhankelijkheid
    - Duur van de aanvraag
    - Resultaat code en succes status van de aanvraag
    - ID (indien van toepassing) van de gebruiker die de aanvraag maakt
    - Correlatie context (indien aanwezig) waar de aanvraag is ingediend
- **Gebruikers informatie** (bijvoorbeeld locatie, netwerk, IP)
- **Apparaatgegevens** (bijvoorbeeld browser, besturings systeem, versie, taal, resolutie, model)
- **Sessie gegevens**

### <a name="telemetry-initializers"></a>Initialisatie functies voor telemetrie
Initialisatie functies voor telemetrie worden gebruikt om de inhoud van de verzamelde telemetrie te wijzigen voordat ze worden verzonden vanuit de browser van de gebruiker. Ze kunnen ook worden gebruikt om te voor komen dat bepaalde telemetriegegevens worden verzonden door `false` te retour neren. U kunt meerdere telemetrie-initialisatie functies toevoegen aan uw Application Insights-exemplaar en ze worden uitgevoerd in de volg orde waarin ze worden toegevoegd.

Het invoer argument voor `addTelemetryInitializer` is een call back die een [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API.md#addTelemetryInitializer) als argument gebruikt en retourneert een `boolean` of `void`. Als `false` wordt geretourneerd, wordt het telemetrie-item niet verzonden. anders wordt het teruggestuurd naar de volgende telemetrie-initialisatie functie, indien van toepassing, of verzonden naar het eind punt van de telemetrie-verzameling.

Een voor beeld van het gebruik van telemetrie-initialisatie functies:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```
## <a name="configuration"></a>Configuratie
De meeste configuratie velden hebben de naam zo, dat ze standaard kunnen worden ingesteld op ONWAAR. Alle velden zijn optioneel, met uitzonde ring van `instrumentationKey`.

| Naam | Standaard | Beschrijving |
|------|---------|-------------|
| instrumentationKey | Null | **Vereist**<br>Instrumentatie sleutel die u hebt verkregen van de Azure Portal. |
| accountId | Null | Een optionele account-ID als uw app gebruikers in accounts groepeert. Geen spaties, komma's, punt komma's, is gelijk aan of verticale balken |
| sessionRenewalMs | 1800000 | Er wordt een sessie geregistreerd als de gebruiker gedurende deze tijd inactief is in milliseconden. De standaard waarde is 30 minuten |
| sessionExpirationMs | 86400000 | Een sessie wordt geregistreerd als deze de tijd in milliseconden heeft geduurd. De standaard waarde is 24 uur |
| maxBatchSizeInBytes | 10.000 | De maximale grootte van de telemetrie batch. Als een batch deze limiet overschrijdt, wordt deze onmiddellijk verzonden en wordt een nieuwe batch gestart |
| maxBatchInterval | 15.000 | Hoe lang batch-telemetrie voor verzen ding (in milliseconden) |
| disableExceptionTracking | onwaar | Als deze eigenschap waar is, worden uitzonde ringen niet verzameld. De standaardinstelling is onwaar. |
| disableTelemetry | onwaar | Indien waar, wordt de telemetrie niet verzameld of verzonden. De standaardinstelling is onwaar. |
| enableDebug | onwaar | Indien waar, worden **interne** fout opsporingsgegevens gegenereerd als een uitzonde ring **in plaats** van vastgelegd, ongeacht de instellingen voor de logboek registratie van de SDK. De standaardinstelling is onwaar. <br>***Opmerking:*** Als u deze instelling inschakelt, wordt de telemetrie verwijderd wanneer er een interne fout optreedt. Dit kan handig zijn voor het snel identificeren van problemen met uw configuratie of het gebruik van de SDK. Als u geen telemetriegegevens wilt verliezen tijdens het opsporen van fouten, kunt u het beste `consoleLoggingLevel` of `telemetryLoggingLevel` gebruiken in plaats van `enableDebug`. |
| loggingLevelConsole | 0 | Registreert **interne** Application Insights fouten in de console. <br>0: uit, <br>1: alleen kritieke fouten, <br>2: alles (fouten & waarschuwingen) |
| loggingLevelTelemetry | 1 | Hiermee worden **interne** Application Insights-fouten als telemetriegegevens verzonden. <br>0: uit, <br>1: alleen kritieke fouten, <br>2: alles (fouten & waarschuwingen) |
| diagnosticLogInterval | 10.000 | binnen Polling-interval (in MS) voor interne logboek registratie wachtrij |
| samplingPercentage | 100 | Het percentage gebeurtenissen dat wordt verzonden. De standaard waarde is 100, wat betekent dat alle gebeurtenissen worden verzonden. Stel deze waarde in als u uw gegevens limiet voor grootschalige toepassingen wilt behouden. |
| autoTrackPageVisitTime | onwaar | Indien true, op een pagina weergave, wordt de vorige gestuurde pagina tijd bijgehouden als telemetrie en wordt er een nieuwe timer gestart voor de huidige pagina weergave. De standaardinstelling is onwaar. |
| disableAjaxTracking | onwaar | Indien true, worden Ajax-aanroepen niet autoinnen. De standaardinstelling is onwaar. |
| disableFetchTracking | waar | Indien waar, worden aanvragen voor ophalen niet meer verzameld. De standaard waarde is True |
| overridePageViewDuration | onwaar | Als deze eigenschap waar is, wordt het standaard gedrag van trackPageView gewijzigd om het eind punt van de pagina weergave te registreren wanneer trackPageView wordt aangeroepen. Als deze eigenschap onwaar is en er geen aangepaste duur wordt gegeven aan trackPageView, wordt de prestaties van de pagina weergave berekend met behulp van de API voor navigatie tijd. De standaardinstelling is onwaar. |
| maxAjaxCallsPerView | 500 | Standaard 500-bepaalt hoeveel Ajax-aanroepen worden bewaakt per pagina weergave. Stel deze waarde in op-1 om alle Ajax-aanroepen (onbeperkt) op de pagina te bewaken. |
| disableDataLossAnalysis | waar | Indien onwaar, worden interne telemetriegegevens van de verzender bij het opstarten gecontroleerd op items die nog niet zijn verzonden. |
| disableCorrelationHeaders | onwaar | Als deze eigenschap onwaar is, voegt de SDK twee headers (' aanvraag-id ' en ' request-context ') toe aan alle afhankelijkheids aanvragen om ze te correleren met bijbehorende aanvragen aan de server zijde. De standaardinstelling is onwaar. |
| correlationHeaderExcludedDomains |  | Correlatie headers voor specifieke domeinen uitschakelen |
| correlationHeaderDomains |  | Correlatie headers voor specifieke domeinen inschakelen |
| disableFlushOnBeforeUnload | onwaar | De standaard waarde is False. Indien waar, wordt de methode flush niet aangeroepen wanneer onBeforeUnload gebeurtenis triggers |
| enableSessionStorageBuffer | waar | Standaard waarde waar. Als deze eigenschap waar is, wordt de buffer met alle niet-verzonden telemetrie opgeslagen in de sessie opslag. De buffer wordt hersteld bij het laden van pagina's |
| isCookieUseDisabled | onwaar | De standaard waarde is False. Als deze eigenschap waar is, worden er geen gegevens van cookies opgeslagen of gelezen met de SDK.|
| cookieDomain | Null | Aangepast cookie domein. Dit is handig als u Application Insights cookies wilt delen in subdomeinen. |
| isRetryDisabled | onwaar | De standaard waarde is False. Indien onwaar, opnieuw proberen op 206 (gedeeltelijk geslaagd), 408 (time-out), 429 (te veel aanvragen), 500 (interne server fout), 503 (service niet beschikbaar) en 0 (alleen offline, alleen indien gedetecteerd) |
| isStorageUseDisabled | onwaar | Als deze eigenschap waar is, worden er geen gegevens uit lokale en sessie opslag opgeslagen of gelezen met de SDK. De standaardinstelling is onwaar. |
| isBeaconApiDisabled | waar | Als deze eigenschap onwaar is, verzendt de SDK alle telemetrie met behulp van de [Beacon-API](https://www.w3.org/TR/beacon) |
| onunloadDisableBeacon | onwaar | De standaard waarde is False. Wanneer het tabblad wordt gesloten, verzendt de SDK alle resterende telemetrie met behulp van de [Beacon-API](https://www.w3.org/TR/beacon) |
| sdkExtension | Null | Hiermee stelt u de naam van de SDK-extensie. Alleen alfabetische tekens zijn toegestaan. De naam van de extensie wordt toegevoegd als een voor voegsel voor de tag AI. internal. sdkVersion (bijvoorbeeld ' ext_javascript: 2.0.0 '). De standaard waarde is null. |
| isBrowserLinkTrackingEnabled | onwaar | De standaardinstelling is onwaar. Indien waar, worden alle aanvragen voor [browser koppelingen](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) door de SDK bijgehouden. |
| appId | Null | AppId wordt gebruikt voor de correlatie tussen AJAX-afhankelijkheden die zich voordoen aan de client zijde met de aanvragen aan de server zijde. Als Beacon-API is ingeschakeld, kan deze niet automatisch worden gebruikt, maar deze kan hand matig worden ingesteld in de configuratie. De standaard waarde is null |
| enableCorsCorrelation | onwaar | Als deze eigenschap waar is, voegt de SDK twee headers (' aanvraag-id ' en ' request-context ') toe aan alle CORS-aanvragen voor het correleren van uitgaande AJAX-afhankelijkheden met bijbehorende aanvragen aan de server zijde. De standaard waarde is False |
| namePrefix | gedefinieerde | Een optionele waarde die wordt gebruikt als naam achtervoegsel voor localStorage en cookie naam.
| enableAutoRouteTracking | onwaar | Route wijzigingen automatisch bijhouden in toepassingen met één pagina (SPA). Indien waar, stuurt elke route wijziging een nieuwe pagina weergave naar Application Insights. Wijzigingen in de hash-route (`example.com/foo#bar`) worden ook vastgelegd als nieuwe pagina weergaven.
| enableRequestHeaderTracking | onwaar | Indien waar, worden AJAX-& aanvraag headers voor ophalen bijgehouden. de standaard waarde is False.
| enableResponseHeaderTracking | onwaar | Als deze waarde True is, wordt de antwoord headers voor de aanvraag van AJAX & ophalen bijgehouden. de standaard waarde is False.
| distributedTracingMode | `DistributedTracingModes.AI` | Hiermee stelt u de gedistribueerde traceer modus in. Als de AI_AND_W3C-modus of de W3C-modus is ingesteld, worden W3C-tracerings context headers (traceparent/tracestate) gegenereerd en opgenomen in alle uitgaande aanvragen. AI_AND_W3C wordt verzorgd voor back-compatibiliteit met verouderde services van Application Insights instrumented.

## <a name="single-page-applications"></a>Toepassingen met één pagina

Deze SDK verwerkt standaard **geen** route wijzigingen op basis van status die optreden in toepassingen met één pagina. Om het bijhouden van automatische route wijzigingen voor uw toepassing met één pagina in te scha kelen, kunt u `enableAutoRouteTracking: true` toevoegen aan uw installatie configuratie.

Op dit moment bieden we een afzonderlijke [reageer-invoeg toepassing](#react-extensions) die u kunt initialiseren met deze SDK. Ook wordt het bijhouden van route wijzigingen voor u uitgevoerd, evenals [andere gereageerde telemetrie](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md).

## <a name="react-extensions"></a>Extensies voor reageren

| Extensies |
|---------------|
| [Reageren](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [Systeem eigen](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Browser/gegevens van client zijde verkennen

De browser/client-side gegevens kunnen worden weer gegeven door te gaan naar **metrieken** en afzonderlijke metrieken toe te voegen: 

![](./media/javascript/page-view-load-time.png)

U kunt uw gegevens ook weer geven via de Java script-SDK via de browser ervaring in de portal.

Selecteer **browser** en kies vervolgens **fouten** of **prestaties**.

![](./media/javascript/browser.png)

### <a name="performance"></a>Prestaties 

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Afhankelijkheden

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analyse 

Als u een query wilt uitvoeren op uw telemetrie die is verzameld door de Java script-SDK, selecteert u de knop **weer gave op Logboeken (analyse)** Door de `client_Type == "Browser"`-instructie `where` toe te voegen, worden alleen gegevens van de Java script-SDK weer gegeven en worden de telemetrie aan de server zijde die door andere Sdk's is verzameld, uitgesloten.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Ondersteuning voor bron toewijzing

De minified-call stack van uw telemetrie van de uitzonde ring kan worden unminified in de Azure Portal. Alle bestaande integraties in het deel venster uitzonderings Details werken met de nieuwe unminified-opgeroepen procedures. Slepen en neerzetten unminifying ondersteunt alle bestaande en toekomstige JS-Sdk's (+ node. JS). u hoeft dus geen upgrade uit te voeren van uw SDK-versie. Als u uw unminified-Call stacks wilt weer geven,
1. Selecteer een telemetrie voor uitzonde ringen in de Azure Portal om de ' end-to-end-transactie details weer te geven '
2. Identificeer welke bron kaarten overeenkomen met deze aanroep stack. De bron toewijzing moet overeenkomen met het bron bestand van een stack frame, maar een achtervoegsel met `.map`
3. Sleep de bron kaarten en zet deze neer op de aanroep stack in de Azure Portal ![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web Basic

Voor een licht gewicht-ervaring kunt u in plaats daarvan de basis versie van Application Insights installeren
```
npm i --save @microsoft/applicationinsights-web-basic
```
Deze versie wordt geleverd met het minimale aantal functies en functionaliteiten en is afhankelijk van u om het te kunnen bouwen. Er wordt bijvoorbeeld geen verzameling (niet-onderschepte uitzonde ringen, AJAX, enzovoort) uitgevoerd. De Api's voor het verzenden van bepaalde typen telemetrie, zoals `trackTrace`, `trackException`, enzovoort, zijn niet opgenomen in deze versie, dus u moet uw eigen wrapper opgeven. De enige API die beschikbaar is, is `track`. Hier vindt u een voor [beeld](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) .

## <a name="examples"></a>Voorbeelden

Voor voor beelden van uitvoer bare raadpleegt u [Application Insights java script SDK](https://github.com/topics/applicationinsights-js-demo) -voor beelden

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Een upgrade uitvoeren van de oude versie van Application Insights

Belang rijke wijzigingen in de SDK v2-versie:
- Als u betere API-hand tekeningen wilt toestaan, zijn een aantal van de API-aanroepen, zoals trackPageView, trackException bijgewerkt. Uitvoeren in IE8 of lagere versies van de browser wordt niet ondersteund.
- De telemetrie-envelop heeft veld naam-en structuur wijzigingen door gegevens schema-updates.
- @No__t-0 is verplaatst naar `context.telemetryTrace`. Sommige velden zijn ook gewijzigd (`operation.id` @ no__t-1 @ no__t-2)
  - Als u de huidige pagina weergave-ID hand matig wilt vernieuwen (bijvoorbeeld in SPA-apps), kunt u dit doen met `appInsights.properties.context.telemetryTrace.traceID = Util.newId()`

Als u de huidige Application Insights PRODUCTION SDK (1.0.20) gebruikt en wilt zien of de nieuwe SDK in runtime werkt, werkt u de URL bij, afhankelijk van het huidige scenario voor het laden van de SDK.

- Down load via CDN scenario: werk het code fragment dat u momenteel gebruikt om naar de volgende URL te verwijzen:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- NPM-scenario: roep `downloadAndSetup` aan om het volledige ApplicationInsights-script uit CDN te downloaden en Initialiseer het met de instrumentatie sleutel:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Test in interne omgeving om te controleren of telemetrie van de bewaking naar verwachting werkt. Als dat niet het geval is, werkt u de API-hand tekeningen goed bij naar SDK versie 2 en implementeert u deze in uw productie omgevingen.

## <a name="sdk-performanceoverhead"></a>SDK-prestaties/overhead

Application Insights een verschuldigde hoeveelheid loadtime aan uw website toevoegen, zodat er slechts ongeveer 25 KB gzipped is. Met behulp van het fragment worden minimale onderdelen van de bibliotheek snel geladen. Ondertussen wordt het volledige script gedownload op de achtergrond.

Tijdens het downloaden van het script uit het CDN, wordt het bijhouden van de pagina in een wachtrij geplaatst. Zodra het gedownloade script asynchroon is geïnitialiseerd, worden alle gebeurtenissen bijgehouden die in de wachtrij zijn geplaatst. Als gevolg hiervan zult u geen telemetrie kwijt raken tijdens de hele levens cyclus van de pagina. Dit installatie proces voorziet uw pagina van een naadloos analyse systeem, dat niet zichtbaar is voor uw gebruikers.

> Overzicht
> - **25 KB** gzipped
> - **15 MS** algemene initialisatie tijd
> - Bijhouden van **nul** gemist tijdens de levens cyclus van de pagina

## <a name="browser-support"></a>Browser ondersteuning

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Nieuwste ✔ | Nieuwste ✔ | 9 + ✔ | Nieuwste ✔ | Nieuwste ✔ |

## <a name="open-source-sdk"></a>Open-Source-SDK

De Application Insights java script SDK is open source om de bron code weer te geven of om bij te dragen aan het project Ga naar de [officiële github-opslag plaats](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="next"></a> Volgende stappen
* [Bijhouden van gebruik](usage-overview.md)
* [Aangepaste gebeurtenissen en metrische gegevens](api-custom-events-metrics.md)
* [Bouwen-meten-leren](usage-overview.md)

