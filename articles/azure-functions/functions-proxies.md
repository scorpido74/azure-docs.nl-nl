---
title: Werken met proxy's in Azure-functies
description: Overzicht van het gebruik van Azure Functions Proxy's
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 09e4616bc7cbb4361ad067ed64984ed95e9a20c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849187"
---
# <a name="work-with-azure-functions-proxies"></a>Werken met Proxy's voor Azure-functies

In dit artikel wordt uitgelegd hoe u Azure Functions Proxies configureert en werkt. Met deze functie u eindpunten opgeven in uw functie-app die door een andere bron worden geïmplementeerd. U deze proxy's gebruiken om een grote API op te splitsen in meerdere functie-apps (zoals in een microservice-architectuur), terwijl u nog steeds één API-oppervlak voor clients presenteert.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Facturering met standaardfuncties is van toepassing op proxy-uitvoeringen. Zie [Prijzen voor Azure Functions](https://azure.microsoft.com/pricing/details/functions/) voor meer informatie.

## <a name="create-a-proxy"></a><a name="create"></a>Een proxy maken

In deze sectie ziet u hoe u een proxy maakt in de portal Functies.

1. Open de [Azure-portal]en ga vervolgens naar uw functie-app.
2. Selecteer In het linkerdeelvenster de optie **Nieuwe proxy**.
3. Geef een naam op voor uw proxy.
4. Configureer het eindpunt dat in deze functie-app wordt weergegeven door de **routesjabloon** en **HTTP-methoden**op te geven. Deze parameters gedragen zich volgens de regels voor [HTTP-triggers.]
5. Stel de **backend-URL** in op een ander eindpunt. Dit eindpunt kan een functie in een andere functie-app zijn, of het kan een andere API zijn. De waarde hoeft niet statisch te zijn en kan verwijzen naar [toepassingsinstellingen] en [parameters van de oorspronkelijke clientaanvraag.]
6. Klik **op Maken**.

Uw proxy bestaat nu als een nieuw eindpunt in uw functie-app. Vanuit het oogpunt van de client is dit gelijk aan een HttpTrigger in Azure-functies. U uw nieuwe proxy uitproberen door de proxy-URL te kopiëren en te testen met uw favoriete HTTP-client.

## <a name="modify-requests-and-responses"></a><a name="modify-requests-responses"></a>Verzoeken en antwoorden wijzigen

Met Azure Functions Proxies u aanvragen voor en antwoorden van de back-end wijzigen. Deze transformaties kunnen variabelen gebruiken zoals gedefinieerd in [Variabelen gebruiken].

### <a name="modify-the-back-end-request"></a><a name="modify-backend-request"></a>De back-endaanvraag wijzigen

Standaard wordt de back-endaanvraag geparafeerd als een kopie van het oorspronkelijke verzoek. Naast het instellen van de back-end-URL u ook wijzigingen aanbrengen in de HTTP-methode, kopteksten en querytekenreeksparameters. De gewijzigde waarden kunnen verwijzen naar [toepassingsinstellingen] en [parameters van de oorspronkelijke clientaanvraag].

Back-endaanvragen kunnen in de portal worden gewijzigd door het gedeelte *overschrijven* van aanvragen van de pagina met proxydetails uit te breiden. 

### <a name="modify-the-response"></a><a name="modify-response"></a>Het antwoord wijzigen

Standaard wordt het clientantwoord geïnitialiseerd als een kopie van het back-endantwoord. U wijzigingen aanbrengen in de statuscode, redenzin, kopteksten en hoofdtekst van het antwoord. De gewijzigde waarden kunnen verwijzen naar [toepassingsinstellingen], [parameters uit de oorspronkelijke clientaanvraag]en parameters van het [back-endantwoord].

Back-endaanvragen kunnen in de portal worden gewijzigd door het gedeelte *overschrijven* van antwoorden van de pagina met proxydetails uit te breiden. 

## <a name="use-variables"></a><a name="using-variables"></a>Variabelen gebruiken

De configuratie voor een proxy hoeft niet statisch te zijn. U opvoorwaarde stellen variabelen te gebruiken van de oorspronkelijke clientaanvraag, het back-endantwoord of de toepassingsinstellingen.

