---
title: HTTP-functies in duurzame functies - Azure-functies
description: Meer informatie over de geïntegreerde HTTP-functies in de extensie Duurzame functies voor Azure-functies.
author: cgillum
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: fece1155d2f707f11dda9f3896bd8a08deff1557
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802380"
---
# <a name="http-features"></a>HTTP-functies

Duurzame functies hebben verschillende functies die het gemakkelijk maken om duurzame orkestraties en entiteiten in HTTP-workflows op te nemen. Dit artikel gaat in detail over een aantal van deze functies.

## <a name="exposing-http-apis"></a>HTTP-API's blootstellen

Orkestraties en entiteiten kunnen worden aangeroepen en beheerd met HTTP-aanvragen. De extensie Duurzame functies legt ingebouwde HTTP-API's bloot. Het biedt ook API's voor interactie met orkestraties en entiteiten vanuit http-geactiveerde functies.

### <a name="built-in-http-apis"></a>Ingebouwde HTTP-API's

De extensie Duurzame functies voegt automatisch een set HTTP-API's toe aan de Azure Functions-host. Met deze API's u orchestrations en entiteiten gebruiken en beheren zonder code te schrijven.

De volgende ingebouwde HTTP-API's worden ondersteund.

* [Start nieuwe orkestratie](durable-functions-http-api.md#start-orchestration)
* [Instantie van queryorkestratie](durable-functions-http-api.md#get-instance-status)
* [Instantie orchestration beëindigen](durable-functions-http-api.md#terminate-instance)
* [Een externe gebeurtenis naar een orkestratie verzenden](durable-functions-http-api.md#raise-event)
* [Purge orchestration geschiedenis](durable-functions-http-api.md#purge-single-instance-history)
* [Een bewerkingsgebeurtenis naar een entiteit verzenden](durable-functions-http-api.md#signal-entity)
* [De status van een entiteit weergeven](durable-functions-http-api.md#get-entity)
* [De lijst met entiteiten opvragen](durable-functions-http-api.md#list-entities)

Zie het [HTTP API's-artikel](durable-functions-http-api.md) voor een volledige beschrijving van alle ingebouwde HTTP-API's die worden blootgesteld door de extensie Duurzame functies.

### <a name="http-api-url-discovery"></a>HTTP API-URL-detectie

De [orchestration client binding](durable-functions-bindings.md#orchestration-client) onthult API's die handige HTTP-responspayloads kunnen genereren. Het kan bijvoorbeeld een antwoord maken met koppelingen naar beheer-API's voor een specifieke orchestration-instantie. In de volgende voorbeelden wordt een HTTP-triggerfunctie weergegeven die laat zien hoe u deze API gebruiken voor een nieuwe instantie voor orkestratie:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**function.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

---

Het starten van een orchestrator-functie met behulp van de eerder getoonde HTTP-triggerfuncties kan met elke HTTP-client worden uitgevoerd. Met de volgende cURL-opdracht wordt `DoWork`een orchestrator-functie gestart met de naam:

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Vervolgens is een voorbeeldreactie voor `abc123` een orkestratie die als id heeft. Sommige details zijn verwijderd voor de duidelijkheid.

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

In het vorige voorbeeld komt elk `Uri` van de velden die eindigen overeen met een ingebouwde HTTP-API. U deze API's gebruiken om de doelorkestratie-instantie te beheren.

> [!NOTE]
> De indeling van de webhook-URL's is afhankelijk van de versie van de Azure Functions-host die u uitvoert. Het vorige voorbeeld is voor de Azure Functions 2.0-host.

Zie de [HTTP API-verwijzing](durable-functions-http-api.md)voor een beschrijving van alle ingebouwde HTTP-API's.

### <a name="async-operation-tracking"></a>Async-bewerkingbijhouden

Het eerder genoemde HTTP-antwoord is ontworpen om langdurige HTTP-async API's met duurzame functies te implementeren. Dit patroon wordt soms aangeduid als de *polling consument patroon*. De client/serverflow werkt als volgt:

1. De client geeft een HTTP-aanvraag uit om een langlopend proces te starten, zoals een orchestrator-functie.
1. De doel-HTTP-trigger retourneert een HTTP 202-antwoord met een koptekst Locatie met de waarde 'statusQueryGetUri'.
1. De client peilt de URL in de koptekst Locatie. De client blijft HTTP 202-antwoorden zien met een koptekst Locatie.
1. Wanneer de instantie is voltooid of mislukt, retourneert het eindpunt in de kopLocatie HTTP 200.

Dit protocol maakt coördinatie van langlopende processen mogelijk met externe clients of services die een HTTP-eindpunt kunnen peilen en de koptekst Locatie kunnen volgen. Zowel de client- als serverimplementaties van dit patroon zijn ingebouwd in de HTTP-API's voor duurzame functies.

> [!NOTE]
> Standaard ondersteunen alle HTTP-gebaseerde acties van [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) het standaard asynchrone bewerkingspatroon. Deze mogelijkheid maakt het mogelijk om een duurzame functie voor lang gebruik in te sluiten als onderdeel van een Logic Apps-workflow. Meer informatie over Logic Apps-ondersteuning voor asynchrone HTTP-patronen vindt u in de [werkstroomacties van Azure Logic Apps en activeert documentatie.](../../logic-apps/logic-apps-workflow-actions-triggers.md)

> [!NOTE]
> Interacties met orkestraties kunnen worden gedaan vanuit elk functietype, niet alleen http-geactiveerde functies.

Zie het [artikel Instancemanagement](durable-functions-instance-management.md)voor meer informatie over het beheren van orchestrations en entiteiten met behulp van client-API's.

## <a name="consuming-http-apis"></a>HTTP-API's consumeren

Zoals beschreven in de [orchestrator functie code beperkingen,](durable-functions-code-constraints.md)orchestrator functies kunnen niet direct I / O doen. In plaats daarvan noemen ze meestal [activiteitsfuncties](durable-functions-types-features-overview.md#activity-functions) die I/O-bewerkingen uitvoeren.

Vanaf duurzame functies 2.0 kunnen orkestraties http-API's native gebruiken met behulp van de [orchestration trigger binding](durable-functions-bindings.md#orchestration-trigger).

In de volgende voorbeeldcode wordt een orchestrator-functie weergegeven die een uitgaand HTTP-verzoek indient:

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

Met de actie 'OPROEP HTTP' u de volgende acties uitvoeren in uw orchestrator-functies:

* Bel HTTP API's rechtstreeks vanuit orchestration-functies, met enkele beperkingen die later worden vermeld.
* Ondersteuning voor HTTP 202-statuspollingpatronen aan de clientzijde.
* Gebruik [Azure Managed Identities](../../active-directory/managed-identities-azure-resources/overview.md) om geautoriseerde HTTP-aanroepen naar andere Azure-eindpunten te voeren.

De mogelijkheid om HTTP API's rechtstreeks uit orchestrator-functies te gebruiken, is bedoeld als een gemak voor een bepaalde set veelvoorkomende scenario's. U al deze functies zelf implementeren met behulp van activiteitsfuncties. In veel gevallen kunnen activiteitsfuncties u meer flexibiliteit bieden.

### <a name="http-202-handling"></a>HTTP 202-afhandeling

De "call HTTP" API kan automatisch de clientkant van het polling consumentenpatroon implementeren. Als een aangeroepen API een HTTP 202-antwoord retourneert met een koptekst Locatie, wordt de functie Locatie automatisch gepeild totdat de bron Locatie wordt bijgewerkt totdat een andere reactie dan 202 wordt ontvangen. Dit antwoord is het antwoord dat wordt geretourneerd naar de functiecode van de orchestrator.

> [!NOTE]
> Orchestrator-functies ondersteunen ook native het server-side polling consumentenpatroon, zoals beschreven in [Async-bewerkingtracking.](#async-operation-tracking) Deze ondersteuning betekent dat orkestraties in één functie-app de orchestrator-functies eenvoudig kunnen coördineren in andere functie-apps. Dit is vergelijkbaar met het [suborkestratieconcept,](durable-functions-sub-orchestrations.md) maar met ondersteuning voor cross-app communicatie. Deze ondersteuning is vooral handig voor de ontwikkeling van microservice-achtige apps.

### <a name="managed-identities"></a>Beheerde identiteiten

Duurzame functies ondersteunen standaard oproepen naar API's die Azure Active Directory -tokens (Azure AD) accepteren voor autorisatie. Deze ondersteuning maakt gebruik van [door Azure beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md) om deze tokens te verkrijgen.

De volgende code is een voorbeeld van een .NET orchestrator-functie. De functie zorgt ervoor dat geverifieerde aanroepen om een virtuele machine opnieuw op te starten met behulp van de Azure Resource Manager [virtual machines REST API.](https://docs.microsoft.com/rest/api/compute/virtualmachines)

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
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

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

---

In het vorige `tokenSource` voorbeeld is de parameter geconfigureerd om Azure AD-tokens voor [Azure Resource Manager](../../azure-resource-manager/management/overview.md)te verkrijgen. De tokens worden geïdentificeerd door `https://management.core.windows.net`de bron URI . In het voorbeeld wordt ervan uitgegaan dat de huidige functie-app lokaal wordt uitgevoerd of is geïmplementeerd als een functie-app met een beheerde identiteit. De lokale identiteit of de beheerde identiteit wordt verondersteld toestemming `myRG`te hebben om VM's in de opgegeven resourcegroep te beheren.

Bij runtime retourneert de geconfigureerde tokenbron automatisch een OAuth 2.0-toegangstoken. De bron voegt het token vervolgens toe als een token aan toonder aan de autorisatiekop van de uitgaande aanvraag. Dit model is een verbetering ten opzichte van het handmatig toevoegen van autorisatiekoppen aan HTTP-aanvragen om de volgende redenen:

* Tokenvernieuwing wordt automatisch verwerkt. U hoeft zich geen zorgen te maken over verlopen tokens.
* Tokens worden nooit opgeslagen in de duurzame orchestration-status.
* U hoeft geen code te schrijven om tokenacquisitie te beheren.

U vindt een vollediger voorbeeld in het [vooraf gecompileerde C# RestartVm's-voorbeeld.](https://github.com/Azure/azure-functions-durable-extension/blob/dev/samples/precompiled/RestartVMs.cs)

Beheerde identiteiten zijn niet beperkt tot Azure-bronbeheer. U beheerde identiteiten gebruiken om toegang te krijgen tot elke API die Azure AD-tokens voor ad-dragers accepteert, waaronder Azure-services van Microsoft en web-apps van partners. De web-app van een partner kan zelfs een andere functie-app zijn. Zie [Azure-services die Azure AD-verificatie ondersteunen](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)voor een lijst met Azure-services die verificatie van Azure ONDERSTEUNEN.

### <a name="limitations"></a>Beperkingen

De ingebouwde ondersteuning voor het aanroepen van HTTP API's is een handige functie. Het is niet geschikt voor alle scenario's.

HTTP-aanvragen die worden verzonden door orchestrator-functies en hun antwoorden zijn geserialiseerd en persistent als wachtrijberichten. Dit wachtrijgedrag zorgt ervoor dat HTTP-oproepen betrouwbaar en veilig zijn [voor het opnieuw afspelen van orkestratie.](durable-functions-orchestrations.md#reliability) Echter, de wachtrij gedrag heeft ook beperkingen:

* Elke HTTP-aanvraag vereist extra latentie in vergelijking met een native HTTP-client.
* Grote aanvraag- of antwoordberichten die niet in een wachtrijbericht passen, kunnen de orchestration-prestaties aanzienlijk verslechteren. De overhead van het ontladen van berichten payloads naar blob opslag kan leiden tot potentiële prestatiedegradatie.
* Streaming, chunked, en binaire payloads worden niet ondersteund.
* De mogelijkheid om het gedrag van de HTTP-client aan te passen is beperkt.

Als een van deze beperkingen van invloed kan zijn op uw use case, u in plaats daarvan activiteitsfuncties en taalspecifieke HTTP-clientbibliotheken gebruiken om uitgaande HTTP-aanroepen te voeren.

> [!NOTE]
> Als u een .NET-ontwikkelaar bent, u zich afvragen waarom deze functie de typen **DuurzaamHttp1** en **DuurzaamHttpResponse** gebruikt in plaats van de ingebouwde typen .NET **HttpRequestMessage** en **HttpResponseMessage.**
>
> Deze ontwerpkeuze is opzettelijk. De belangrijkste reden is dat aangepaste typen ervoor zorgen dat gebruikers geen onjuiste veronderstellingen maken over het ondersteunde gedrag van de interne HTTP-client. Types die specifiek zijn voor duurzame functies maken het ook mogelijk om API-ontwerp te vereenvoudigen. Ze kunnen ook gemakkelijker speciale functies beschikbaar stellen, zoals [beheerde identiteitsintegratie](#managed-identities) en het [polling-consumentenpatroon.](#http-202-handling) 

### <a name="extensibility-net-only"></a>Uitbreidbaarheid (alleen.NET)

Het aanpassen van het gedrag van de interne HTTP-client van de orchestration is mogelijk met [azure-afhankelijkheidsinjectie .NET.](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection) Dit vermogen kan nuttig zijn voor het maken van kleine gedragsveranderingen. Het kan ook handig zijn voor het testen van de HTTP-client door het injecteren van mock-objecten.

In het volgende voorbeeld wordt aangetoond dat u afhankelijkheidsinjectie gebruikt om TLS/SSL-certificaatvalidatie uit te schakelen voor orchestrator-functies die externe HTTP-eindpunten aanroepen.

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable TLS/SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over duurzame entiteiten](durable-functions-entities.md)
