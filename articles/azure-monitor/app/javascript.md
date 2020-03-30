---
title: Azure Application Insights voor JavaScript-webapps
description: Ontvang paginaweergave- en sessietellingen, webclientgegevens, toepassingen met één pagina (SPA) en houd gebruikspatronen bij. Detecteer uitzonderingen en prestatieproblemen in JavaScript-webpagina's.
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: 5414a70180a82be8253dace7d800c90c1ae6a9bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276073"
---
# <a name="application-insights-for-web-pages"></a>Application Insights voor webpagina’s

Krijg inzicht in de prestaties en het gebruik van uw webpagina's of app. Als u [Application Insights](app-insights-overview.md) toevoegt aan uw paginascript, krijgt u timings van paginabelastingen en AJAX-oproepen, tellingen en details van browseruitzonderingen en AJAX-fouten, evenals gebruikers en sessietellingen. Al deze gegevens kunnen worden gesegmenteerd op pagina, clientbesturingssysteem en browserversie, geografische locatie en andere dimensies. U kunt waarschuwingen instellen voor foutaantallen of het langzaam laden van de pagina. En door het invoegen van trace-aanroepen in JavaScript-code, kunt u bijhouden hoe de verschillende functies van uw webpaginatoepassing worden gebruikt.

Application Insights kan met elke webpagina worden gebruikt. Het enige wat u hiervoor hoeft te doen, is een klein stukje JavaScript toevoegen. Als uw webservice [Java](java-get-started.md) of [ASP.NET](asp-net.md)is, u de SDK's aan de serverzijde gebruiken in combinatie met de JavaScript SDK aan de clientzijde om een end-to-end inzicht te krijgen in de prestaties van uw app.

## <a name="adding-the-javascript-sdk"></a>De JavaScript SDK toevoegen