### <a name="reference-local-functions"></a><a name="reference-localhost"></a>Lokale functies voor verwijzing
U kunt `localhost` direct naar een functie in dezelfde functie-app verwijzen, zonder een retourproxyverzoek.

`"backendurl": "https://localhost/api/httptriggerC#1"`verwijst naar een lokale HTTP-geactiveerde functie op de route`/api/httptriggerC#1`

 
>[!Note]  
>Als uw functie *functie-, beheer- of sysautorisatieniveaus* gebruikt, moet u de code en clientId opgeven, volgens de oorspronkelijke functie-URL. In dit geval zou de `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` verwijzing eruit zien: We raden u aan deze sleutels op te slaan in [toepassingsinstellingen] en te verwijzen naar de toetsen in uw proxy's. Dit voorkomt het opslaan van geheimen in uw broncode. 

### <a name="reference-request-parameters"></a><a name="request-parameters"></a>Parameters voor referentieaanvragen

U aanvraagparameters gebruiken als invoer voor de eigenschap back-end URL of als onderdeel van het wijzigen van aanvragen en antwoorden. Sommige parameters kunnen worden gekoppeld aan de routesjabloon die is opgegeven in de configuratie van de basisproxy en andere kunnen afkomstig zijn van eigenschappen van de binnenkomende aanvraag.

#### <a name="route-template-parameters"></a>Parameters van routesjabloon
Parameters die in de routesjabloon worden gebruikt, kunnen op naam worden verwezen. De parameternamen zijn ingesloten{}in accolades ( ).

