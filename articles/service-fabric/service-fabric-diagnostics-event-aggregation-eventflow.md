---
title: Aggregatie van Azure Service Fabric-gebeurtenis met Gebeurtenisstroom
description: Meer informatie over het aggregeren en verzamelen van gebeurtenissen met EventFlow voor monitoring en diagnostiek van Azure Service Fabric-clusters.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: cde24657cc8ed78b91e72df16d51df4077a6e030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463089"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Gebeurtenisaggregatie en verzameling met EventFlow

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) kan gebeurtenissen van een knooppunt routeren naar een of meer bewakingsbestemmingen. Omdat het is opgenomen als een NuGet-pakket in uw serviceproject, worden EventFlow-code en configuratiereizen met de service geëlimineerd, waardoor het configuratieprobleem per knooppunt dat eerder over Azure Diagnostics is vermeld, wordt geëlimineerd. EventFlow wordt uitgevoerd binnen uw serviceproces en maakt rechtstreeks verbinding met de geconfigureerde uitvoer. Vanwege de directe verbinding werkt EventFlow voor Azure-, container- en on-premises service-implementaties. Wees voorzichtig als u EventFlow uitvoert in scenario's met een hoge dichtheid, zoals in een container, omdat elke EventFlow-pijplijn een externe verbinding maakt. Dus als je meerdere processen host, krijg je verschillende uitgaande verbindingen! Dit is niet zozeer een zorg voor Service Fabric-toepassingen, omdat alle replica's van een `ServiceType` run in hetzelfde proces, en dit beperkt het aantal uitgaande verbindingen. EventFlow biedt ook gebeurtenisfiltering, zodat alleen de gebeurtenissen die overeenkomen met het opgegeven filter worden verzonden.

## <a name="set-up-eventflow"></a>EventFlow instellen

EventFlow-binaries zijn beschikbaar als een set NuGet-pakketten. Als u EventFlow wilt toevoegen aan een Service Fabric-serviceproject, klikt u met de rechtermuisknop op het project in de Solution Explorer en kiest u 'NuGet-pakketten beheren'. Ga naar het tabblad Bladeren en`Diagnostics.EventFlow`zoek naar " ":

