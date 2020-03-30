---
title: Gebruiksanalyse met Azure Application Insights | Microsoft-documenten
description: Begrijp uw gebruikers en wat ze met uw app doen.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: e964b1b5b9d5500f2d9f24ed765299389e6dbbb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283953"
---
# <a name="usage-analysis-with-application-insights"></a>Gebruiksanalyse met Application Insights

Welke functies van uw web of mobiele app zijn het populairst? Bereiken uw gebruikers hun doelen met uw app? Vallen ze af op bepaalde punten, en komen ze later terug?  [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) helpt u krachtige inzichten te krijgen in hoe mensen uw app gebruiken. Elke keer dat u uw app bijwerkt, u beoordelen hoe goed het werkt voor gebruikers. Met deze kennis u datagedreven beslissingen nemen over uw volgende ontwikkelingscycli.

## <a name="send-telemetry-from-your-app"></a>Telemetrie verzenden vanuit uw app

De beste ervaring wordt verkregen door Application Insights te installeren, zowel in uw app-servercode als in uw webpagina's. De client- en serveronderdelen van uw app sturen telemetrie terug naar de Azure-portal voor analyse.

1. **Servercode:** Installeer de juiste module voor uw [ASP.NET,](../../azure-monitor/app/asp-net.md) [Azure,](../../azure-monitor/app/app-insights-overview.md) [Java,](../../azure-monitor/app/java-get-started.md) [Node.js](../../azure-monitor/app/nodejs.md)of [andere](../../azure-monitor/app/platforms.md) app.

    * *Wilt u geen servercode installeren? Maak [gewoon een Azure Application Insights-bron.](../../azure-monitor/app/create-new-resource.md )*

2. **Webpaginacode:** Voeg het volgende script toe aan ``</head>``uw webpagina voordat u het slot sluit . Vervang de instrumentatiesleutel door de juiste waarde voor uw Application Insights-bron:
    
    ```html
    <script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
    </script>
    ```

    Bekijk het [Referentieartikel JavaScript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript)voor meer geavanceerde configuraties voor het bewaken van websites.

3. **Mobiele app-code:** Gebruik de App Center SDK om gebeurtenissen uit uw app te verzamelen en stuur vervolgens kopieën van deze gebeurtenissen naar Application Insights voor analyse door [deze handleiding te volgen.](../../azure-monitor/learn/mobile-center-quickstart.md)

4. **Ontvang telemetrie:** Voer uw project een paar minuten uit in de foutopsporingsmodus en zoek vervolgens naar resultaten in het overzichtsblad in Application Insights.

    Publiceer uw app om de prestaties van uw app te controleren en te ontdekken wat uw gebruikers met uw app doen.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Gebruikers- en sessie-id opnemen in uw telemetrie
Om gebruikers in de loop van de tijd te kunnen volgen, vereist Application Insights een manier om ze te identificeren. Het gereedschap Gebeurtenissen is het enige hulpprogramma voor gebruik waarvoor geen gebruikersnaam of sessie-id vereist is.

Begin met het verzenden van gebruikers- en sessie-id's met behulp van [dit proces](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Bekijk de demografische gegevens en statistieken van het gebruik
Ontdek wanneer mensen uw app gebruiken, in welke pagina's ze het meest geïnteresseerd zijn, waar uw gebruikers zich bevinden, welke browsers en besturingssystemen ze gebruiken. 

De rapporten Gebruikers en sessies filteren uw gegevens op pagina's of aangepaste gebeurtenissen en segmenteren deze op eigenschappen zoals locatie, omgeving en pagina. U ook uw eigen filters toevoegen.

![Gebruikers](./media/usage-overview/users.png)  

Inzichten aan de rechterkant wijzen op interessante patronen in de set van gegevens.  

* Het rapport **Gebruikers** telt het aantal unieke gebruikers dat toegang heeft tot uw pagina's binnen de door u gekozen tijdsperioden. Voor web-apps worden gebruikers geteld met behulp van cookies. Als iemand toegang heeft tot uw site met verschillende browsers of clientmachines, of zijn cookies verwijdert, worden deze cookies meer dan één keer geteld.
* Het rapport **Sessies** telt het aantal gebruikerssessies dat toegang heeft tot uw site. Een sessie is een periode van activiteit door een gebruiker, beëindigd door een periode van inactiviteit van meer dan een half uur.

