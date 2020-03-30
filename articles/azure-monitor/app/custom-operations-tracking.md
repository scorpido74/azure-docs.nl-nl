---
title: Aangepaste bewerkingen bijhouden met Azure Application Insights .NET SDK
description: Aangepaste bewerkingen bijhouden met Azure Application Insights .NET SDK
ms.topic: conceptual
ms.date: 11/26/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 31c1fb366e7b109ea1fa4977d8e2f908e766e0f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276099"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Aangepaste bewerkingen bijhouden met Application Insights .NET SDK

Azure Application Insights SDKs volgen automatisch binnenkomende HTTP-aanvragen en oproepen naar afhankelijke services, zoals HTTP-aanvragen en SQL-query's. Het bijhouden en correlatie van aanvragen en afhankelijkheden geeft u inzicht in de responsiviteit en betrouwbaarheid van de hele toepassing voor alle microservices die deze toepassing combineren. 

Er is een klasse van toepassingspatronen die niet generiek kunnen worden ondersteund. Een goede monitoring van dergelijke patronen vereist handmatige code-instrumentatie. Dit artikel bevat een aantal patronen waarvoor handmatige instrumentatie nodig is, zoals aangepaste wachtrijverwerking en het uitvoeren van langlopende achtergrondtaken.

Dit document biedt richtlijnen voor het bijhouden van aangepaste bewerkingen met de Application Insights SDK. Deze documentatie is relevant voor:

- Application Insights voor .NET (ook bekend als Base SDK) versie 2.4+.
- Application Insights voor webapplicaties (met ASP.NET) versie 2.4+.
- Application Insights voor ASP.NET Core versie 2.1+.

## <a name="overview"></a>Overzicht
Een bewerking is een logisch werk stuk dat wordt uitgevoerd door een toepassing. Het heeft een naam, begintijd, duur, resultaat en een context van uitvoering zoals gebruikersnaam, eigenschappen en resultaat. Als bewerking A is gestart door bewerking B, wordt bewerking B ingesteld als bovenliggend voor A. Een bewerking kan slechts één ouder hebben, maar kan veel onderliggende bewerkingen hebben. Zie [Azure Application Insights-telemetriecorrelatie](correlation.md)voor meer informatie over bewerkingen en telemetriecorrelatie.

