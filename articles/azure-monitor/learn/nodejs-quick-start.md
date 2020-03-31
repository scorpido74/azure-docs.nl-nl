---
title: 'Snelstart: Node.js bewaken met Azure Monitor Application Insights'
description: Geeft instructies om snel een Node.js Web App in te stellen voor bewaking met Azure Monitor Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/12/2019
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 694d2ae529202223869fcbb2a084e32bccaedbf1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77660220"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Snelstart: uw Node.js-webtoepassing controleren met Azure Application Insights

In deze quickstart voegt u de Application Insights SDK-versie 0.22 voor Node.js toe aan een bestaande Node.js-webtoepassing.

Met Azure Application Insights kunt u eenvoudig de beschikbaarheid, de prestaties en het gebruik van een webtoepassing controleren. U kunt ook snel fouten in de toepassing identificeren en er een diagnose voor uitvoeren, zonder dat u hoeft te wachten totdat een gebruiker ze heeft gerapporteerd. Vanaf de release van versie 0.20 van de SDK kunt u veelgebruikte pakketten van derden controleren, inclusief MongoDB, MySQL en Redis.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Een goed werkende Node.js applicatie.

## <a name="enable-application-insights"></a>Application Insights inschakelen

Application Insights kan telemetriegegevens verzamelen van elke met internet verbonden toepassing, ongeacht of deze on-premises of in de cloud wordt uitgevoerd. Gebruik de volgende stappen om deze gegevens te bekijken.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Selecteer De**toepassingsinzichten van hulpprogramma's** >  **voor resourceontwikkelaars** > **maken**.

   ![Een Azure Application Insights-bron toevoegen](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >Als dit de eerste keer is dat u een Application Insights-bron maakt, u meer informatie krijgen door het document Resource [van Toepassingsinzichten maken te](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) bezoeken.

   Er wordt een configuratiepagina weergegeven; gebruik de volgende tabel om de invoervelden in te vullen. 

    | Instellingen        | Waarde           | Beschrijving  |
   | ------------- |:-------------|:-----|
   | **Naam**      | Globaal unieke waarde | Naam die de app identificeert die u controleert |
   | **Resourcegroep**     | myResourceGroup      | Naam voor de nieuwe brongroep om AppInsights-gegevens te hosten. Maak een nieuwe resourcegroep of gebruik een bestaande. |
   | **Locatie** | VS - oost | Kies een locatie in uw buurt of in de buurt van waar de app wordt gehost |

3. Selecteer **Maken**.

## <a name="configure-appinsights-sdk"></a>AppInsights SDK configureren

1. Selecteer **Overzicht** en kopieer de **instrumentatietoets van**uw toepassing.

   ![De instrumentatiesleutel van Application Insights weergeven](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Voeg de Application Insights-SDK voor Node.js toe aan uw toepassing. Voer deze opdracht uit vanuit de hoofdmap van uw app:

   ```bash
   npm install applicationinsights --save
   ```

3. Bewerk het eerste *JS-bestand* van uw app en voeg de twee regels hieronder toe aan het bovenste deel van uw script. Als u de [Quickstart-app Van Knooppunt.js](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs)gebruikt, wijzigt u het *bestand index.js.* Vervang `<instrumentation_key>` door de instrumentatiesleutel van uw toepassing. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Start de app opnieuw.

> [!NOTE]
> Het duurt 3-5 minuten voordat de gegevens worden weergegeven in de portal. Als deze app een test-app met weinig verkeer is, houd er dan rekening mee dat de meeste metrische gegevens alleen vastgelegd wanneer er actieve aanvragen en bewerkingen zijn.

## <a name="start-monitoring-in-the-azure-portal"></a>Beginnen met controleren in Azure Portal

1. U kunt de pagina **Overzicht** van Application Insights in Azure Portal, waar u de instrumentatiesleutel hebt opgehaald, nu opnieuw openen om de details te bekijken van de toepassing die momenteel wordt uitgevoerd.

   ![Overzichtsmenu Toepassingsinzichten](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. Selecteer **Toepassingstoewijzing** voor een visuele indeling van de afhankelijkheidsrelaties tussen uw toepassingsonderdelen. Voor elk onderdeel worden KPI's weergegeven, zoals belasting, prestaties, fouten en waarschuwingen.

   ![Toepassingskaart voor toepassingsinzichten voor toepassingen](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. Selecteer **App Analytics** het pictogram ![](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) Toepassingstoewijzing van App Analytics **in Analytics**.  Met deze actie opent **Application Insights Analytics**, dat een uitgebreide querytaal biedt voor het analyseren van alle gegevens die worden verzameld door Application Insights. In dit geval wordt er een query gegenereerd waarmee het aantal aanvragen wordt weergegeven als een grafiek. U kunt uw eigen query's schrijven om andere gegevens te analyseren.

   ![Analytics-grafieken voor Application Insights Analytics](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. Ga terug naar de pagina **Overzicht** en bekijk de KPI-grafieken.  Dit dashboard biedt statistische gegevens over de toepassingsstatus, waaronder het aantal inkomende aanvragen, de duur van deze aanvragen en eventuele fouten die optreden.

   ![Tijdlijngrafieken van het overzicht van de status van toepassingsinzichten](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

   Als u de grafiek **Laadtijd voor paginaweergave** wilt vullen met **telemetriegegevens aan de clientzijde**, voegt u dit script toe aan elke pagina die u wilt bijhouden:

   ```HTML
   <!-- 
   To collect user behavior analytics tools about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
       function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
       }({
           instrumentationKey:"<insert instrumentation key>"
       });
       
       window.appInsights=appInsights;
       appInsights.trackPageView();
   </script>
   ```

5. Aan de linkerkant selecteert u **Metrische gegevens**. Gebruik de verkenner met statistieken om de status en het gebruik van uw resource te onderzoeken. U **Nieuwe grafiek toevoegen** selecteren om extra aangepaste weergaven te maken of **bewerken** selecteren om de bestaande grafiektypen, hoogte, kleurenpalet, groeperingen en statistieken te wijzigen. U bijvoorbeeld een grafiek maken met de gemiddelde laadtijd van de browserpagina door 'Laadtijd van browserpagina's' te selecteren in de vervolgkeuzelijst met statistieken en 'Avg' uit aggregatie. Ga aan [de slag met Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md)voor meer informatie over Azure Metrics Explorer.

   ![Grafiek met statistieken van Application Insights Server](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

Voor meer informatie over het monitoren van Node.js, raadpleegt u de [aanvullende documentatie appInsights Node.js.](../../azure-monitor/app/nodejs.md)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met testen, u de brongroep en alle gerelateerde bronnen verwijderen. Volg hiervoor de onderstaande stappen.

> [!NOTE]
> Als u een bestaande resourcegroep hebt gebruikt, werken de onderstaande instructies niet en moet u alleen de afzonderlijke application insights-bron verwijderen. Houd er rekening mee dat wanneer u een brongroep verwijdert, alle onderunderylingbronnen die lid zijn van die groep, worden verwijderd.

1. Selecteer in het menu links in de Azure-portal **Resourcegroepen** en selecteer vervolgens **myResourceGroup**.
2. Selecteer op de pagina Met uw brongroep de optie **Verwijderen,** typ **myResourceGroup** in het tekstvak en selecteer **Vervolgens Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Prestatieproblemen zoeken en diagnoses uitvoeren](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
