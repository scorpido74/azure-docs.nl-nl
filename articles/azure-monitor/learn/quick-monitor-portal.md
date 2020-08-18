---
title: Een ASP.NET-web-app bewaken met Azure Application Insights | Microsoft Docs
description: Biedt instructies om snel een ASP.NET-web-app in te stellen voor controle met Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: ed7cec34b4bca1882d069997d4f89677cda32447
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080946"
---
# <a name="start-monitoring-your-aspnet-web-application"></a>Begin uw ASP.NET-webtoepassing te controleren

Met Azure Application Insights kunt u eenvoudig de beschikbaarheid, de prestaties en het gebruik van een webtoepassing controleren.  U kunt ook snel fouten in de toepassing identificeren en er een diagnose voor uitvoeren, zonder dat u hoeft te wachten totdat een gebruiker ze heeft gerapporteerd.  Met de informatie die u met Application Insights verzamelt over de prestaties en de effectiviteit van uw app, kunt u weloverwogen keuzes maken bij het onderhouden en verbeteren van uw toepassing.

Deze snelstartgids laat zien hoe u Application Insights kunt toevoegen aan een bestaande ASP.NET-webtoepassing en live statistieken kunt analyseren, wat slechts een van de diverse methoden is die u kunt gebruiken om uw toepassing te analyseren. Als u geen ASP.NET-webtoepassing hebt, kunt u er een maken door de [quickstart voor het maken van een ASP.NET-webapp](../../app-service/quickstart-dotnet-framework.md) te volgen.

## <a name="prerequisites"></a>Vereisten
Dit zijn de vereisten voor het voltooien van deze snelstart:

- Installeer [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) met de volgende workloads:
    - ASP.NET-ontwikkeling en webontwikkeling
    - Azure-ontwikkeling


Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="enable-application-insights"></a>Application Insights inschakelen

1. Open uw project in Visual Studio 2019.
2. Selecteer **Configure Application Insights** in het menu Project. De Application Insights-SDK wordt toegevoegd aan uw toepassing.

    > [!IMPORTANT]
    > Het proces voor het toevoegen van Application Insights hangt af van het type ASP.NET-sjabloon. Als u de sjabloon **Leeg** of **Azure mobile app** gebruikt, selecteer dan **Project** > **Application Insights Telemetry toevoegen**. Raadpleeg voor alle andere ASP.NET-sjablonen de instructies in de stap hierboven. 

3. Klik op **Aan de slag** (eerdere versies van Visual Studio hebben in plaats daarvan de knop **Gratis beginnen**).

    ![Application Insights toevoegen aan Visual Studio](./media/quick-monitor-portal/add-application-insights-b.png)

4. Selecteer uw abonnement en klik op **Registreren**.

5. Selecteer **Project** > **NuGet-pakketten beheren** > **Pakketbron: nuget.org** > **Update** de Application Insights SDK-pakketten naar de nieuwste stabiele release.

6. Voer uw toepassing uit door **Start Debugging** (Foutopsporing starten) te selecteren in het menu **Debug** (Foutopsporing) of door op de F5-toets te drukken.

## <a name="confirm-app-configuration"></a>App-configuratie bevestigen

Application Insights verzamelt telemetriegegevens voor uw toepassing, ongeacht waar deze wordt uitgevoerd. Gebruik de volgende stappen om deze gegevens te bekijken.

1. Open Application Insights door op **Beeld** -> **Andere vensters** -> **Application Insights-zoekopdracht** te klikken.  U ziet de telemetrie van uw huidige sessie.<BR><br>![Telemetrie in Visual Studio](./media/quick-monitor-portal/telemetry-in-vs.png)

