---
title: Momentopnamefoutopsporing inschakelen voor .NET-apps in Azure Service Fabric, Cloud Service en Virtuele machines | Microsoft Documenten
description: Snapshot Debugger inschakelen voor .NET-apps in Azure Service Fabric, Cloud Service en Virtuele Machines
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 194a2da23c8fb405c492df8f6ee173cc97fde4ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671336"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Snapshot Debugger inschakelen voor .NET-apps in Azure Service Fabric, Cloud Service en Virtuele Machines

Als uw ASP.NET of ASP.NET kerntoepassing wordt uitgevoerd in Azure App Service, wordt het ten zeerste aanbevolen om [Momentopnamefoutopsporing in te schakelen via de portalpagina Application Insights.](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) Als uw toepassing echter een aangepaste Snapshot Debugger-configuratie of een preview-versie van de .NET-kern vereist, moet deze instructie ***worden*** gevolgd naast de instructies voor [het inschakelen via de portalpagina Application Insights.](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)

Als uw toepassing wordt uitgevoerd in Azure Service Fabric, Cloud Service, Virtual Machines of on-premises machines, moeten de volgende instructies worden gebruikt. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Momentopnameverzameling configureren voor ASP.NET toepassingen

1. [Toepassingsinzichten inschakelen in uw web-app,](../../azure-monitor/app/asp-net.md)als u dit nog niet hebt gedaan.

2. Neem het [NuGet-pakket microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) op in uw app.

3. Stel indien nodig de configuratie Van de momentopnamefoutfout aan toegevoegd aan [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). De standaard configuratie van momentopnamefoutopsporing is grotendeels leeg en alle instellingen zijn optioneel. Hier is een voorbeeld met een configuratie die gelijkwaardig is aan de standaardconfiguratie:

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Momentopnamen worden alleen verzameld op uitzonderingen die worden gerapporteerd aan Application Insights. In sommige gevallen (bijvoorbeeld oudere versies van het .NET-platform) moet u mogelijk [uitzonderingsverzameling configureren](../../azure-monitor/app/asp-net-exceptions.md#exceptions) om uitzonderingen met momentopnamen in de portal te zien.


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>Momentopnameverzameling configureren voor toepassingen met ASP.NET Core 2.0 of hoger

1. [Application Insights inschakelen in uw ASP.NET Core web-app,](../../azure-monitor/app/asp-net-core.md)als u het nog niet hebt gedaan.

    > [!NOTE]
    > Zorg ervoor dat uw toepassing verwijst naar versie 2.1.1 of nieuwer van het Microsoft.ApplicationInsights.AspNetCore-pakket.

2. Neem het [NuGet-pakket microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) op in uw app.

3. Wijzig de klasse `Startup` van uw toepassing om de telemetrieprocessor van de snapshotcollector toe te voegen en te configureren.
    1. Als [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-pakketversie 1.3.5 of hoger wordt gebruikt, voegt u het volgende toe met instructies aan `Startup.cs`.

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       Voeg het volgende toe aan het einde `Startup` van `Startup.cs`de methode ConfigureServices in de klasse in .

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. Als [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-pakketversie 1.3.4 of lager wordt gebruikt, voegt u het volgende toe met instructies aan `Startup.cs`.

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```
    
       Voeg de `SnapshotCollectorTelemetryProcessorFactory` volgende `Startup` klasse toe aan de klasse.
    
       ```csharp
       class Startup
       {
           private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
           {
               private readonly IServiceProvider _serviceProvider;
    
               public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
                   _serviceProvider = serviceProvider;
    
               public ITelemetryProcessor Create(ITelemetryProcessor next)
               {
                   var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
                   return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
               }
           }
           ...
        ```
        Voeg `SnapshotCollectorConfiguration` de `SnapshotCollectorTelemetryProcessorFactory` services en services toe aan de opstartpijplijn:
    
        ```csharp
           // This method gets called by the runtime. Use this method to add services to the container.
           public void ConfigureServices(IServiceCollection services)
           {
               // Configure SnapshotCollector from application settings
               services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));
    
               // Add SnapshotCollector telemetry processor.
               services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));
    
               // TODO: Add other services your application needs here.
           }
       }
       ```

4. Stel indien nodig de configuratie van de momentopnamefoutfout aan door een sectie SnapshotCollectorConfiguration toe te voegen aan appsettings.json. Alle instellingen in de configuratie van momentopnamefoutopsporing zijn optioneel. Hier is een voorbeeld met een configuratie die gelijkwaardig is aan de standaardconfiguratie:

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Momentopnameverzameling configureren voor andere .NET-toepassingen

1. Als uw toepassing nog niet is uitgerust met Application Insights, gaat u aan de slag door [Toepassingsinzichten in te schakelen en de instrumentatietoets in te stellen.](../../azure-monitor/app/windows-desktop.md)

2. Voeg het [NuGet-pakket microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) toe in uw app.

3. Momentopnamen worden alleen verzameld op uitzonderingen die worden gerapporteerd aan Application Insights. Mogelijk moet u uw code wijzigen om deze te melden. De code voor het afhandelen van uitzonderingen is afhankelijk van de structuur van uw toepassing, maar een voorbeeld vindt hieronder:
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="next-steps"></a>Volgende stappen

- Verkeer genereren naar uw toepassing dat een uitzondering kan activeren. Wacht vervolgens 10 tot 15 minuten voordat momentopnamen naar het exemplaar Application Insights worden verzonden.
- Bekijk [momentopnamen](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) in de Azure-portal.
- Zie Problemen met [momentopnamefoutopsporing](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)voor hulp bij het oplossen van problemen met momentopnamenbugger .
