---
title: Een live ASP.NET-web-app bewaken met Azure Application Insights | Microsoft Docs
description: Bewaak de prestaties van een website zonder de website opnieuw te implementeren. Werkt met ASP.NET web-apps die on-premises of in VM's worden gehost.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 63d632df61548d15a1e0a606cf2e198207faf341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670046"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-codeless-attach"></a>Instrument web apps bij runtime met Application Insights Codeless Attach

> [!IMPORTANT]
> Status monitor wordt niet langer aanbevolen voor gebruik. Het is vervangen door de Azure Monitor Application Insights Agent (voorheen statusmonitor v2 genoemd). Bekijk onze documentatie voor [on-premises serverimplementaties](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) of [Azure virtual machine en virtual machine scale set deployments.](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps)

U kunt een live web-app instrumenteren met Azure Application Insights, zonder dat u de code hoeft te wijzigen of opnieuw hoeft te implementeren. U hebt een [Microsoft Azure](https://azure.com)-abonnement nodig.

Statusmonitor wordt gebruikt om een .NET-toepassing te instrumenteren die wordt gehost in IIS, on-premises of in een vm.

- Als uw app is geïmplementeerd in Azure VM of Azure virtual machine scale set, volgt u [deze instructies](azure-vm-vmss-apps.md).
- Als uw app is geïmplementeerd in Azure-app-services, volgt u [deze instructies](azure-web-apps.md).
- Als uw app is geïmplementeerd in een Azure VM, u de bewaking van Application Insights inschakelen vanuit het Azure-configuratiescherm.
- (Er zijn ook afzonderlijke artikelen over het bewerken van [Azure Cloud Services](../../azure-monitor/app/cloudservices.md).)


![Schermafbeelding van overzichtsgrafieken van App Insights met informatie over mislukte aanvragen, serverresponstijd en serveraanvragen](./media/monitor-performance-live-website-now/overview-graphs.png)

Je hebt de keuze uit twee routes om Application Insights toe te passen op je .NET-webapplicaties:

* **Tijdens het bouwen:** [voeg de Application Insights-SDK][greenbrown] toe aan uw web-app-code.
* **Tijdens het gebruik:** Instrumenteer uw web-app op de server, zoals hieronder wordt beschreven, zonder deze opnieuw op te bouwen en de code opnieuw te implementeren.

> [!NOTE]
> Als u de instrumentatie van de bouwtijd gebruikt, werkt de instrumentatie van de looptijd niet, zelfs niet als deze is ingeschakeld.

Hier volgt een samenvatting van wat elke route u biedt:

|  | Tijdens het bouwen | Tijdens het gebruik |
| --- | --- | --- |
| Aanvragen en uitzonderingen |Ja |Ja |
| [Meer gedetailleerde uitzonderingen](../../azure-monitor/app/asp-net-exceptions.md) | |Ja |
| [Diagnostische gegevens over afhankelijkheid](../../azure-monitor/app/asp-net-dependencies.md) |Op .NET 4.6+, maar minder details |Ja, volledige details: resultaatcodes, SQL-opdrachttekst, HTTP-woord|
| [Systeemprestatiemeteritems](../../azure-monitor/app/performance-counters.md) |Ja |Ja |
| [API voor aangepaste telemetrie][api] |Ja |Nee |
| [Integratie traceerlogboeken](../../azure-monitor/app/asp-net-trace-logs.md) |Ja |Nee |
| [Paginaweergave en gebruikersgegevens](../../azure-monitor/app/javascript.md) |Ja |Nee |
| Code moet worden herbouwd |Ja | Nee |



## <a name="monitor-a-live-iis-web-app"></a>Een live IIS-web-app bewaken

Als uw app wordt gehost op een IIS-server, kunt u Application Insights inschakelen met Status Monitor.

1. Meld u op uw IIS-webserver aan met beheerdersreferenties.
2. Als Application Insights Status Monitor nog niet is geïnstalleerd, download u het [installatieprogramma](#download) en voert u het uit
3. In Status Monitor selecteert u de geïnstalleerde web-app of website die u wilt bewaken. Meld u aan met uw Azure-referenties.

    Configureer de resource waarvan u de resultaten wilt weergeven in de Application Insights-portal. (Normaal gesproken is het het beste om een nieuwe resource te maken. Selecteer een bestaande resource als u al [webtests][availability] of [clientbewaking][client] hebt voor deze app.) 

    ![Kies een app en een resource.](./media/monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Start IIS opnieuw.

    ![Klik boven in het dialoogvenster op Restart.](./media/monitor-performance-live-website-now/appinsights-036-restart.png)

    Uw webservice wordt enkele ogenblikken onderbroken.

## <a name="customize-monitoring-options"></a>Bewakingsopties aanpassen

Als u Application Insights inschakelt, worden ddl-bestanden en het bestand ApplicationInsights.config toegevoegd aan uw web-app. U kunt [het bestand .config bewerken](../../azure-monitor/app/configuration-with-applicationinsights-config.md) om enkele van de opties te wijzigen.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Wanneer u uw app opnieuw publiceert, schakelt u Application Insights opnieuw in

Voordat u uw app opnieuw publiceert, overweegt u om [Application Insights toe te voegen aan de code in Visual Studio][greenbrown]. U krijgt dan meer gedetailleerde telemetrie en de mogelijkheid om aangepaste telemetrie te schrijven.

Als u opnieuw wilt publiceren zonder Application Insights toe te voegen aan de code, moet u zich realiseren dat tijdens het implementatieproces de ddl-bestanden en het bestand ApplicationInsights.config mogelijk worden verwijderd van de gepubliceerde website. Daarom:

1. Als u ApplicationInsights.config hebt bewerkt, maakt u er een kopie van voordat u de app opnieuw publiceert.
2. Publiceer uw app opnieuw.
3. Schakel Application Insights-bewaking opnieuw in. (Gebruik de juiste methode: het configuratiescherm van de Azure-web-app of Status Monitor op een IIS-host.)
4. Voer alle wijzigingen die u hebt doorgevoerd in het .config-bestand opnieuw door.


## <a name="troubleshooting"></a><a name="troubleshoot"></a>Probleemoplossing

### <a name="confirm-a-valid-installation"></a>Een geldige installatie bevestigen 

Dit zijn enkele stappen die u uitvoeren om te bevestigen dat uw installatie is geslaagd.

- Controleer of het bestand applicationInsights.config aanwezig is in de doel-app-map en uw ikey bevat.

- Als u vermoedt dat gegevens ontbreken, u een eenvoudige query uitvoeren in [Analytics](../log-query/get-started-portal.md) om alle cloudrollen weer te geven die momenteel telemetrie verzenden.
  ```Kusto
  union * | summarize count() by cloud_RoleName, cloud_RoleInstance
  ```

- Als u wilt bevestigen dat Application Insights is gekoppeld, u [Sysinternals Handle](https://docs.microsoft.com/sysinternals/downloads/handle) uitvoeren in een opdrachtvenster om te bevestigen dat applicationinsights.dll door IIS is geladen.
  ```cmd
  handle.exe /p w3wp.exe
  ```


### <a name="cant-connect-no-telemetry"></a>Kunt u geen verbinding maken? Geen telemetrie?

* Open [de benodigde uitgaande poorten](../../azure-monitor/app/ip-addresses.md#outgoing-ports) in de firewall van uw server om Status Monitor uit te voeren.

### <a name="unable-to-login"></a>Kan niet inloggen

* Als Statusmonitor niet kan inloggen, moet u in plaats daarvan een opdrachtregel installeren. Status monitor probeert in te loggen om uw ikey te verzamelen, maar u dit handmatig met behulp van de opdracht:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>Kan geen bestand of assemblage laden 'System.Diagnostics.DiagnosticSource'

Mogelijk krijgt u deze fout nadat u Application Insights hebt ingemaakt. Dit komt omdat het installatieprogramma deze dll vervangt in uw opslaglocatiemap.
Ga als eerste over een update van uw web.config:

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

We volgen dit probleem [hier.](https://github.com/Microsoft/ApplicationInsights-Home/issues/301)


### <a name="application-diagnostic-messages"></a>Diagnostische berichten voor toepassingen

* Open Status Monitor en selecteer in het linkerdeelvenster uw toepassing. Controleer in het gedeelte Configuration notifications of er diagnostische meldingen zijn voor de toepassing:

  ![Open de blade Performance om aanvragen, reactietijden, afhankelijkheden en andere gegevens te bekijken](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>Gedetailleerde logboeken

* Standaard voert statusmonitor diagnostische logboeken uit op:`C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`

* Als u breedboselogboeken wilt uitvoergeven, wijzigt u het config-bestand en `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` voegt u toe `<add key="TraceLevel" value="All" />` aan de `appsettings`.
Start vervolgens de statusmonitor opnieuw op.

* Aangezien Status monitor een .NET-toepassing is, u ook [.net-tracering inschakelen door de juiste diagnose toe te voegen aan het config-bestand.](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/trace-debug/system-diagnostics-element) In sommige scenario's kan het bijvoorbeeld handig zijn om te zien wat er op netwerkniveau gebeurt door [netwerktracering te configureren](https://docs.microsoft.com/dotnet/framework/network-programming/how-to-configure-network-tracing)

### <a name="insufficient-permissions"></a>Onvoldoende machtigingen
  
* Als u op de server een bericht over 'insufficient permissions' (onvoldoende machtigingen) ziet, probeert u het volgende:
  * Selecteer in IIS Manager uw groep met toepassingen, open **Advanced Settings** en noteer de identiteit onder **Proces Model**.
  * Voeg in het configuratiescherm voor computerbeheer deze identiteit toe aan de groep Prestatiemetergebruikers.

### <a name="conflict-with-systems-center-operations-manager"></a>Conflict met Operations Manager van Systems Center

* Als op uw server MMA/SCOM (Systems Center Operations Manager) is geïnstalleerd, kan er een conflict optreden met sommige versies. Verwijder zowel SCOM als Status Monitor en installeer de meest recente versies.

### <a name="failed-or-incomplete-installation"></a>Mislukte of onvolledige installatie

Als statusmonitor tijdens een installatie mislukt, u een onvolledige installatie behouden waarvan statusmonitor niet kan worden hersteld. Hiervoor is een handmatige reset nodig.

Verwijder een van deze bestanden in uw toepassingsmap:
- Alle DLL's in uw opslaglocatiemap die beginnen met 'Microsoft.AI'. of "Microsoft.ApplicationInsights".
- Deze DLL in uw opslaglocatiemap "Microsoft.Web.Infrastructure.dll"
- Deze DLL in uw bin directory "System.Diagnostics.DiagnosticSource.dll"
- Verwijder in uw toepassingsmap 'App_Data\packages'
- Verwijder in uw toepassingsmap "applicationinsights.config"


### <a name="additional-troubleshooting"></a>Andere problemen oplossen

* Zie Aanvullende [probleemoplossing][qna].

## <a name="system-requirements"></a>Systeemvereisten
Ondersteuning van het besturingssysteem voor Application Insights Status Monitor op de server:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

met de nieuwste SP en .NET Framework 4.5 (Status Monitor is gebouwd op deze versie van het framework)

Aan de clientzijde: Windows 7, 8, 8.1 en 10, eveneens met .NET Framework 4.5

Ondersteuning voor IIS is: IIS 7, 7.5, 8, 8.5 (IIS is vereist)

## <a name="automation-with-powershell"></a>Automatisering met PowerShell
Met PowerShell kunt u de bewaking op de IIS-server starten en stoppen.

Importeer eerst de module Application Insights:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Controleer welke apps worden bewaakt:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Optioneel) De naam van een web-app.
* Geeft de Application Insights-bewakingsstatus voor elke web-app (of de benoemde app) op deze IIS-server.
* Retourneert `ApplicationInsightsApplication` voor elke app:

  * `Start-ApplicationInsightsMonitoring`: de app wordt bewaakt en was in runtime geïnstrumenteerd, door het hulpprogramma Status Monitor of door `SdkState==EnabledAfterDeployment`.
  * `SdkState==Disabled` : de app is niet geïnstrumenteerd voor Application Insights. De app is niet geïnstrumenteerd, of bewaking tijdens de uitvoering is uitgeschakeld met het hulpprogramma Status Monitor of met `Stop-ApplicationInsightsMonitoring`.
  * `SdkState==EnabledByCodeInstrumentation`: de app was geïnstrumenteerd door de SDK toe te voegen aan de broncode. De SDK kan niet worden bijgewerkt of gestopt.
  * `SdkVersion` toont de versie die voor het bewaken van deze app wordt gebruikt.
  * `LatestAvailableSdkVersion` toont de versie die momenteel beschikbaar is in de NuGet-galerie. Als u de app naar deze versie wilt bijwerken, gebruikt u `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` De naam van de app in IIS
* `-InstrumentationKey` De iKey van de Application Insights-resource waar u de resultaten wilt weergeven.
* Deze cmdlet geldt alleen voor apps die niet al zijn geïnstrumenteerd, dus SdkState==NotInstrumented.

    De cmdlet heeft geen invloed op een app die al is geïmplementeerd. Het maakt niet uit of de app is geïmplementeerd tijdens het bouwen (toen de SDK aan de code werd toegevoegd), of tijdens de uitvoering (met gebruik van deze cmdlet).

    De SDK-versie die voor het instrumenteren van de app is gebruikt, is de versie die het laatst is gedownload naar deze server.

    Voor het downloaden van de meest recente versie gebruikt u Update-ApplicationInsightsVersion.
* Retourneert `ApplicationInsightsApplication` wanneer het is gelukt. Als het mislukt, wordt er een tracering geregistreerd in stderr.

          Name                      : Default Web Site/WebApp1
          InstrumentationKey        : 00000000-0000-0000-0000-000000000000
          ProfilerState             : ApplicationInsights
          SdkState                  : EnabledAfterDeployment
          SdkVersion                : 1.2.1
          LatestAvailableSdkVersion : 1.2.3

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` De naam van een app in IIS
* `-All` Stopt het bewaken van alle apps op deze IIS-server waarvoor `SdkState==EnabledAfterDeployment`
* Stopt het bewaken van de opgegeven apps en verwijdert instrumentatie. Deze functie werkt alleen voor apps die tijdens de uitvoering zijn geïnstrumenteerd met het hulpprogramma Status Monitor of Start-ApplicationInsightsApplication. (`SdkState==EnabledAfterDeployment`)
* Retourneert ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name` : de naam van een web-app in IIS.
* `-InstrumentationKey`(Optioneel.) Gebruik dit om de bron te wijzigen waarnaar de telemetrie van de app wordt verzonden.
* Deze cmdlet:
  * Upgradet de benoemde app naar de versie van de SDK die het laatst naar deze computer is gedownload. (Werkt alleen als `SdkState==EnabledAfterDeployment`)
  * Als u een instrumentatiesleutel opgeeft, wordt de vermelde app opnieuw geconfigureerd voor het verzenden van telemetrie naar de resource met die sleutel. (Werkt als `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* Downloadt de nieuwste Application Insights-SDK naar de server.

## <a name="questions-about-status-monitor"></a><a name="questions"></a>Vragen over Status Monitor

### <a name="what-is-status-monitor"></a>Wat is Status Monitor?

Een bureaubladtoepassing die u op uw IIS-webserver kunt installeren. Hiermee kunt u web-apps instrumenteren en configureren. 

### <a name="when-do-i-use-status-monitor"></a>Wanneer maak ik gebruik van Status Monitor?

* Wanneer u web-apps wilt instrumenteren die worden uitgevoerd op uw IIS-server, zelfs als deze al worden uitgevoerd.
* Om extra telemetrie mogelijk te maken voor web-apps die bij het compileren zijn [gebouwd met de Application Insights-SDK](../../azure-monitor/app/asp-net.md). 

### <a name="can-i-close-it-after-it-runs"></a>Kan ik de applicatie sluiten nadat deze is uitgevoerd?

Ja. Zodra de applicatie de gekozen websites heeft geïnstrumenteerd, kunt u deze sluiten.

Status Monitor verzamelt niet zelf telemetrie. Het configureert enkel de web-apps en stelt enkele machtigingen in.

### <a name="what-does-status-monitor-do"></a>Wat doet Status Monitor?

Wanneer u een web-app selecteert die u met Status Monitor wilt instrumenteren:

* Downloadt en plaatst de application insights-samenstellingen en het bestand ApplicationInsights.config in de binaire map van de web-app.
* Schakelt CLR-profilering in voor het verzamelen van afhankelijkheidsaanroepen.

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Welke versie van Application Insights SDK installeert Status Monitor?

Status Monitor kan vanaf nu alleen Application Insights SDK-versies 2.3 of 2.4 installeren. 

De Application Insights SDK Versie 2.4 is de [laatste versie die .NET 4.0 ondersteunt,](https://github.com/microsoft/ApplicationInsights-dotnet/releases/tag/v2.5.0-beta1) wat [EOL januari 2016](https://devblogs.microsoft.com/dotnet/support-ending-for-the-net-framework-4-4-5-and-4-5-1/)was. Daarom kan statusmonitor vanaf nu worden gebruikt om een .NET 4.0-toepassing te gebruiken. 

### <a name="do-i-need-to-run-status-monitor-whenever-i-update-the-app"></a>Moet ik Status Monitor telkens uitvoeren wanneer ik de app heb bijgewerkt?

Niet als u deze stapsgewijs opnieuw implementeert. 

Als u de optie 'Bestaande bestanden verwijderen' selecteert in het publicatieproces, dient u Status Monitor opnieuw uit te voeren om Application Insights te configureren.

### <a name="what-telemetry-is-collected"></a>Welke telemetrie wordt verzameld?

Voor toepassingen die u met behulp van de Status Monitor bij het uitvoeren instrumenteert:

* HTTP-aanvragen
* Afhankelijkheidsaanroepen
* Uitzonderingen
* Prestatiemeteritems

Voor toepassingen die bij het compileren al zijn geïnstrumenteerd:

 * Procestellers.
 * Afhankelijkheidsaanroepen (.NET 4.5); retourwaarden in afhankelijkheidsaanroepen (.NET 4.6).
 * Stack-tracewaarden van uitzonderingen.

[Meer informatie](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="download-status-monitor"></a><a name="download"></a>Statusmonitor downloaden

- De nieuwe [PowerShell-module gebruiken](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview)
- Het [installatieprogramma Statusmonitor](https://go.microsoft.com/fwlink/?LinkId=506648) downloaden en uitvoeren
- Of voer [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) uit en zoek daarin naar Application Insights Status Monitor.

## <a name="next-steps"></a><a name="next"></a>Volgende stappen

Uw telemetrie weergeven:

* [Verken de metrische gegevens](../../azure-monitor/app/metrics-explorer.md) om de prestaties en het gebruik te bewaken
* [Doorzoek gebeurtenissen en logboeken][diagnostic] om problemen te analyseren
* [Gebruik analyses](../../azure-monitor/app/analytics.md) voor meer geavanceerde query's

Meer telemetrie toevoegen:

* [Maak webtests][availability] om ervoor te zorgen dat uw site actief blijft.
* [Voeg telemetrie van de webclient toe][usage] om uitzonderingen op de webpaginacode weer te geven en traceringsaanroepen in te voegen.
* [Voeg de Application Insights-SDK toe aan uw code][greenbrown] zodat u tracerings- en logboekaanroepen kunt invoegen

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[usage]: ../../azure-monitor/app/javascript.md