Als een proxy bijvoorbeeld een routesjabloon `/pets/{petId}`heeft, zoals , kan de `{petId}`back-end-URL de waarde van , zoals in `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Als de routesjabloon eindigt in een `/api/{*restOfPath}`wildcard, `{restOfPath}` zoals , is de waarde een tekenreeksweergave van de resterende padsegmenten van de binnenkomende aanvraag.

#### <a name="additional-request-parameters"></a>Aanvullende aanvraagparameters
Naast de parameters van de routesjabloon kunnen de volgende waarden worden gebruikt in config-waarden:

* **{request.method}**: de HTTP-methode die wordt gebruikt op de oorspronkelijke aanvraag.
* **{request.headers.\< HeaderName\>}**: een koptekst die kan worden gelezen uit het oorspronkelijke verzoek. Vervang * \<HeaderName\> * door de naam van de koptekst die u wilt lezen. Als de koptekst niet op de aanvraag is opgenomen, is de waarde de lege tekenreeks.
* **{request.querystring.\< ParameterName\>}**: een parameter querytekenreeks die kan worden gelezen uit de oorspronkelijke aanvraag. * \<Vervang ParameterName\> * door de naam van de parameter die u wilt lezen. Als de parameter niet op de aanvraag is opgenomen, is de waarde de lege tekenreeks.

### <a name="reference-back-end-response-parameters"></a><a name="response-parameters"></a>Back-endresponsparameters naverwijzen

Responsparameters kunnen worden gebruikt als onderdeel van het wijzigen van het antwoord op de client. De volgende waarden kunnen worden gebruikt in config-waarden:

* **{backend.response.statusCode}**: de HTTP-statuscode die is geretourneerd op het back-endantwoord.
* **{backend.response.statusReason}**: De HTTP-redenzin die is geretourneerd op de back-endreactie.
* **{backend.response.headers.\< HeaderName\>}**: een koptekst die kan worden gelezen uit de back-endrespons. Vervang * \<HeaderName\> * door de naam van de koptekst die u wilt lezen. Als de koptekst niet in het antwoord is opgenomen, is de waarde de lege tekenreeks.

### <a name="reference-application-settings"></a><a name="use-appsettings"></a>Referentietoepassingsinstellingen

U ook verwijzen naar [toepassingsinstellingen die zijn gedefinieerd voor de functie-app](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) door de instellingsnaam te omringen met procenttekens (%).

Een back-end-URL van *https://%ORDER_PROCESSING_HOST%/api/orders* de instelling '%ORDER_PROCESSING_HOST%' wordt bijvoorbeeld vervangen door de waarde van de ORDER_PROCESSING_HOST instelling.

> [!TIP] 
> Gebruik toepassingsinstellingen voor back-endhosts wanneer u meerdere implementaties of testomgevingen hebt. Op die manier u ervoor zorgen dat u altijd met de rechtsback voor die omgeving praat.

## <a name="troubleshoot-proxies"></a><a name="debugProxies"></a>Problemen oplossen van proxy's

Door de `"debug":true` vlag toe te `proxies.json` voegen aan een proxy in je zal inschakelen foutopsporings logboekregistratie. Logs worden `D:\home\LogFiles\Application\Proxies\DetailedTrace` opgeslagen in en toegankelijk via de geavanceerde tools (kudu). Eventuele HTTP-antwoorden bevatten `Proxy-Trace-Location` ook een koptekst met een URL om toegang te krijgen tot het logboekbestand.

U een proxy van de clientzijde debuggen door een `Proxy-Trace-Enabled` koptekstset toe te voegen aan `true`. Dit zal ook een trace naar het bestandssysteem registreren en de trace-URL als koptekst in het antwoord retourneren.

### <a name="block-proxy-traces"></a>Proxytraceringen blokkeren

Om veiligheidsredenen wilt u mogelijk niet toestaan dat iemand die uw service belt, een trace genereert. Ze hebben geen toegang tot de traceerinhoud zonder uw inloggegevens, maar het genereren van het traceert bronnen en onthult dat u functieproxy's gebruikt.

Schakel sporen helemaal `"debug":false` uit door toe `proxies.json`te voegen aan een bepaalde proxy in uw .

## <a name="advanced-configuration"></a>Geavanceerde configuratie

De proxy's die u configureert, worden opgeslagen in een *proxy.json-bestand,* dat zich in de hoofdmap van een functie-app bevindt. U dit bestand handmatig bewerken en implementeren als onderdeel van uw app wanneer u een van de [implementatiemethoden](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) gebruikt die Functies ondersteunt. 

> [!TIP] 
> Als u een van de implementatiemethoden niet hebt ingesteld, u ook werken met het bestand *proxy's.json* in de portal. Ga naar de functie-app, selecteer **Platformfuncties**en selecteer **vervolgens App Service Editor**. Op deze plaats u de volledige bestandsstructuur van uw functie-app bekijken en vervolgens wijzigingen aanbrengen.

*Volmachten.json* wordt gedefinieerd door een proxy's object, dat bestaat uit benoemde volmachten en hun definities. Optioneel u, als uw editor dit ondersteunt, verwijzen naar een [JSON-schema](http://json.schemastore.org/proxies) voor het voltooien van de code. Een voorbeeldbestand kan er als volgt uitzien:

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

Elke proxy heeft een vriendelijke naam, zoals *proxy1* in het voorgaande voorbeeld. Het bijbehorende object voor proxydefinitie wordt gedefinieerd door de volgende eigenschappen:

* **matchCondition**: Vereist, een object dat de aanvragen definieert die de uitvoering van deze proxy activeren. Het bevat twee eigenschappen die worden gedeeld met [HTTP-triggers:]
    * _methoden:_ een array van de HTTP-methoden waarop de proxy reageert. Als deze niet is opgegeven, reageert de proxy op alle HTTP-methoden op de route.
    * _route_: Vereist - definieert de routesjabloon, waarmee u bepaalt op welke aanvraag-URL's uw proxy reageert. In tegenstelling tot in HTTP-triggers is er geen standaardwaarde.
* **backendUri**: De URL van de back-end resource waaraan de aanvraag moet worden geproxied. Deze waarde kan verwijzen naar toepassingsinstellingen en parameters van de oorspronkelijke clientaanvraag. Als deze eigenschap niet is opgenomen, reageert Azure Functions met een HTTP 200 OK.
* **requestOverrides:** een object dat transformaties definieert in de back-endaanvraag. Zie [Een aanvraag definiërenOverschrijven object].
* **antwoordenOverschrijft:** een object dat transformaties naar de clientrespons definieert. Zie [Een object responseoverrides definiëren].

> [!NOTE] 
> De *eigenschap route* in Azure Functions Proxies is niet eer aan de eigenschap *routePrefix* van de hostconfiguratie van de functie-app. Als u een voorvoegsel `/api`zoals , moet het worden opgenomen in de *route* eigenschap.

### <a name="disable-individual-proxies"></a><a name="disableProxies"></a>Individuele volmachten uitschakelen

U afzonderlijke proxy's `"disabled": true` uitschakelen door `proxies.json` toe te voegen aan de proxy in het bestand. Dit zal ertoe leiden dat alle verzoeken die voldoen aan de matchCondition 404 retourneren.
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

### <a name="application-settings"></a><a name="applicationSettings"></a>Toepassingsinstellingen

Het proxygedrag kan worden beheerd door verschillende app-instellingen. Ze worden allemaal beschreven in de [verwijzing Naar instellingen voor functies-app](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reserved-characters-string-formatting"></a><a name="reservedChars"></a>Gereserveerde tekens (tekenreeksopmaak)

Proxy's lezen alle tekenreeksen uit een JSON-bestand, met \ als ontsnappingssymbool. Volmachten interpreteren ook krullende beugels. Zie hieronder een volledige reeks voorbeelden.

|Teken|Ontsnapt karakter|Voorbeeld|
|-|-|-|
|{ of }|{{ of }}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="define-a-requestoverrides-object"></a><a name="requestOverrides"></a>Een aanvraag definiërenOverschrijft object

Het object requestOverrides definieert wijzigingen die zijn aangebracht in de aanvraag wanneer de back-endbron wordt aangeroepen. Het object wordt gedefinieerd door de volgende eigenschappen:

* **backend.request.method:** de HTTP-methode die wordt gebruikt om de back-end aan te roepen.
* **backend.request.querystring. ParameterName:\>een parameter querytekenreeks die kan worden ingesteld voor de aanroep naar de back-end. \<** Vervang * \<ParameterName\> * door de naam van de parameter die u wilt instellen. Houd er rekening mee dat als de lege tekenreeks is opgegeven, de parameter nog steeds is opgenomen in de back-endaanvraag.
* **backend.request.headers. HeaderName:\>een koptekst die kan worden ingesteld voor de aanroep naar de back-end. \<** Vervang * \<HeaderName\> * door de naam van de koptekst die u wilt instellen. Als u de lege tekenreeks opgeeft, wordt de koptekst niet opgenomen in de back-endaanvraag.

Waarden kunnen verwijzen naar toepassingsinstellingen en parameters van de oorspronkelijke clientaanvraag.

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

### <a name="define-a-responseoverrides-object"></a><a name="responseOverrides"></a>Een object responseOverrides definiëren

Het object requestOverrides definieert wijzigingen die zijn aangebracht in het antwoord dat wordt doorgegeven aan de client. Het object wordt gedefinieerd door de volgende eigenschappen:

* **response.statusCode**: De HTTP-statuscode die moet worden geretourneerd aan de client.
* **response.statusReason**: De HTTP-redenzin die moet worden geretourneerd naar de client.
* **response.body**: De string weergave van het lichaam terug te keren naar de client.
* **response.headers. HeaderName:\>een koptekst die kan worden ingesteld voor het antwoord op de client. \<** Vervang * \<HeaderName\> * door de naam van de koptekst die u wilt instellen. Als u de lege tekenreeks opgeeft, wordt de koptekst niet opgenomen in het antwoord.

Waarden kunnen verwijzen naar toepassingsinstellingen, parameters uit de oorspronkelijke clientaanvraag en parameters uit het back-endantwoord.

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
> In dit voorbeeld wordt de antwoordinstantie `backendUri` direct ingesteld, zodat er geen eigenschap nodig is. In het voorbeeld ziet u hoe u Azure Functions Proxies gebruiken voor het bespotten van API's.

[Azure-portal]: https://portal.azure.com
[HTTP-triggers]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Een aanvraag definiërenOverschrijft object]: #requestOverrides
[Een object responseOverrides definiëren]: #responseOverrides
[toepassingsinstellingen]: #use-appsettings
[Variabelen gebruiken]: #using-variables
[parameters van het oorspronkelijke clientverzoek]: #request-parameters
[parameters van de back-endrespons]: #response-parameters
