---
title: End-to-end tracering en diagnose van Azure Service Bus | Microsoft Documenten
description: Overzicht van servicebus-clientdiagnostiek en end-to-end tracering (client via alle services die betrokken zijn bij de verwerking.)
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 7c2efc9c736097873201505f280af5d47bed4847
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294185"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Gedistribueerde tracering en correlatie via Service Bus-berichten

Een van de veelvoorkomende problemen bij de ontwikkeling van microservices is de mogelijkheid om de werking van een client te traceren via alle services die betrokken zijn bij de verwerking. Het is handig voor foutopsporing, prestatieanalyse, A/B-tests en andere typische diagnostische scenario's.
Een deel van dit probleem is het bijhouden van logische stukken van het werk. Het bevat het resultaat en latentie- en externe afhankelijkheidsoproepen voor berichtenverwerking. Een ander deel is correlatie van deze diagnostische gebeurtenissen buiten procesgrenzen.

Wanneer een producent een bericht door een wachtrij verzendt, gebeurt dit meestal in het bereik van een andere logische bewerking, geïnitieerd door een andere client of service. Dezelfde bewerking wordt voortgezet door de consument zodra het een bericht ontvangt. Zowel producent als consument (en andere diensten die de bewerking verwerken), zenden vermoedelijk telemetriegebeurtenissen uit om de werkingsstroom en het resultaat te traceren. Om dergelijke gebeurtenissen te correleren en de werking end-to-end te traceren, moet elke service die telemetrie rapporteert, elke gebeurtenis stempelen met een traceringscontext.

Microsoft Azure Service Bus messaging heeft payload-eigenschappen gedefinieerd die producenten en consumenten moeten gebruiken om een dergelijke tracecontext door te geven.
Het protocol is gebaseerd op het [HTTP-correlatieprotocol](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Naam van eigenschap        | Beschrijving                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostische-id       | Unieke id van een externe oproep van producent naar de wachtrij. Raadpleeg [Request-Id in http-protocol](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) voor de motivering, overwegingen en indeling |
|  Correlatie-context | Operationele context, die wordt gepropageerd over alle services die betrokken zijn bij de verwerking van de bewerking. Zie [Correlatie-context in HTTP-protocol](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) voor meer informatie |

## <a name="service-bus-net-client-autotracing"></a>ServiceBus .NET Client autotracing

Beginnend met versie 3.0.0 [Microsoft Azure ServiceBus Client voor .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) biedt traceringinstrumentatiepunten die kunnen worden gekoppeld door traceringssystemen of een stuk clientcode.
De instrumentatie maakt het mogelijk het bijhouden van alle oproepen naar de Service Bus messaging service van client kant. Als berichtverwerking wordt uitgevoerd met het patroon van de [berichthandler,](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)wordt berichtverwerking ook

### <a name="tracking-with-azure-application-insights"></a>Bijhouden met Azure Application Insights

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) biedt uitgebreide mogelijkheden voor prestatiebewaking, waaronder automagische aanvragen en afhankelijkheidstracking.

Installeer Application Insights SDK, afhankelijk van uw projecttype:
- [ASP.NET](../azure-monitor/app/asp-net.md) - versie 2.5-beta2 of hoger installeren
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) - installeer versie 2.2.0-beta2 of hoger.
Deze koppelingen bevatten details over het installeren van SDK, het maken van resources en het configureren van SDK (indien nodig). Raadpleeg voor non-ASP.NET toepassingen het artikel [Azure Application Insights for Console Applications.](../azure-monitor/app/console.md)

Als u [het patroon van de berichthandler](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) gebruikt om berichten te verwerken, bent u klaar: alle Service Bus-oproepen die door uw service worden uitgevoerd, worden automatisch bijgehouden en gecorreleerd met andere telemetrie-items. Anders verwijzen naar het volgende voorbeeld voor handmatige bericht verwerking bijhouden.

#### <a name="trace-message-processing"></a>Verwerking van traceringsberichten

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

