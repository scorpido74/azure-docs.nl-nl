---
title: HTTP-uitvoerbindingen voor Azure Functions
description: Meer informatie over het retourneren van HTTP-antwoorden in Azure-functies.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277620"
---
# <a name="azure-functions-http-output-bindings"></a>HTTP-uitvoerbindingen voor Azure Functions

Gebruik de HTTP-uitvoerbinding om te reageren op de http-aanvraagafzender. Voor deze binding is een HTTP-trigger vereist en met de binding kunt u de reactie aanpassen die aan de aanvraag van de trigger is gekoppeld.

De standaardretourwaarde voor een http-geactiveerde functie is:

- `HTTP 204 No Content`met een leeg lichaam in functies 2.x en hoger
- `HTTP 200 OK`met een lege behuizing in functies 1.x

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd die u inhet *bestand function.json* instelt. Voor klassebibliotheken van C# zijn er geen eigenschappen van kenmerk die overeenkomen met deze *eigenschappen van function.json.*

|Eigenschap  |Beschrijving  |
|---------|---------|
| **Type** |Moet ingesteld `http`zijn op. |
| **direction** | Moet ingesteld `out`zijn op. |
| **Naam** | De variabele naam die in de `$return` functiecode voor het antwoord wordt gebruikt of om de retourwaarde te gebruiken. |

## <a name="usage"></a>Gebruik

Als u een HTTP-antwoord wilt verzenden, gebruikt u de taalstandaardresponspatronen. Maak in het script C# of `IActionResult` C# het type functieretour of `Task<IActionResult>`. In C#, is een retourwaardeattribuut niet vereist.

Zie bijvoorbeeld het [triggervoorbeeld](./functions-bindings-http-webhook-trigger.md#example).

## <a name="hostjson-settings"></a>host.json-instellingen

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versies 2.x en hoger. Het voorbeeld host.json-bestand hieronder bevat alleen de instellingen van versie 2.x+ voor deze binding. Zie [host.json-verwijzing naar Azure-functies voor](functions-host-json.md)meer informatie over algemene configuratie-instellingen in versies 2.x en verder.

> [!NOTE]
> Zie [host.json-verwijzing naar Azure-functies 1.x voor](functions-host-json-v1.md#http)een verwijzing naar host.json in Functies 1.x .

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
| aangepaste kopteksten|geen|Hiermee u aangepaste kopteksten instellen in het HTTP-antwoord. In het vorige `X-Content-Type-Options` voorbeeld wordt de koptekst aan het antwoord toegevoegd om te voorkomen dat inhoudstype snuift. |
|dynamicThrottlesEnabled|Waar<sup>\*</sup>|Wanneer deze instelling is ingeschakeld, wordt de aanvraagverwerkingspijplijn `connections/threads/processes/memory/cpu/etc` ertoe gebracht om systeemprestatiemeteritems periodiek te controleren, zoals en als een `429 "Too Busy"` van deze tellers een ingebouwde hoge drempel (80%) heeft, worden aanvragen geweigerd met een antwoord totdat de teller(en) weer op normale niveaus is.<br/><sup>\*</sup>De standaardinstelling in `true`een verbruiksplan is . De standaardinstelling in `false`een toegewezen abonnement is .|
|hsts hsts|niet ingeschakeld|Wanneer `isEnabled` is `true`ingesteld op , wordt het [HTTP Strict Transport Security (HSTS)-gedrag van .NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) afgedwongen, zoals gedefinieerd in de [ `HstsOptions` klasse](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0). Het bovenstaande voorbeeld [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) stelt de eigenschap ook in op 10 dagen. Ondersteunde eigenschappen `hsts` van zijn: <table><tr><th>Eigenschap</th><th>Beschrijving</th></tr><tr><td>uitgeslotenHosts</td><td>Een tekenreeksarray met hostnamen waarvoor de HSTS-header niet wordt toegevoegd.</td></tr><tr><td>includeSubDomains</td><td>Booleaanse waarde die aangeeft of de parameter includeSubDomain van de header Strict-Transport-Security is ingeschakeld.</td></tr><tr><td>maxAge maxAge maxAge maxAge</td><td>Tekenreeks die de parameter max-age van de header Strict-Transport-Security definieert.</td></tr><tr><td>preload</td><td>Booleaan die aangeeft of de parameter preload van de header Strict-Transport-Security is ingeschakeld.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|Het maximum aantal HTTP-functies dat parallel wordt uitgevoerd. Met deze waarde u gelijktijdigheid beheren, waarmee u het gebruik van resources beheren. U bijvoorbeeld een HTTP-functie hebben die een groot aantal systeembronnen (geheugen/cpu/sockets) gebruikt, zodat deze problemen veroorzaakt wanneer gelijktijdigheid te hoog is. Of u hebt mogelijk een functie die uitgaande aanvragen maakt voor een service van derden, en die oproepen moeten worden beperkt. In deze gevallen kan het toepassen van een gaspedaal hier helpen. <br/><sup>*</sup>De standaardinstelling voor een verbruiksabonnement is 100. De standaardinstelling voor een toegewezen`-1`abonnement is onbegrensd ( ).|
|maxOutstandingRequests|200<sup>\*</sup>|Het maximum aantal openstaande aanvragen dat op een bepaald moment wordt gehouden. Deze limiet omvat aanvragen die in de wachtrij staan, maar die niet zijn gestart met uitvoeren, evenals uitvoeringen in uitvoering. Alle binnenkomende aanvragen boven deze limiet worden afgewezen met een antwoord van 429 'Te bezet'. Dat stelt bellers in staat om op tijd gebaseerde strategieën voor nieuwe technieken te gebruiken en helpt u ook om maximale aanvraaglatencies te controleren. Dit bepaalt alleen wachtrijen die plaatsvinden binnen het uitvoeringspad van scripthost. Andere wachtrijen, zoals de wachtrij voor ASP.NET aanvraag, zijn nog steeds van kracht en worden niet beïnvloed door deze instelling. <br/><sup>\*</sup>De standaardinstelling voor een verbruiksabonnement is 200. De standaardinstelling voor een toegewezen`-1`abonnement is onbegrensd ( ).|
|routeVoorvoegsel|api|Het routevoorvoegsel dat van toepassing is op alle routes. Gebruik een lege tekenreeks om het standaardvoorvoegsel te verwijderen. |

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren vanuit een HTTP-aanvraag](./functions-bindings-http-webhook-trigger.md)