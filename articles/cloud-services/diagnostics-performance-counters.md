---
title: Verzamelen op prestatiemeteritems in Azure Cloud Services | Microsoft Documenten
description: Meer informatie over het ontdekken, gebruiken en maken van prestatiemeteritems in Cloud Services met Azure Diagnostics en Application Insights.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 02/02/2018
ms.author: tagore
ms.openlocfilehash: 3b4028a09f69acd5d7a6579b4610785ed32e227d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469524"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Prestatiemeteritems verzamelen voor uw Azure Cloud Service

Prestatiemeteritems bieden u een manier om bij te houden hoe goed uw toepassing en de host presteren. Windows Server biedt veel verschillende prestatiemeteritems met betrekking tot hardware, toepassingen, het besturingssysteem en meer. Door prestatiemeteritems naar Azure te verzamelen en te verzenden, u deze informatie analyseren om betere beslissingen te nemen. 

## <a name="discover-available-counters"></a>Beschikbare tellers ontdekken

Een prestatieteller bestaat uit twee delen, een vaste naam (ook wel categorie genoemd) en een of meer tellers. U PowerShell gebruiken om een lijst met beschikbare prestatiemeteritems te krijgen:

```powershell
Get-Counter -ListSet * | Select-Object CounterSetName, Paths | Sort-Object CounterSetName

CounterSetName                                  Paths
--------------                                  -----
.NET CLR Data                                   {\.NET CLR Data(*)\SqlClient...
.NET CLR Exceptions                             {\.NET CLR Exceptions(*)\# o...
.NET CLR Interop                                {\.NET CLR Interop(*)\# of C...
.NET CLR Jit                                    {\.NET CLR Jit(*)\# of Metho...
.NET Data Provider for Oracle                   {\.NET Data Provider for Ora...
.NET Data Provider for SqlServer                {\.NET Data Provider for Sql...
.NET Memory Cache 4.0                           {\.NET Memory Cache 4.0(*)\C...
AppV Client Streamed Data Percentage            {\AppV Client Streamed Data ...
ASP.NET                                         {\ASP.NET\Application Restar...
ASP.NET Apps v4.0.30319                         {\ASP.NET Apps v4.0.30319(*)...
ASP.NET State Service                           {\ASP.NET State Service\Stat...
ASP.NET v2.0.50727                              {\ASP.NET v2.0.50727\Applica...
ASP.NET v4.0.30319                              {\ASP.NET v4.0.30319\Applica...
Authorization Manager Applications              {\Authorization Manager Appl...

#... results cut to save space ...
```

De `CounterSetName` eigenschap vertegenwoordigt een set (of categorie) en is een goede indicator van waar de prestatiemeteritems mee te maken hebben. De `Paths` eigenschap vertegenwoordigt een verzameling van tellers voor een set. U ook `Description` de accommodatie krijgen voor meer informatie over de teller.

Als u alle tellers voor een `CounterSetName` set wilt `Paths` ophalen, gebruikt u de waarde en breidt u de verzameling uit. Elk paditem is een teller die u opvragen. Als u bijvoorbeeld de beschikbare tellers `Processor` met betrekking `Paths` tot de set wilt ophalen, vouwt u de verzameling uit:

```powershell
Get-Counter -ListSet * | Where-Object CounterSetName -eq "Processor" | Select -ExpandProperty Paths

\Processor(*)\% Processor Time
\Processor(*)\% User Time
\Processor(*)\% Privileged Time
\Processor(*)\Interrupts/sec
\Processor(*)\% DPC Time
\Processor(*)\% Interrupt Time
\Processor(*)\DPCs Queued/sec
\Processor(*)\DPC Rate
\Processor(*)\% Idle Time
\Processor(*)\% C1 Time
\Processor(*)\% C2 Time
\Processor(*)\% C3 Time
\Processor(*)\C1 Transitions/sec
\Processor(*)\C2 Transitions/sec
\Processor(*)\C3 Transitions/sec
```

Deze afzonderlijke tellerpaden kunnen worden toegevoegd aan het diagnoseframework dat uw cloudservice gebruikt. Zie [Een tellerpad opgeven](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))voor meer informatie over de wijze waarop een prestatietellerpad is opgebouwd.

## <a name="collect-a-performance-counter"></a>Een prestatiemeter verzamelen

Er kan een prestatiemeter aan uw cloudservice worden toegevoegd voor Azure Diagnostics of Application Insights.

### <a name="application-insights"></a>Application Insights