In de Application Insights .NET SDK wordt de bewerking beschreven door de abstracte klasse [OperationTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) en de afstammelingen [RequestTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) and [DependencyTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Inkomende operationele tracking 
De Application Insights web SDK verzamelt automatisch HTTP-aanvragen voor ASP.NET toepassingen die worden uitgevoerd in een IIS-pijplijn en al ASP.NET Core-toepassingen. Er zijn door de community ondersteunde oplossingen voor andere platforms en frameworks. Als de toepassing echter niet wordt ondersteund door een van de standaard- of door de community ondersteunde oplossingen, u deze handmatig implementeren.

Een ander voorbeeld dat aangepaste tracking vereist, is de werknemer die items uit de wachtrij ontvangt. Voor sommige wachtrijen wordt de oproep om een bericht aan deze wachtrij toe te voegen, bijgehouden als een afhankelijkheid. De bewerking op hoog niveau die de verwerking van berichten beschrijft, wordt echter niet automatisch verzameld.

Laten we eens kijken hoe dergelijke operaties kunnen worden gevolgd.

Op een hoog niveau is `RequestTelemetry` het de taak om bekende eigenschappen te maken en in te stellen. Nadat de bewerking is voltooid, volgt u de telemetrie. In het volgende voorbeeld wordt deze taak aangetoond.

### <a name="http-request-in-owin-self-hosted-app"></a>HTTP-verzoek in owin zelf gehoste app
In dit voorbeeld wordt de traceringscontext gepropageerd volgens het [HTTP-protocol voor correlatie](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). U mag verwachten dat u headers ontvangt die daar worden beschreven.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    // you may create a new TelemetryConfiguration instance, reuse one you already have
    // or fetch the instance created by Application Insights SDK.
    private readonly TelemetryConfiguration telemetryConfiguration = TelemetryConfiguration.CreateDefault();
    private readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

Het HTTP-protocol voor `Correlation-Context` correlatie verklaart ook de koptekst. Echter, het is hier weggelaten voor eenvoud.

## <a name="queue-instrumentation"></a>Wachtrijinstrumentatie
Hoewel er [W3C Trace Context](https://www.w3.org/TR/trace-context/) en [HTTP-protocol voor correlatie](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) zijn om correlatiegegevens door te geven met HTTP-aanvraag, moet elk wachtrijprotocol bepalen hoe dezelfde details worden doorgegeven aan het wachtrijbericht. Sommige wachtrijprotocollen (zoals AMQP) maken het mogelijk om extra metagegevens door te geven en sommige andere (zoals Azure Storage Queue) vereisen dat de context wordt gecodeerd in de payload van het bericht.

> [!NOTE]
> * **Cross-component tracering wordt nog niet ondersteund voor wachtrijen** Als uw producent en consument telemetrie verzenden naar verschillende Application Insights-bronnen, tonen Transactiediagnostics-ervaring en toepassingskaart met HTTP transacties en kaartend op de kaart. In het geval van wachtrijen wordt dit nog niet ondersteund. 

### <a name="service-bus-queue"></a>Service Bus-wachtrij
Application Insights traceert Service Bus Messaging-gesprekken met de nieuwe [Microsoft Azure ServiceBus-client voor .NET-versie](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) 3.0.0 en hoger.
Als u [het patroon van de berichthandler](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) gebruikt om berichten te verwerken, bent u klaar: alle Service Bus-oproepen die door uw service worden uitgevoerd, worden automatisch bijgehouden en gecorreleerd met andere telemetrie-items. Raadpleeg de [servicebusclienttracering met Microsoft Application Insights](../../service-bus-messaging/service-bus-end-to-end-tracing.md) als u berichten handmatig verwerkt.

Als u het [WindowsAzure.ServiceBus-pakket](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) gebruikt, leest u verder - volgende voorbeelden laten zien hoe u oproepen naar de Servicebus bijhouden (en correleren) als servicebuswachtrij amqp-protocol gebruikt en Application Insights niet automatisch wachtrijbewerkingen bijhoudt.
Correlatie-id's worden doorgegeven in de berichteigenschappen.

#### <a name="enqueue"></a>Inzetten

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    
    operation.Telemetry.Type = "Azure Service Bus";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Proces
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Azure Storage-wachtrij
In het volgende voorbeeld ziet u hoe u de [wachtrijbewerkingen voor Azure Storage bijhoudt](../../storage/queues/storage-dotnet-how-to-use-queues.md) en telemetrie correleert tussen de producent, de consument en Azure Storage. 

De opslagwachtrij heeft een HTTP-API. Alle oproepen naar de wachtrij worden bijgehouden door het Afhankelijkheidsverzamelaar voor toepassingsinzichten voor HTTP-aanvragen.
Het is standaard geconfigureerd op ASP.NET en ASP.NET Core-toepassingen, met andere soorten toepassingen, u verwijzen naar documentatie voor [consoletoepassingen](../../azure-monitor/app/console.md)

U de bewerkings-id van Application Insights ook correleren met de opslagaanvraag-id. Zie [Azure Storage controleren, diagnosticeren en oplossen van Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing)voor informatie over het instellen en opvragen van een opslagaanvraag.

#### <a name="enqueue"></a>Inzetten
Omdat opslagwachtrijen de HTTP-API ondersteunen, worden alle bewerkingen met de wachtrij automatisch bijgehouden door Application Insights. In veel gevallen moet deze instrumentatie voldoende zijn. Echter, om sporen aan de consumentenkant te correleren met producersporen, moet u een correlatiecontext doorgeven die vergelijkbaar is met hoe we het doen in het HTTP-protocol voor correlatie. 

In dit voorbeeld ziet `Enqueue` u hoe u de bewerking bijhouden. U kunt:

 - **Correleren retries (indien aanwezig)**: Ze hebben `Enqueue` allemaal een gemeenschappelijke ouder dat is de bewerking. Anders worden ze gevolgd als kinderen van het binnenkomende verzoek. Als er meerdere logische aanvragen voor de wachtrij zijn, kan het moeilijk zijn om te achterhalen welke oproep heeft geleid tot nieuwe pogingen.
 - **Correleer opslaglogboeken (indien nodig)**: ze zijn gecorreleerd met application insights-telemetrie.

De `Enqueue` bewerking is het onderliggende kind van een bovenliggende bewerking (bijvoorbeeld een binnenkomende HTTP-aanvraag). De HTTP-afhankelijkheidsoproep is `Enqueue` het kind van de bewerking en het kleinkind van het binnenkomende verzoek:

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

Als u de hoeveelheid telemetrie wilt verminderen die uw `Enqueue` toepassingsrapporten zijn of `Activity` als u de bewerking om andere redenen niet wilt bijhouden, gebruikt u de API rechtstreeks:

- Maak (en start) `Activity` een nieuwe in plaats van de bewerking Application Insights te starten. U hoeft *er geen* eigenschappen aan toe te wijzen, behalve de naam van de bewerking.
- Serialiseren `yourActivity.Id` in het bericht `operation.Telemetry.Id`payload in plaats van . U `Activity.Current.Id`ook gebruik maken van.


#### <a name="dequeue"></a>Wachtrij
Net als `Enqueue`bij een daadwerkelijk HTTP-verzoek voor de opslagwachtrij wordt automatisch bijgehouden door Application Insights. De `Enqueue` bewerking vindt echter vermoedelijk plaats in de bovenliggende context, zoals een inkomende aanvraagcontext. Application Insights SDKs correleren een dergelijke bewerking (en het HTTP-onderdeel) automatisch met de bovenliggende aanvraag en andere telemetrie die in hetzelfde bereik wordt gerapporteerd.

De `Dequeue` operatie is lastig. De Application Insights SDK houdt automatisch HTTP-aanvragen bij. Het kent echter de correlatiecontext pas als het bericht is ontleed. Het is niet mogelijk om het HTTP-verzoek te correleren om het bericht te krijgen met de rest van de telemetrie, vooral wanneer er meer dan één bericht wordt ontvangen.

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("dequeue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Dequeue " + queue.Name;
    
    try
    {
        var message = await queue.GetMessageAsync();
    }
    catch (StorageException e)
    {
        operation.telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.telemetry.Success = false;
        operation.telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }

    return null;
}
```

#### <a name="process"></a>Proces

In het volgende voorbeeld wordt een binnenkomend bericht bijgehouden op een manier die vergelijkbaar is met de binnenkomende HTTP-aanvraag:

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };
    
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

Ook andere wachtrijbewerkingen kunnen worden uitgevoerd. Een peek-bewerking moet op dezelfde manier worden uitgevoerd als een dequeue-bewerking. Het instrumenteren van wachtrijbeheer is niet nodig. Application Insights houdt bewerkingen zoals HTTP bij en in de meeste gevallen is het voldoende.

Wanneer u het verwijderen van een instrument bericht instelt, moet u de id's van de bewerking (correlatie) instellen. U ook de `Activity` API gebruiken. Vervolgens hoeft u geen bewerkings-id's in te stellen op de telemetrie-items, omdat Application Insights SDK dit voor u doet:

- Maak een `Activity` nieuw item nadat u een item uit de wachtrij hebt ontvangen.
- Gebruiken `Activity.SetParentId(message.ParentId)` om consumenten- en producentenlogboeken te correleren.
- Start `Activity`de .
- Houd wachtrij-, proces- en `Start/StopOperation` verwijderbewerkingen bij met behulp van helpers. Doe het vanuit dezelfde asynchrone controlestroom (uitvoeringscontext). Op deze manier zijn ze goed gecorreleerd.
- Stop `Activity`de .
- Gebruik `Start/StopOperation`of `Track` bel telemetrie handmatig.

### <a name="dependency-types"></a>Afhankelijkheidstypen

Application Insights maakt gebruik van afhankelijkheidstype om ui-ervaringen te cusomize. Voor wachtrijen herkent `DependencyTelemetry` het volgende typen die [de ervaring voor transactiediagnostiek](/azure/azure-monitor/app/transaction-diagnostics)verbeteren:
- `Azure queue`wachtrijen voor Azure-opslag
- `Azure Event Hubs`voor Azure-gebeurtenishubs
- `Azure Service Bus`voor Azure Service Bus

### <a name="batch-processing"></a>Batchverwerking
Bij sommige wachtrijen u meerdere berichten met één verzoek de wachtrij en wachtrijen. Het verwerken van dergelijke berichten is vermoedelijk onafhankelijk en behoort tot de verschillende logische bewerkingen. Het is niet mogelijk `Dequeue` om de bewerking te correleren met een bepaald bericht dat wordt verwerkt.

Elk bericht moet worden verwerkt in zijn eigen asynchrone controlestroom. Zie de sectie [Uitgaande afhankelijkheden bijhouden](#outgoing-dependencies-tracking) voor meer informatie.

## <a name="long-running-background-tasks"></a>Langlopende achtergrondtaken

Sommige toepassingen starten langlopende bewerkingen die mogelijk worden veroorzaakt door gebruikersverzoeken. Vanuit het perspectief van tracering/instrumentatie verschilt het niet van aanvraag- of afhankelijkheidsinstrumentatie: 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

Hiermee `telemetryClient.StartOperation` maakt en `DependencyTelemetry` vult u de correlatiecontext. Stel dat u een bovenliggende bewerking hebt die is gemaakt door binnenkomende aanvragen die de bewerking hebben gepland. Zolang het `BackgroundTask` begint in dezelfde asynchrone controlestroom als een binnenkomende aanvraag, is deze gecorreleerd met die bovenliggende bewerking. `BackgroundTask`en alle geneste telemetrie-items worden automatisch gecorreleerd met het verzoek dat het heeft veroorzaakt, zelfs nadat het verzoek is beëindigd.

Wanneer de taak begint vanaf de achtergrondthread die`Activity`geen bewerking `BackgroundTask` () heeft, heeft geen bovenliggende informatie. Het kan echter geneste bewerkingen hebben. Alle telemetrie-items die vanuit de `DependencyTelemetry` taak `BackgroundTask`worden gerapporteerd, zijn gecorreleerd met de gemaakte in .

## <a name="outgoing-dependencies-tracking"></a>Uitgaande afhankelijkheden bijhouden
U uw eigen afhankelijkheidssoort of een bewerking bijhouden die niet wordt ondersteund door Application Insights.

De `Enqueue` methode in de wachtrij servicebus of de opslagwachtrij kan als voorbeelden dienen voor dergelijke aangepaste tracking.

De algemene aanpak voor het bijhouden van aangepaste afhankelijkheid is:

- Roep `TelemetryClient.StartOperation` de (extensie)methode `DependencyTelemetry` aan die de eigenschappen vult die nodig zijn voor correlatie en enkele andere eigenschappen (begintijdstempel, duur).
- Stel andere aangepaste `DependencyTelemetry`eigenschappen in op de , zoals de naam en elke andere context die u nodig hebt.
- Maak een afhankelijkheidsgesprek en wacht erop.
- Stop de `StopOperation` bewerking met wanneer deze klaar is.
- Uitzonderingen afhandelen.

```csharp
public async Task RunMyTaskAsync()
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1"))
    {
        try 
        {
            var myTask = await StartMyTaskAsync();
            // Update status code and success as appropriate.
        }
        catch(...) 
        {
            // Update status code and success as appropriate.
        }
    }
}
```

Verwijdering stoorbewerking zorgt ervoor dat de bewerking `StopOperation`wordt gestopt, dus u dit doen in plaats van bellen.

*Waarschuwing:* in sommige gevallen kan een onhandige uitzondering [voorkomen dat](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) `finally` ze worden aangeroepen, zodat bewerkingen niet kunnen worden bijgehouden.

### <a name="parallel-operations-processing-and-tracking"></a>Verwerking en tracking van parallelle bewerkingen

`StopOperation`stopt alleen de bewerking die is gestart. Als de huidige bewerking niet overeenkomt met de `StopOperation` bewerking die u wilt stoppen, doet u niets. Deze situatie kan optreden als u meerdere bewerkingen parallel start in dezelfde uitvoeringscontext:

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// FAILURE!!! This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Zorg ervoor dat `StartOperation` u altijd de aanroep- en procesbewerking uitvoert in dezelfde **asyncmethode** om bewerkingen die parallel worden uitgevoerd, te isoleren. Als de bewerking synchroon is (of niet `Task.Run`async), wikkelproces en track met:

```csharp
public void RunMyTask(string name)
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>(name))
    {
        Process();
        // Update status code and success as appropriate.
    }
}

