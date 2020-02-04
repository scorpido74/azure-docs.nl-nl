---
title: 'Snelstartgids: node. js controleren met Azure Monitor Application Insights'
description: Biedt instructies om snel een node. js-web-app in te stellen voor bewaking met Azure Monitor Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/12/2019
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: a6d6d70336badeaa86c9982dfa977ea389ed5402
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963526"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Quick Start: uw node. js-webtoepassing bewaken met Azure-toepassing Insights

In deze Quick Start voegt u de Application Insights SDK-versie 0,22 voor node. js toe aan een bestaande node. js-webtoepassing.

Met Azure Application Insights kunt u eenvoudig de beschikbaarheid, de prestaties en het gebruik van een webtoepassing controleren. U kunt ook snel fouten in de toepassing identificeren en er een diagnose voor uitvoeren, zonder dat u hoeft te wachten totdat een gebruiker ze heeft gerapporteerd. Vanaf de release van versie 0.20 van de SDK kunt u veelgebruikte pakketten van derden controleren, inclusief MongoDB, MySQL en Redis.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Een werkende node. js-toepassing.

## <a name="enable-application-insights"></a>Application Insights inschakelen

Application Insights kunt telemetriegegevens verzamelen van elke toepassing met een Internet verbinding, ongeacht of deze on-premises of in de Cloud wordt uitgevoerd. Gebruik de volgende stappen om deze gegevens te bekijken.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

2. Selecteer **Een resource maken** > **Hulpprogramma's voor ontwikkelaars** > **Application Insights**.

   ![Een Azure-toepassing Insights-resource toevoegen](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >Als dit de eerste keer is dat u een Application Insights resource maakt, kunt u meer informatie vinden op het document [Create a Application Insights resource](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

   Er wordt een configuratie pagina weer gegeven. Gebruik de volgende tabel om de invoer velden in te vullen. 

    | Instellingen        | Waarde           | Beschrijving  |
   | ------------- |:-------------|:-----|
   | **Naam**      | Globaal unieke waarde | Naam waarmee de app wordt geïdentificeerd die u bewaken |
   | **Resourcegroep**     | myResourceGroup      | Naam voor de nieuwe resource groep voor het hosten van AppInsights-gegevens. Maak een nieuwe resourcegroep of gebruik een bestaande. |
   | **Locatie** | VS - oost | Kies een locatie in uw buurt of in de buurt van waar de app wordt gehost |

3. Selecteer **Maken**.

## <a name="configure-appinsights-sdk"></a>AppInsights SDK configureren

1. Selecteer **overzicht** en kopieer de **instrumentatie sleutel**van uw toepassing.

   ![De Application Insights instrumentatie sleutel weer geven](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Voeg de Application Insights-SDK voor Node.js toe aan uw toepassing. Voer deze opdracht uit vanuit de hoofdmap van uw app:

   ```bash
   npm install applicationinsights --save
   ```

3. Bewerk het eerste *. js* -bestand van uw app en voeg de twee regels hieronder toe aan het bovenste deel van uw script. Als u de Quick Start- [app node. js](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs)gebruikt, wijzigt u het bestand *index. js* . Vervang `<instrumentation_key>` door de instrumentatie sleutel van uw toepassing. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Start de app opnieuw.

> [!NOTE]
> Het duurt 3-5 minuten voordat de gegevens worden weergegeven in de portal. Als deze app een test-app met weinig verkeer is, houd er dan rekening mee dat de meeste metrische gegevens alleen vastgelegd wanneer er actieve aanvragen en bewerkingen zijn.

## <a name="start-monitoring-in-the-azure-portal"></a>Beginnen met controleren in Azure Portal

1. U kunt de pagina **Overzicht** van Application Insights in Azure Portal, waar u de instrumentatiesleutel hebt opgehaald, nu opnieuw openen om de details te bekijken van de toepassing die momenteel wordt uitgevoerd.

   ![Menu overzicht van Application Insights](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. Selecteer **toepassings toewijzing** voor een visuele indeling van de afhankelijkheids relaties tussen de onderdelen van uw toepassing. Voor elk onderdeel worden KPI's weergegeven, zoals belasting, prestaties, fouten en waarschuwingen.

   ![Toepassings toewijzing Application Insights](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. Selecteer het pictogram voor de **app-analyse** ![toepassings kaart pictogram](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) **weer gave in Analytics**.  Met deze actie wordt **Application Insights Analytics**geopend, waarmee u een uitgebreide query taal kunt uitvoeren voor het analyseren van alle gegevens die zijn verzameld door Application Insights. In dit geval wordt er een query gegenereerd waarmee het aantal aanvragen wordt weergegeven als een grafiek. U kunt uw eigen query's schrijven om andere gegevens te analyseren.

   ![Application Insights Analytics-grafieken](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. Ga terug naar de pagina **Overzicht** en bekijk de KPI-grafieken.  Dit dashboard biedt statistische gegevens over de toepassingsstatus, waaronder het aantal inkomende aanvragen, de duur van deze aanvragen en eventuele fouten die optreden.

   ![Tijdlijn grafieken voor Application Insights status overzicht](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

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

5. Selecteer aan de linkerkant de optie **metrische gegevens**. Gebruik metrische gegevens Verkenner om de status en het gebruik van uw resource te onderzoeken. U kunt **nieuwe grafiek toevoegen** selecteren om extra aangepaste weer gaven te maken of **bewerken** selecteren om de bestaande grafiek typen, hoogte, kleuren palet, groeperingen en metrische gegevens te wijzigen. U kunt bijvoorbeeld een grafiek maken waarin de gemiddelde laad tijd van een browser pagina wordt weer gegeven door ' browser pagina laadtijd ' te selecteren in de vervolg keuzelijst metrische gegevens en ' Gem ' van aggregatie. Ga voor meer informatie over Azure Metrics Explorer aan de slag [met azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

   ![Grafiek met metrische gegevens van Application Insights server](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

Raadpleeg de [extra AppInsights node. js-documentatie](../../azure-monitor/app/nodejs.md)voor meer informatie over het bewaken van node. js.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met testen, kunt u de resource groep en alle gerelateerde resources verwijderen. Volg hiervoor de onderstaande stappen.

> [!NOTE]
> Als u een bestaande resource groep hebt gebruikt, werken de onderstaande instructies niet en hoeft u alleen de afzonderlijke Application Insights resource te verwijderen. Houd er rekening mee dat u een resource groep verwijdert alle underyling-resources die lid zijn van die groep worden verwijderd.

1. Selecteer in het menu links in de Azure-portal **Resourcegroepen** en selecteer vervolgens **myResourceGroup**.
2. Selecteer op de pagina resource groep **verwijderen**, Voer **myResourceGroup** in het tekstvak in en selecteer vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Prestatieproblemen zoeken en diagnoses uitvoeren](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
