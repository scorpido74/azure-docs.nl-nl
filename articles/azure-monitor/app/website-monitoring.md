---
title: 'Snelstart: websites controleren met Azure Monitor Application Insights'
description: Biedt snelstartinstructies voor het instellen van client/browser-side websitemonitoring met Azure Monitor Application Insights
ms.topic: quickstart
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: 495c40ca8e383dd5a3cf3ba9e5bd42e2936ea015
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80132371"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Snelstart: uw website controleren met Azure Monitor Application Insights

In deze quickstart leert u de open-source Application Insights JavaScript SDK aan uw website toe te voegen. U leert ook hoe u de ervaring aan de client/browserkant van bezoekers van uw website beter begrijpen.

Met Azure Monitor Application Insights kunt u eenvoudig de beschikbaarheid, de prestaties en het gebruik van een website controleren. U kunt ook snel fouten in de toepassing identificeren en er een diagnose voor uitvoeren, zonder dat u hoeft te wachten totdat een gebruiker ze heeft gerapporteerd. Application Insights biedt zowel server-side monitoring als client/browser-side monitoring mogelijkheden.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Een website waaraan u de Application Insights JavaScript SDK toevoegen.

## <a name="enable-application-insights"></a>Application Insights inschakelen

Met Application Insights kunnen telemetriegegevens worden verzameld vanuit elke toepassing met een internetverbinding, ongeacht of deze on-premises wordt uitgevoerd of in de cloud. Gebruik de volgende stappen om deze gegevens te bekijken.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer De**toepassingsinzichten**van de**hulpprogramma's** >  **voor resourcebeheer** > maken .

   > [!NOTE]
   >Als dit de eerste keer is dat u een Application Insights-bron maakt, u meer informatie krijgen door het artikel Resource [van Toepassingsinzichten maken](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) te bezoeken.

   Er wordt een configuratiescherm weergegeven. Gebruik de volgende tabel om de invoervelden in te vullen.

    | Instellingen        | Waarde           | Beschrijving  |
   | ------------- |:-------------|:-----|
   | **Naam**      | Globaal unieke waarde | Naam die de app identificeert die u controleert |
   | **Resourcegroep**     | myResourceGroup      | Naam voor de nieuwe resourcegroep om Application Insights-gegevens te hosten. Maak een nieuwe resourcegroep of gebruik een bestaande. |
   | **Locatie** | VS - oost | Kies een locatie in uw buurt of in de buurt van waar de app wordt gehost |

3. Klik **op Maken**.

## <a name="create-an-html-file"></a>Een HTML-bestand maken

1. Maak op uw lokale computer een bestand met de naam ``hello_world.html``. In dit voorbeeld wordt het bestand in de hoofdmap van de C-schijf geplaatst in ``C:\hello_world.html``.
2. Kopieer het onderstaande script naar ``hello_world.html``:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Application Insights SDK configureren

1. Selecteer > **OverzichtEssentials** > De **instrumentatiesleutel van**uw toepassing kopiëren. **Overview**

   ![Nieuw Application Insights-resourceformulier](media/website-monitoring/instrumentation-key-001.png)

2. Voeg het volgende script toe aan ``hello_world.html`` vóór de afsluitende ``</head>``-code:

   ```javascript
   <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
      {
         instrumentationKey:"INSTRUMENTATION_KEY"
      }
      );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

3. Bewerk ``hello_world.html`` en voeg de instrumentatiesleutel toe.

4. Open ``hello_world.html`` in een lokale browsersessie. Met deze actie wordt één paginaweergave weergegeven. U kunt uw browser vernieuwen voor het genereren van meerdere testpaginaweergaven.

## <a name="start-monitoring-in-the-azure-portal"></a>Beginnen met controleren in Azure Portal

1. U nu de pagina **Overzicht** van toepassingsinzichten in de Azure-portal opnieuw openen om details over uw momenteel draaiende toepassing weer te geven. Op **de pagina Overzicht** hebt u de instrumentatiesleutel opgehaald. De vier standaardgrafieken op de overzichtspagina hebben betrekking op toepassingsgegevens op de server. Aangezien we de client/browser-side interacties met de JavaScript SDK instrumenteren, is deze specifieke weergave niet van toepassing, tenzij we ook een SDK aan de serverzijde hebben geïnstalleerd.

2. Klik op het ![pictogram Toepassingskaart](media/website-monitoring/006.png) **Analytics**.  Deze actie opent **Analytics**, die een uitgebreide querytaal biedt voor het analyseren van alle gegevens die zijn verzameld door Application Insights. Als u gegevens wilt weergeven die betrekking hebben op de browseraanvragen van de client, moet u de volgende query uitvoeren:

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

3. Ga terug naar de **overzichtspagina**. Klik op **Browser** onder de header **Onderzoeken**, selecteer vervolgens **Prestaties**. Hier vindt u metrische gegevens over de prestaties van uw website. Er is ook een overeenkomstige weergave voor het analyseren van fouten en uitzonderingen op uw website. U kunt op **Voorbeelden** klikken om in te zoomen op details van afzonderlijke transacties. Hier hebt u toegang tot de [end-to-end-ervaring](../../azure-monitor/app/transaction-diagnostics.md) met betrekking tot transactiedetails.

   ![Grafiek voor metrische servergegevens](./media/website-monitoring/browser-performance.png)

4. Als u wilt beginnen met de verkenning van de [hulpprogramma’s voor het analyseren van gebruikersgedrag ](../../azure-monitor/app/usage-overview.md), selecteert u in het hoofdmenu van Application Insights de optie [**Gebruikers**](../../azure-monitor/app/usage-segmentation.md) onder de header **Gebruik**. Aangezien we testen vanaf één machine, zien we slechts gegevens voor één gebruiker. Voor een live website kan de verdeling van gebruikers er als volgt uitzien:

     ![Gebruikersgrafiek](./media/website-monitoring/usage-users.png)

5. Als we een complexere website met meerdere pagina's hadden geïnstrumenteerd, was het hulpprogramma [**Gebruikersstromen**](../../azure-monitor/app/usage-flows.md) ook heel handig geweest. Met **Gebruikersstromen** kunt u het traject volgen dat bezoekers afleggen door de diverse onderdelen van uw website.

   ![Gebruikersstromen visualiseren](./media/website-monitoring/user-flows.png)

Zie de [naslagdocumentatie over de JavaScript SDK API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md) als u meer wilt weten over geavanceerdere configuraties voor het controleren van websites.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om door te gaan met extra quickstarts of met de zelfstudies, ruimt u de bronnen die in deze quickstart zijn gemaakt, niet op. Als u anders niet van plan bent door te gaan, gebruikt u de volgende stappen om alle resources te verwijderen die door deze quickstart in de Azure-portal zijn gemaakt.

> [!NOTE]
> Als u een bestaande resourcegroep hebt gebruikt, werken de onderstaande instructies niet en moet u alleen de afzonderlijke application insights-bron verwijderen. Houd er rekening mee dat wanneer u een brongroep verwijdert, alle onderunderylingbronnen die lid zijn van die groep, worden verwijderd.

1. Klik in het linkermenu in de Azure-portal op **Resourcegroepen**en klik vervolgens op **myResourceGroup** of de naam van uw tijdelijke resourcegroep.
2. Klik op de pagina Resourcegroep op **Verwijderen,** typ **myResourceGroup** in het tekstvak en klik op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Prestatieproblemen zoeken en diagnoses uitvoeren](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