1. Eerst hebt u een Application Insights-bron nodig. Als u nog geen resource- en instrumentatiesleutel hebt, volgt u de [nieuwe resourceinstructies maken.](create-new-resource.md)
2. Kopieer de instrumentatiesleutel uit de bron waar u uw JavaScript-telemetrie wilt verzonden.
3. Voeg de Application Insights JavaScript SDK toe aan uw webpagina of app via een van de volgende twee opties:
    * [npm-installatie](#npm-based-setup)
    * [JavaScript-fragment](#snippet-based-setup)

> [!IMPORTANT]
> Gebruik slechts één methode om de JavaScript SDK aan uw toepassing toe te voegen. Als u de NPM-installatie gebruikt, gebruikt u het Fragment niet en vice versa.

> [!NOTE]
> NPM Setup installeert de JavaScript SDK als afhankelijkheid van uw project, waarbij IntelliSense wordt in- en inschakelt, terwijl het Fragment de SDK tijdens runtime ophaalt. Beide ondersteunen dezelfde functies. Ontwikkelaars die meer aangepaste gebeurtenissen en configuratie wensen, kiezen echter over het algemeen voor NPM Setup, terwijl gebruikers die op zoek zijn naar een snelle enablement van out-of-the-box webanalytics kiezen voor het Fragment.

### <a name="npm-based-setup"></a>npm-gebaseerde installatie

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Installatie op basis van snippet

Als uw app geen npm gebruikt, u uw webpagina's rechtstreeks met Application Insights implementeren door dit fragment boven aan elk van uw pagina's te plakken. Bij voorkeur moet het het eerste `<head>` script in uw sectie, zodat het eventuele problemen met al uw afhankelijkheden kan controleren. Als u Blazor Server App gebruikt, voegt u `_Host.cshtml` het `<head>` fragment boven aan het bestand in de sectie toe.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Telemetrie verzenden naar de Azure-portal

Standaard verzamelt de Application Insights JavaScript SDK automatisch een aantal telemetrie-items die nuttig zijn bij het bepalen van de status van uw toepassing en de onderliggende gebruikerservaring. Deze omvatten:

- **Niet-gevangen uitzonderingen** in uw app, inclusief informatie over
    - Stapeltracering
    - Uitzonderingsgegevens en bericht bij de fout
    - Lijn & kolomaantal fouten
    - URL waar fout is verhoogd
- **Netwerkafhankelijkheidsverzoeken** van uw app **XHR** en **Ophalen** (ophalen is standaard uitgeschakeld) aanvragen, bevatten informatie over
    - Url van afhankelijkheidsbron
    - Opdracht & methode die wordt gebruikt om de afhankelijkheid op te vragen
    - Duur van het verzoek
    - Resultaatcode en successtatus van de aanvraag
    - ID (indien aanwezig) van de gebruiker die het verzoek indient
    - Correlatiecontext (indien aanwezig) wanneer het verzoek wordt ingediend
- **Gebruikersinformatie** (bijvoorbeeld locatie, netwerk, IP)
- **Apparaatinformatie** (bijvoorbeeld browser, besturingssysteem, versie, taal, resolutie, model)
- **Sessiegegevens**

### <a name="telemetry-initializers"></a>Telemetrieinitialisten
Telemetrieinitialisten worden gebruikt om de inhoud van de verzamelde telemetrie te wijzigen voordat ze vanuit de browser van de gebruiker worden verzonden. Ze kunnen ook worden gebruikt om te voorkomen `false`dat bepaalde telemetrie wordt verzonden, door terug te keren. Meerdere telemetrieinitialisators kunnen worden toegevoegd aan uw instantie Application Insights en worden uitgevoerd in volgorde van toevoeging ervan.

Het invoerargument `addTelemetryInitializer` voor is een [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) callback die een `boolean` `void`als argument neemt en een of . Als `false`het telemetrie-item wordt teruggestuurd, wordt het artikel niet verzonden, anders gaat het over naar de eventuele telemetrieinitialisator of wordt het naar het eindpunt van de telemetrieverzameling verzonden.

Een voorbeeld van het gebruik van telemetrieinitialisatoren:
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
De meeste configuratievelden hebben de naam zodanig dat ze standaard kunnen worden uitgevoerd als false. Alle velden zijn `instrumentationKey`optioneel, behalve .

| Name | Standaard | Beschrijving |
|------|---------|-------------|
| instrumentatieSleutel | null | **Vereist**<br>Instrumentatiesleutel die u hebt verkregen van de Azure-portal. |
| accountId | null | Een optionele account-id als uw app gebruikers groepeert in accounts. Geen spaties, komma's, puntkomma's, gelijken of verticale balken |
| sessionRenewalMs | 1800000 | Een sessie wordt geregistreerd als de gebruiker inactief is voor deze hoeveelheid tijd in milliseconden. Standaard is 30 minuten |
| sessionExpirationMs | 86400000 | Een sessie wordt geregistreerd als deze gedurende deze tijd in milliseconden is voortgezet. Standaard is 24 uur |
| maxBatchSizeInBytes | 10.000 | Maximale grootte van telemetrie batch. Als een batch deze limiet overschrijdt, wordt deze onmiddellijk verzonden en wordt een nieuwe batch gestart |
| maxBatchInterval | 15.000 | Hoe lang de batch telemetrie voor voor voor het verzenden (milliseconden) |
| ExceptionTracking uitschakelen | false | Als dit waar is, worden uitzonderingen niet automatisch verzameld. De standaardinstelling is onwaar. |
| uitschakelenTelemetrie | false | Als dit waar is, wordt telemetrie niet verzameld of verzonden. De standaardinstelling is onwaar. |
| inschakelenFoutopsporing | false | Als dit waar is, worden **interne** foutopsporingsgegevens als uitzondering gegooid **in plaats** van te worden geregistreerd, ongeacht de instellingen voor SDK-logboekregistratie. De standaardinstelling is onwaar. <br>***Let op:*** Als u deze instelling inschakelt, wordt telemetrie verloren wanneer er een interne fout optreedt. Dit kan handig zijn om problemen met uw configuratie of gebruik van de SDK snel te identificeren. Als u geen telemetrie wilt verliezen tijdens `consoleLoggingLevel` het `telemetryLoggingLevel` debuggen, u overwegen om te gebruiken of in plaats van `enableDebug`. |
| logboekenLevelConsole | 0 | Logt **interne** Application Insights-fouten op de console. <br>0: uit, <br>1: Alleen kritieke fouten, <br>2: Alles (fouten & waarschuwingen) |
| loggingLevelTelemetry | 1 | Hiermee worden **interne** fouten op application insights als telemetrie verzendt. <br>0: uit, <br>1: Alleen kritieke fouten, <br>2: Alles (fouten & waarschuwingen) |
| diagnosticLogInterval | 10.000 | (intern) Polling-interval (in ms) voor interne logboekwachtrij |
| bemonsteringPercentage | 100 | Percentage gebeurtenissen dat wordt verzonden. Standaard is 100, wat betekent dat alle gebeurtenissen worden verzonden. Stel dit in als u uw gegevenslimiet voor grootschalige toepassingen wilt behouden. |
| autoTrackPageVisitTime | false | Als dit waar is, wordt in een paginaweergave de weergavetijd van de vorige instrumented-pagina bijgehouden en verzonden als telemetrie en wordt een nieuwe timer gestart voor de huidige paginaweergave. De standaardinstelling is onwaar. |
| AjaxTracking uitschakelen | false | Als dat waar is, worden Ajax-oproepen niet automatisch verzameld. De standaardinstelling is onwaar. |
| fetchfetchtracking uitschakelen | waar | Als dit waar is, worden fetch-aanvragen niet automatisch verzameld. Standaard is waar |
| overschrijvenPageViewDuration | false | Als dit waar is, wordt het standaardgedrag van trackPageView gewijzigd in het einde van het interval van de duur van de paginaweergave wanneer trackPageView wordt aangeroepen. Als er onwaar en geen aangepaste duur is opgegeven om PageView bij te houden, wordt de prestaties van de paginaweergave berekend met behulp van de API voor navigatietiming. De standaardinstelling is onwaar. |
| maxAjaxCallsPerView | 500 | Standaard 500 - bepaalt hoeveel Ajax-oproepen per paginaweergave worden gecontroleerd. Ingesteld op -1 om alle (onbeperkte) Ajax-oproepen op de pagina te volgen. |
| gegevensverliesuitschakelen | waar | Als deze niet waar is, worden interne buffers voor telemetrieafzenders bij het opstarten gecontroleerd op items die nog niet zijn verzonden. |
| CorrelationHeaders uitschakelen | false | Als deze niet klopt, voegt de SDK twee kopteksten ('Request-Id' en 'Request-Context') toe aan alle afhankelijkheidsverzoeken om ze te correleren met bijbehorende verzoeken aan de serverzijde. De standaardinstelling is onwaar. |
| correlatieHeaderExcludedDomains |  | Correlatiekoppen voor specifieke domeinen uitschakelen |
| correlatieHeaderDomains |  | Correlatiekoppen inschakelen voor specifieke domeinen |
| disableFlushOnBeforeUnload disableFlushOnBeforeUnload disableFlushOnBeforeUnload disableFlush | false | Standaard false. Als dit waar is, wordt de spoelmethode niet aangeroepen wanneer de gebeurtenistriggers voor onBeforeUnload worden geactiveerd |
| inschakelingsessionStorageBuffer | waar | Standaard waar. Als dit waar is, wordt de buffer met alle niet-verzonden telemetrie opgeslagen in sessieopslag. De buffer wordt hersteld op paginabelasting |
| isCookieUseDisabled | false | Standaard false. Als dit waar is, slaat de SDK geen gegevens van cookies op of leest deze deze niet.|
| cookieDomein | null | Aangepast cookiedomein. Dit is handig als u Cookies van Application Insights wilt delen in verschillende subdomeinen. |
| isRetryDisabled | false | Standaard false. Als false, probeer dan opnieuw op 206 (gedeeltelijk succes), 408 (time-out), 429 (te veel aanvragen), 500 (interne serverfout), 503 (service niet beschikbaar) en 0 (offline, alleen als deze wordt gedetecteerd) |
| isStorageUseDisabled | false | Als dit waar is, slaat de SDK geen gegevens op van lokale en sessieopslag. De standaardinstelling is onwaar. |
| isBeaconApiDisabled | waar | Als deze niet klopt, stuurt de SDK alle telemetrie via de [Beacon API](https://www.w3.org/TR/beacon) |
| onunloadDisableBeacon | false | Standaard false. wanneer het tabblad is gesloten, stuurt de SDK alle resterende telemetrie via de [Beacon API](https://www.w3.org/TR/beacon) |
| sdkExtension | null | Hiermee stelt u de naam van de sdk-extensie in. Alleen alfabetische tekens zijn toegestaan. De extensienaam wordt toegevoegd als voorvoegsel aan de tag 'ai.internal.sdkVersion' (bijvoorbeeld 'ext_javascript:2.0.0'). Standaard is null. |
| isBrowserLinkTrackingIngeschakeld | false | De standaardinstelling is onwaar. Als dit waar is, volgt de SDK alle [browserkoppelingsverzoeken.](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) |
| appId | null | AppId wordt gebruikt voor de correlatie tussen AJAX-afhankelijkheden die aan de clientzijde plaatsvinden met de server-side aanvragen. Wanneer Beacon API is ingeschakeld, kan deze niet automatisch worden gebruikt, maar kan deze handmatig worden ingesteld in de configuratie. Standaard is null |
| enableCorsCorrelation | false | Als dit waar is, voegt de SDK twee headers ('Request-Id' en 'Request-Context') toe aan alle CORS-verzoeken om uitgaande AJAX-afhankelijkheden te correleren met bijbehorende verzoeken aan de serverzijde. Standaard is false |
| naamVoorvoegsel | Ongedefinieerde | Een optionele waarde die wordt gebruikt als naampostfix voor localStorage en cookienaam.
| autoroutetracking inschakelen | false | Automatisch routewijzigingen bijhouden in toepassingen met één pagina (SPA). Als dit waar is, stuurt elke routewijziging een nieuwe paginaweergave naar Toepassingsstatistieken. Wijzigingen in`example.com/foo#bar`hashroutes ( ) worden ook geregistreerd als nieuwe paginaweergaven.
| enableRequestHeaderTracking inschakelen | false | Als dit waar is, worden de headers van ajax & fetch-aanvraag bijgehouden, is de standaardwaarde onjuist.
| responseheadertracking inschakelen | false | Als dit waar is, worden de antwoordkoppen van Ajax & Fetch-verzoek bijgehouden, is standaard de standaardfout.
| distributedTracingMode distributedTracingMode | `DistributedTracingModes.AI` | Hiermee stelt u de gedistribueerde traceringsmodus in. Als AI_AND_W3C modus of W3C-modus is ingesteld, worden W3C trace context headers (traceparent/tracestate) gegenereerd en opgenomen in alle uitgaande aanvragen. AI_AND_W3C is voorzien voor back-compatibiliteit met alle verouderde Application Insights-geinstrumenteerde services.

## <a name="single-page-applications"></a>Toepassingen met één pagina

Standaard verwerkt deze SDK **geen** op de status gebaseerde routewijziging die optreedt in toepassingen met één pagina. Als u automatische routewijzigingstracking voor uw `enableAutoRouteTracking: true` toepassing met één pagina wilt inschakelen, u deze toevoegen aan uw installatieconfiguratie.

Momenteel bieden we een aparte [React plugin](#react-extensions) aan die je initialiseren met deze SDK. Het zal ook route verandering tracking voor u te bereiken, evenals het verzamelen [van andere React specifieke telemetrie](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md).

## <a name="react-extensions"></a>Extensies reageren

| Extensies |
|---------------|
| [Reageren](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [Reageren Native](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Browser-/client-side gegevens verkennen

Browser-/client-side gegevens kunnen worden bekeken door naar **Statistieken** te gaan en individuele statistieken toe te voegen waarin u geïnteresseerd bent:

![](./media/javascript/page-view-load-time.png)

U uw gegevens van de JavaScript SDK ook bekijken via de browserervaring in de portal.

Selecteer **Browser** en kies **Vervolgens Fouten** of **Prestaties**.

![](./media/javascript/browser.png)

### <a name="performance"></a>Prestaties

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Afhankelijkheden

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analyse

Als u uw telemetrie wilt opvragen die is verzameld door de JavaScript SDK, selecteert u de knop **Weergave in logboeken (Analytics).** Door het `where` toevoegen `client_Type == "Browser"`van een verklaring van , ziet u alleen gegevens van de JavaScript SDK en alle server-side telemetrie verzameld door andere SDK's zal worden uitgesloten.
 
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

### <a name="source-map-support"></a>Ondersteuning voor bronkaarten

De minified callstack van uw uitzonderingstelemetrie kan worden onminificeerd in de Azure-portal. Alle bestaande integraties in het deelvenster Uitzonderingsdetails werken met de nieuw geminte callstack.

#### <a name="link-to-blob-storage-account"></a>Koppeling naar Blob-opslagaccount

U uw Application Insights-bron koppelen aan uw eigen Azure Blob Storage-container om oproepstapels automatisch te verwijderen. Zie [ondersteuning voor automatische bronkaarten](./source-map-support.md)om aan de slag te gaan.

### <a name="drag-and-drop"></a>Slepen en neerzetten

1. Selecteer een uitzonderingstelemetrieitem in de Azure-portal om de 'End-to-end transactiedetails' weer te geven.
2. Bepaal welke bronkaarten overeenkomen met deze oproepstack. De bronkaart moet overeenkomen met het bronbestand van een stapelframe, maar`.map`
3. Sleep en drop de bronkaarten naar de oproepstack in de Azure-portal![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web Basic

Voor een lichtgewicht ervaring u in plaats daarvan de basisversie van Application Insights installeren
```
npm i --save @microsoft/applicationinsights-web-basic
```
Deze versie wordt geleverd met het absolute minimum aantal functies en functionaliteiten en vertrouwt op u om het op te bouwen zoals u dat wilt. Bijvoorbeeld, het voert geen autocollection (niet-gevangen uitzonderingen, AJAX, enz.). De API's om bepaalde telemetrietypen te verzenden, zoals `trackTrace`, `trackException`enz., zijn niet opgenomen in deze versie, dus u moet uw eigen wrapper bieden. De enige API die `track`beschikbaar is, is . Een [monster](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) is hier te vinden.

## <a name="examples"></a>Voorbeelden

Zie [Application Insights JavaScript SDK-voorbeelden](https://github.com/topics/applicationinsights-js-demo) voor bruikbare voorbeelden

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Upgraden van de oude versie van Application Insights

Wijzigingen in de SDK V2-versie verbreken:
- Om betere API-handtekeningen mogelijk te maken, zijn sommige API-aanroepen, zoals trackPageView en trackException, bijgewerkt. Deze wordt uitgevoerd in Internet Explorer 8 en eerdere versies van de browser wordt niet ondersteund.
- De telemetrieenvelop heeft veldnaam- en structuurwijzigingen als gevolg van updates van het gegevensschema.
- Verplaatst `context.operation` `context.telemetryTrace`naar . Sommige velden werden`operation.id` --> `telemetryTrace.traceID`ook gewijzigd ( ).
  - Als u de huidige paginaweergave-ID handmatig wilt vernieuwen `appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()`(bijvoorbeeld in SPA-apps), gebruikt u .
    > [!NOTE]
    > Om de trace-ID uniek te `Util.newId()`houden, `Util.generateW3CId()`waar u eerder hebt gebruikt, gebruikt u nu . Beide uiteindelijk uiteindelijk wordt de operatie-ID.

Als u de huidige toepassingsinzichten PRODUCTIE SDK (1.0.20) gebruikt en wilt zien of de nieuwe SDK in runtime werkt, werkt u de URL bij, afhankelijk van het huidige SDK-laadscenario.

- Downloaden via CDN-scenario: werk het codefragment bij dat u momenteel gebruikt om de volgende URL aan te wijzen:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- npm-scenario: `downloadAndSetup` Bel om het volledige ApplicationInsights-script van CDN te downloaden en te initialiseren met instrumentatietoets:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Test in interne omgeving om te controleren of de controle telemetrie werkt zoals verwacht. Als alles werkt, werkt u uw API-handtekeningen op de juiste manier bij naar de SDK V2-versie en implementeert u deze in uw productieomgevingen.

## <a name="sdk-performanceoverhead"></a>SDK-prestaties/overhead

Op slechts 25 KB gzipped, en het nemen van slechts ~ 15 ms te initialiseren, Application Insights voegt een verwaarloosbare hoeveelheid laadtijd aan uw website. Door het fragment te gebruiken, worden minimale onderdelen van de bibliotheek snel geladen. In de tussentijd wordt het volledige script op de achtergrond gedownload.

Terwijl het script wordt gedownload van het CDN, wordt alle tracking van uw pagina in de wachtrij geplaatst. Zodra het gedownloade script asynchroon initialiseren is voltooid, worden alle gebeurtenissen die in de wachtrij stonden, bijgehouden. Als gevolg hiervan verliest u geen telemetrie gedurende de gehele levenscyclus van uw pagina. Dit installatieproces biedt uw pagina een naadloos analysesysteem, onzichtbaar voor uw gebruikers.

> Samenvatting:
> - **25 KB** gzipped
> - **15 ms** totale initialisatietijd
> - **Nul** tracking gemist tijdens de levenscyclus van de pagina

## <a name="browser-support"></a>Browserondersteuning

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Chrome Laatste ✔ |  Firefox Laatste ✔ | IE 9+ & Edge ✔ | Opera Laatste ✔ | Safari Laatste ✔ |

## <a name="open-source-sdk"></a>Open-source SDK

De Application Insights JavaScript SDK is open-source om de broncode te bekijken of om bij te dragen aan het project bezoek de [officiële GitHub repository](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="next-steps"></a><a name="next"></a>Volgende stappen
* [Bijhouden van gebruik](usage-overview.md)
* [Aangepaste gebeurtenissen en metrische gegevens](api-custom-events-metrics.md)
* [Bouwen-meten-leren](usage-overview.md)
