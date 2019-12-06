---
title: Werken met proxy's in Azure Functions
description: Overzicht van het gebruik van Azure Functions-proxy 's
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 09e4616bc7cbb4361ad067ed64984ed95e9a20c5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849187"
---
# <a name="work-with-azure-functions-proxies"></a>Werken met Azure Functions-proxy 's

In dit artikel wordt uitgelegd hoe u kunt configureren en werken met Azure Functions-proxy's. Met deze functie kunt u eindpunten op uw functie-app die worden geïmplementeerd door een andere resource. Deze proxy's kunt u een grote API opsplitsen in meerdere functie-apps (zoals in een microservice-architectuur), terwijl u nog steeds één API-oppervlak voor clients.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Standaardfuncties facturering is van toepassing op proxy uitvoeringen. Zie [Prijzen voor Azure Functions](https://azure.microsoft.com/pricing/details/functions/) voor meer informatie.

## <a name="create"></a>Maak een proxy

Deze sectie leest u hoe u een proxy maakt in de Functions-portal.

1. Open de [Azure-portal], en ga vervolgens naar uw functie-app.
2. Selecteer in het linkerdeelvenster **nieuwe proxy**.
3. Geef een naam voor de proxy.
4. Configureer het eindpunt dat wordt weergegeven op deze functie-app door op te geven de **Routesjabloon** en **HTTP-methoden**. Deze parameters zich gedragen volgens de regels voor [HTTP-triggers].
5. Stel de **back-end-URL** naar een ander eindpunt. Dit eindpunt kan een functie in een andere functie-app, of wordt een andere API. De waarde hoeft niet te niet statisch zijn, en deze kan verwijzen naar [toepassingsinstellingen] en [parameters van de aanvraag van de oorspronkelijke client].
6. Klik op **Maken**.

De proxy wordt nu bestaat als een nieuw eindpunt op uw functie-app. Vanuit het clientperspectief van een is het gelijk aan een HttpTrigger in Azure Functions. U kunt uw nieuwe proxy uitproberen door de Proxy-URL kopiëren en testen met uw favoriete HTTP-client.

## <a name="modify-requests-responses"></a>Wijzigen van aanvragen en antwoorden

U kunt met Azure Functions-proxy's aanvragen en antwoorden van de back-end wijzigen. Deze transformaties variabelen kunnen gebruiken, zoals gedefinieerd in [variabelen gebruiken].

### <a name="modify-backend-request"></a>Wijzigen van de back-end-aanvraag

Standaard is de back-end-aanvraag als een kopie van de oorspronkelijke aanvraag geïnitialiseerd. Naast het instellen van de back-end-URL, kunt u wijzigingen aanbrengt aan de HTTP-methode, kopteksten en queryreeksparameters. De gewijzigde waarden kunnen verwijzen naar [toepassingsinstellingen] en [parameters van de aanvraag van de oorspronkelijke client].

Back-end-aanvragen kunnen worden gewijzigd in de portal door de sectie *aanvraag onderdrukking* uit te vouwen op de pagina met proxy Details. 

### <a name="modify-response"></a>Het antwoord worden gewijzigd

Standaard wordt het antwoord van de client als een kopie van het antwoord van de back-end geïnitialiseerd. U kunt wijzigingen aanbrengen aan van de reactie statuscode, reden, headers en hoofdtekst. De gewijzigde waarden kunnen verwijzen naar [toepassingsinstellingen], [parameters van de aanvraag van de oorspronkelijke client], en [parameters uit het antwoord van de back-end].

U kunt back-end-aanvragen in de portal wijzigen door de sectie *antwoorden negeren* van de pagina met proxy details uit te vouwen. 

## <a name="using-variables"></a>Variabelen gebruiken

De configuratie van een proxy hoeft niet statisch. U kunt het gebruik van variabelen van de aanvraag van de oorspronkelijke client, de reactie van de back-end, of de toepassingsinstellingen-voorwaarde.

### <a name="reference-localhost"></a>Naslaginformatie over lokale functies
U kunt `localhost` om te verwijzen naar de functie binnen dezelfde functie-app rechtstreeks, zonder dat de aanvraag voor een retour-proxy.

`"backendurl": "https://localhost/api/httptriggerC#1"` verwijst naar een lokale HTTP-geactiveerde functie in de route `/api/httptriggerC#1`

 
>[!Note]  
>Als uw functie maakt gebruik van *functie, beheerder of sys* machtigingsniveaus, moet u de code en de clientId, aan de hand van de oorspronkelijke functie-URL opgeven. In dit geval ziet de verwijzing er als volgt uit: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` het is aan te raden deze sleutels op te slaan in [Toepassingsinstellingen] en te verwijzen naar die in uw proxy's. Zo voor komt u dat geheimen worden opgeslagen in de bron code. 

### <a name="request-parameters"></a>Aanvraagparameters verwijzing

U kunt parameters van de aanvraag kunt gebruiken als invoer voor de eigenschap van de back-end-URL of als onderdeel van het wijzigen van aanvragen en antwoorden. Sommige parameters kunnen afhankelijk zijn van de Routesjabloon die opgegeven in de basis-proxyconfiguratie en anderen kunnen afkomstig zijn van de eigenschappen van de inkomende aanvraag.

#### <a name="route-template-parameters"></a>Route-Sjabloonparameters
Parameters die worden gebruikt in de Routesjabloon zijn beschikbaar voor naar worden verwezen door de naam. De namen van parameters zijn tussen accolades ({}).

Bijvoorbeeld, als een proxy heeft een Routesjabloon, zoals `/pets/{petId}`, de URL van de back-end kunt opnemen de waarde van `{petId}`, zoals in `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Als de Routesjabloon wordt beëindigd in een jokerteken, zoals `/api/{*restOfPath}`, de waarde `{restOfPath}` wordt een tekenreeksweergave van de resterende padsegmenten van de inkomende aanvraag.

#### <a name="additional-request-parameters"></a>Aanvullende aanvraagparameters
Naast de parameters van de route, kunnen de volgende waarden worden gebruikt in configuratiewaarden:

* **{request.method}** : De HTTP-methode die wordt gebruikt op de oorspronkelijke aanvraag.
* **{request.headers. \<Headernaam\>}** : een header die kan worden gelezen uit de oorspronkelijke aanvraag. Vervang *\<headernaam\>* met de naam van de header die u wilt lezen. Als de header niet in de aanvraag opgenomen is, wordt de waarde een lege tekenreeks zijn.
* **{request.querystring. \<ParameterName\>}** : een queryreeks-parameter die kan worden gelezen uit de oorspronkelijke aanvraag. Vervang *\<ParameterName\>* met de naam van de parameter die u wilt lezen. Als de parameter niet is opgenomen in de aanvraag, wordt de waarde een lege tekenreeks zijn.

### <a name="response-parameters"></a>Naslaginformatie over back-end antwoord parameters

Antwoord parameters kunnen worden gebruikt als onderdeel van het wijzigen van het antwoord op de client. De volgende waarden kunnen worden gebruikt in configuratiewaarden:

* **{backend.response.statusCode}** : De HTTP-statuscode die op de back-end-antwoord wordt geretourneerd.
* **{backend.response.statusReason}** : De HTTP-reden dat op de back-end-antwoord wordt geretourneerd.
* **{backend.response.headers. \<Headernaam\>}** : een header die kan worden gelezen uit het antwoord van de back-end. Vervang *\<headernaam\>* met de naam van de header die u wilt lezen. Als de header niet in het antwoord opgenomen is, wordt de waarde een lege tekenreeks zijn.

### <a name="use-appsettings"></a>Naslaginformatie over toepassingsinstellingen

U kunt ook verwijzen naar [toepassingsinstellingen gedefinieerd voor de functie-app](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) door de naam van de instelling procenttekens (%).

Bijvoorbeeld, een back-end-URL van *https://%ORDER_PROCESSING_HOST%/api/orders* "% ORDER_PROCESSING_HOST %" vervangen door de waarde van de instelling ORDER_PROCESSING_HOST zou hebben.

> [!TIP] 
> Toepassingsinstellingen voor back-end-hosts gebruiken wanneer u meerdere implementaties of testomgevingen. Op die manier kunt u ervoor zorgen dat u altijd praten dan over naar de juiste back-end voor die omgeving.

## <a name="debugProxies"></a>Proxy's oplossen

Door toe te voegen van de vlag `"debug":true` naar een proxy in uw `proxies.json` schakelt u logboekregistratie voor foutopsporing. Logboeken worden opgeslagen in `D:\home\LogFiles\Application\Proxies\DetailedTrace` en toegankelijk zijn via de geavanceerde hulpmiddelen (kudu). Een HTTP-antwoorden bevat ook een `Proxy-Trace-Location` -header met een URL voor toegang tot het logboekbestand.

U kunt fouten opsporen in een proxy vanaf de client door toe te voegen een `Proxy-Trace-Enabled` header ingesteld op `true`. Dit wordt ook een tracering Meld naar het bestandssysteem en de trace-URL als een header in het antwoord retourneren.

### <a name="block-proxy-traces"></a>Proxy-traceringen blokkeren

Uit veiligheidsoverwegingen mag u niet wilt dat iedereen die uw service voor het genereren van een tracering aanroepen. Ze pas weer toegang tot de inhoud van de tracering zonder uw aanmeldingsreferenties, maar de tracering genereren resources verbruikt en wordt aangegeven dat u van functie-proxy's gebruikmaakt.

Traceringen helemaal uitschakelen door toe te voegen `"debug":false` naar een bepaalde proxy in uw `proxies.json`.

## <a name="advanced-configuration"></a>Geavanceerd configuratie

De proxy's die u configureert, worden opgeslagen in een *proxies.json* bestand, dat in de hoofdmap van een functie-app-map bevindt zich. U kunt handmatig dit bestand bewerken en implementeren als onderdeel van uw app, wanneer u een van de [implementatiemethoden](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) die functies worden ondersteund. 

> [!TIP] 
> Als u niet hebt ingesteld om een van de methoden voor het implementeren, kunt u ook werken met de *proxies.json* bestand in de portal. Ga naar uw functie-app, selecteer **platformfuncties**, en selecteer vervolgens **App Service-Editor**. Op deze manier kunt u weergeven van de volledige structuur van uw functie-app en breng vervolgens wijzigingen.

*Proxies.JSON* wordt gedefinieerd door een proxy-object, dat uit de benoemde proxy's en de bijbehorende definities bestaat. (Optioneel) als de editor wordt ondersteund, kunt u verwijzen naar een [JSON-schema](http://json.schemastore.org/proxies) voor de codevoltooiing. Een voorbeeld van een bestand kan er als volgt uitzien:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Elke proxy heeft een beschrijvende naam, zoals *proxy1* in het voorgaande voorbeeld. De bijbehorende proxy definition-object wordt gedefinieerd door de volgende eigenschappen:

* **matchCondition**: vereist--een object voor het definiëren van de aanvragen die de uitvoering van deze proxy activeren. Deze twee eigenschappen die worden gedeeld met bevat [HTTP-triggers]:
    * _methoden_: een matrix met de proxy reageert op HTTP-methoden. Als deze niet is opgegeven, wordt de proxy reageert op HTTP-methoden worden in de route.
    * _route_: vereist--definieert de Routesjabloon beheren die aanvraag-URL's uw proxy reageert op. In tegenstelling tot in HTTP-triggers is er geen standaardwaarde.
* **backendUri**: de URL van de back-end-resource waarvoor u de aanvraag via proxy moet. Deze waarde kan verwijzen naar toepassings- en parameters van de oorspronkelijke clientaanvraag. Als deze eigenschap niet opgenomen is, wordt Azure Functions reageert met een HTTP 200 OK.
* **requestOverrides**: een object dat definieert de transformaties met de back-end-aanvraag. Zie [Een object requestOverrides definiëren].
* **responseOverrides**: een object dat definieert de transformaties met het antwoord van de client. Zie [Een object responseOverrides definiëren].

> [!NOTE] 
> De *route* eigenschap in Azure Functions-proxy's komt niet voor de eer de *routePrefix* eigenschap van de configuratie van de functie-App-host. Als u wilt opnemen een voorvoegsel zoals `/api`, moeten worden opgenomen in de *route* eigenschap.

### <a name="disableProxies"></a> Afzonderlijke proxy's uitschakelen

U kunt afzonderlijke proxy's uitschakelen door toe te voegen `"disabled": true` naar de proxy in het `proxies.json` bestand. Dit zorgt ervoor dat aanvragen die aan de matchCondition voldoen, 404 retour neren.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

### <a name="applicationSettings"></a> Toepassingsinstellingen

De proxy-gedrag kan worden beheerd door verschillende app-instellingen. Ze worden alle beschreven in de [naslaginformatie voor Functions-App](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reservedChars"></a> Gereserveerde tekens (tekenreeks opmaak)

Proxy's lezen alle teken reeksen uit een JSON-bestand met behulp van \ als een escape-teken. Proxy's interpreteren ook gekrulde accolades. Bekijk een volledige set met voor beelden hieronder.

|Teken|Escape-teken|Voorbeeld|
|-|-|-|
|{of}|{{of}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="requestOverrides"></a>Een object requestOverrides definiëren

Het object requestOverrides definieert wijzigingen aangebracht in de aanvraag wanneer de back-end-bron wordt aangeroepen. Het object wordt gedefinieerd door de volgende eigenschappen:

* **backend.Request.Method**: de HTTP-methode die wordt gebruikt voor het aanroepen van de back-end.
* **backend.Request.QueryString. \<ParameterName\>** : een queryreeks-parameter die kan worden ingesteld voor het aanroepen van de back-end. Vervang *\<ParameterName\>* met de naam van de parameter die u wilt instellen. Houd er rekening mee dat als de lege teken reeks wordt opgegeven, de para meter nog steeds wordt opgenomen in de back-end-aanvraag.
* **backend.Request.headers. \<Headernaam\>** : een header die kan worden ingesteld voor het aanroepen van de back-end. Vervang *\<headernaam\>* met de naam van de header die u wilt instellen. Als u een lege tekenreeks opgeeft, wordt de header is niet opgenomen in de back-end-aanvraag.

Waarden kunnen verwijzen naar toepassings- en parameters van de oorspronkelijke clientaanvraag.

Een voorbeeldconfiguratie kan er als volgt uitzien:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="responseOverrides"></a>Een object responseOverrides definiëren

Het object requestOverrides definieert wijzigingen die worden aangebracht in de reactie die wordt doorgegeven aan de client. Het object wordt gedefinieerd door de volgende eigenschappen:

* **response.statusCode**: de HTTP-statuscode moeten worden geretourneerd naar de client.
* **response.statusReason**: de HTTP-reden wordt geretourneerd naar de client.
* **Response.BODY**: de tekenreeksweergave van de instantie moet worden geretourneerd naar de client.
* **Response.headers. \<Headernaam\>** : een header die kan worden ingesteld voor het antwoord op de client. Vervang *\<headernaam\>* met de naam van de header die u wilt instellen. Als u een lege tekenreeks opgeeft, wordt de header is niet opgenomen in het antwoord.

Waarden kunnen verwijzen naar toepassingsinstellingen, de parameters van de aanvraag van de oorspronkelijke client en de parameters uit het antwoord van de back-end.

Een voorbeeldconfiguratie kan er als volgt uitzien:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> In dit voorbeeld wordt de antwoordtekst is rechtstreeks instellen, dus geen `backendUri` eigenschap nodig is. Het voorbeeld ziet hoe u Azure Functions-proxy's kunt gebruiken voor het simuleren van API's.

[Azure-portal]: https://portal.azure.com
[HTTP-triggers]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Een object requestOverrides definiëren]: #requestOverrides
[Een object responseOverrides definiëren]: #responseOverrides
[Toepassingsinstellingen]: #use-appsettings
[Variabelen gebruiken]: #using-variables
[parameters van de aanvraag van de oorspronkelijke client]: #request-parameters
[parameters uit het antwoord van de back-end]: #response-parameters
