---
title: HTTP-functies in Durable Functions-Azure Functions
description: Meer informatie over de geïntegreerde HTTP-functies in de Durable Functions-extensie voor Azure Functions.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 5a9143643b1a1cabb32903933dbd68d665d0424f
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71171141"
---
# <a name="http-features"></a>HTTP-functies

Durable Functions heeft verschillende functies waarmee u eenvoudig duurzame Orchestrations en entiteiten kunt opnemen in HTTP-werk stromen. In dit artikel vindt u meer informatie over sommige van deze functies.

## <a name="exposing-http-apis"></a>HTTP-Api's weer geven

Orchestrations en entiteiten kunnen worden opgeroepen en beheerd met behulp van HTTP-aanvragen. De uitbrei ding Durable Functions maakt ingebouwde HTTP-Api's beschikbaar en biedt ook Api's voor interactie met Orchestrations en entiteiten van binnen HTTP geactiveerde functies.

### <a name="built-in-http-apis"></a>Ingebouwde HTTP-Api's

Met de extensie Durable Functions wordt automatisch een set HTTP-Api's aan de Azure Functions-host toegevoegd. Met deze Api's kunt u werken met Orchestrations en entiteiten zonder dat u code hoeft te schrijven.

De volgende ingebouwde HTTP-Api's worden ondersteund.

