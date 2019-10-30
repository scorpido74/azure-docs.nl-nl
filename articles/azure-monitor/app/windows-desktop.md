---
title: Gebruik en prestaties bewaken voor Windows-bureaublad-apps
description: Analyseer het gebruik en de prestaties van uw Windows-bureaublad-app met Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/29/2019
ms.openlocfilehash: a9dfc32a0f33db5639d5f74667a90a248dc358a1
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73052456"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Gebruik en prestaties bewaken in Klassieke Windows-bureaublad-apps

Toepassingen die on-premises worden gehost, in Azure, en in andere clouds, kunnen allemaal profiteren van Application Insights. De enige beperking is de noodzaak om [communicatie toe te staan](../../azure-monitor/app/ip-addresses.md) met de Application Insights-service. Voor het bewaken van UWP-toepassingen (Universal Windows Platform), raden we [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md) aan.

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Telemetrie verzenden naar Application Insights vanuit een Klassieke Windows-toepassing
1. Maak in de [Azure Portal](https://portal.azure.com) [een Application Insights-resource](../../azure-monitor/app/create-new-resource.md ). Kies ASP.NET-app als het toepassingstype.
2. Kopieer de instrumentatiesleutel. Zoek naar de sleutel in de vervolgkeuzelijst van Essentials van de nieuwe resource die u net hebt gemaakt. 
3. Bewerk in Visual Studio de NuGet-pakketten van uw app-project en voeg Microsoft.ApplicationInsights.WindowsServer toe. (Of kies Microsoft.ApplicationInsights als u alleen de kale API wilt hebben, zonder de standaardmodules voor het verzamelen van telemetrie.)
4. Stel de instrumentatiesleutel in uw code in:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *uw sleutel* `";`
   
    of in ApplicationInsights.config (als u een van de standaardtelemetriepakketten hebt geïnstalleerd):
   
    `<InstrumentationKey>`*uw sleutel*`</InstrumentationKey>` 
   
    Als u ApplicationInsights.config gebruikt, moet u ervoor zorgen dat de bijbehorende eigenschappen in Solution Explorer zijn ingesteld op **Buildactie = Inhoud, Naar uitvoermap kopiëren = Kopiëren**.
5. [Gebruik de API](../../azure-monitor/app/api-custom-events-metrics.md) om telemetrie te verzenden.
6. Voer uw app uit en Bekijk de telemetrie in de resource die u hebt gemaakt in de Azure Portal.

## <a name="telemetry"></a>Voorbeeldcode
```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
        {
            e.Cancel = true;

            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="override-storage-of-computer-name"></a>Opslag van computer naam overschrijven

Standaard verzamelt deze SDK de computer naam van het systeem dat telemetrie verzendt en opslaat. Als u de verzameling wilt overschrijven, moet u een initialisatie functie voor telemetrie gebruiken:

**Schrijf aangepaste TelemetryInitializer zoals hieronder.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here, you can pass an empty string if needed.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```
Exemplaar de initialisatie functie in het `Program.cs` `Main()` onderstaande methode de instrumentatie sleutel in te stellen:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
        }
```

## <a name="next-steps"></a>Volgende stappen
* [Een dashboard maken](../../azure-monitor/app/overview-dashboard.md)
* [Diagnostische gegevens doorzoeken](../../azure-monitor/app/diagnostic-search.md)
* [Metrische gegevens verkennen](../../azure-monitor/app/metrics-explorer.md)
* [Analytics-query's schrijven](../../azure-monitor/app/analytics.md)

