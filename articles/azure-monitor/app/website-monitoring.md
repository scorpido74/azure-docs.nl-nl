---
title: Quickstart websites controleren met Azure Monitor Application Insights | Microsoft Docs
description: Hier vindt u instructies voor het snel instellen van client/browser-functies voor het controleren van websites met Azure Monitor Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: eecc3d739cb5500abdbd04a9aaca1ddcf86e352b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899258"
---
# <a name="start-monitoring-your-website"></a>Start met het controleren van uw website

Met Azure Monitor Application Insights kunt u eenvoudig de beschikbaarheid, de prestaties en het gebruik van een website controleren. U kunt ook snel fouten in de toepassing identificeren en er een diagnose voor uitvoeren, zonder dat u hoeft te wachten totdat een gebruiker ze heeft gerapporteerd. Application Insights biedt mogelijkheden voor controles van de server evenals van de browser/client.

Deze quickstart helpt u bij het toevoegen van de [opensource Application Insights JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS) waarmee u inzicht krijgt in de ervaring aan de browser-/clientzijde van bezoekers van uw website.

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstartgids:

- U hebt een Azure-abonnement nodig.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Application Insights inschakelen

Met Application Insights kunnen telemetriegegevens worden verzameld vanuit elke toepassing met een internetverbinding, ongeacht of deze on-premises wordt uitgevoerd of in de cloud. Gebruik de volgende stappen om deze gegevens te bekijken.

1. Selecteer **Een resource maken** > **Hulpprogramma's voor beheer** > **Application Insights**.

   > [!NOTE]
   >Als dit de eerste keer is dat u een Application Insights resource maakt, kunt u meer informatie vinden op het artikel [een Application Insights-resource maken](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

   Er wordt een configuratiescherm weergegeven. Gebruik de volgende tabel om de invoervelden in te vullen.

    | Instellingen        | Waarde           | Beschrijving  |
   | ------------- |:-------------|:-----|
   | **Naam**      | Globaal unieke waarde | Naam die de app beschrijft die u wilt controleren |
   | **Resourcegroep**     | myResourceGroup      | Naam voor de nieuwe resourcegroep waarin App Insights-gegevens worden gehost |
   | **Locatie** | VS - oost | Kies een locatie in uw buurt of in de buurt van waar de app wordt gehost |

2. Klik op **Maken**.

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

## <a name="configure-app-insights-sdk"></a>App Insights-SDK configureren

1. Selecteer **Overzicht** > **Essentials** > kopieer de **Instrumentatiesleutel** van uw toepassing.

   ![Nieuw App Insights-resourceformulier](media/website-monitoring/instrumentation-key-001.png)

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

4. Open ``hello_world.html`` in een lokale browsersessie. Er wordt dan een enkele paginaweergave gemaakt. U kunt uw browser vernieuwen voor het genereren van meerdere testpaginaweergaven.

## <a name="start-monitoring-in-the-azure-portal"></a>Beginnen met controleren in Azure Portal

1. U kunt de pagina **Overzicht** van Application Insights in Azure Portal, waar u de instrumentatiesleutel hebt opgehaald, nu opnieuw openen om de details te bekijken van de toepassing die momenteel wordt uitgevoerd. De vier standaardgrafieken op de overzichtspagina hebben betrekking op toepassingsgegevens op de server. Omdat we de interacties van de browser/client instrumenteren met de JavaScript SDK, is deze specifieke weergave niet van toepassing tenzij er ook een SDK op de server is geïnstalleerd.

2. Klik op het ![pictogram Toepassingskaart](media/website-monitoring/006.png) **Analytics**.  Hierdoor wordt **Analytics** geopend. Dit biedt een uitgebreide querytaal voor het analyseren van alle gegevens die zijn verzameld met Application Insights. Als u gegevens wilt weergeven die betrekking hebben op de browseraanvragen van de client, moet u de volgende query uitvoeren:

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

3. Ga terug naar de **overzichtspagina**. Klik op **Browser** onder de header **Onderzoeken**, selecteer vervolgens **Prestaties**. Hier vindt u metrische gegevens over de prestaties van uw website. Er is ook een overeenkomstige weergave waarmee u fouten en uitzonderingen op uw website kunt analyseren. U kunt op **Voorbeelden** klikken om in te zoomen op details van afzonderlijke transacties. Hier hebt u toegang tot de [end-to-end-ervaring](../../azure-monitor/app/transaction-diagnostics.md) met betrekking tot transactiedetails.

   ![Grafiek voor metrische servergegevens](./media/website-monitoring/browser-performance.png)

4. Als u wilt beginnen met de verkenning van de [hulpprogramma’s voor het analyseren van gebruikersgedrag ](../../azure-monitor/app/usage-overview.md), selecteert u in het hoofdmenu van Application Insights de optie [**Gebruikers**](../../azure-monitor/app/usage-segmentation.md) onder de header **Gebruik**. Omdat we vanaf één enkele computer testen, zien we alleen gegevens voor één gebruiker. Voor een live website kan de verdeling van gebruikers er als volgt uitzien:

     ![Gebruikersgrafiek](./media/website-monitoring/usage-users.png)

5. Als we een complexere website met meerdere pagina's hadden geïnstrumenteerd, was het hulpprogramma [**Gebruikersstromen**](../../azure-monitor/app/usage-flows.md) ook heel handig geweest. Met **Gebruikersstromen** kunt u het traject volgen dat bezoekers afleggen door de diverse onderdelen van uw website.

   ![Gebruikersstromen visualiseren](./media/website-monitoring/user-flows.png)

Zie de [naslagdocumentatie over de JavaScript SDK API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md) als u meer wilt weten over geavanceerdere configuraties voor het controleren van websites.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om door te gaan met andere Quick Starts of met de zelfstudies, verwijdert u de resources die u in deze Quick Start hebt gemaakt niet. Als u echter niet wilt doorgaan, gebruikt u de volgende stappen om alle resources te verwijderen die tijdens deze quickstart in de Azure-portal zijn gemaakt.

1. Klik in het menu links in Azure Portal op **Resourcegroepen** en klik vervolgens op **myResourceGroup**.
2. Klik op de pagina van uw resourcegroep op **Verwijderen**, typ **myResourceGroup** in het tekstvak en klik vervolgens op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Prestatieproblemen zoeken en diagnoses uitvoeren](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
