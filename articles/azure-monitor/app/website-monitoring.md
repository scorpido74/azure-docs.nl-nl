---
title: 'Snelstartgids: websites controleren met Azure Monitor Application Insights'
description: In deze Quick Start leert u hoe u de bewaking van client/browser-side website met Azure Monitor Application Insights kunt instellen.
ms.topic: quickstart
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: b47f3ce1ebed12d14dffd68e87dd013bb86218ea
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801634"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Quick Start: uw website bewaken met Azure Monitor Application Insights

In deze Snelstartgids leert u hoe u de Open Source-Application Insights java script SDK kunt toevoegen aan uw website. U leert ook hoe u beter inzicht krijgt in de client/browser-side-ervaring voor bezoekers van uw website.

Met Azure Monitor Application Insights kunt u eenvoudig de beschikbaarheid, de prestaties en het gebruik van een website controleren. U kunt ook snel fouten in de toepassing identificeren en er een diagnose voor uitvoeren, zonder dat u hoeft te wachten totdat een gebruiker ze heeft gerapporteerd. Application Insights biedt bewakings mogelijkheden aan de server zijde en de client/browser-zijde.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Een website waaraan u de Application Insights java script-SDK kunt toevoegen.

## <a name="enable-application-insights"></a>Application Insights inschakelen

Application Insights kunt telemetriegegevens verzamelen van elke toepassing met Internet verbinding die on-premises of in de Cloud wordt uitgevoerd. Gebruik de volgende stappen om deze gegevens weer te geven:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Selecteer **een resource** > **beheer hulpprogramma's** > maken**Application Insights**.

   > [!NOTE]
   >Als dit de eerste keer is dat u een Application Insights resource maakt, raadpleegt u [een Application Insights resource maken](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).
1. Wanneer het configuratie venster wordt weer gegeven, gebruikt u de volgende tabel om de invoer velden te volt ooien:

    | Instellingen        | Waarde           | Beschrijving  |
   | ------------- |:-------------|:-----|
   | **Naam**      | Globaal unieke waarde | De naam waarmee de app wordt geïdentificeerd die u bewaken. |
   | **Resource groep**     | myResourceGroup      | De naam voor de nieuwe resource groep voor het hosten van Application Insights gegevens. Maak een nieuwe resourcegroep of gebruik een bestaande. |
   | **Locatie** | VS - oost | Kies een locatie bij u in de buurt of in de buurt van waar de app wordt gehost. |
1. Selecteer **Maken**.

## <a name="create-an-html-file"></a>Een HTML-bestand maken

1. Maak op uw lokale computer een bestand met de naam ``hello_world.html``. Voor dit voor beeld maakt u het bestand in de hoofdmap van station C zodat het eruit ziet ``C:\hello_world.html``.
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

## <a name="configure-application-insights-sdk"></a>Application Insights SDK configureren

1. Selecteer **overzicht** > **essentiële**elementen en kopieer de **instrumentatie sleutel**van uw toepassing.

   ![Nieuw Application Insights-resourceformulier](media/website-monitoring/instrumentation-key-001.png)

1. Voeg het volgende script toe aan ``hello_world.html`` het bestand vóór de ``</head>`` afsluitende tag:

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

1. Open ``hello_world.html`` in een lokale browsersessie. Met deze actie maakt u één pagina weergave. U kunt uw browser vernieuwen om meerdere test pagina weergaven te genereren.

## <a name="monitor-your-website-in-the-azure-portal"></a>Controleer uw website in de Azure Portal

1. Open de pagina **overzicht** van Application Insights in het Azure Portal om de details van de toepassing die momenteel wordt uitgevoerd, weer te geven. Op de **overzichts** pagina wordt de instrumentatie sleutel opgehaald.

   De vier standaardgrafieken op de overzichtspagina hebben betrekking op toepassingsgegevens op de server. Omdat we de communicatie tussen de client en de browser met de Java script SDK behandelen, is deze specifieke weer gave niet van toepassing tenzij er ook een SDK aan de server zijde is geïnstalleerd.

1. Het **Analytics** ![pictogram](media/website-monitoring/006.png)van het Analytics-toepassings overzicht selecteren.  Met deze actie wordt de **analyse**geopend. deze biedt een uitgebreide query taal voor het analyseren van alle gegevens die worden verzameld door Application Insights. Voer de volgende query uit om gegevens te bekijken die gerelateerd zijn aan de browser aanvragen aan de client zijde:

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

1. Ga terug naar de **overzichtspagina**. Selecteer in de kop **onderzoek** de optie **browser**en selecteer vervolgens **prestaties**.  De metrische gegevens met betrekking tot de prestaties van uw website worden weer gegeven. Er is een overeenkomende weer gave voor het analyseren van fouten en uitzonde ringen in uw website. U kunt voor **beelden** selecteren om toegang te krijgen tot de [end-to-end-transactie gegevens](../../azure-monitor/app/transaction-diagnostics.md).

   ![Grafiek voor metrische servergegevens](./media/website-monitoring/browser-performance.png)

1. Selecteer in het hoofd menu Application Insights, onder de kop **Usage** , de optie [**gebruikers**](../../azure-monitor/app/usage-segmentation.md) om te beginnen met het verkennen van de [analyse hulpprogramma's voor gebruikers gedrag](../../azure-monitor/app/usage-overview.md). Omdat we testen vanaf één computer, worden er alleen gegevens voor één gebruiker weer gegeven. Voor een live website kan de distributie van gebruikers er als volgt uitzien:

     ![Gebruikersgrafiek](./media/website-monitoring/usage-users.png)

1. Voor een complexere website met meerdere pagina's kunt u het hulp programma [**Gebruikersstromen**](../../azure-monitor/app/usage-flows.md) gebruiken om de routes bij te houden die bezoekers door de verschillende onderdelen van uw website door nemen.

   ![Gebruikersstromen visualiseren](./media/website-monitoring/user-flows.png)

Zie de naslag informatie over [Java script SDK API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)voor meer geavanceerde configuraties voor het controleren van websites.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om verder te gaan met andere Quick starts of zelf studies, kunt u de resources die u in deze Quick Start hebt gemaakt, niet opschonen. Gebruik anders de volgende stappen om alle resources te verwijderen die door deze Quick Start in de Azure Portal zijn gemaakt.

> [!NOTE]
> Als u een bestaande resource groep hebt gebruikt, werken de volgende instructies niet. U kunt in plaats daarvan alleen de afzonderlijke Application Insights resource verwijderen. Als u een resource groep verwijdert, worden alle underyling-resources die lid zijn van die groep ook verwijderd.

1. Selecteer **resource groepen**in het menu links op het Azure Portal en selecteer vervolgens **myResourceGroup** of de naam van uw tijdelijke resource groep.
1. Selecteer op de pagina resource groep **verwijderen**, Voer **myResourceGroup** in het tekstvak in en selecteer vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Prestatieproblemen zoeken en diagnoses uitvoeren](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