Met Azure Application Insights for Cloud Services u opgeven welke prestatiemeteritems u wilt verzamelen. Nadat u [Application Insights aan uw project hebt toegevoegd,](../azure-monitor/app/cloudservices.md#sdk)wordt een config-bestand met de naam **ApplicationInsights.config** toegevoegd aan uw Visual Studio-project. Dit config-bestand definieert welk type informatie Application Insights verzamelt en naar Azure verzendt.

Open het **bestand ApplicationInsights.config** en zoek het element **ApplicationInsights** > **TelemetryModules.** Elk `<Add>` onderliggend element definieert een type telemetrie om te verzamelen, samen met de configuratie. Het type telemetriemodule `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`van de prestatieteller is . Als dit element al is gedefinieerd, moet u het niet een tweede keer toevoegen. Elke prestatieteller die u wilt verzamelen, `<Counters>`wordt gedefinieerd onder een knooppunt met de naam . Hier is een voorbeeld dat schijfprestatiemeteritems verzamelt:

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Write Bytes/sec" ReportAs="Disk write (C:)" />
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Read Bytes/sec" ReportAs="Disk read (C:)" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

Elke prestatieteller wordt weergegeven `<Add>` als `<Counters>`een element onder . Het `PerformanceCounter` kenmerk bepaalt welke prestatiemeterteller moet worden verzameld. Het `ReportAs` kenmerk is de titel die moet worden weergegeven in de Azure-portal voor de prestatiemeter. Elke prestatiemeter die u verzamelt, wordt in de portal in een categorie met de naam **Aangepast** geplaatst. In tegenstelling tot Azure Diagnostics u het interval dat deze prestatiemeteritems worden verzameld en naar Azure worden verzonden, niet instellen. Met Application Insights worden prestatiemeteritems elke minuut verzameld en verzonden. 

Application Insights verzamelt automatisch de volgende prestatiemeteritems:

* \Proces(?? APP_WIN32_PROC??) \% Processortijd
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Zie [Systeemprestatiemeteritems voor](../azure-monitor/app/performance-counters.md) meer informatie in Application Insights en [Application Insights voor Azure Cloud Services.](../azure-monitor/app/cloudservices.md#performance-counters)

### <a name="azure-diagnostics"></a>Azure Diagnostics

> [!IMPORTANT]
> Hoewel al deze gegevens worden samengevoegd in het opslagaccount, **biedt** de portal geen native manier om de gegevens in kaart te brengen. Het is ten zeerste aan te raden om een andere diagnostische service, zoals Application Insights, in uw toepassing te integreren.

Met de Azure Diagnostics-extensie voor Cloud Services u opgeven welke prestatiemeteritems u wilt verzamelen. Zie [Overzicht van azure-servicebewaking](cloud-services-how-to-monitor.md#setup-diagnostics-extension)voor het instellen van Azure Diagnostics.

De prestatiemeteritems die u wilt verzamelen, worden gedefinieerd in het **bestand diagnostics.wadcfgx.** Open dit bestand (het wordt per rol gedefinieerd) in Visual Studio en zoek het element **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters.** Voeg als onderliggend element een nieuw **element PerformanceCounterConfiguration** toe. Dit element heeft twee `counterSpecifier` `sampleRate`kenmerken: en . Het `counterSpecifier` kenmerk bepaalt welke systeemprestatietellerset (beschreven in de vorige sectie) moet worden verzameld. De `sampleRate` waarde geeft aan hoe vaak die waarde wordt gepeild. Als geheel worden alle prestatietellers naar Azure `PerformanceCounters` overgebracht op `scheduledTransferPeriod` basis van de kenmerkwaarde van het bovenliggende element.

Zie het Azure `PerformanceCounters` Diagnostics Schema voor meer informatie over het [schemaelement](../azure-monitor/platform/diagnostics-extension-schema-windows.md#performancecounters-element).

De periode die `sampleRate` door het kenmerk wordt gedefinieerd, gebruikt het gegevenstype XML-duur om aan te geven hoe vaak het prestatiemeterteller wordt gepeild. In het onderstaande voorbeeld wordt `PT3M`het `[P]eriod[T]ime[3][M]inutes`tarief ingesteld op , wat betekent : om de drie minuten.

Zie de sectie `sampleRate` `scheduledTransferPeriod` **Duurgegevenstype** in de zelfstudie [W3 XML-datum- en tijddatumtypen](https://www.w3schools.com/XML/schema_dtypes_date.asp) voor meer informatie over hoe de sectie En worden gedefinieerd.

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />

          <!-- This is a new perf counter which will track the C: disk read activity in bytes per second, every minute -->
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(C:)\Disk Read Bytes/sec" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="create-a-new-perf-counter"></a>Een nieuwe perf-teller maken

Een nieuwe prestatiemeter kan worden gemaakt en gebruikt door uw code. Uw code die een nieuwe prestatiemeter maakt, moet verhoogd worden uitgevoerd, anders mislukt deze. Uw opstartcode voor cloudservices `OnStart` kan de prestatiemeter maken, waardoor u de rol in een verhoogde context moet uitvoeren. U ook een opstarttaak maken die verhoogd wordt uitgevoerd en de prestatiemeter maakt. Zie [Opstarttaken configureren en uitvoeren voor een cloudservice voor](cloud-services-startup-tasks.md)meer informatie over opstarttaken.

Als u uw rol wilt configureren `<Runtime>` om verhoogd uit te voeren, voegt u een element toe aan het [.csdef-bestand.](cloud-services-model-and-package.md#servicedefinitioncsdef)

```xml
<ServiceDefinition name="CloudServiceLoadTesting" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Large">

    <!-- ... cut to save space ... -->

    <Runtime executionContext="elevated">
      
    </Runtime>

    <!-- ... cut to save space ... -->

  </WorkerRole>
</ServiceDefinition>
```

U een nieuwe prestatiemeter maken en registreren met een paar regels code. Gebruik `System.Diagnostics.PerformanceCounterCategory.Create` de overbelasting van de methode die zowel de categorie als de teller maakt. Met de volgende code wordt eerst gecontroleerd of de categorie bestaat en als deze ontbreekt, wordt zowel de categorie als de teller gezoen.

```csharp
using System.Diagnostics;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRoleWithSBQueue1
{
    public class WorkerRole : RoleEntryPoint
    {
        // Perf counter variable representing times service was used.
        private PerformanceCounter counterServiceUsed;

        public override bool OnStart()
        {
            // ... Other startup code here ...

            // Define the category and counter names.
            string perfCounterCatName = "MyService";
            string perfCounterName = "Times Used";

            // Create the counter if needed. Our counter category only has a single counter.
            // Both the category and counter are created in the same method call.
            if (!PerformanceCounterCategory.Exists(perfCounterCatName))
            {
                PerformanceCounterCategory.Create(perfCounterCatName, "Collects information about the cloud service.", 
                                                  PerformanceCounterCategoryType.SingleInstance, 
                                                  perfCounterName, "How many times the cloud service was used.");
            }

            // Get reference to our counter
            counterServiceUsed = new PerformanceCounter(perfCounterCatName, perfCounterName);
            counterServiceUsed.ReadOnly = false;
            
            return base.OnStart();
        }

        // ... cut class code to save space
    }
}
```

Wanneer u de teller wilt `Increment` gebruiken, roept u de of `IncrementBy` methode aan.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Nu uw toepassing uw aangepaste teller gebruikt, moet u Azure Diagnostics of Application Insights configureren om de teller bij te houden.


### <a name="application-insights"></a>Application Insights

Zoals eerder vermeld, worden de prestatiemeteritems voor Application Insights gedefinieerd in het **bestand ApplicationInsights.config.** Open **ApplicationInsights.config** en zoek het element **ApplicationInsights** > **TelemetryModules** > **Add** > **Counters.** Maak `<Add>` een onderliggend element `PerformanceCounter` en stel het kenmerk in op de categorie en de naam van het prestatiemeterrecht dat u in uw code hebt gemaakt. Stel `ReportAs` het kenmerk in op een vriendelijke naam die u in de portal wilt zien.

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <!-- ... cut other perf counters to save space ... -->

        <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
        <Add PerformanceCounter="\MyService\Times Used" ReportAs="Service used counter" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

### <a name="azure-diagnostics"></a>Azure Diagnostics

Zoals eerder vermeld, worden de prestatietellers die u wilt verzamelen gedefinieerd in het **bestand diagnostics.wadcfgx.** Open dit bestand (het wordt per rol gedefinieerd) in Visual Studio en zoek het element **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters.** Voeg als onderliggend element een nieuw **element PerformanceCounterConfiguration** toe. Stel `counterSpecifier` het kenmerk in op de categorie en de naam van het prestatiemeterrecht dat u in uw code hebt gemaakt. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <!-- ... cut other perf counters to save space ... -->
          
          <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
          <PerformanceCounterConfiguration counterSpecifier="\MyService\Times Used" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="more-information"></a>Meer informatie

- [Application Insights voor Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters)
- [Systeemprestatiemeteritems in Application Insights](../azure-monitor/app/performance-counters.md)
- [Een tegenpad opgeven](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Azure Diagnostics-schema - Prestatiemeteritems](../azure-monitor/platform/diagnostics-extension-schema-windows.md#performancecounters-element)



