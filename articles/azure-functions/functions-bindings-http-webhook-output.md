---
title: Azure Functions HTTP-uitvoer bindingen
description: Meer informatie over het retour neren van HTTP-antwoorden in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277620"
---
# <a name="azure-functions-http-output-bindings"></a>Azure Functions HTTP-uitvoer bindingen

Gebruik de HTTP-uitvoer binding om te reageren op de afzender van de HTTP-aanvraag. Deze binding vereist een HTTP-trigger en Hiermee kunt u het antwoord aanpassen dat is gekoppeld aan de aanvraag van de trigger.

De standaard retour waarde voor een door HTTP geactiveerde functie is:

- `HTTP 204 No Content` met een lege hoofd tekst in de functies 2. x en hoger
- `HTTP 200 OK` met een lege hoofd tekst in functions 1. x

## <a name="configuration"></a>Configuratie

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u in het bestand *Function. json* hebt ingesteld. Voor C# class-bibliotheken zijn er geen kenmerk eigenschappen die overeenkomen met deze *functie. json* -eigenschappen.

|Eigenschap  |Beschrijving  |
|---------|---------|
| **type** |Moet worden ingesteld op `http`. |
| **direction** | Moet worden ingesteld op `out`. |
| **naam** | De naam van de variabele die wordt gebruikt in de functie code voor het antwoord of `$return` om de retour waarde te gebruiken. |

## <a name="usage"></a>Gebruik

Als u een HTTP-antwoord wilt verzenden, gebruikt u de standaard-antwoord patronen voor de taal. In C# of C# script, maakt u het functie retour type `IActionResult` of `Task<IActionResult>`. In C#is geen retour waarde-kenmerk vereist.

Zie voor beeld van antwoorden het [voor beeld](./functions-bindings-http-webhook-trigger.md#example)van de trigger.

## <a name="hostjson-settings"></a>instellingen voor host.JSON

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versie 2. x en hoger. In het voor beeld van een host. JSON-bestand bevat alleen de instellingen van versie 2. x + voor deze binding. Zie voor meer informatie over globale configuratie-instellingen in versie 2. x en hoger de [verwijzing host. json voor Azure functions](functions-host-json.md).

> [!NOTE]
> Zie [host. json Reference voor Azure functions 1. x](functions-host-json-v1.md#http)voor een verwijzing naar de host. json in functions 1. x.

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
| customHeaders|geen|Hiermee kunt u aangepaste kopteksten instellen in het HTTP-antwoord. In het vorige voor beeld wordt de `X-Content-Type-Options`-header toegevoegd aan het antwoord om te voor komen dat inhouds type sniffing. |
|dynamicThrottlesEnabled|waar<sup>\*</sup>|Als deze instelling is ingeschakeld, wordt door de aanvraag verwerkings pijplijn regel matig de prestatie meter items van het systeem gecontroleerd, zoals `connections/threads/processes/memory/cpu/etc`. als een van deze prestatie meter items een ingebouwde hoge drempel waarde (80%) heeft, worden aanvragen met een `429 "Too Busy"`-antwoord geweigerd totdat de teller (s) op normale niveaus terugkeren.<br/><sup>\*</sup> De standaard waarde in een verbruiks abonnement is `true`. De standaard instelling in een toegewezen abonnement is `false`.|
|hsts|niet ingeschakeld|Als `isEnabled` is ingesteld op `true`, wordt het [HSTS-gedrag (http strict Trans Port Security) van .net core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) afgedwongen, zoals gedefinieerd in de [`HstsOptions`-klasse](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0). In het bovenstaande voor beeld wordt de eigenschap [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) ook ingesteld op 10 dagen. Ondersteunde eigenschappen van `hsts` zijn: <table><tr><th>Eigenschap</th><th>Beschrijving</th></tr><tr><td>excludedHosts</td><td>Een teken reeks matrix met hostnamen waarvoor de HSTS-header niet is toegevoegd.</td></tr><tr><td>includeSubDomains</td><td>Een Booleaanse waarde die aangeeft of de para meter includeSubDomain van de header strict-Trans Port-Security is ingeschakeld.</td></tr><tr><td>maxAge</td><td>Teken reeks die de para meter max-age van de header strict-Trans Port-Security definieert.</td></tr><tr><td>vooraf laden</td><td>Een Booleaanse waarde die aangeeft of de preload-para meter van de strict-Trans Port-Security-header is ingeschakeld.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|Het maximum aantal HTTP-functies dat parallel wordt uitgevoerd. Met deze waarde kunt u gelijktijdigheid beheren, waardoor het resource gebruik kan worden beheerd. U kunt bijvoorbeeld een HTTP-functie hebben die gebruikmaakt van een groot aantal systeem bronnen (geheugen/CPU/sockets), zodat er problemen ontstaan wanneer gelijktijdigheid te hoog is. Het is ook mogelijk dat u een functie hebt waarmee uitgaande aanvragen worden uitgevoerd naar een service van derden. deze aanroepen moeten een beperkt aantal zijn. In dergelijke gevallen kan het Toep assen van een beperking hier helpen. <br/><sup>*</sup> De standaard waarde voor een verbruiks abonnement is 100. De standaard waarde voor een toegewezen plan is ongebonden (`-1`).|
|maxOutstandingRequests|200<sup>\*</sup>|Het maximum aantal openstaande aanvragen dat op een bepaald moment wordt bewaard. Deze limiet omvat aanvragen die in de wachtrij zijn geplaatst, maar die nog niet zijn gestart, evenals de uitvoeringen die worden uitgevoerd. Alle binnenkomende aanvragen die deze limiet overschrijden, worden geweigerd met een antwoord van 429 ' bezet '. Hiermee kunnen aanroepers op tijd gebaseerde strategieën voor nieuwe pogingen gebruiken en kunt u ook de maximum latentie van aanvragen beheren. Hiermee beheert u alleen de wachtrij die zich in het uitvoerings traject van de Script Host voordoet. Andere wacht rijen, zoals de ASP.NET-aanvraag wachtrij, blijven van kracht en worden niet beïnvloed door deze instelling. <br/><sup>\*</sup> De standaard waarde voor een verbruiks abonnement is 200. De standaard waarde voor een toegewezen plan is ongebonden (`-1`).|
|routePrefix|api|Het route voorvoegsel dat van toepassing is op alle routes. Gebruik een lege teken reeks om het standaard voorvoegsel te verwijderen. |

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren vanuit een HTTP-aanvraag](./functions-bindings-http-webhook-trigger.md)