![EventFlow NuGet pakketten in Visual Studio NuGet package manager UI](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

U ziet een lijst van verschillende pakketten verschijnen, gelabeld met "Inputs" en "Uitgangen". EventFlow ondersteunt verschillende logging providers en analyzers. De service hosting EventFlow moet geschikte pakketten bevatten, afhankelijk van de bron en bestemming voor de toepassingslogboeken. Naast het kernServiceFabric-pakket hebt u ook ten minste één input en uitvoer geconfigureerd. U bijvoorbeeld de volgende pakketten toevoegen om EventSource-gebeurtenissen naar Application Insights te verzenden:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource`om gegevens uit de klasse EventSource van de service vast te leggen en van standaard EventSources zoals *Microsoft-ServiceFabric-Services* en *Microsoft-ServiceFabric-Actors*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`(we gaan de logboeken naar een Azure Application Insights-bron verzenden)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(maakt initialisatie van de EventFlow-pijplijn mogelijk vanuit de configuratie van de Service Fabric-service en rapporteert eventuele problemen met het verzenden van diagnostische gegevens als servicefabric-statusrapporten)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource`pakket vereist dat het serviceproject zich richt op .NET Framework 4.6 of nieuwer. Zorg ervoor dat u het juiste doelkader in projecteigenschappen instelt voordat u dit pakket installeert.

Nadat alle pakketten zijn geïnstalleerd, is de volgende stap het configureren en inschakelen van EventFlow in de service.

## <a name="configure-and-enable-log-collection"></a>Logboekverzameling configureren en inschakelen
De EventFlow-pijplijn die verantwoordelijk is voor het verzenden van de logboeken, wordt gemaakt op basis van een specificatie die is opgeslagen in een configuratiebestand. Het `Microsoft.Diagnostics.EventFlow.ServiceFabric` pakket installeert een start-EventFlow-configuratiebestand onder `PackageRoot\Config` de oplossingsmap met de naam `eventFlowConfig.json`. Dit configuratiebestand moet worden gewijzigd om gegevens `EventSource` uit de standaardserviceklasse en alle andere invoer die u wilt configureren vast te leggen en gegevens naar de juiste plaats te verzenden.

>[!NOTE]
>Als uw projectbestand visualstudio 2017-indeling heeft, wordt het `eventFlowConfig.json` bestand niet automatisch toegevoegd. Als u dit wilt `Config` oplossen, maakt u `Copy if newer`het bestand in de map en stelt u de buildactie in op . 

Hier is een voorbeeld *eventFlowConfig.json* op basis van de NuGet pakketten hierboven vermeld:
```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

De naam van serviceEventSource van de service is `EventSourceAttribute` de waarde van de eigenschap Naam van de eigenschap toegepast op de klasse ServiceEventSource. Het is allemaal `ServiceEventSource.cs` opgegeven in het bestand, dat deel uitmaakt van de servicecode. In het volgende codefragment is de naam van de ServiceEventSource bijvoorbeeld *MyCompany-Application1-Stateless1:*

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Houd `eventFlowConfig.json` er rekening mee dat het bestand deel uitmaakt van het serviceconfiguratiepakket. Wijzigingen in dit bestand kunnen worden opgenomen in volledige- of configuratie-upgrades van de service, onder voorbehoud van servicefabric-upgradestatuscontroles en automatische terugdraaiing als er een upgradefout optreedt. Zie [Service Fabric-toepassingsupgrade](service-fabric-application-upgrade.md)voor meer informatie.

Met het *filtergedeelte* van de config u de informatie die via de EventFlow-pijplijn gaat, verder aanpassen aan de uitvoer, zodat u bepaalde informatie laten vallen of opnemen of de structuur van de gebeurtenisgegevens wijzigen. Zie [EventFlow-filters voor](https://github.com/Azure/diagnostics-eventflow#filters)meer informatie over filteren .

De laatste stap is om de EventFlow-pijplijn te instantiëren `Program.cs` in de opstartcode van uw service, die zich in het bestand bevindt:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

De naam die wordt `CreatePipeline` doorgegeven `ServiceFabricDiagnosticsPipelineFactory` als de parameter van de methode van de is de naam van de *statusentiteit* die de gebeurtenisstroomlogboekverzamelingspijplijn vertegenwoordigt. Deze naam wordt gebruikt als EventFlow opkomt en fout loopt en deze rapporteert via het subsysteem Service Fabric health.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Servicefabric-instellingen en toepassingsparameters gebruiken in gebeurtenisFlowConfig

EventFlow ondersteunt het gebruik van Service Fabric-instellingen en toepassingsparameters om EventFlow-instellingen te configureren. U de parameters van de instellingen van de servicestructuur gebruiken met deze speciale syntaxis voor waarden:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>`is de naam van de sectie `<setting-name>` Service Fabric-configuratie en is de configuratie-instelling die de waarde biedt die wordt gebruikt om een EventFlow-instelling te configureren. Ga voor meer informatie over hoe u dit doet naar [Instellingen voor Servicefabric en toepassingsparameters voor ondersteuning.](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters)

## <a name="verification"></a>Verificatie

Start uw service en observeer het uitvoervenster Foutopsporing in Visual Studio. Nadat de service is gestart, moet u bewijs gaan zien dat uw service records verzendt naar de uitvoer die u hebt geconfigureerd. Navigeer naar uw gebeurtenisanalyse- en visualisatieplatform en bevestig dat logboeken zijn weergegeven (kan enkele minuten duren).

## <a name="next-steps"></a>Volgende stappen

* [Gebeurtenisanalyse en -visualisatie met toepassingsinzichten](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Gebeurtenisanalyse en -visualisatie met Azure Monitor-logboeken](service-fabric-diagnostics-event-analysis-oms.md)
* [EventFlow-documentatie](https://github.com/Azure/diagnostics-eventflow)
