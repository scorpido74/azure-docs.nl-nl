---
title: End-to-end-tracering en diagnostische gegevens Azure Service Bus | Microsoft Docs
description: Overzicht van Service Bus client diagnostiek en end-to-end tracering
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: fa71ca7ea976ab4d724a061d0d0809cdb5767f4f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705748"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Gedistribueerde tracering en correlatie via Service Bus berichten

Een van de veelvoorkomende problemen in micro Services-ontwikkeling is de mogelijkheid om de werking van een client te traceren via alle services die bij de verwerking betrokken zijn. Het is handig voor fout opsporing, prestatie analyse, A/B testen en andere typische diagnose scenario's.
Een deel van dit probleem is het bijhouden van logische stukjes werk. Het omvat bericht verwerkings resultaten en latentie en externe afhankelijkheids aanroepen. Een ander deel is de correlatie van deze diagnostische gebeurtenissen buiten de proces grenzen.

Wanneer een producent een bericht via een wachtrij verzendt, gebeurt dit doorgaans in het bereik van een andere logische bewerking, geïnitieerd door een andere client of service. Dezelfde bewerking wordt voortgezet door de Consumer zodra de gebruiker een bericht ontvangt. Zowel producent als consument (en andere services die de bewerking verwerken) vermoeden dat er waarschijnlijk telemetrie-gebeurtenissen worden getraceerd om de bewerkings stroom en het resultaat te traceren. Om dergelijke gebeurtenissen en tracerings bewerkingen end-to-end te correleren, moet elke service die telemetrie rapporteert elke gebeurtenis met een tracerings context afschrijven.