2. Klik op de eerste aanvraag in de lijst (in dit voorbeeld GET Home/Index) om de aanvraagdetails te bekijken. U ziet dat de statuscode en de reactietijd beide worden opgenomen, samen met andere waardevolle informatie over de aanvraag.<br><br>![Responsdetails in Visual Studio](media/quick-monitor-portal/request-details.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Beginnen met controleren in Azure Portal

U kunt nu Application Insights openen in Azure Portal om allerlei gegevens over uw actieve toepassing weer te geven.

1. Vouw in de Solution Explorer de map **verbonden services** uit (pictogram van wolk en stekker) en klik met de rechtermuisknop op de map **Application Insights**. Klik vervolgens op **Application Insights-portal openen**.  U ziet een aantal gegevens over uw toepassing en allerlei opties.

    ![Toepassingskaart](media/quick-monitor-portal/04-overview.png)

2. Klik op **Toepassingsoverzicht** voor een visueel overzicht van de afhankelijkheidsrelaties tussen de onderdelen van de toepassing.  Voor elk onderdeel worden KPI's weergegeven, zoals belasting, prestaties, fouten en waarschuwingen.

    ![Toepassingskaart](media/quick-monitor-portal/05-appmap.png)

3. Klik op het pictogram ![Toepassingskaart](media/quick-monitor-portal/app-viewinlogs-icon.png) van **App-analyse** **Weergave in logboeken (Analytics)** bij een van de toepassingsonderdelen. Hierdoor wordt **Logboeken (Analytics)** geopend. Dit biedt een uitgebreide querytaal voor het analyseren van alle gegevens die zijn verzameld met Application Insights. In dit geval wordt er een query gegenereerd waarmee het aantal aanvragen wordt weergegeven als een grafiek. U kunt uw eigen query's schrijven om andere gegevens te analyseren.

    ![Analyse](media/quick-monitor-portal/6viewanalytics.png)

4. Klik op **Live Metrics Stream** aan de linkerkant onder Onderzoeken. Hier worden live statistieken weergegeven over uw toepassing terwijl deze wordt uitgevoerd. Dit zijn onder andere gegevens over het aantal inkomende aanvragen, de duur van deze aanvragen en eventuele fouten die optreden. U kunt ook kritieke prestatiegegevens inspecteren, zoals de processor en het geheugen.

    ![Live Stream](media/quick-monitor-portal/7livemetrics.png)

    Als u klaar bent om uw toepassing in Azure te hosten, kunt u deze nu publiceren. Volg de stappen [Create an ASP.NET Web App Quickstart](../../app-service/quickstart-dotnetcore.md#update-the-app-and-redeploy) (Snelstartgids voor het maken van een ASP.NET-webapp).

5. Als u Visual Studio gebruikt om controle door Application Insights toe te voegen, kunt u automatische controle van de client toevoegen. Als u automatische controle van de client wilt toevoegen aan een toepassing, moet u het volgende JavaScript aan uw toepassing toevoegen:

```html
<!-- 
To collect user behavior analytics about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var appInsights=window.appInsights||function(a){
  function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
  }({
      instrumentationKey:"<your instrumentation key>"
  });

window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
</script>
```

Raadpleeg de GitHub-opslagplaats voor de [open-source JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="clean-up-resources"></a>Resources opschonen
U kunt de resourcegroep en alle gerelateerde resources verwijderen wanneer u klaar bent met testen. Volg de onderstaande stappen om dit te doen.
1. Klik in het menu links in Azure Portal op **Resourcegroepen** en klik vervolgens op **myResourceGroup**.
2. Klik op de pagina van uw resourcegroep op **Verwijderen**, typ **myResourceGroup** in het tekstvak en klik vervolgens op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen
Aan de hand van deze quickstart hebt u uw toepassing ingesteld voor controle met Azure Application Insights.  Ga verder met de zelfstudies om te leren hoe u dit kunt gebruiken om statistieken bij te houden en problemen in uw toepassing te detecteren.

> [!div class="nextstepaction"]
> [Zelfstudies voor Azure Application Insights](tutorial-runtime-exceptions.md)
