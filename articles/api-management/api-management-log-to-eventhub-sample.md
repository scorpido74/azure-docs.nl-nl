---
title: API's controleren met Azure API-beheer, gebeurtenishubs en Moesif
titleSuffix: Azure API Management
description: Voorbeeldtoepassing die het log-to-eventhub-beleid demonstreert door Azure API Management, Azure Event Hubs en Moesif te verbinden voor HTTP-logboekregistratie en -bewaking
services: api-management
documentationcenter: ''
author: darrelmiller
manager: erikre
editor: ''
ms.assetid: c528cf6f-5f16-4a06-beea-fa1207541a47
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2018
ms.author: apimpm
ms.openlocfilehash: 4a0717bf7a284668af4808acae3050cc7f42f836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442531"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-moesif"></a>Uw API's controleren met Azure API-beheer, gebeurtenishubs en Moesif
De [API Management-service](api-management-key-concepts.md) biedt veel mogelijkheden om de verwerking van HTTP-aanvragen die naar uw HTTP-API worden verzonden, te verbeteren. Het bestaan van de verzoeken en antwoorden is echter van voorbijgaande aard. De aanvraag wordt gedaan en het stroomt via de API Management-service naar uw backend API. Uw API verwerkt de aanvraag en een antwoord vloeit terug naar de API-consument. De API Management-service houdt een aantal belangrijke statistieken bij over de API's voor weergave in het Azure-portaldashboard, maar verder zijn de details verdwenen.

Door het log-to-eventhub-beleid in de API-beheerservice te gebruiken, u alle gegevens van de aanvraag en het antwoord naar een [Azure Event Hub verzenden.](../event-hubs/event-hubs-what-is-event-hubs.md) Er zijn verschillende redenen waarom u gebeurtenissen wilt genereren van HTTP-berichten die naar uw API's worden verzonden. Enkele voorbeelden zijn audittrail van updates, gebruiksanalyses, uitzonderingswaarschuwingen en integraties van derden.

In dit artikel wordt uitgelegd hoe u het volledige HTTP-aanvraag- en antwoordbericht vastleggen, naar een gebeurtenishub verzenden en dit bericht vervolgens doorstuurt naar een service van derden die HTTP-logboekregistratie- en bewakingsservices biedt.

## <a name="why-send-from-api-management-service"></a>Waarom verzenden vanuit API-beheerservice?
Het is mogelijk om HTTP middleware te schrijven die kan worden aangesloten op HTTP API-frameworks om HTTP-verzoeken en -antwoorden vast te leggen en ze in log- en bewakingssystemen te voeren. Het nadeel van deze aanpak is dat de HTTP middleware moet worden geïntegreerd in de backend API en moet overeenkomen met het platform van de API. Als er meerdere API's zijn, moet elk de middleware implementeren. Vaak zijn er redenen waarom backend API's niet kunnen worden bijgewerkt.

Het gebruik van de Azure API Management-service om te integreren met de logging-infrastructuur biedt een gecentraliseerde en platformonafhankelijke oplossing. Het is ook schaalbaar, deels vanwege de [geo-replicatiemogelijkheden](api-management-howto-deploy-multi-region.md) van Azure API Management.

## <a name="why-send-to-an-azure-event-hub"></a>Waarom verzenden naar een Azure Event Hub?
Het is een redelijke vraag, waarom een beleid maken dat specifiek is voor Azure Event Hubs? Er zijn veel verschillende plaatsen waar ik zou willen mijn verzoeken log. Waarom sturen we de aanvragen niet gewoon rechtstreeks naar de eindbestemming?  Dat is een optie. Bij het indienen van logboekaanvragen van een API-beheerservice moet echter worden overwogen hoe logboekberichten de prestaties van de API beïnvloeden. Geleidelijke toename van de belasting kan worden afgehandeld door het verhogen van de beschikbare exemplaren van systeemcomponenten of door gebruik te maken van geo-replicatie. Korte pieken in het verkeer kunnen er echter voor zorgen dat aanvragen worden vertraagd als aanvragen voor logboekregistratieinfrastructuur onder belasting beginnen te vertragen.