Microsoft Azure Service Bus Messa ging heeft eigenschappen van Payload gedefinieerd die door producenten en consumenten moeten worden gebruikt om deze tracerings context door te geven.
Het protocol is gebaseerd op het [http-correlatie protocol](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| De naam van eigenschap        | Beschrijving                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnose-id       | De unieke id van een externe aanroep van de producent naar de wachtrij. Raadpleeg de [aanvraag-id in het HTTP-protocol](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) voor de motivering, overwegingen en indeling |
|  Correlatie-context | Bewerkings context, die wordt door gegeven voor alle services die bij de verwerking van de bewerking betrokken zijn. Zie [correlatie-context in http-protocol](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) voor meer informatie. |

## <a name="service-bus-net-client-auto-tracing"></a>Automatische tracering van Service Bus .NET-client

Met ingang van versie 3.0.0 [Microsoft Azure ServiceBus-client voor .net](/dotnet/api/microsoft.azure.servicebus.queueclient) kunt u tracerings instrumentatie punten aanwijzen die kunnen worden aangesloten door traceer systemen of client code.
Met de instrumentatie kunt u alle aanroepen van de Service Bus Messa ging-service van aan de client zijde bijhouden. Als bericht verwerking wordt uitgevoerd met het [patroon van de bericht afhandelingsprocedure](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), wordt de bericht verwerking ook instrumentatie

### <a name="tracking-with-azure-application-insights"></a>Bijhouden met Azure-toepassing Insights

[Micro soft Application Insights](https://azure.microsoft.com/services/application-insights/) biedt uitgebreide mogelijkheden voor het controleren van prestaties, waaronder Automagic-aanvraag en het bijhouden van afhankelijkheden.

Installeer Application Insights SDK, afhankelijk van het project type:
- [ASP.net](../azure-monitor/app/asp-net.md) -versie 2,5-Beta2 of hoger installeren
- [ASP.net core](../azure-monitor/app/asp-net-core.md) -install versie 2.2.0-beta2 of hoger.
Deze koppelingen bieden Details over het installeren van SDK, het maken van resources en het configureren van SDK (indien nodig). Raadpleeg het artikel [Azure-toepassing Insights voor console toepassingen](../azure-monitor/app/console.md) voor non-ASP.NET-toepassingen.

Als u een [bericht afhandelingsprocedure](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) gebruikt voor het verwerken van berichten, bent u klaar: alle Service Bus-aanroepen die door uw service worden uitgevoerd, worden automatisch getraceerd en gecorreleerd met andere telemetrie-items. Raadpleeg anders het volgende voor beeld voor het bijhouden van hand matige bericht verwerking.

#### <a name="trace-message-processing"></a>Verwerking van tracerings berichten

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you are using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

In dit voor beeld wordt `RequestTelemetry` gerapporteerd voor elk verwerkte bericht, met een tijds tempel, duur en resultaat (geslaagd). De telemetrie heeft ook een set correlatie-eigenschappen.
Geneste traceringen en uitzonde ringen die worden gerapporteerd tijdens de bericht verwerking, worden ook gestempeld met correlatie-eigenschappen die ze vertegenwoordigen als ' kinderen ' van de `RequestTelemetry`.

Als u tijdens de verwerking van berichten aanroepen naar ondersteunde externe onderdelen maakt, worden deze ook automatisch gevolgd en gecorreleerd. Raadpleeg [aangepaste bewerkingen bijhouden met Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md) voor hand matig bijhouden en correlatie.

### <a name="tracking-without-tracing-system"></a>Bijhouden zonder tracering systeem
Als uw tracerings systeem geen automatische Service Bus traceringen ondersteunt, is het mogelijk om dergelijke ondersteuning toe te voegen aan een tracerings systeem of in uw toepassing. In deze sectie worden de diagnostische gebeurtenissen beschreven die worden verzonden door Service Bus .NET-client.  

Service Bus .NET-client is instrumentatie met behulp van .NET-tracering primitieven [System. Diagnostics. activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) en [System. Diagnostics. DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` fungeert als tracerings context terwijl `DiagnosticSource` een meldings mechanisme is. 

Als er geen listener voor de DiagnosticSource-gebeurtenissen is, is instrumentatie uitgeschakeld, waardoor de kosten voor de instrumentatie gelijk blijven. DiagnosticSource biedt alle controle over de listener:
- Listener bepaalt naar welke bronnen en gebeurtenissen moet worden geluisterd
- de controle van de gebeurtenis frequentie en de steek proef van de listener
- gebeurtenissen worden verzonden met een nettolading die volledige context biedt zodat u tijdens de gebeurtenis toegang krijgt tot het bericht object en deze kunt wijzigen

Raadpleeg de [Gebruikers handleiding voor DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) voordat u doorgaat met de implementatie.

We gaan een listener maken voor Service Bus gebeurtenissen in ASP.NET Core app die logboeken schrijft met micro soft. extension. logger.
Er wordt gebruikgemaakt van [System. Reactive. core](https://www.nuget.org/packages/System.Reactive.Core) -bibliotheek om u te abonneren op DiagnosticSource (u kunt zich ook eenvoudig abonneren op DiagnosticSource zonder IT)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

In dit voor beeld worden de duur, het resultaat, de unieke id en de start tijd van de listener voor elke Service Bus bewerking geregistreerd.

#### <a name="events"></a>Evenements

Voor elke bewerking worden twee gebeurtenissen verzonden: Start en stop. Waarschijnlijk bent u alleen geïnteresseerd in ' Stop '-gebeurtenissen. Ze bieden het resultaat van de bewerking, evenals de begin tijd en duur als een activiteit.

De nettolading van de gebeurtenis biedt een listener met de context van de bewerking, repliceert de API-binnenkomende para meters en retour waarden. De nettolading van de gebeurtenis stop heeft alle eigenschappen van de nettolading van de gebeurtenis, dus u kunt de gebeurtenis start volledig negeren.

Alle gebeurtenissen hebben ook de eigenschappen entity en endpoint, die in de onderstaande tabel worden wegge laten
  * `string Entity`: de naam van de entiteit (wachtrij, onderwerp, enzovoort)
  * `Uri Endpoint`-Service Bus eind punt-URL

Elke gebeurtenis ' Stop ' heeft `Status` eigenschap met `TaskStatus` asynchrone bewerking is voltooid met, die ook in de volgende tabel wordt wegge laten voor eenvoud.

Hier volgt de volledige lijst met bewerkingen met een instrument:

| Naam van bewerking | Bijgehouden API | Eigenschappen van specifieke nettolading|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages`-lijst met berichten die worden verzonden |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message`-bericht dat wordt verwerkt<br/>offset voor `DateTimeOffset ScheduleEnqueueTimeUtc`-gepland bericht<br/>`long SequenceNumber` Volg nummer van het geplande bericht (de nettolading van de gebeurtenis stoppen) |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber` Volg nummer van te annuleren bericht | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount`-het maximum aantal berichten dat kan worden ontvangen.<br/>`IList<Message> Messages`-lijst met ontvangen berichten (' Stop ' gebeurtenis lading) |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber`: het begin punt van waaruit een batch berichten moet worden gebladerd.<br/>`int RequestedMessageCount`: het aantal berichten dat moet worden opgehaald.<br/>`IList<Message> Messages`-lijst met ontvangen berichten (' Stop ' gebeurtenis lading) |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers`: de lijst met de reeks nummers die moeten worden ontvangen.<br/>`IList<Message> Messages`-lijst met ontvangen berichten (' Stop ' gebeurtenis lading) |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens`: de lijst met de vergrendelings tokens van de bijbehorende berichten die moeten worden voltooid.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken`-het vergrendelings token van het bijbehorende bericht dat moet worden verlaten. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken`-het vergrendelings token van het bijbehorende bericht dat moet worden uitgesteld. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken`-het vergrendelings token van het bijbehorende bericht naar een onbestelbare letter. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken`-het vergrendelings token van het bijbehorende bericht waarop de vergren deling moet worden vernieuwd.<br/>`DateTime LockedUntilUtc`-de verloop datum en-tijd van het nieuwe vergrendelings token in UTC-indeling. (' Stop ' gebeurtenis lading)|
| Microsoft.Azure.ServiceBus.Process | Lambda-functie Message Handler in [IReceiverClient. RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message`-bericht dat wordt verwerkt. |
| Microsoft.Azure.ServiceBus.ProcessSession | De Lambda-functie Message session handler in [IQueueClient. RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message`-bericht dat wordt verwerkt.<br/>`IMessageSession Session`-sessie wordt verwerkt |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule`: de regel beschrijving die de toe te voegen regel levert. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName`-naam van de regel die moet worden verwijderd. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules`-alle regels die zijn gekoppeld aan het abonnement. (Alleen Payload stoppen) |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId`-de sessionId die aanwezig is in de berichten. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId`-de sessionId die aanwezig is in de berichten.<br/>`byte [] State`-sessie status (' Stop ' gebeurtenis lading) |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId`-de sessionId die aanwezig is in de berichten.<br/>`byte [] State`-sessie status |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId`-de sessionId die aanwezig is in de berichten. |
| Microsoft.Azure.ServiceBus.Exception | een instrumentele API| `Exception Exception`-uitzonderings exemplaar |

In elk geval kunt u toegang krijgen tot `Activity.Current` die de huidige bewerkings context bevatten.

#### <a name="logging-additional-properties"></a>Logboek registratie van aanvullende eigenschappen

`Activity.Current` biedt gedetailleerde context van de huidige bewerking en de bovenliggende bewerkingen. Zie voor meer informatie [activiteiten documentatie](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) voor meer informatie.
Service Bus instrumentatie bevat extra informatie over de `Activity.Current.Tags`-ze `MessageId` en `SessionId` wanneer deze beschikbaar zijn.

Activiteiten die de gebeurtenis ' Receive ', ' Peek ' en ' ReceiveDeferred ' volgen, hebben mogelijk ook `RelatedTo`-tag. Het bevat een afzonderlijke lijst met `Diagnostic-Id`(s) van berichten die als gevolg hiervan zijn ontvangen.
Deze bewerking kan ertoe leiden dat er verschillende niet-gerelateerde berichten worden ontvangen. De `Diagnostic-Id` is ook niet bekend wanneer de bewerking wordt gestart. Daarom kunnen receive-bewerkingen alleen worden gecorreleerd aan proces bewerkingen met deze tag. Het is handig bij het analyseren van prestatie problemen om te controleren hoe lang het heeft geduurd om het bericht te ontvangen.

Een efficiënte manier om tags te registreren, is om ze te herhalen. het toevoegen van labels aan het voor gaande voor beeld ziet eruit als 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", "{tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>Filters en steek proeven

In sommige gevallen is het wenselijk om slechts een deel van de gebeurtenissen te registreren om de prestaties van de overhead of het opslag verbruik te verminderen. U kunt stop gebeurtenissen alleen registreren (zoals in het voor gaande voor beeld) of het voorbeeld percentage van de gebeurtenissen. 
`DiagnosticSource` bieden om dit te doen met `IsEnabled` predicaat. Zie [context-based filtering in DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering)voor meer informatie.

`IsEnabled` kan meermaals worden aangeroepen voor één bewerking om de prestaties te verminderen.

`IsEnabled` wordt in de volgende volg orde aangeroepen:

1. `IsEnabled(<OperationName>, string entity, null)` bijvoorbeeld `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Houd er rekening mee dat er aan het einde geen ' Start ' of ' Stop ' is. Gebruik het om bepaalde bewerkingen of wacht rijen te filteren. Als terugbellen `false`retourneert, worden gebeurtenissen voor de bewerking niet verzonden

   * Voor de bewerkingen ' process ' en ' ProcessSession ' ontvangt u ook `IsEnabled(<OperationName>, string entity, Activity activity)`-call back. Gebruik deze functie om gebeurtenissen te filteren op basis van eigenschappen van `activity.Id` of tags.
  
2. `IsEnabled(<OperationName>.Start)` bijvoorbeeld `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Hiermee wordt gecontroleerd of de gebeurtenis start moet worden gestart. Het resultaat is alleen van invloed op de gebeurtenis start, maar verdere instrumentatie is niet afhankelijk van het item.

Er is geen `IsEnabled` voor de gebeurtenis stop.

Als een bepaalde bewerkings resultaat uitzonde ring is, wordt `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` aangeroepen. U kunt zich alleen abonneren op uitzonderings gebeurtenissen en de rest van de instrumentatie verhinderen. In dit geval moet u dergelijke uitzonde ringen nog steeds afhandelen. Omdat andere instrumentatie is uitgeschakeld, moet u niet verwachten dat de tracerings context wordt gestroomd met de berichten van de gebruiker naar de producent.

U kunt `IsEnabled` ook voorbeeld strategieën implementeren. Steek proeven op basis van `Activity.Id` of `Activity.RootId` zorgen voor consistente steek proeven in alle banden (zolang deze door tracering systeem of door uw eigen code worden door gegeven).

Als er meerdere `DiagnosticSource` listeners voor dezelfde bron aanwezig zijn, is het voldoende voor slechts één listener om de gebeurtenis te accepteren, waardoor `IsEnabled` niet kan worden aangeroepen,

## <a name="next-steps"></a>Volgende stappen

* [Application Insights correlatie](../azure-monitor/app/correlation.md)
* [Application Insights controle afhankelijkheden](../azure-monitor/app/asp-net-dependencies.md) om te zien of rest, SQL of andere externe bronnen worden vertraagd.
* [Aangepaste bewerkingen bijhouden met Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md)
