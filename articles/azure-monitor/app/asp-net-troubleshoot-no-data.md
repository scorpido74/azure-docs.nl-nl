---
title: Problemen met ontbrekende gegevens oplossen - Application Insights voor .NET
description: Ziet u geen gegevens in Azure Application Insights? Probeer het hier.
ms.topic: conceptual
ms.date: 07/23/2018
ms.openlocfilehash: 34fc51f8f656ec0f630bd984ac1b28fbaa5e4dae
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802583"
---
# <a name="troubleshooting-no-data---application-insights-for-netnet-core"></a>Probleemoplossing voor geen gegevens - Application Insights voor .NET/.NET Core

## <a name="some-of-my-telemetry-is-missing"></a>Sommige van mijn telemetrie ontbreekt
*In Application Insights zie ik slechts een fractie van de gebeurtenissen die door mijn app worden gegenereerd.*

* Als u consequent dezelfde fractie ziet, is dit waarschijnlijk te wijten aan adaptieve [bemonstering.](../../azure-monitor/app/sampling.md) Als u dit wilt bevestigen, opent u Zoeken (in het overzichtsblad) en bekijkt u een instantie van een aanvraag of een andere gebeurtenis. Klik onder aan de sectie eigenschappen op "..." om volledige eigendomsgegevens te krijgen. Als het aantal aanvragen > 1, wordt de bemonstering uitgevoerd.
* Anders is het mogelijk dat u een limiet voor de [gegevenssnelheid](../../azure-monitor/app/pricing.md#limits-summary) voor uw prijsplan bereikt. Deze limieten worden per minuut toegepast.

*Ik ervaar willekeurig gegevensverlies.*

* Controleren of u gegevensverlies ondervindt bij [Telemetry Channel](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost)

* Controleer op bekende problemen in Telemetry Channel [GitHub repo](https://github.com/Microsoft/ApplicationInsights-dotnet/issues)

*Ik ervaar gegevensverlies in de Console App of in Web App wanneer de app op het punt staat te stoppen.*

* SDK-kanaal houdt telemetrie in buffer, en stuurt ze in batches. Als de toepassing wordt afgesloten, moet u mogelijk expliciet [Flush()](api-custom-events-metrics.md#flushing-data)bellen. Gedrag `Flush()` van is afhankelijk van het werkelijke [kanaal](telemetry-channels.md#built-in-telemetry-channels) dat wordt gebruikt.

## <a name="no-data-from-my-server"></a>Geen gegevens van mijn server
*Ik installeerde mijn app op mijn webserver, en nu zie ik er geen telemetrie van. Het werkte OK op mijn dev machine.*

* Waarschijnlijk een firewall probleem. [Stel firewalluitzonderingen in voor Application Insights om gegevens te verzenden.](../../azure-monitor/app/ip-addresses.md)
* IIS Server mist mogelijk enkele vereisten: .NET Extensibility 4.5 en ASP.NET 4.5.

*Ik heb [Status Monitor](../../azure-monitor/app/monitor-performance-live-website-now.md) op mijn webserver geïnstalleerd om bestaande apps te controleren. Ik zie geen resultaten.*

* Zie [Statusmonitor probleemoplossing](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot).

## <a name="no-add-application-insights-option-in-visual-studio"></a><a name="q01"></a>Geen optie 'Application Insights' toevoegen in Visual Studio
*Wanneer ik met de rechtermuisknop op een bestaand project klik in Solution Explorer, zie ik geen opties voor toepassingsinzichten.*

* Niet alle soorten .NET-projecten worden ondersteund door de tools. Web- en WCF-projecten worden ondersteund. Voor andere projecttypen, zoals desktop- of servicetoepassingen, u nog steeds [handmatig een Application Insights SDK aan uw project toevoegen.](../../azure-monitor/app/windows-desktop.md)
* Zorg ervoor dat u [Visual Studio 2013 Update 3 of hoger hebt.](https://docs.microsoft.com/visualstudio/releasenotes/vs2013-update3-rtm-vs) Het wordt geleverd met een voorgeïnstalleerde Developer Analytics-tools, die de Application Insights SDK bieden.
* Selecteer **Hulpprogramma's,** **extensies en updates** en controleer of **hulpprogramma's voor ontwikkelaarsanalyse** zijn geïnstalleerd en ingeschakeld. Klik dan op **Updates** om te zien of er een update beschikbaar is.
* Open het dialoogvenster Nieuw project en kies ASP.NET webtoepassing. Als u daar de optie Application Insights ziet, worden de hulpprogramma's geïnstalleerd. Als dit niet het zo is, probeert u de hulpprogramma's voor ontwikkelaarsanalyse te verwijderen en vervolgens opnieuw te installeren.

## <a name="adding-application-insights-failed"></a><a name="q02"></a>Toepassingsinzichten toevoegen is mislukt
*Wanneer ik Application Insights probeer toe te voegen aan een bestaand project, zie ik een foutmelding.*

Waarschijnlijke oorzaken:

* Communicatie met de Portal Application Insights is mislukt; Of
* Er is een probleem met uw Azure-account;
* U hebt alleen [leestoegang tot het abonnement of de groep waar u de nieuwe bron probeerde te maken.](../../azure-monitor/app/resources-roles-access-control.md)

Fix:

* Controleer of u aanmeldingsreferenties hebt opgegeven voor het juiste Azure-account.
* Controleer in uw browser of u toegang hebt tot de [Azure-portal.](https://portal.azure.com) Open Instellingen en kijk of er een beperking is.
* [Toepassingsinzichten toevoegen aan uw bestaande project:](../../azure-monitor/app/asp-net.md)Klik in Solution Explorer met de rechtermuisknop op uw project en kies 'Toepassingsinzichten toevoegen'.

## <a name="i-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>Ik krijg een foutmelding "Instrumentatiesleutel kan niet leeg zijn"
Het lijkt erop dat er iets mis is gegaan tijdens het installeren van Application Insights of misschien een logging adapter.

Klik in Solution Explorer met de rechtermuisknop op uw project en kies **Application Insights > Application Insights configureren.** U krijgt een dialoogvenster waarin u wordt uitgenodigd om u aan te melden bij Azure en een Application Insights-bron maakt of een bestaande bron opnieuw gebruikt.

## <a name="nuget-packages-are-missing-on-my-build-server"></a><a name="NuGetBuild"></a>"NuGet pakket(s) ontbreken" op mijn build server
*Alles bouwt OK als ik ben debugging op mijn ontwikkeling machine, maar ik krijg een NuGet fout op de build server.*

Zie [NuGet Package Restore](https://docs.nuget.org/Consume/Package-Restore) en [Automatic Package Restore](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Opdracht Ontbrekend menu om Toepassingsinzichten vanuit Visual Studio te openen
*Wanneer ik met de rechtermuisknop op mijn project Solution Explorer klik, zie ik geen opdrachten voor Application Insights of zie ik geen opdracht Inzichten voor toepassingen openen.*

Waarschijnlijke oorzaken:

* Als u de resource Application Insights handmatig hebt gemaakt of als het project een type is dat niet wordt ondersteund door de tools Voor Application Insights.
* De tools voor Developer Analytics zijn uitgeschakeld in uw Visual Studio.
* Uw Visual Studio is ouder dan 2013 Update 3.

Fix:

* Zorg ervoor dat uw Visual Studio-versie update 3 of hoger 2013 is.
* Selecteer **Hulpprogramma's,** **extensies en updates** en controleer of **de hulpprogramma's voor Developer Analytics** zijn geïnstalleerd en ingeschakeld. Klik dan op **Updates** om te zien of er een update beschikbaar is.
* Klik met de rechtermuisknop op uw project in Solution Explorer. Als u de opdracht **Application Insights > Application Insights configureren**ziet, gebruikt u deze om uw project te verbinden met de bron in de Application Insights-service.

Anders wordt uw projecttype niet rechtstreeks ondersteund door de tools voor Developer Analytics. Als u uw telemetrie wilt bekijken, meldt u zich aan bij de [Azure-portal,](https://portal.azure.com)kiest u Toepassingsinzichten op de linkernavigatiebalk en selecteert u uw toepassing.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>'Toegang geweigerd' bij opening Application Insights van Visual Studio
*De menuopdracht 'Open Application Insights' brengt me naar de Azure-portal, maar ik krijg een foutmelding 'toegang geweigerd'.*

De Microsoft-aanmelding die u voor het laatst hebt gebruikt in uw standaardbrowser, heeft geen toegang tot [de bron die is gemaakt toen Application Insights aan deze app is toegevoegd.](../../azure-monitor/app/asp-net.md) Er zijn twee waarschijnlijke redenen:

* Je hebt meer dan een Microsoft-account - misschien een werk en een persoonlijk Microsoft-account? De aanmelding die u voor het laatst in uw standaardbrowser hebt gebruikt, was voor een ander account dan het account dat toegang heeft om [Application Insights aan het project toe te voegen.](../../azure-monitor/app/asp-net.md)
  * Oplossing: klik rechtsboven in het browservenster op uw naam en meld u af. Meld je dan aan met het account dat toegang heeft. Klik vervolgens op de linkernavigatiebalk op Toepassingsinzichten en selecteer uw app.
* Iemand anders heeft Application Insights aan het project toegevoegd en is vergeten u toegang te geven [tot de brongroep](../../azure-monitor/app/resources-roles-access-control.md) waarin het is gemaakt.
  * Oplossing: Als ze een organisatieaccount hebben gebruikt, kunnen ze je toevoegen aan het team; of ze kunnen u individuele toegang verlenen tot de resourcegroep.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>'Asset not found' bij opening Application Insights van Visual Studio
*De menuopdracht 'Open Application Insights' brengt me naar de Azure-portal, maar ik krijg een fout 'asset not found'.*

Waarschijnlijke oorzaken:

* De application Insights-bron voor uw toepassing is verwijderd. Of
* De instrumentatiesleutel is ingesteld of gewijzigd in ApplicationInsights.config door deze rechtstreeks te bewerken, zonder het projectbestand bij te werken.

De instrumentatiesleutel in ApplicationInsights.config bepaalt waar de telemetrie wordt verzonden. Een regel in het projectbestand bepaalt welke resource wordt geopend wanneer u de opdracht in Visual Studio gebruikt.

Fix:

* Klik in Solution Explorer met de rechtermuisknop op het project en kies Toepassingsinzichten, Toepassingsinzichten configureren. In het dialoogvenster u ervoor kiezen om telemetrie naar een bestaande bron te verzenden of een nieuwe resource te maken. Of:
* Open de bron rechtstreeks. Meld u aan bij [de Azure-portal,](https://portal.azure.com)klik op Toepassingsinzichten op de linkernavigatiebalk en selecteer vervolgens uw app.

## <a name="where-do-i-find-my-telemetry"></a>Waar vind ik mijn telemetrie?
*Ik heb me aangemeld bij de [Microsoft Azure-portal](https://portal.azure.com)en ik bekijk het Azure-thuisdashboard. Dus waar vind ik mijn Application Insights gegevens?*

* Klik op de linkernavigatiebalk op Toepassingsinzichten en vervolgens op de naam van uw app. Als u daar geen projecten hebt, moet u [Application Insights toevoegen of configureren in uw webproject.](../../azure-monitor/app/asp-net.md)  
  Daar zie je een aantal overzichtsgrafieken. U door hen klikken om meer details te zien.
* Klik in Visual Studio terwijl u uw app debugt op de knop Toepassingsinzichten.

## <a name="no-server-data-or-no-data-at-all"></a><a name="q03"></a>Geen servergegevens (of helemaal geen gegevens)
*Ik heb mijn app uitgevoerd en vervolgens de Application Insights-service geopend in Microsoft Azure, maar in alle grafieken staat 'Leer hoe te verzamelen...' of 'Niet geconfigureerd.'* Of *alleen paginaweergave en gebruikersgegevens, maar geen servergegevens.*

* Voer uw toepassing uit in de foutopsporingsmodus in Visual Studio (F5). Gebruik de toepassing om wat telemetrie te genereren. Controleer of u gebeurtenissen zien die zijn aangemeld in het uitvoervenster Visual Studio.  
  ![](./media/asp-net-troubleshoot-no-data/output-window.png)
* Open [Diagnostisch zoeken](../../azure-monitor/app/diagnostic-search.md)in de portal Application Insights . Gegevens verschijnen meestal hier eerst.
* Klik op de knop Vernieuwen. Het blad verfrist zich regelmatig, maar je het ook handmatig doen. Het vernieuwingsinterval is langer voor grotere tijdsbereiken.
* Controleer de instrumentatietoetsen overeenkomen. Kijk op het hoofdblad voor uw app in de Application Insights-portal in de vervolgkeuzelijst **Essentials** naar **de toets Instrumentatie.** Open vervolgens in uw project in Visual Studio ApplicationInsights.config en vind de `<instrumentationkey>`. Controleer of de twee toetsen gelijk zijn. Zo niet:  
  * Klik in de portal op Toepassingsinzichten en zoek naar de app-bron met de juiste sleutel. Of
  * Klik in Visual Studio Solution Explorer met de rechtermuisknop op het project en kies Toepassingsinzichten configureren. Stel de app opnieuw in om telemetrie naar de juiste bron te verzenden.
  * Als u de overeenkomende toetsen niet vinden, controleert u of u dezelfde aanmeldingsreferenties gebruikt in Visual Studio als in de portal.
* Bekijk de kaart Servicestatus in het [Microsoft Azure-thuisdashboard.](https://portal.azure.com) Als er een aantal waarschuwingsaanwijzingen zijn, wacht u tot ze zijn teruggekeerd naar OK en sluit en opent u het toepassingsblad van Application Insights opnieuw.
* Check ook [onze status blog](https://blogs.msdn.microsoft.com/servicemap-status/).
* Heb je code geschreven voor de SDK aan de [serverzijde](../../azure-monitor/app/api-custom-events-metrics.md) die de instrumentatiesleutel in `TelemetryClient` instanties of in? `TelemetryContext` Of heb je een [filter of sampling configuratie](../../azure-monitor/app/api-filtering-sampling.md) die zou kunnen filteren te veel?
* Als u ApplicationInsights.config hebt bewerkt, controleert u zorgvuldig de configuratie van [telemetrieinitializers en telemetrieprocessors.](../../azure-monitor/app/api-filtering-sampling.md) Een verkeerd benoemde type of parameter kan ertoe leiden dat de SDK geen gegevens verzendt.

## <a name="no-data-on-page-views-browsers-usage"></a><a name="q04"></a>Geen gegevens over paginaweergaven, browsers, gebruik
*Ik zie gegevens in de grafieken Serverresponstijd en Serveraanvragen, maar geen gegevens in de laadtijd van de paginaweergave of in de browser- of gebruiksbladen.*

De gegevens zijn afkomstig van scripts op de webpagina's. 

* Als u Application Insights aan een bestaand webproject hebt toegevoegd, [moet u de scripts met de hand toevoegen.](../../azure-monitor/app/javascript.md)
* Controleer of Internet Explorer uw site niet weergeeft in de compatibiliteitsmodus.
* Gebruik de foutopsporingsfunctie van de browser (F12 in sommige browsers en kies `dc.services.visualstudio.com`Netwerk) om te controleren of gegevens naar .

## <a name="no-dependency-or-exception-data"></a>Geen afhankelijkheids- of uitzonderingsgegevens
Zie [telemetrie van afhankelijkheid](../../azure-monitor/app/asp-net-dependencies.md) en [uitzonderingstelemetrie](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Geen prestatiegegevens
Prestatiegegevens (CPU, IO-snelheid, enzovoort) zijn beschikbaar voor [Java-webservices,](../../azure-monitor/app/java-collectd.md) [Windows-bureaubladapps,](../../azure-monitor/app/windows-desktop.md) [IIS-webapps en -services als u statusmonitor installeert](../../azure-monitor/app/monitor-performance-live-website-now.md)en [Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md). je vindt het onder Instellingen, Servers.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Geen (server)gegevens sinds ik de app op mijn server heb gepubliceerd
* Controleer of u daadwerkelijk alle Microsoft hebt gekopieerd. ApplicationInsights DLL's naar de server, samen met Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* In uw firewall moet u mogelijk [bepaalde TCP-poorten openen.](../../azure-monitor/app/ip-addresses.md)
* Als u een proxy moet gebruiken om uit uw bedrijfsnetwerk te verzenden, stelt u [standaardProxy](https://msdn.microsoft.com/library/aa903360.aspx) in web.config in
* Windows Server 2008: Zorg ervoor dat u de volgende updates hebt geïnstalleerd: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Ik gebruikte om gegevens te zien, maar het is gestopt
* Controleer de [status blog](https://blogs.msdn.com/b/applicationinsights-status/).
* Heeft u uw maandelijkse quotum van gegevenspunten bereikt? Open de instellingen/quota en prijzen om erachter te komen. Als dat het geval is, u uw abonnement upgraden of betalen voor extra capaciteit. Zie het [prijsschema](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Ik zie niet alle gegevens die ik verwacht
Als uw toepassing veel gegevens verzendt en u de Application Insights SDK gebruikt voor ASP.NET versie 2.0.0-beta3 of hoger, kan de [functie adaptieve bemonstering](../../azure-monitor/app/sampling.md) slechts een percentage van uw telemetrie bedienen en verzenden.

U het uitschakelen, maar dit wordt niet aanbevolen. Bemonstering is zo ontworpen dat gerelateerde telemetrie correct wordt verzonden, voor diagnostische doeleinden.

## <a name="client-ip-address-is-0000"></a>Ip-adres van client is 0,0.0.0

Op 5 februari 2018 hebben we aangekondigd dat we de logging van het IP-adres van de client hebben verwijderd. Dit heeft geen invloed op geolocatie.

> [!NOTE]
> Als u de eerste 3 octetten van het IP-adres nodig hebt, u een [telemetrieinitialisator](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer) gebruiken om een aangepast kenmerk toe te voegen.
> Dit heeft geen invloed op gegevens die zijn verzameld vóór 5 februari 2018.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Verkeerde geografische gegevens in gebruikerstelemetrie
De afmetingen van de stad, de regio en het land zijn afgeleid van IP-adressen en zijn niet altijd nauwkeurig. Deze IP-adressen worden eerst voor locatie verwerkt en vervolgens gewijzigd in 0.0.0.0 om te worden opgeslagen.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Uitzondering Methode niet gevonden bij het uitvoeren van uw app in Azure Cloud Services
Hebt u uw app ontwikkeld voor .NET 4.6? 4.6 wordt niet automatisch ondersteund in Azure Cloud Services-rollen. [Installeer 4.6 voor elke rol](../../cloud-services/cloud-services-dotnet-install-dotnet.md) voordat u uw app uitvoert.

## <a name="troubleshooting-logs"></a>Problemen met logboeken oplossen

Volg deze instructies om logboeken voor het oplossen van problemen vast te leggen voor uw framework.

### <a name="net-framework"></a>.NET Framework

1. Installeer het [Microsoft.AspNet.ApplicationInsights.HostingStartup-pakket](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) van NuGet. De versie die u installeert, moet overeenkomen met de huidige geïnstalleerde versie van`Microsoft.ApplicationInsighs`

2. Wijzig het bestand applicationinsights.config om het volgende op te nemen:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.HostingStartup.FileDiagnosticsTelemetryModule, Microsoft.AspNet.ApplicationInsights.HostingStartup">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    Uw toepassing moet schrijfmachtigingen hebben voor de geconfigureerde locatie

3. Start het proces opnieuw op, zodat deze nieuwe instellingen worden opgepikt door SDK

4. Deze wijzigingen terugdraaien wanneer u klaar bent.

### <a name="net-core"></a>.NET Core

1. Installeer het [Microsoft.AspNet.ApplicationInsights.HostingStartup-pakket](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) van NuGet. De versie die u installeert, moet overeenkomen met de huidige geïnstalleerde versie van`Microsoft.ApplicationInsights`

De nieuwste versie van Microsoft.ApplicationInsights.AspNetCore is 2.8.2 en verwijst naar Microsoft.ApplicationInsights versie 2.11.2. Vandaar dat de versie van Microsoft.AspNet.ApplicationInsights.HostingStartup moet worden geïnstalleerd 2.11.2

2. Wijzig `ConfigureServices` de `Startup.cs` methode in uw klasse.:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    Uw toepassing moet schrijfmachtigingen hebben voor de geconfigureerde locatie

3. Start het proces opnieuw op, zodat deze nieuwe instellingen worden opgepikt door SDK

4. Deze wijzigingen terugdraaien wanneer u klaar bent.


## <a name="collect-logs-with-perfview"></a><a name="PerfView"></a>Logboeken verzamelen met PerfView
[PerfView](https://github.com/Microsoft/perfview) is een gratis diagnostische en prestatieanalysetool die cpu-, geheugen- en andere problemen isoleren door diagnostische informatie uit vele bronnen te verzamelen en te visualiseren.

De Application Insights SDK log EventSource zelfoplossend logboeken voor zelfoplossend probleem die door PerfView kunnen worden vastgelegd.

Als u logboeken wilt verzamelen, downloadt u PerfView en voert u deze opdracht uit:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

U deze parameters indien nodig wijzigen:
- **MaxCollectsec**. Stel deze parameter in om te voorkomen dat PerfView voor onbepaalde tijd wordt uitgevoerd en de prestaties van uw server beïnvloedt.
- **AlleenProviders**. Stel deze parameter in om alleen logboeken van de SDK te verzamelen. U deze lijst aanpassen op basis van uw specifieke onderzoeken. 
- **NoGui.** Stel deze parameter in om logboeken te verzamelen zonder de GUI.


Meer informatie
- [Prestatiesporen vastleggen met PerfView](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Gebeurtenisbronnen voor toepassingsinzichten](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/ETW)

## <a name="how-to-remove-application-insights"></a>Toepassingsinzichten verwijderen

Meer informatie over het verwijderen van toepassingsinzichten in Visual Studio door de stappen in het [verwijderingsartikel](../../azure-monitor/app/remove-application-insights.md)te volgen.

## <a name="still-not-working"></a>Nog steeds niet werkt ...
* [Application Insights forum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