public async Task RunAllTasks()
{
    var task1 = Task.Run(() => RunMyTask("task 1"));
    var task2 = Task.Run(() => RunMyTask("task 2"));
    
    await Task.WhenAll(task1, task2);
}
```

## <a name="applicationinsights-operations-vs-systemdiagnosticsactivity"></a>ApplicationInsights-bewerkingen vs System.Diagnostics.Activity
`System.Diagnostics.Activity`vertegenwoordigt de gedistribueerde traceringscontext en wordt door frameworks en bibliotheken gebruikt om context binnen en buiten het proces te maken en te verspreiden en telemetrie-items te correleren. Activiteit werkt `System.Diagnostics.DiagnosticSource` samen met - het meldingsmechanisme tussen het framework/bibliotheek om te informeren over interessante gebeurtenissen (inkomende of uitgaande verzoeken, uitzonderingen, enz.).

Activiteiten zijn eersteklas burgers in Application Insights en automatische afhankelijkheid en het `DiagnosticSource` verzamelen van aanvragen is sterk afhankelijk van hen, samen met evenementen. Als u Activiteit in uw toepassing maakt, leidt dit niet tot het maken van telemetrie van Application Insights. Application Insights moet DiagnosticSource-gebeurtenissen ontvangen en de gebeurtenissen bekend maken met namen en payloads om Activiteit te vertalen naar telemetrie.

Elke bewerking Application Insights (aanvraag `Activity` of `StartOperation` afhankelijkheid) omvat - wanneer wordt aangeroepen, wordt activiteit eronder gemaakt. `StartOperation`is de aanbevolen manier om aanvraag- of afhankelijkheidstelemetrie handmatig bij te houden en ervoor te zorgen dat alles gecorreleerd is.

## <a name="next-steps"></a>Volgende stappen

- Leer de basisprincipes van [telemetriecorrelatie](correlation.md) in Application Insights.
- Bekijk hoe gecorreleerde gegevens [transactiediagnostische ervaring](../../azure-monitor/app/transaction-diagnostics.md) en [toepassingskaart mogelijk maken.](../../azure-monitor/app/app-map.md)
- Zie het [gegevensmodel](../../azure-monitor/app/data-model.md) voor toepassingsinzichten en gegevensmodel.
- Rapporteer aangepaste [gebeurtenissen en statistieken](../../azure-monitor/app/api-custom-events-metrics.md) aan Application Insights.
- Bekijk de [standaardconfiguratie](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) voor het verzamelen van contexteigenschappen.
- Raadpleeg de [gebruikershandleiding van System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) om te zien hoe we telemetrie correleren.