De Azure Event Hubs is ontworpen om grote hoeveelheden gegevens binnen te vallen, met capaciteit voor het omgaan met een veel hoger aantal gebeurtenissen dan het aantal HTTP-aanvragen dat de meeste API's verwerken. De Event Hub fungeert als een soort geavanceerde buffer tussen uw API-beheerservice en de infrastructuur die de berichten opslaat en verwerkt. Dit zorgt ervoor dat uw API-prestaties niet zullen lijden als gevolg van de logging-infrastructuur.

Zodra de gegevens zijn doorgegeven aan een Event Hub, wordt deze gehandhaafd en wachten ze tot de consumenten van Event Hub deze verwerken. De Event Hub maakt niet uit hoe het wordt verwerkt, het geeft er gewoon om ervoor te zorgen dat het bericht met succes wordt afgeleverd.

Event Hubs heeft de mogelijkheid om gebeurtenissen te streamen naar meerdere consumentengroepen. Hierdoor kunnen gebeurtenissen door verschillende systemen worden verwerkt. Hierdoor kunnen veel integratiescenario's worden ondersteund zonder dat de verwerking van de API-aanvraag binnen de API-beheerservice wordt opgezegd, omdat er slechts één gebeurtenis hoeft te worden gegenereerd.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Een beleid voor het verzenden van toepassings-/http-berichten
Een gebeurtenishub accepteert gebeurtenisgegevens als een eenvoudige tekenreeks. De inhoud van die touwtje is aan jou. Om een HTTP-aanvraag te kunnen verpakken en naar gebeurtenishubs te kunnen verzenden, moeten we de tekenreeks opmaken met de aanvraag- of antwoordgegevens. In situaties als deze, als er een bestaand formaat dat we kunnen hergebruiken, dan kunnen we niet onze eigen parsing code te schrijven. Aanvankelijk heb ik overwogen het gebruik van de [HAR](http://www.softwareishard.com/blog/har-12-spec/) voor het verzenden van HTTP-verzoeken en antwoorden. Deze indeling is echter geoptimaliseerd voor het opslaan van een reeks HTTP-aanvragen in een op JSON gebaseerde indeling. Het bevatte een aantal verplichte elementen die onnodige complexiteit toegevoegd voor het scenario van het doorgeven van de HTTP-bericht over de draad.

Een alternatieve optie was `application/http` het gebruik van het mediatype zoals beschreven in de HTTP-specificatie [RFC 7230](https://tools.ietf.org/html/rfc7230). Dit mediatype maakt gebruik van exact dezelfde indeling die wordt gebruikt om http-berichten daadwerkelijk via de draad te verzenden, maar het hele bericht kan in de hoofdtekst van een ander HTTP-verzoek worden geplaatst. In ons geval gaan we gewoon het lichaam gebruiken als onze boodschap om naar Event Hubs te sturen. Handig is dat er een parser bestaat in [Microsoft ASP.NET Web API 2.2-clientbibliotheken](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) die `HttpRequestMessage` deze `HttpResponseMessage` indeling kunnen ontleden en converteren naar de native en objecten.

Om dit bericht te kunnen maken, moeten we profiteren van c#-gebaseerde [beleidsexpressies](/azure/api-management/api-management-policy-expressions) in Azure API Management. Hier is het beleid, dat een HTTP-aanvraagbericht verzendt naar Azure Event Hubs.

```xml
<log-to-eventhub logger-id="conferencelogger" partition-id="0">
@{
   var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                               context.Request.Method,
                                               context.Request.Url.Path + context.Request.Url.QueryString);

   var body = context.Request.Body?.As<string>(true);
   if (body != null && body.Length > 1024)
   {
       body = body.Substring(0, 1024);
   }

   var headers = context.Request.Headers
                          .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                          .ToArray<string>();

   var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

   return "request:"   + context.Variables["message-id"] + "\n"
                       + requestLine + headerString + "\r\n" + body;
}
</log-to-eventhub>
```

### <a name="policy-declaration"></a>Beleidsverklaring
Er zijn een paar bijzondere dingen die het vermelden waard zijn over deze beleidsuitdrukking. Het log-to-eventhub-beleid heeft een kenmerk genaamd logger-id, dat verwijst naar de naam van logger die is gemaakt binnen de API Management-service. De details over het instellen van een Event Hub-logger in de API-beheerservice vindt u in het document [Hoe gebeurtenissen te registreren bij Azure Event Hubs in Azure API Management](api-management-howto-log-event-hubs.md). Het tweede kenmerk is een optionele parameter die gebeurtenishubs instrueert in welke partitie het bericht moet worden opgeslagen. Event Hubs gebruikt partities om schaalbaarheid mogelijk te maken en vereist minimaal twee. De bestelde levering van berichten is alleen gegarandeerd binnen een partitie. Als we Event Hub niet instrueren in welke partitie het bericht moet worden geplaatst, wordt een round-robin-algoritme gebruikt om de belasting te verdelen. Dat kan er echter toe leiden dat sommige van onze berichten buiten de orde worden verwerkt.

### <a name="partitions"></a>Partities
Om ervoor te zorgen dat onze berichten op bestelling aan consumenten worden geleverd en gebruik maken van de mogelijkheid voor de verdeling van de lading van partities, heb ik ervoor gekozen om HTTP-verzoekberichten naar één partitie en HTTP-antwoordberichten naar een tweede partitie te sturen. Dit zorgt voor een gelijkmatige belastingsverdeling en we kunnen garanderen dat alle aanvragen op orde worden verbruikt en dat alle antwoorden op orde worden verbruikt. Het is mogelijk dat een antwoord vóór het overeenkomstige verzoek wordt geconsumeerd, maar omdat dat geen probleem is omdat we een ander mechanisme hebben voor het correleren van verzoeken op antwoorden en we weten dat verzoeken altijd vóór antwoorden komen.

### <a name="http-payloads"></a>HTTP-payloads
Na het `requestLine`bouwen van de , controleren we om te zien of het verzoek lichaam moet worden afgekapt. Het verzoek lichaam is afgekapt tot slechts 1024. Dit kan worden verhoogd, maar individuele Event Hub-berichten zijn beperkt tot 256 KB, dus het is waarschijnlijk dat sommige HTTP-berichtinstanties niet in één bericht passen. Bij het doen van logging en analytics kan een aanzienlijke hoeveelheid informatie worden afgeleid uit alleen de HTTP-aanvraagregel en headers. Ook veel API's verzoek alleen terug te sturen kleine lichamen en dus het verlies van informatiewaarde door afgekapt grote lichamen is vrij minimaal in vergelijking met de vermindering van de overdracht, verwerking, en opslag kosten om alle inhoud van het lichaam te houden. Een laatste opmerking over de verwerking van `true` het `As<string>()` lichaam is dat we moeten doorgeven aan de methode, omdat we het lezen van de inhoud van het lichaam, maar was ook wilde de backend API in staat zijn om het lichaam te lezen. Door trouw aan deze methode te geven, zorgen we ervoor dat het lichaam wordt gebufferd, zodat het een tweede keer kan worden gelezen. Dit is belangrijk om bewust te zijn van als u een API die het uploaden van grote bestanden doet of maakt gebruik van lange polling. In deze gevallen zou het het beste zijn om te voorkomen dat het lezen van het lichaam op alle.

### <a name="http-headers"></a>HTTP-kopteksten
HTTP-headers kunnen worden overgezet naar de berichtindeling in een eenvoudige sleutel-/waardepaarindeling. We hebben ervoor gekozen om bepaalde beveiligingsgevoelige velden uit te kleden, om onnodig lekkende referentie-informatie te voorkomen. Het is onwaarschijnlijk dat API-sleutels en andere referenties worden gebruikt voor analysedoeleinden. Als we willen analyseren van de gebruiker en het specifieke product dat `context` ze gebruiken, dan kunnen we dat uit het object en voeg dat aan het bericht.

### <a name="message-metadata"></a>Metagegevens van berichten
Bij het bouwen van het volledige bericht dat naar de gebeurtenishub moet worden verzonden, maakt de eerste regel eigenlijk geen deel uit van het `application/http` bericht. De eerste regel is extra metagegevens, bestaande uit de vraag of het bericht een verzoek- of antwoordbericht is en een bericht-id, die wordt gebruikt om verzoeken aan reacties te correleren. De bericht-id wordt gemaakt met een ander beleid dat er als volgt uitziet:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

We hadden het verzoekbericht kunnen maken, dat in een variabele kunnen opslaan totdat het antwoord is geretourneerd en vervolgens het verzoek en antwoord als één bericht kunnen verzenden. Echter, door het verzenden van de aanvraag en het antwoord onafhankelijk en met behulp van een bericht-id om de twee te correleren, krijgen we een beetje meer flexibiliteit in het bericht grootte, de mogelijkheid om te profiteren van meerdere partities met behoud van berichtvolgorde en het verzoek zal verschijnen in ons logging dashboard eerder. Er kunnen ook enkele scenario's zijn waarin een geldig antwoord nooit naar de gebeurtenishub wordt verzonden, mogelijk als gevolg van een fout met fatale aanvraag in de API-beheerservice, maar we hebben nog steeds een record van de aanvraag.

Het beleid om het antwoord HTTP-bericht te verzenden lijkt op de aanvraag en dus ziet de volledige beleidsconfiguratie er als volgt uit:

```xml
<policies>
  <inbound>
      <set-variable name="message-id" value="@(Guid.NewGuid())" />
      <log-to-eventhub logger-id="conferencelogger" partition-id="0">
      @{
          var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                      context.Request.Method,
                                                      context.Request.Url.Path + context.Request.Url.QueryString);

          var body = context.Request.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Request.Headers
                               .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                               .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                               .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "request:"   + context.Variables["message-id"] + "\n"
                              + requestLine + headerString + "\r\n" + body;
      }
  </log-to-eventhub>
  </inbound>
  <backend>
      <forward-request follow-redirects="true" />
  </backend>
  <outbound>
      <log-to-eventhub logger-id="conferencelogger" partition-id="1">
      @{
          var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                              context.Response.StatusCode,
                                              context.Response.StatusReason);

          var body = context.Response.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Response.Headers
                                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                          .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "response:"  + context.Variables["message-id"] + "\n"
                              + statusLine + headerString + "\r\n" + body;
     }
  </log-to-eventhub>
  </outbound>
</policies>
```

Het `set-variable` beleid creëert een waarde die `log-to-eventhub` toegankelijk `<inbound>` is voor `<outbound>` zowel het beleid in de sectie als de sectie.

## <a name="receiving-events-from-event-hubs"></a>Evenementen ontvangen van gebeurtenishubs
Gebeurtenissen van Azure Event Hub worden ontvangen met behulp van het [AMQP-protocol](https://www.amqp.org/). Het Microsoft Service Bus-team heeft clientbibliotheken beschikbaar gemaakt om de tijdrovende gebeurtenissen gemakkelijker te maken. Er zijn twee verschillende benaderingen ondersteund, een is een directe `EventProcessorHost` *consument* en de andere is het gebruik van de klasse. Voorbeelden van deze twee benaderingen zijn te vinden in de [Programmeerhandleiding Event Hubs.](../event-hubs/event-hubs-programming-guide.md) De korte versie van `Direct Consumer` de verschillen is, `EventProcessorHost` geeft u volledige controle en de doet een deel van het sanitair werk voor u, maar maakt bepaalde veronderstellingen over hoe je deze gebeurtenissen te verwerken.

### <a name="eventprocessorhost"></a>EventProcessorHost
In dit voorbeeld gebruiken `EventProcessorHost` we het voor eenvoud, maar het is misschien niet de beste keuze voor dit specifieke scenario. `EventProcessorHost`doet het harde werk om ervoor te zorgen dat u zich geen zorgen hoeft te maken over threading problemen binnen een bepaalde gebeurtenis processor klasse. In ons scenario converteren we het bericht echter eenvoudig naar een ander formaat en geven we het door aan een andere service met behulp van een async-methode. Er is geen noodzaak voor het bijwerken van gedeelde staat en dus geen risico van threading problemen. Voor de `EventProcessorHost` meeste scenario's, is waarschijnlijk de beste keuze en het is zeker de gemakkelijkere optie.

### <a name="ieventprocessor"></a>IEventProcessor
Het centrale concept `EventProcessorHost` bij gebruik is `IEventProcessor` het creëren van `ProcessEventAsync`een implementatie van de interface, die de methode bevat. De essentie van die methode wordt hier getoond:

```csharp
async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
{

    foreach (EventData eventData in messages)
    {
        _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

        try
        {
            var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
            await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
        }
        catch (Exception ex)
        {
            _Logger.LogError(ex.Message);
        }
    }
    ... checkpointing code snipped ...
}
```

Een lijst met EventData-objecten wordt doorgegeven aan de methode en we herhalen die lijst. De bytes van elke methode worden geparseerd in een HttpMessage-object en dat object wordt doorgegeven aan een instantie van IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage (HttpMessage)
De `HttpMessage` instantie bevat drie gegevens:

```csharp
public class HttpMessage
{
    public Guid MessageId { get; set; }
    public bool IsRequest { get; set; }
    public HttpRequestMessage HttpRequestMessage { get; set; }
    public HttpResponseMessage HttpResponseMessage { get; set; }

... parsing code snipped ...

}
```

De `HttpMessage` instantie `MessageId` bevat een GUID waarmee we het HTTP-verzoek kunnen koppelen aan het bijbehorende HTTP-antwoord en een booleaanse waarde die identificeert of het object een exemplaar van een HttpRequestMessage en HttpResponseMessage bevat. Door gebruik te maken van `System.Net.Http`de ingebouwde HTTP-klassen `application/http` van , was ik `System.Net.Http.Formatting`in staat om te profiteren van de parsing code die is opgenomen in .  

### <a name="ihttpmessageprocessor"></a>IhttpMessageProcessor
De `HttpMessage` instantie wordt vervolgens doorgestuurd `IHttpMessageProcessor`naar de implementatie van , dat is een interface die ik heb gemaakt om de ontvangst en interpretatie van de gebeurtenis los te koppelen van Azure Event Hub en de daadwerkelijke verwerking ervan.

## <a name="forwarding-the-http-message"></a>Het HTTP-bericht doorsturen
Voor dit voorbeeld, besloot ik dat het interessant zou zijn om de HTTP-aanvraag te duwen naar [Moesif API Analytics](https://www.moesif.com). Moesif is een cloud gebaseerde service die gespecialiseerd is in HTTP analytics en debugging. Ze hebben een gratis laag, dus het is gemakkelijk om te proberen en het stelt ons in staat om de HTTP-verzoeken in real-time te zien stromen via onze API Management-service.

De `IHttpMessageProcessor` implementatie ziet er als volgt uit,

```csharp
public class MoesifHttpMessageProcessor : IHttpMessageProcessor
{
    private readonly string RequestTimeName = "MoRequestTime";
    private MoesifApiClient _MoesifClient;
    private ILogger _Logger;
    private string _SessionTokenKey;
    private string _ApiVersion;
    public MoesifHttpMessageProcessor(ILogger logger)
    {
        var appId = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-APP-ID", EnvironmentVariableTarget.Process);
        _MoesifClient = new MoesifApiClient(appId);
        _SessionTokenKey = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-SESSION-TOKEN", EnvironmentVariableTarget.Process);
        _ApiVersion = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-API-VERSION", EnvironmentVariableTarget.Process);
        _Logger = logger;
    }

    public async Task ProcessHttpMessage(HttpMessage message)
    {
        if (message.IsRequest)
        {
            message.HttpRequestMessage.Properties.Add(RequestTimeName, DateTime.UtcNow);
            return;
        }

        EventRequestModel moesifRequest = new EventRequestModel()
        {
            Time = (DateTime) message.HttpRequestMessage.Properties[RequestTimeName],
            Uri = message.HttpRequestMessage.RequestUri.OriginalString,
            Verb = message.HttpRequestMessage.Method.ToString(),
            Headers = ToHeaders(message.HttpRequestMessage.Headers),
            ApiVersion = _ApiVersion,
            IpAddress = null,
            Body = message.HttpRequestMessage.Content != null ? System.Convert.ToBase64String(await message.HttpRequestMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        EventResponseModel moesifResponse = new EventResponseModel()
        {
            Time = DateTime.UtcNow,
            Status = (int) message.HttpResponseMessage.StatusCode,
            IpAddress = Environment.MachineName,
            Headers = ToHeaders(message.HttpResponseMessage.Headers),
            Body = message.HttpResponseMessage.Content != null ? System.Convert.ToBase64String(await message.HttpResponseMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApimMessageId", message.MessageId.ToString());

        EventModel moesifEvent = new EventModel()
        {
            Request = moesifRequest,
            Response = moesifResponse,
            SessionToken = _SessionTokenKey != null ? message.HttpRequestMessage.Headers.GetValues(_SessionTokenKey).FirstOrDefault() : null,
            Tags = null,
            UserId = null,
            Metadata = metadata
        };

        Dictionary<string, string> response = await _MoesifClient.Api.CreateEventAsync(moesifEvent);

        _Logger.LogDebug("Message forwarded to Moesif");
    }

    private static Dictionary<string, string> ToHeaders(HttpHeaders headers)
    {
        IEnumerable<KeyValuePair<string, IEnumerable<string>>> enumerable = headers.GetEnumerator().ToEnumerable();
        return enumerable.ToDictionary(p => p.Key, p => p.Value.GetEnumerator()
                                                         .ToEnumerable()
                                                         .ToList()
                                                         .Aggregate((i, j) => i + ", " + j));
    }
}
```

Het `MoesifHttpMessageProcessor` maakt gebruik van een [C# API-bibliotheek voor Moesif](https://www.moesif.com/docs/api?csharp#events) die het gemakkelijk maakt om HTTP-gebeurtenisgegevens in hun service te pushen. Om HTTP-gegevens naar de Moesif Collector API te verzenden, hebt u een account en een toepassings-id nodig. Je krijgt een Moesif Application Id door het maken van een account op [de website van Moesif](https://www.moesif.com) en ga dan naar de _Top Right Menu_ -> _App Setup_.

## <a name="complete-sample"></a>Volledig voorbeeld
De [broncode](https://github.com/dgilling/ApimEventProcessor) en tests voor het monster staan op GitHub. U hebt een [API Management Service,](get-started-create-service-instance.md) [een verbonden Gebeurtenishub](api-management-howto-log-event-hubs.md)en een [opslagaccount](../storage/common/storage-create-storage-account.md) nodig om het voorbeeld zelf uit te voeren.   

Het voorbeeld is slechts een eenvoudige Console-toepassing die luistert naar gebeurtenissen `EventRequestModel` die `EventResponseModel` afkomstig zijn van Event Hub, ze omzet in een Moesif en objecten en deze vervolgens doorstuurt naar de Moesif Collector API.

In de volgende geanimeerde afbeelding ziet u een aanvraag voor een API in de ontwikkelaarsportal, de consoletoepassing waarin het bericht wordt weergegeven dat wordt ontvangen, verwerkt en doorgestuurd en vervolgens het verzoek en antwoord dat wordt weergegeven in de gebeurtenisstream.

![Demonstratie van het verzoek wordt doorgestuurd naar Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Samenvatting
Azure API Management-service biedt een ideale plek om het HTTP-verkeer vast te leggen dat van en naar uw API's reist. Azure Event Hubs is een zeer schaalbare, goedkope oplossing voor het vastleggen van dat verkeer en het voeden in secundaire verwerkingssystemen voor logboekregistratie, monitoring en andere geavanceerde analyses. Verbinding maken met verkeersbewakingssystemen van derden zoals Moesif is zo eenvoudig als enkele tientallen regels code.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over Azure Event Hubs
  * [Aan de slag met Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Berichten ontvangen met EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Programmeerhandleiding voor gebeurtenishubs](../event-hubs/event-hubs-programming-guide.md)
* Meer informatie over API-beheer en eventhubs-integratie
  * [Gebeurtenissen aanmelden bij Azure Event Hubs in Azure API-beheer](api-management-howto-log-event-hubs.md)
  * [Referentie van loggerentiteit](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [log-to-eventhub-beleidsverwijzing](/azure/api-management/api-management-advanced-policies#log-to-eventhub)