In dit `RequestTelemetry` voorbeeld wordt gerapporteerd voor elk verwerkt bericht, met een tijdstempel, duur en resultaat (succes). De telemetrie heeft ook een set correlatie-eigenschappen.
Geneste sporen en uitzonderingen die tijdens de verwerking van berichten worden `RequestTelemetry`gerapporteerd, worden ook gestempeld met correlatie-eigenschappen die hen vertegenwoordigen als 'kinderen' van de .

In het geval u tijdens de verwerking van berichten naar ondersteunde externe componenten belt, worden ze ook automatisch bijgehouden en gecorreleerd. Raadpleeg [aangepaste bewerkingen bijhouden met Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md) voor handmatige tracking en correlatie.

Als u naast de Application Insights SDK ook externe code uitvoert, verwacht u een langere **duur** bij het bekijken van Application Insights-logboeken. 

![Langere duur in logboek Application Insights](./media/service-bus-end-to-end-tracing/longer-duration.png)

Het betekent niet dat er een vertraging in het ontvangen van het bericht. In dit scenario is het bericht al ontvangen sinds het bericht wordt doorgegeven als parameter naar de SDK-code. En het **naamplaatje** in de app-statistiekenlogboeken **(Proces)** geeft aan dat het bericht nu wordt verwerkt door uw externe gebeurtenisverwerkingscode. Dit probleem is niet azure-gerelateerd. In plaats daarvan verwijzen deze statistieken naar de efficiëntie van uw externe code, aangezien het bericht al is ontvangen van Service Bus. Zie [dit bestand op GitHub](https://github.com/Azure/azure-sdk-for-net/blob/4bab05144ce647cc9e704d46d3763de5f9681ee0/sdk/servicebus/Microsoft.Azure.ServiceBus/src/ServiceBusDiagnosticsSource.cs) om te zien waar de **procestag** wordt gegenereerd en toegewezen zodra het bericht is ontvangen van Service Bus. 

### <a name="tracking-without-tracing-system"></a>Tracking zonder traceringssysteem
In het geval dat uw traceringssysteem geen ondersteuning biedt voor automatische Service Bus-oproepen die u volgt, u dergelijke ondersteuning toevoegen aan een traceringssysteem of in uw toepassing. In deze sectie worden diagnostische gebeurtenissen beschreven die zijn verzonden door de Service Bus .NET-client.  

Service Bus .NET Client is geinstrumenteerd met .NET tracing primitives [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) en [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity`dient als een `DiagnosticSource` traceringscontext, terwijl het een meldingsmechanisme is. 

Als er geen listener is voor de DiagnosticSource-gebeurtenissen, is instrumentatie uitgeschakeld, waardoor de kosten voor nul instrumentatie worden beperkt. DiagnosticSource geeft alle controle aan de luisteraar:
- luisteraar bepaalt welke bronnen en gebeurtenissen u wilt beluisteren
- listener bepaalt gebeurtenissnelheid en sampling
- gebeurtenissen worden verzonden met een payload die volledige context biedt, zodat u het object Message tijdens de gebeurtenis openen en wijzigen

Maak uzelf vertrouwd met [diagnosticsource-gebruikershandleiding](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) voordat u verdergaat met de implementatie.

Laten we een listener maken voor Service Bus-gebeurtenissen in ASP.NET Core-app die logboeken schrijft met Microsoft.Extension.Logger.
Het maakt gebruik van [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) bibliotheek om zich te abonneren op DiagnosticSource (het is ook gemakkelijk om zich te abonneren op DiagnosticSource zonder)

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

In dit voorbeeld registreert listener duur, resultaat, unieke id en begintijd voor elke servicebusbewerking.

#### <a name="events"></a>Gebeurtenissen

Voor elke operatie worden twee evenementen verzonden: 'Start' en 'Stop'. Waarschijnlijk bent u alleen geïnteresseerd in 'Stop' evenementen. Ze bieden het resultaat van de bewerking, evenals begintijd en duur als activiteiteigenschappen.

Gebeurtenispayload biedt een listener de context van de bewerking, het repliceert API-binnenkomende parameters en retourwaarde. 'Stop' event payload heeft alle eigenschappen van 'Start' event payload, dus je 'Start' evenement volledig negeren.

Alle gebeurtenissen hebben ook eigenschappen 'Entiteit' en 'Eindpunt', ze worden weggelaten in onderstaande tabel
  * `string Entity`- - Naam van de entiteit (wachtrij, onderwerp, enz.)
  * `Uri Endpoint`- URL van servicebus-eindpunt

Elke 'Stop'-gebeurtenis `Status` `TaskStatus` heeft eigenschap met async-bewerking is voltooid met, die ook wordt weggelaten in de volgende tabel voor eenvoud.

Hier is de volledige lijst van instrumented operaties:

| Naam van de bewerking | Bijgehouden API | Specifieke payload-eigenschappen|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages`- Lijst met berichten die worden verzonden |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message`- Bericht wordt verwerkt<br/>`DateTimeOffset ScheduleEnqueueTimeUtc`- Geplande berichtverschuiving<br/>`long SequenceNumber`- Volgnummer van het geplande bericht ('Stop'-gebeurtenispayload) |
| Microsoft.Azure.ServiceBus.Annuleren | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber`- Volgnummer van het te-bericht dat moet worden geannuleerd | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount`- Het maximum aantal berichten dat kan worden ontvangen.<br/>`IList<Message> Messages`- Lijst met ontvangen berichten ('Stop'-gebeurtenislading) |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber`- Het startpunt van waaruit een partij berichten bladeren.<br/>`int RequestedMessageCount`- Het aantal berichten dat moet worden opgehaald.<br/>`IList<Message> Messages`- Lijst met ontvangen berichten ('Stop'-gebeurtenislading) |
| Microsoft.Azure.ServiceBus.OntvangenUitgesteld | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers`- De lijst met de te ontvangen volgnummers.<br/>`IList<Message> Messages`- Lijst met ontvangen berichten ('Stop'-gebeurtenislading) |
| Microsoft.Azure.ServiceBus.Compleet | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens`- De lijst met de vergrendeltokens van de bijbehorende berichten die moeten worden voltooid.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken`- Het slotteken van het bijbehorende bericht om op te geven. |
| Microsoft.Azure.ServiceBus.Uitstellen | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken`- Het slottoken van het bijbehorende bericht om uit te stellen. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken`- Het slotteken van het overeenkomstige bericht aan dode brief. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken`- Het vergrendelingstoken van het bijbehorende bericht om het slot te vernieuwen.<br/>`DateTime LockedUntilUtc`- Nieuwe vervaldatum en -tijd voor het vergrendelen van token in UTC-indeling. ('Stop'-gebeurtenislading)|
| Microsoft.Azure.ServiceBus.Process | Message Handler lambda-functie aangeboden in [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message`- Bericht wordt verwerkt. |
| Microsoft.Azure.ServiceBus.ProcessSession | Message Session Handler lambda-functie aangeboden in [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message`- Bericht wordt verwerkt.<br/>`IMessageSession Session`- Sessie wordt verwerkt |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule`- De regelbeschrijving die de regel bevat om toe te voegen. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName`- Naam van de te verwijderen regel. |
| Microsoft.Azure.ServiceBus.GetRules | [AbonnementClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules`- Alle regels die aan het abonnement zijn gekoppeld. ('Stop' payload only) |
| Microsoft.azure.servicebus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId`- De sessionId aanwezig in de berichten. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId`- De sessionId aanwezig in de berichten.<br/>`byte [] State`- Sessiestatus ('Stop'-gebeurtenispayload) |
| Microsoft.azure.servicebus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId`- De sessionId aanwezig in de berichten.<br/>`byte [] State`- Sessiestatus |
| Microsoft.azure.servicebus.renewsessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId`- De sessionId aanwezig in de berichten. |
| Microsoft.Azure.ServiceBus.Exception | elke geinstrumenteerde API| `Exception Exception`- Uitzonderingsinstantie |

In elk geval hebt `Activity.Current` u toegang tot de huidige bedrijfscontext.

#### <a name="logging-additional-properties"></a>Extra eigenschappen registreren

`Activity.Current`biedt gedetailleerde context van de huidige operatie en haar ouders. Zie [Activiteitendocumentatie](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) voor meer informatie voor meer informatie.
Service Bus instrumentatie biedt `Activity.Current.Tags` aanvullende `MessageId` informatie `SessionId` in de - ze houden en wanneer ze beschikbaar zijn.

Activiteiten die het evenement 'Ontvangen', 'Peek' en 'Ontvangen uitgesteld' bijhouden, kunnen ook een tag hebben. `RelatedTo` Het bevat een `Diagnostic-Id`duidelijke lijst van (s) van berichten die zijn ontvangen als gevolg.
Een dergelijke bewerking kan ertoe leiden dat verschillende niet-gerelateerde berichten worden ontvangen. Ook is `Diagnostic-Id` het niet bekend wanneer de bewerking begint, dus 'Ontvangen' bewerkingen kunnen worden gecorreleerd aan 'Proces' bewerkingen met behulp van deze tag alleen. Het is handig bij het analyseren van prestatieproblemen om te controleren hoe lang het duurde om het bericht te ontvangen.

Efficiënte manier om tags te loggen is om er overheen te herhalen, dus het toevoegen van tags aan het voorgaande voorbeeld ziet eruit als 

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

#### <a name="filtering-and-sampling"></a>Filteren en bemonsteren

In sommige gevallen is het wenselijk om slechts een deel van de gebeurtenissen in te loggen om de overhead- of opslagverbruik te verminderen. U alleen gebeurtenissen 'Stoppen' (zoals in het voorgaande voorbeeld) of het voorbeeldpercentage van de gebeurtenissen registreren. 
`DiagnosticSource`zorgen voor een `IsEnabled` manier om het te bereiken met predicaat. Zie [Contextgebaseerd filteren in DiagnosticSource voor](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering)meer informatie.

`IsEnabled`kan meerdere keren worden aangeroepen voor één bewerking om de impact op de prestaties te minimaliseren.

`IsEnabled`wordt in de volgende volgorde aangeroepen:

1. `IsEnabled(<OperationName>, string entity, null)`bijvoorbeeld . `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")` Let op: er is geen 'Start' of 'Stop' aan het einde. Gebruik het om bepaalde bewerkingen of wachtrijen uit te filteren. Als callback `false`terugkeert, worden gebeurtenissen voor de bewerking niet verzonden

   * Voor de 'Process' en 'ProcessSession' `IsEnabled(<OperationName>, string entity, Activity activity)` operaties ontvangt u ook callback. Gebruik het om gebeurtenissen `activity.Id` te filteren op basis van of tagseigenschappen.
  
2. `IsEnabled(<OperationName>.Start)`bijvoorbeeld . `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")` Controleert of 'Start'-gebeurtenis moet worden afgevuurd. Het resultaat heeft alleen invloed op de gebeurtenis 'Start', maar verdere instrumentatie hangt er niet van af.

Er is `IsEnabled` geen voor 'Stop' evenement.

Als een bepaald bedrijfsresultaat een uitzondering is, `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` wordt aangeroepen. U zich alleen abonneren op 'Exception'-gebeurtenissen en de rest van de instrumentatie voorkomen. In dit geval moet u dergelijke uitzonderingen nog steeds afhandelen. Aangezien andere instrumentatie is uitgeschakeld, moet u niet verwachten dat de traceringscontext met de berichten van consument naar producent stroomt.

U kunt `IsEnabled` ook samplingstrategieën implementeren. Bemonstering op `Activity.Id` basis `Activity.RootId` van de of zorgt voor consistente bemonstering over alle banden (zolang deze wordt gepropageerd door tracing systeem of door uw eigen code).

In aanwezigheid `DiagnosticSource` van meerdere luisteraars voor dezelfde bron, is het genoeg voor `IsEnabled` slechts één luisteraar om het evenement te accepteren, dus is niet gegarandeerd te worden genoemd,

## <a name="next-steps"></a>Volgende stappen

* [Correlatie met toepassingsinzichten](../azure-monitor/app/correlation.md)
* [Toepassingsinzichten controleren afhankelijkheden](../azure-monitor/app/asp-net-dependencies.md) om te zien of REST, SQL of andere externe bronnen u vertragen.
* [Aangepaste bewerkingen bijhouden met Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md)
