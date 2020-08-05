---
title: 'Quickstart: Websites bewaken met Azure Monitor Application Insights'
description: In deze quickstart krijgt u informatie over het instellen van client/browser-functies voor het controleren van websites met Azure Monitor Application Insights.
ms.topic: quickstart
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: eb6c0a8319257949cee8f35be6cdfac22f1fe5d4
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323431"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Quickstart: Uw website bewaken met Azure Monitor Application Insights

In deze quickstart leert u hoe u de opensource Application Insights JavaScript-SDK aan uw website kunt toevoegen. Ook leert u hoe meer inzicht krijgt in de client-/browserervaring voor bezoekers van uw website.

Met Azure Monitor Application Insights kunt u eenvoudig de beschikbaarheid, de prestaties en het gebruik van een website controleren. U kunt ook snel fouten in de toepassing identificeren en er een diagnose voor uitvoeren, zonder dat u hoeft te wachten totdat een gebruiker ze heeft gerapporteerd. Application Insights biedt mogelijkheden voor controles van de server evenals van de browser/client.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Een website waaraan u de Application Insights JavaScript-SDK kunt toevoegen.

## <a name="enable-application-insights"></a>Application Insights inschakelen

Met Application Insights kunnen telemetriegegevens worden verzameld vanuit elke toepassing met een internetverbinding, ongeacht of deze on-premises wordt uitgevoerd of in de cloud. Gebruik de volgende stappen om deze gegevens weer te geven:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Selecteer **Een resource maken** > **Hulpprogramma's voor beheer** > **Application Insights**.

   > [!NOTE]
   >Als dit de eerste keer is dat u een Application Insights-resource maakt, raadpleegt u [Een Application Insights-resource maken](./create-new-resource.md).
1. Wanneer een configuratiescherm wordt weergegeven, gebruikt u de volgende tabel om de invoervelden in te vullen:

    | Instellingen        | Waarde           | Beschrijving  |
   | ------------- |:-------------|:-----|
   | **Naam**      | Globaal unieke waarde | De naam die de app identificeert die u wilt controleren. |
   | **Resourcegroep**     | myResourceGroup      | De naam voor de nieuwe resourcegroep waarin App Insights-gegevens worden gehost. Maak een nieuwe resourcegroep of gebruik een bestaande. |
   | **Locatie** | VS - oost | Kies een locatie in uw buurt of in de buurt van waar de app wordt gehost. |
1. Selecteer **Maken**.

## <a name="create-an-html-file"></a>Een HTML-bestand maken

1. Maak op uw lokale computer een bestand met de naam ``hello_world.html``. Maak bijvoorbeeld het bestand in de hoofdmap van station C zodat het er als volgt uitziet: ``C:\hello_world.html``.
1. Kopieer en plak het volgende script in ``hello_world.html``:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations, visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Application Insights-SDK configureren

1. Selecteer **Overzicht** > **Essentials** en kopieer de **Instrumentatiesleutel** van uw toepassing.

   ![Nieuw Application Insights-resourceformulier](media/website-monitoring/instrumentation-key-001.png)

1. Voeg het volgende script toe aan het ``hello_world.html``-bestand vóór de afsluitende ``</head>``-code:

   ```javascript
   <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
      {
         instrumentationKey:"INSTRUMENTATION_KEY"
      }
      );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

1. Bewerk ``hello_world.html`` en voeg de instrumentatiesleutel toe.

1. Open ``hello_world.html`` in een lokale browsersessie. Met deze actie maakt u één paginaweergave. U kunt uw browser vernieuwen voor het genereren van meerdere testpaginaweergaven.

## <a name="monitor-your-website-in-the-azure-portal"></a>Uw website bewaken in Azure Portal

1. Open de pagina **Overzicht** van Application Insights opnieuw in Azure Portal om details weer te geven van de toepassing die momenteel wordt uitgevoerd. De pagina **Overzicht** is de locatie waar u uw instrumentatiesleutel hebt opgehaald.

   De vier standaardgrafieken op de overzichtspagina hebben betrekking op toepassingsgegevens op de server. Omdat we de interacties van de browser/client instrumenteren met de JavaScript SDK, is deze specifieke weergave niet van toepassing tenzij er ook een SDK op de server is geïnstalleerd.

1. Selecteer **Analytics** ![pictogram van toepassingsoverzicht](media/website-monitoring/006.png).  Door deze actie wordt **Analytics** geopend. Dit biedt een uitgebreide querytaal voor het analyseren van alle gegevens die zijn verzameld met Application Insights. Als u gegevens wilt weergeven die betrekking hebben op de browseraanvragen van de client, moet u de volgende query uitvoeren:

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   ![Analytics-grafiek met gebruikersaanvragen gedurende een tijdsperiode](./media/website-monitoring/analytics-query.png)

1. Ga terug naar de **overzichtspagina**. Selecteer onder de header **Onderzoeken** de optie **Browser** en selecteer vervolgens **Prestaties**.  Er worden metrische gegevens met betrekking tot de prestaties van uw website weergegeven. Er is een overeenkomstige weergave waarmee u fouten en uitzonderingen op uw website kunt analyseren. U kunt **Voorbeelden** selecteren voor toegang tot de [volledige transactiedetails](./transaction-diagnostics.md).

   ![Grafiek voor metrische servergegevens](./media/website-monitoring/browser-performance.png)

1. Selecteer in het hoofdmenu van Application Insights, onder de header **Gebruik** de optie [**Gebruikers**](./usage-segmentation.md) om aan de slag te gaan met het verkennen van de [hulpprogramma's voor analyse van het gedrag van gebruikers](./usage-overview.md). Omdat we vanaf één computer testen, zien we alleen gegevens voor één gebruiker. Voor een live website kan de verdeling van gebruikers er als volgt uitzien:

     ![Gebruikersgrafiek](./media/website-monitoring/usage-users.png)

1. Voor een complexere website met meerdere pagina's kunt u het hulpprogramma [**User Flows**](./usage-flows.md) gebruiken om de routes die bezoekers door de verschillende onderdelen van uw website bij te houden.

   ![Gebruikersstromen visualiseren](./media/website-monitoring/user-flows.png)

Zie de [naslagdocumentatie over de JavaScript SDK API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md) als u meer wilt weten over geavanceerdere configuraties voor het controleren van websites.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om met extra quickstarts of zelfstudies te werken, moet u geen resources opschonen die u in deze quickstart hebt gemaakt. Anders kunt u de volgende stappen gebruiken om alle resources te verwijderen die door deze quickstart in Azure Portal zijn gemaakt.

> [!NOTE]
> Als u een bestaande resourcegroep gebruikt, werken de volgende instructies niet. In plaats daarvan kunt u gewoon de afzonderlijke Application Insights-resource verwijderen. Houd er rekening mee dat wanneer u een resourcegroep verwijdert, alle onderliggende resources die lid zijn van die groep ook worden verwijderd.

1. Selecteer in het linkermenu in Azure Portal de optie **Resourcegroepen** en selecteer vervolgens **myResourceGroup** of de naam van uw tijdelijke resourcegroep.
1. Selecteer op de pagina van uw resourcegroep de optie **Verwijderen**, voer **myResourceGroup** in het tekstvak in en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Prestatieproblemen zoeken en diagnoses uitvoeren](../log-query/log-query-overview.md)