* [Nieuwe indeling starten](durable-functions-http-api.md#start-orchestration)
* [Query Orchestrator-exemplaar](durable-functions-http-api.md#get-instance-status)
* [Orchestrator-exemplaar beëindigen](durable-functions-http-api.md#terminate-instance)
* [Een externe gebeurtenis naar een indeling verzenden](durable-functions-http-api.md#raise-event)
* [Orchestration-geschiedenis opschonen](durable-functions-http-api.md#purge-single-instance-history)
* [Een bewerkings gebeurtenis naar een entiteit verzenden](durable-functions-http-api.md#signal-entity)
* [Een query uitvoeren op de status van een entiteit](durable-functions-http-api.md#query-entity)

Zie het artikel [http-api's](durable-functions-http-api.md) voor een volledige beschrijving van alle ingebouwde http-api's die door de Durable functions-extensie worden weer gegeven.

### <a name="http-api-url-discovery"></a>URL-detectie van HTTP API

De [Orchestration-client binding](durable-functions-bindings.md#orchestration-client) stelt api's beschikbaar die kunnen worden gebruikt voor het genereren van handige nettoladingen voor HTTP-antwoorden. Het kan bijvoorbeeld een antwoord met koppelingen naar beheer-Api's maken voor een specifiek Orchestration-exemplaar. Hier volgt een voor beeld van een HTTP-activerings functie die laat zien hoe u deze API gebruikt voor een nieuw exemplaar van Orchestration:

#### <a name="precompiled-c"></a>Vooraf gecompileerdeC#

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

#### <a name="c-script"></a>C# Script

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

#### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

#### <a name="functionjson"></a>Function.json

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

Het starten van een Orchestrator-functie met de HTTP-trigger functies die hierboven worden weer gegeven, kan worden uitgevoerd met behulp van een HTTP-client. Met de volgende krul opdracht start u een Orchestrator- `DoWork`functie met de naam.

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Hier volgt een voor beeld van een indeling voor een `abc123` indeling met de id (enkele details die zijn verwijderd voor duidelijkheid):

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

Elk van de `~Uri` velden in het vorige voor beeld komt overeen met ingebouwde http-api's. Deze Api's kunnen worden gebruikt voor het beheren van het doel-Orchestrator-exemplaar.

> [!NOTE]
> De indeling van de webhook-Url's kan verschillen, afhankelijk van welke versie van de Azure Functions-host u uitvoert. Het bovenstaande voor beeld is voor de Azure Functions 2,0-host.

Zie de [http API-referentie](durable-functions-http-api.md) documentatie voor een beschrijving van alle ingebouwde http-api's.

### <a name="async-operation-tracking"></a>Asynchrone bewerkingen bijhouden

Het HTTP-antwoord dat eerder is vermeld, is ontworpen om te helpen met het implementeren van langlopende HTTP async-Api's met Durable Functions. Dit patroon wordt soms ook wel het *polling Consumer-patroon*genoemd. De client/server-stroom werkt als volgt:

1. De client geeft een HTTP-aanvraag voor het starten van een langlopend proces, zoals een Orchestrator-functie.
2. De http-trigger van het doel retourneert een http 202 `Location` -antwoord met `statusQueryGetUri` een header met de waarde.
3. De client pollt de URL in de `Location` koptekst. Er worden nog http 202-antwoorden weer geven `Location` met een header.
4. Wanneer het exemplaar is voltooid (of mislukt), retourneert het eind punt `Location` in de header HTTP 200.

Met dit protocol kunnen langlopende processen worden gecooerdineerd met externe clients of services die ondersteuning bieden voor het pollen van `Location` een http-eind punt en het volgen van de header. De client-en server implementaties van dit patroon zijn ingebouwd in de Durable Functions HTTP-Api's.

> [!NOTE]
> Standaard worden alle op HTTP gebaseerde acties die worden geboden door [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/) het standaard asynchrone bewerkings patroon ondersteunen. Deze mogelijkheid maakt het mogelijk een langdurige, duurzame functie in te sluiten als onderdeel van een Logic Apps werk stroom. Meer informatie over Logic Apps ondersteuning voor asynchrone HTTP-patronen vindt u in de [Azure Logic apps werk stroom acties en triggers-documentatie](../../logic-apps/logic-apps-workflow-actions-triggers.md).

> [!NOTE]
> Interactie met Orchestrations kan worden uitgevoerd vanuit elk functie type, niet alleen met HTTP geactiveerde functies.

Zie het artikel [instance Management](durable-functions-instance-management.md) voor meer informatie over het beheren van Orchestrations en entiteiten met behulp van client-api's.

## <a name="consuming-http-apis"></a>HTTP-Api's gebruiken

Orchestrator-functies mogen I/O niet rechtstreeks uitvoeren, zoals beschreven in de [functie code beperkingen van Orchestrator](durable-functions-code-constraints.md). In plaats daarvan roepen Orchestrator doorgaans [activiteiten functies](durable-functions-types-features-overview.md#activity-functions) aan die I/O-bewerkingen uitvoeren.

Met ingang van Durable Functions 2,0 kunnen indelingen systeem eigen HTTP Api's gebruiken met behulp van de [Orchestration trigger binding](durable-functions-bindings.md#orchestration-trigger).

> [!NOTE]
> De mogelijkheid om HTTP-eind punten rechtstreeks vanuit Orchestrator-functies aan te roepen, is nog niet beschikbaar in Java script.

De volgende voorbeeld code toont een C# Orchestrator-functie die een uitgaande HTTP-aanvraag maakt `CallHttpAsync` met behulp van de .net API:

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

Met de actie ' HTTP aanroepen ' kunt u het volgende doen in uw Orchestrator-functies:

* Roep HTTP-Api's rechtstreeks aan vanuit Orchestration functions (met enkele beperkingen die later worden vermeld).
* Automatische ondersteuning voor HTTP 202-status polling aan client zijde.
* Gebruik [Azure Managed Identities](../../active-directory/managed-identities-azure-resources/overview.md) om geautoriseerde http-aanroepen naar andere Azure-eind punten te maken.

De mogelijkheid om HTTP-Api's rechtstreeks van Orchestrator-functies te gebruiken, is bedoeld als gemak voor een bepaalde reeks veelvoorkomende scenario's. U kunt al deze functies zelf implementeren met behulp van activiteiten functies. In veel gevallen kunnen activiteiten functies u meer flexibiliteit bieden.

### <a name="http-202-handling"></a>HTTP 202-verwerking

De API call HTTP kan automatisch de client zijde van het *polling Consumer-patroon*implementeren. Als een aangeroepen API een http 202-antwoord met een `Location` header retourneert, wordt de `Location` resource door de Orchestrator-functie automatisch gecontroleerd totdat een niet-202-antwoord terugkomt. Het niet-202-antwoord is het antwoord dat wordt geretourneerd door de functie code van Orchestrator.

> [!NOTE]
> Orchestrator functions biedt ook systeem eigen ondersteuning voor het *polling-consument patroon*aan de server zijde, zoals beschreven in [async-bewerkingen bijhouden](#async-operation-tracking). Dit betekent dat de integratie in één functie-app de Orchestrator-functies in andere functie-apps eenvoudig kan coördineren. Dit is vergelijkbaar met het principe van de [Sub-Orchestration](durable-functions-sub-orchestrations.md) , maar met ondersteuning voor communicatie tussen apps. Dit is met name handig voor het ontwikkelen van micro service-stijl toepassingen.

### <a name="managed-identities"></a>Beheerde identiteiten

Durable Functions biedt systeem eigen ondersteuning voor het aanroepen van Api's die Azure AD-tokens accepteren voor autorisatie. Deze ondersteuning maakt gebruik van door [Azure beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md) voor het verkrijgen van deze tokens.

De volgende code is een voor beeld van een .NET Orchestrator-functie waarmee geauthenticeerde aanroepen worden gestart om een virtuele machine opnieuw op te starten met behulp van de Azure Resource Manager [Virtual Machines rest API](https://docs.microsoft.com/rest/api/compute/virtualmachines).

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

In het vorige voor beeld is `tokenSource` de para meter geconfigureerd voor het verkrijgen van Azure AD-tokens voor [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) (geïdentificeerd door de `https://management.core.windows.net`' resource-URI '). In het voor beeld wordt ervan uitgegaan dat de huidige functie-app lokaal wordt uitgevoerd of is geïmplementeerd als een Azure Functions-app met een beheerde identiteit. Er wordt van uitgegaan dat de lokale identiteit of de beheerde identiteit gemachtigd is voor het beheren van virtuele machines `myRG`in de opgegeven resource groep.

Tijdens runtime retourneert de geconfigureerde token bron automatisch een OAuth 2,0-toegangs token en voegt deze toe als een Bearer- `Authorization` token aan de header van de uitgaande aanvraag. Dit model is een verbetering ten aanzien van het hand matig toevoegen van autorisatie headers aan HTTP-aanvragen omdat:

* Het vernieuwen van tokens wordt automatisch afgehandeld. U hoeft zich geen zorgen te maken over verlopen tokens.
* Tokens worden nooit opgeslagen in de duurzame indelings status.
* U hoeft geen code te schrijven om te omgaan met het verkrijgen van tokens.

Een vollediger voor beeld vindt u in de [vooraf gecompileerde C# ' RestartVMs](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs)-voor beelden.

Beheerde identiteiten zijn niet beperkt tot Azure resource management. Beheerde identiteiten kunnen worden gebruikt om toegang te krijgen tot elke API die Azure AD Bearer-tokens accepteert, waaronder Azure-Services van de eerste partij of webtoepassingen van derden. De Web-App van derden kan zelfs een andere functie-app zijn. Zie [Azure-Services die ondersteuning bieden voor Azure AD-verificatie](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)voor een lijst met Azure-Services van de eerste partij die ondersteuning bieden voor verificatie met Azure AD.

### <a name="limitations"></a>Beperkingen

De ingebouwde ondersteuning voor het aanroepen van HTTP-Api's is een gebruiks vriendelijke functie en is niet geschikt voor alle scenario's. HTTP-aanvragen die worden verzonden door Orchestrator-functies en de bijbehorende antwoorden, worden geserialiseerd en blijven als wachtrij berichten. Dit gedrag van de wachtrij zorgt ervoor dat HTTP-aanroepen [betrouwbaar en veilig zijn voor Orchestration replay](durable-functions-orchestrations.md#reliability). Dit houdt echter ook in dat:

* Elke HTTP-aanvraag omvat extra latentie in vergelijking met een systeem eigen HTTP-client.
* Grote aanvraag-of antwoord berichten die niet in een wachtrij bericht passen, kunnen de Orchestration-prestaties aanzienlijk afnemen. De mogelijke prestatie vermindering is het gevolg van de overhead van het offloaden van bericht lading naar Blob Storage.
* Het streamen, chunked en binaire payloads worden niet ondersteund.
* De mogelijkheid om het gedrag van de HTTP-client aan te passen, is beperkt.

Als een van deze beperkingen van invloed kan zijn op uw gebruiks aanvraag, overweeg dan om gebruik te maken van activiteit functies en taalspecifieke HTTP-client bibliotheken voor uitgaande HTTP-aanroepen.

> [!NOTE]
> Als u een .net-ontwikkelaar bent, vraagt u zich mogelijk af waarom deze `DurableHttpRequest` functie `DurableHttpResponse` en typen worden gebruikt in plaats van de `HttpRequestMessage` ingebouwde `HttpResponseMessage`.net en. Deze ontwerp keuze was opzettelijk. De primaire reden hiervoor is dat gebruikers met aangepaste typen geen onjuiste hypo Thesen kunnen maken over het ondersteunde gedrag van de interne HTTP-client. Met duurzame, specifieke typen kunt u het API-ontwerp ook vereenvoudigen en eenvoudigere speciale functies, zoals [beheerde identiteits integratie](#managed-identities) en het [polling Consumer-patroon](#http-202-handling), eenvoudiger maken.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over duurzame entiteiten](durable-functions-entities.md)
