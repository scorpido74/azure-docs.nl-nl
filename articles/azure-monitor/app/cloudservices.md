---
title: Toepassingsinzichten voor Azure-cloudservices | Microsoft Documenten
description: Controleer uw web- en werkrollen op een effectieve manier met Application Insights
ms.topic: conceptual
ms.date: 09/05/2018
ms.openlocfilehash: 17813d17a1c40caac5587e37e279be6376992b90
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537590"
---
# <a name="application-insights-for-azure-cloud-services"></a>Toepassingsinzichten voor Azure-cloudservices
[Application Insights][start] kan [Azure cloud service apps](https://azure.microsoft.com/services/cloud-services/) controleren op beschikbaarheid, prestaties, storingen en gebruik door gegevens uit Application Insights SDKs te combineren met Azure [Diagnostics-gegevens](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) van uw cloudservices. Op basis van de feedback die u krijgt over de prestaties en de effectiviteit van uw app tijdens het gebruik, kunt u weldoordachte beslissingen nemen over de richting van het ontwerp in elke fase van de ontwikkelingslevenscyclus.

![Overzichtsdashboard](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Vereisten
Voordat u begint, moet u:

* Een [Azure-abonnement.](https://azure.com) Meld u aan met uw Microsoft-account voor Windows, Xbox Live of andere Microsoft-cloudservices. 
* Microsoft Azure-hulpprogramma's 2.9 of hoger.
* Developer Analytics Tools 7.10 of hoger.

## <a name="get-started-quickly"></a>Snel aan de slag
Als u uw cloudservice wilt controleren met Application Insights, kunt u dat het snelst en gemakkelijkst doen door die optie te kiezen wanneer u uw service naar Azure publiceert.

![Pagina Voorbeelddiagnostische instellingen](./media/cloudservices/azure-cloud-application-insights.png)

Met deze optie wordt uw app tijdens runtime beheerd, zodat u alle telemetrie krijgt die u nodig hebt om aanvragen, uitzonderingen en afhankelijkheden in uw webrol te controleren. Het controleert ook prestatiemeteritems van uw werknemersrollen. Alle diagnostische sporen die door uw app worden gegenereerd, worden ook naar Application Insights verzonden.

Als deze optie alles is wat je nodig hebt, ben je klaar. 

Uw volgende stappen zijn [het weergeven van statistieken uit uw app,](../../azure-monitor/platform/metrics-charts.md) [het opvragen van uw gegevens met Analytics](../../azure-monitor/app/analytics.md). 

Als u de prestaties in de browser wilt controleren, u ook [beschikbaarheidstests](../../azure-monitor/app/monitor-web-app-availability.md) instellen en [code toevoegen aan uw webpagina's.](../../azure-monitor/app/javascript.md)

In de volgende secties worden de volgende aanvullende opties besproken:

* Stuur gegevens uit verschillende componenten en bouw configuraties naar afzonderlijke bronnen.
* U kunt aangepaste telemetrie uit uw app toevoegen.

## <a name="sample-app-instrumented-with-application-insights"></a>Voorbeeld-app die is uitgerust met Application Insights
In deze [voorbeeld-app](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)wordt Application Insights toegevoegd aan een cloudservice met twee werknemersrollen die in Azure worden gehost. 

In het volgende gedeelte leert u hoe u uw eigen cloudserviceproject op dezelfde manier aanpassen.

## <a name="plan-resources-and-resource-groups"></a>Resources en resourcegroepen plannen
De telemetrie van uw app wordt opgeslagen, geanalyseerd en weergegeven in een Azure-bron van het type Application Insights. 

Elke resource behoort tot een resourcegroep. Resourcegroepen worden gebruikt om kosten te beheren, teamleden toegang te verlenen en om updates in één gecoördineerde transactie te implementeren. U bijvoorbeeld een script schrijven om een Azure-cloudservice en de monitoringbronnen van Application Insights in één bewerking [te implementeren.](../../azure-resource-manager/templates/deploy-powershell.md)

### <a name="resources-for-components"></a>Resources voor onderdelen
We raden u aan voor elk onderdeel van uw app een aparte bron te maken. Dat wil zeggen dat u een resource maakt voor elke webrol en werkrol. U elk onderdeel afzonderlijk analyseren, maar u maakt een [dashboard](../../azure-monitor/app/overview-dashboard.md) dat de belangrijkste grafieken van alle componenten samenbrengt, zodat u ze in één weergave vergelijken en controleren. 

Een alternatieve benadering is om de telemetrie van meer dan één rol naar dezelfde bron te verzenden, maar [een dimensieeigenschap toe te voegen aan elk telemetrieitem](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) dat de bronrol identificeert. In deze benadering worden in metrische grafieken, zoals uitzonderingen, normaal gesproken een aggregatie van de tellingen uit de verschillende rollen weergegeven, maar u de grafiek zo nodig segmenteren op de rol-id. U zoekopdrachten ook filteren op dezelfde dimensie. Dit alternatief maakt het een beetje makkelijker om alles te bekijken op hetzelfde moment, maar het kan ook leiden tot enige verwarring tussen de rollen.

Telemetrie van de browser wordt gewoonlijk opgenomen in dezelfde resource als de gegevens van de webrol op de server.

Plaats de resources Application Insights voor de verschillende componenten in één resourcegroep. Deze aanpak maakt het gemakkelijk om ze samen te beheren. 

### <a name="separate-development-test-and-production"></a>Ontwikkelings-, test- en productiegegevens scheiden
Als u aangepaste gebeurtenissen ontwikkelt voor uw volgende functie terwijl de vorige versie live is, kunt u de via telemetrie verzamelde ontwikkelingsgegevens het beste verzenden naar een afzonderlijke Application Insights-resource. Anders kan het moeilijk zijn om uw test telemetrie te vinden tussen al het verkeer van de live site.

Om deze situatie te voorkomen, maakt u afzonderlijke bronnen voor elke buildconfiguratie of "stempel" (ontwikkeling, test, productie, enzovoort) van uw systeem. Plaats de resources voor elke buildconfiguratie in een afzonderlijke resourcegroep. 

Als u de telemetrie naar de juiste bronnen wilt verzenden, u de Application Insights SDK zo instellen dat deze een andere instrumentatiesleutel oppikt, afhankelijk van de buildconfiguratie. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Een Application Insights-resource maken voor elke rol

Als u hebt besloten om voor elke rol een afzonderlijke bron te maken en misschien een aparte set voor elke buildconfiguratie, is het het gemakkelijkst om ze allemaal te maken in de Portal Application Insights. Als u veel resources maakt, u [het proces automatiseren.](../../azure-monitor/app/powershell.md)

1. Selecteer in de [Azure-portal][portal] **de** > optie**Nieuwe Ontwikkelaarsservices-toepassingsinzichten****Developer Services** > .  

    ![Deelvenster Toepassingsinzichten](./media/cloudservices/01-new.png)

1. Selecteer **ASP.NET webtoepassing**in de vervolgkeuzelijst **Toepassingstype** .

Elke resource wordt geïdentificeerd door een instrumentatiesleutel. Mogelijk hebt u deze sleutel later nodig als u de configuratie van de SDK handmatig wilt configureren of verifiëren.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Azure Diagnostics instellen voor elke rol
Stel deze optie in om uw app te controleren met Application Insights. Voor webrollen biedt deze optie prestatiebewaking, waarschuwingen, diagnose en gebruiksanalyse. Voor andere rollen u Azure Diagnostics zoeken en controleren, zoals opnieuw opstarten, prestatiemeteritems en oproepen naar System.Diagnostics.Trace. 

1. Open in Visual Studio Solution Explorer onder  >  ** \<YourCloudService>** **Rollen**de eigenschappen van elke rol.

1. Schakel in **Configuratie**het selectievakje **Diagnostische gegevens verzenden naar Application Insights** in en schakel vervolgens de toepassingsinzichtenbron in die u eerder hebt gemaakt.

Als u hebt besloten om een afzonderlijke Application Insights-resource voor elke buildconfiguratie te gebruiken, selecteert u eerst de configuratie.

![Toepassingsinzichten configureren](./media/cloudservices/configure-azure-diagnostics.png)

Dit heeft tot gevolg dat uw instrumentatietoetsen van Application Insights worden ingevoegd in de bestanden met de naam *ServiceConfiguration.\* cscfg*. Hier is de [voorbeeldcode.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)

Als u het niveau van diagnostische gegevens wilt variëren dat naar Application Insights wordt verzonden, u dit doen [door de *.cscfg-bestanden* rechtstreeks te bewerken.](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)

## <a name="install-the-sdk-in-each-project"></a><a name="sdk"></a>De SDK installeren in elk project
Met deze optie u aangepaste bedrijfstelemetrie toevoegen aan elke rol. De optie biedt een nadere analyse van hoe uw app wordt gebruikt en presteert.

Gebruik Visual Studio om de Application Insights-SDK voor elk cloudtoepassingsproject te configureren.

1. Als u **webrollen wilt**configureren, klikt u met de rechtermuisknop op het project en selecteert u **Toepassingsinzichten configureren** of **> telemetrie van Application Insights toevoegen.**

1. Werkrollen **worker roles**configureren: 

    a. Klik met de rechtermuisknop op het project en selecteer **NuGet-pakketten beheren**.

    b. Voeg [Application Insights voor Windows Servers](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) toe.

    ![Naar Application Insights zoeken](./media/cloudservices/04-ai-nuget.png)

1. Ga als u de SDK configureert om gegevens naar de bron Application Insights te verzenden:

    a. Stel in een geschikte opstartfunctie de instrumentatiesleutel in vanuit de configuratie-instelling in het *.cscfg-bestand:*
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    b. Herhaal "stap a" voor elke rol in uw app. Zie de voorbeelden:
   
    * [Webrol](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Werkrol](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [Voor webpagina's](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Stel het bestand *ApplicationInsights.config* in om altijd naar de uitvoermap te worden gekopieerd.

   Een bericht in het *.config-bestand* vraagt u om de instrumentatiesleutel daar te plaatsen. Voor cloud-apps is het echter beter om het in te stellen vanuit het *CSCFG-bestand.* Deze aanpak zorgt ervoor dat de rol correct wordt geïdentificeerd in het portaal.

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>Statusmonitor instellen om volledige SQL-query's te verzamelen (optioneel)

Deze stap is alleen nodig als u volledige SQL-query's wilt vastleggen op .NET Framework. 

1. In `\*.csdef` bestand [Opstarttaak](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks) toevoegen voor elke rol die vergelijkbaar is met 

    ```xml
    <Startup>
      <Task commandLine="AppInsightsAgent\InstallAgent.bat" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="ApplicationInsightsAgent.DownloadLink" value="http://go.microsoft.com/fwlink/?LinkID=522371" />
          <Variable name="RoleEnvironment.IsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
    
2. Download [InstallAgent.bat](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat) en [InstallAgent.ps1](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1), `AppInsightsAgent` zet ze in de map voor elk rolproject. Zorg ervoor dat u ze kopieert naar de uitvoermap via de eigenschappen van Het Bestand van Visual Studio of scripts maakt.

3. Voeg in alle werknemersrollen omgevingsvariabelen toe: 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>De app uitvoeren en publiceren

1. Voer uw app uit en meld u aan bij Azure. 

1. Open de resources Application Insights die u hebt gemaakt.

   Afzonderlijke gegevenspunten worden weergegeven in [Zoeken][diagnostic]en geaggregeerde gegevens worden weergegeven in [Metric Explorer](../../azure-monitor/platform/metrics-charts.md).

1. Voeg meer telemetrie toe (zie de volgende secties) en publiceer uw app om live diagnostische gegevens en feedback over het gebruik te krijgen. 

Als er geen gegevens zijn, gaat u als volgt te werk:

1. Als u afzonderlijke gebeurtenissen wilt weergeven, opent u de tegel [Zoeken.][diagnostic]
1. Open in de app verschillende pagina's zodat deze enige telemetrie genereert.
1. Wacht een paar seconden en klik op **Vernieuwen.**  

Zie [Probleemoplossing][qna] voor meer informatie .

## <a name="view-azure-diagnostics-events"></a>Azure Diagnostics-gebeurtenissen weergeven
Op de volgende locaties vindt u de Azure [Diagnostics-informatie](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) in Toepassingsinzichten:

* Prestatiemeteritems worden weergegeven als aangepaste functies voor het verzamelen van metrische gegevens. 
* Windows-gebeurtenislogboeken worden als traceringen en aangepaste gebeurtenissen weergegeven.
* Toepassinglogboeken, ETW-logboeken en logboeken met diagnostische gegevens over de infrastructuur worden weergegeven als traceringen.

Als u prestatiemeteritems en tellingen van gebeurtenissen wilt weergeven, opent u [Metrics Explorer](../../azure-monitor/platform/metrics-charts.md) en voegt u de volgende grafiek toe:

![Azure Diagnostics-gegevens](./media/cloudservices/23-wad.png)

Als u wilt zoeken in de verschillende traceringslogboeken die door Azure Diagnostics worden verzonden, gebruikt u [Zoeken](../../azure-monitor/app/diagnostic-search.md) of een [Analytics-query](../../azure-monitor/log-query/get-started-portal.md). Stel dat u een onverwerkte uitzondering hebt waardoor een rol is vastgelopen en gerecycled. Deze informatie zou worden weergegeven in het toepassingskanaal van het Windows-gebeurtenislogboek. U Zoeken gebruiken om de fout van Windows Event Log weer te geven en de volledige stacktracering voor de uitzondering op te vragen. Hierdoor u de oorzaak van het probleem vinden.

![Azure Diagnostics-zoekopdracht](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Meer telemetrie
In de volgende secties wordt besproken hoe u extra telemetrie krijgen van verschillende aspecten van uw app.

## <a name="track-requests-from-worker-roles"></a>Aanvragen van werknemersrollen bijhouden
In webrollen worden door de aanvraagmodule automatisch gegevens over HTTP-aanvragen verzameld. Zie het [voorbeeld MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)voor voorbeelden van hoe u het standaardverzamelingsgedrag overschrijven. 

U kunt de prestaties van aanroepen voor werkrollen vastleggen door ze op dezelfde manier bij te houden als HTTP-aanvragen. In Application Insights wordt met het telemetrietype Request een werkeenheid op een benoemde server gemeten die kan worden getimed en als onafhankelijk item kan slagen of mislukken. Hoewel HTTP-aanvragen automatisch worden vastgelegd door de SDK, u uw eigen code invoegen om aanvragen voor werknemersrollen bij te houden.

Bekijk de twee voorbeeldrollen die zijn uitgevoerd om aanvragen te rapporteren: 
* [WorkerRoleA (WorkerRoleA)](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB (WorkerRoleB)](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Uitzonderingen
Zie Uitzonderingen controleren [in Application Insights](../../azure-monitor/app/asp-net-exceptions.md)voor informatie over het verzamelen van niet-afgehandelde uitzonderingen van verschillende typen webapps.

De voorbeeldwebrol heeft MVC5- en Web API 2-controllers. De onverwerkte uitzonderingen van de twee werkrollen worden vastgelegd met de volgende handlers:

* [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) ingesteld voor MVC5-controllers [zoals weergegeven in dit voorbeeld](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) ingesteld voor Web API 2 controllers [zoals weergegeven in dit voorbeeld](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

Voor werknemersrollen u uitzonderingen op twee manieren bijhouden:

* TrackException(ex) gebruiken.
* Als u het NuGet-pakket voor tracelistener Van Toepassing-inzichten hebt toegevoegd, u System.Diagnostics.Trace gebruiken om uitzonderingen in te loggen zoals in dit voorbeeld wordt [weergegeven.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

## <a name="performance-counters"></a>Prestatiemeteritems
Voor de volgende prestatiemeteritems worden gegevens verzameld:

* \Proces(?? APP_WIN32_PROC??) \% Processortijd
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Voor webrollen worden ook gegevens verzameld voor de volgende prestatiemeteritems:

* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

U aanvullende aangepaste of andere Windows-prestatiemeteritems opgeven door *ApplicationInsights.config* te bewerken [zoals in dit voorbeeld wordt weergegeven.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14)

  ![Prestatiemeteritems](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Gecorreleerde telemetrie voor werknemersrollen
Voor een uitgebreide diagnostische ervaring u bekijken wat heeft geleid tot een mislukte of hoge latentieaanvraag. Met webrollen stelt de SDK automatisch een correlatie tussen gerelateerde telemetrie in. 

Als u deze weergave voor werknemersrollen wilt bereiken, u een aangepaste telemetrieinitialisator gebruiken om een gemeenschappelijk Operation.Id contextkenmerk in te stellen voor alle telemetrie. Als u dit doet, u in één oogopslag zien of het probleem met de latentie of het falen is veroorzaakt door een afhankelijkheid of uw code. 

Dit doet u al volgt:

* Stel de correlationId in een CallContext [in, zoals in dit voorbeeld](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36)wordt weergegeven . In dit geval gebruiken we de aanvraag-id als correlatie-id.
* Voeg een aangepaste telemetrieinitializer-implementatie toe om de Operation.Id in te stellen op de eerder ingestelde correlationId. Zie [itemcorrelatietelemetryinitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)voor een voorbeeld .
* Voeg de aangepaste telemetrie-initializer toe. U dit doen in het bestand *ApplicationInsights.config* of in code [zoals in dit voorbeeld](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Telemetrie op de client
Zie [JavaScript SDK toevoegen aan uw webpagina's][client]om op browsergebaseerde telemetrie te krijgen, zoals het aantal paginaweergave's, laadtijden of scriptuitzonderingen, en om aangepaste telemetrie in uw paginascripts te schrijven.

## <a name="availability-tests"></a>Beschikbaarheidstests
Stel [webtests in][availability]om ervoor te zorgen dat uw app live en responsief blijft.

## <a name="display-everything-together"></a>Een totaaloverzicht weergeven
Voor een totaalbeeld van uw systeem u de belangrijkste controlegrafieken samen weergeven op één [dashboard.](../../azure-monitor/app/overview-dashboard.md) U kunt bijvoorbeeld het aantal aanvragen en het aantal fouten voor elke rol aan het dashboard vastmaken. 

Als uw systeem andere Azure-services gebruikt, zoals Stream Analytics, neemt u ook de bewakingsgrafieken op. 

Als u een mobiele client-app hebt, gebruikt u [App Center](../../azure-monitor/learn/mobile-center-quickstart.md). Maak query’s in [Analytics](../../azure-monitor/app/analytics.md) om de aantallen gebeurtenissen weer te geven en aan het dashboard vast te maken.

## <a name="example"></a>Voorbeeld
In [het voorbeeld](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) wordt een service gecontroleerd die een webrol en twee werkrollen heeft.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Uitzondering "methode niet gevonden" bij het uitvoeren in Azure-cloudservices
Hebt u uw app ontwikkeld voor .NET 4.6? .NET 4.6 wordt niet automatisch ondersteund in Azure-functies voor cloudservices. [Installeer .NET 4.6 voor elke rol](../../cloud-services/cloud-services-dotnet-install-dotnet.md) voordat u uw app uitvoert.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Volgende stappen
* [Het verzenden van gegevens van Azure Diagnostics naar Application Insights configureren](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Resources voor Application Insights automatisch maken](../../azure-monitor/app/powershell.md)
* [Azure-diagnose automatiseren](../../azure-monitor/app/powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[azure]: ../../azure-monitor/app/app-insights-overview.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md 