[Meer informatie over de tools voor gebruikers, sessies en evenementen](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Retentie - hoeveel gebruikers komen terug?

Retentie helpt u te begrijpen hoe vaak uw gebruikers terugkeren om hun app te gebruiken, op basis van cohorten gebruikers die tijdens een bepaalde tijdsemmer zakelijke actie hebben uitgevoerd. 

- Begrijpen welke specifieke functies ervoor zorgen dat gebruikers meer terugkomen dan anderen 
- Vormhypothesen op basis van echte gebruikersgegevens 
- Bepalen of retentie een probleem is in uw product 

![Bewaartermijn](./media/usage-overview/retention.png) 

Met de bewaarbesturingselementen bovenaan u specifieke gebeurtenissen en tijdsbereik definiëren om retentie te berekenen. De grafiek in het midden geeft een visuele weergave van het totale retentiepercentage tegen het opgegeven tijdsbereik. De grafiek aan de onderkant staat voor individuele retentie in een bepaalde periode. Met dit detailniveau u begrijpen wat uw gebruikers doen en wat van invloed kan zijn op terugkerende gebruikers op een meer gedetailleerde granulariteit.  

[Meer informatie over het gereedschap Retentie](usage-retention.md)

## <a name="custom-business-events"></a>Aangepaste zakelijke evenementen

Om een duidelijk inzicht te krijgen in wat gebruikers met uw app doen, is het handig om regels code in te voegen om aangepaste gebeurtenissen te registreren. Deze gebeurtenissen kunnen alles bijhouden, van gedetailleerde gebruikersacties, zoals het klikken op specifieke knoppen, tot belangrijkere zakelijke evenementen, zoals het doen van een aankoop of het winnen van een game. 

Hoewel paginaweergaven in sommige gevallen nuttige gebeurtenissen kunnen vertegenwoordigen, is dit in het algemeen niet waar. Een gebruiker kan een productpagina openen zonder het product te kopen. 

Met specifieke zakelijke gebeurtenissen u de voortgang van uw gebruikers via uw site in kaart brengen. U hun voorkeuren voor verschillende opties te vinden, en waar ze afhaken of problemen hebben. Met deze kennis u weloverwogen beslissingen nemen over de prioriteiten in uw ontwikkelingsachterstand.

Gebeurtenissen kunnen worden geregistreerd vanaf de clientzijde van de app:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Of vanaf de serverkant:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

U eigenschapswaarden aan deze gebeurtenissen koppelen, zodat u de gebeurtenissen filteren of splitsen wanneer u ze in de portal inspecteert. Bovendien is aan elke gebeurtenis een standaardset met eigenschappen gekoppeld, zoals een anonieme gebruikers-id, waarmee u de volgorde van de activiteiten van een individuele gebruiker traceren.

Meer informatie over [aangepaste gebeurtenissen](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) en [eigenschappen](../../azure-monitor/app/api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Slice en dobbelstenen evenementen

In de hulpprogramma's Gebruikers, Sessies en Gebeurtenissen u aangepaste gebeurtenissen segmenteren en dobbelen op basis van gebruiker, gebeurtenisnaam en eigenschappen.
![Gebruikers](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>De telemetrie met de app ontwerpen

Wanneer u elke functie van uw app ontwerpt, moet u overwegen hoe u het succes ervan met uw gebruikers gaat meten. Bepaal welke zakelijke gebeurtenissen u moet vastleggen en codeer de trackingcalls voor deze gebeurtenissen vanaf het begin in uw app.

## <a name="a--b-testing"></a>A | B Testen
Als u niet weet welke variant van een functie succesvoller zal zijn, laat u ze allebei los, waardoor ze allemaal toegankelijk zijn voor verschillende gebruikers. Meet het succes van elk, en ga dan naar een uniforme versie.

Voor deze techniek koppelt u verschillende eigenschapswaarden aan alle telemetrie die door elke versie van uw app wordt verzonden. U dat doen door eigenschappen te definiëren in de actieve TelemetrieContext. Deze standaardeigenschappen worden toegevoegd aan elk telemetriebericht dat de toepassing verzendt - niet alleen uw aangepaste berichten, maar ook de standaardtelemetrie.

Filter en spliter uw gegevens in de portal Application Insights op de eigenschapswaarden, zodat de verschillende versies worden vergeleken.

Stel hiervoor [een telemetrieinitialisator in:](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)

**ASP.NET-apps**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

In de web-app initializer zoals Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**ASP.NET Core-apps**

> [!NOTE]
> Het toevoegen `ApplicationInsights.config` van `TelemetryConfiguration.Active` initialisator met behulp van of het gebruik is niet geldig voor ASP.NET Core-toepassingen. 

Voor [ASP.NET Core-toepassingen](asp-net-core.md#adding-telemetryinitializers) `TelemetryInitializer` wordt het toevoegen van een nieuwe gedaan door deze toe te voegen aan de container Dependency Injection, zoals hieronder wordt weergegeven. Dit gebeurt `ConfigureServices` in de `Startup.cs` methode van uw klas.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Alle nieuwe TelemetrieClients voegen automatisch de eigenschapswaarde toe die u opgeeft. Afzonderlijke telemetriegebeurtenissen kunnen de standaardwaarden overschrijven.

## <a name="next-steps"></a>Volgende stappen
   - [Gebruikers, sessies, gebeurtenissen](usage-segmentation.md)
   - [Trechters](usage-funnels.md)
   - [Retentie](usage-retention.md)
   - [Gebruikersstromen](usage-flows.md)
   - [Werkmappen](../../azure-monitor/app/usage-workbooks.md)
   - [Gebruikerscontext toevoegen](usage-send-user-context.md)
