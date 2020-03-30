---
title: Logboekgebeurtenissen genereren vanuit een .NET-app
description: Meer informatie over het toevoegen van logboekregistratie aan uw .NET Service Fabric-toepassing die wordt gehost op een Azure-cluster of een zelfstandig cluster.
author: srrengar
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: srrengar
ms.openlocfilehash: 8c4721584e74bd7f7111c516f2d16bd190392bb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614363"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Logboekregistratie toevoegen aan uw Service Fabric-toepassing

Uw toepassing moet voldoende informatie verstrekken om het forensisch te debuggen wanneer zich problemen voordoen. Logging is een van de belangrijkste dingen die u toevoegen aan uw Service Fabric-toepassing. Wanneer er een storing optreedt, kan een goede logging u een manier geven om fouten te onderzoeken. Door logpatronen te analyseren, u manieren vinden om de prestaties of het ontwerp van uw toepassing te verbeteren. Dit document toont een aantal verschillende registratieopties.

## <a name="eventflow"></a>EventFlow

Met de [EventFlow-bibliotheeksuite](https://github.com/Azure/diagnostics-eventflow) kunnen toepassingen bepalen welke diagnostische gegevens moeten worden verzameld en waar ze moeten worden uitgevoerd. Diagnostische gegevens kunnen van alles zijn, van prestatiemeteritems tot toepassingssporen. Het wordt uitgevoerd in hetzelfde proces als de toepassing, zodat de communicatie overhead wordt geminimaliseerd. Zie [Azure Service Fabric Event Aggregation with EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)voor meer informatie over EventFlow en Service Fabric.

### <a name="using-structured-eventsource-events"></a>Gestructureerde EventSource-gebeurtenissen gebruiken

Als u berichtgebeurtenissen definieert op gebruikscase, u gegevens over de gebeurtenis verpakken in de context van de gebeurtenis. U gemakkelijker zoeken en filteren op basis van de namen of waarden van de opgegeven gebeurteniseigenschappen. Het structureren van de instrumentatie-uitvoer maakt het gemakkelijker om te lezen, maar vereist meer gedachte en tijd om een gebeurtenis voor elke use case te definiëren. 

Sommige gebeurtenisdefinities kunnen worden gedeeld over de hele toepassing. Een gebeurtenis voor het starten of stoppen van methoden wordt bijvoorbeeld hergebruikt voor veel services binnen een toepassing. Een domeinspecifieke service, zoals een bestelsysteem, heeft mogelijk een **CreateOrder-gebeurtenis,** die zijn eigen unieke gebeurtenis heeft. Deze aanpak kan veel gebeurtenissen genereren en vereist mogelijk coördinatie van id's tussen projectteams. 

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The instance constructor is private to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceTypeRegisteredEventId = 3;
    [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
    public void ServiceTypeRegistered(int hostProcessId, string serviceType)
    {
        WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
    }

    // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceHostInitializationFailedEventId = 4;
    [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
    public void ServiceHostInitializationFailed(string exception)
    {
        WriteEvent(ServiceHostInitializationFailedEventId, exception);
    }

    ...

```

### <a name="using-eventsource-generically"></a>EventSource generiek gebruiken

Omdat het definiëren van specifieke gebeurtenissen moeilijk kan zijn, definiëren veel mensen een paar gebeurtenissen met een gemeenschappelijke set parameters die hun informatie over het algemeen als een tekenreeks uitzetten. Veel van het gestructureerde aspect gaat verloren en het is moeilijker om de resultaten te zoeken en te filteren. In deze benadering worden enkele gebeurtenissen gedefinieerd die meestal overeenkomen met de registratieniveaus. In het volgende fragment wordt een foutopsporing en foutbericht gedefinieerd:

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The Instance constructor is private, to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    private const int DebugEventId = 10;
    [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
    public void Debug(string msg)
    {
        WriteEvent(DebugEventId, msg);
    }

    private const int ErrorEventId = 11;
    [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
    public void Error(string error, string msg)
    {
        WriteEvent(ErrorEventId, error, msg);
    }

    ...

```

Het gebruik van een hybride van gestructureerde en generieke instrumentatie kan ook goed werken. Gestructureerde instrumentatie wordt gebruikt voor het rapporteren van fouten en statistieken. Algemene gebeurtenissen kunnen worden gebruikt voor de gedetailleerde logboekregistratie die door technici wordt gebruikt voor het oplossen van problemen.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logboekregistratie

De ASP.NET Core logging[(Microsoft.Extensions.Logging NuGet package)](https://www.nuget.org/packages/Microsoft.Extensions.Logging)is een logging framework dat een standaard logging API voor uw toepassing biedt. Ondersteuning voor andere logging backends kan worden aangesloten op ASP.NET Core logging. Dit geeft u een breed scala aan ondersteuning voor het inloggen in uw aanvraag wordt verwerkt, zonder veel code te veranderen.

1. Voeg het **NuGet-pakket microsoft.extensions.logging** toe aan het project dat u wilt gebruiken. Voeg ook eventuele providerpakketten toe. Zie [Inloggen in ASP.NET Kern](https://docs.microsoft.com/aspnet/core/fundamentals/logging)voor meer informatie.
2. Voeg een **gebruiksrichtlijn** voor **Microsoft.Extensions.Logging** toe aan uw servicebestand.
3. Definieer een privévariabele binnen uw serviceklasse.

   ```csharp
   private ILogger _logger = null;
   ```

4. Voeg in de constructor van uw serviceklasse de als volgt de leiding:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Begin met het instrumenteren van uw code in uw methoden. Hier zijn een paar voorbeelden:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Andere loggingproviders gebruiken

Sommige externe providers maken gebruik van de in de vorige sectie beschreven aanpak, waaronder [Serilog](https://serilog.net/), [NLog](https://nlog-project.org/)en [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). U elk van deze stekker in ASP.NET Core logging, of u ze afzonderlijk gebruiken. Serilog heeft een functie die alle berichten van een logger verrijkt. Deze functie kan handig zijn om de servicenaam, het type en de partitiegegevens uit te zetten. Ga als volgt te werk om deze mogelijkheid te gebruiken in de ASP.NET Core-infrastructuur:

1. Voeg de **Serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Literate**en **Serilog.Sinks.Observable** NuGet pakketten aan het project. 
2. Maak `LoggerConfiguration` een en de logger instantie.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Voeg `Serilog.ILogger` een argument toe aan de serviceconstructor en geef de nieuw gemaakte logger door.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. Maakt in de serviceconstructor eigenschapsverrijkingen voor **ServiceTypeName,** **ServiceName,** **PartitionId**en **InstanceId**.

   ```csharp
   public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
       : base(context)
   {
       PropertyEnricher[] properties = new PropertyEnricher[]
       {
           new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
           new PropertyEnricher("ServiceName", context.ServiceName),
           new PropertyEnricher("PartitionId", context.PartitionId),
           new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
       };

       serilog.ForContext(properties);

       _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
   }
   ```

5. Instrument de code hetzelfde als wanneer u ASP.NET Core zonder Serilog gebruikt.

   >[!NOTE]
   >We raden *don't* u aan de `Log.Logger` statische knop niet te gebruiken in het voorgaande voorbeeld. Service Fabric kan meerdere exemplaren van hetzelfde servicetype hosten binnen één proces. Als u de `Log.Logger`statische , de laatste schrijver van de eigenschap verrijkende geeft waarden voor alle instanties die worden uitgevoerd. Dit is een van de redenen waarom de _logger variabele is een prive-lid variabele van de service klasse. U moet ook `_logger` de algemene code beschikbaar stellen, die mogelijk wordt gebruikt voor alle services.

## <a name="next-steps"></a>Volgende stappen

- Lees meer informatie over [applicatiebewaking in Service Fabric](service-fabric-diagnostics-event-generation-app.md).
- Lees meer over logboekregistratie met [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) en [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).










