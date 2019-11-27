---
title: Werken met proxy's in Azure Functions
description: Overzicht van het gebruik van Azure Functions-proxy 's
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: dffdffdfa80d940c4a50d0a6630c665164f24d5c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230461"
---
# <a name="work-with-azure-functions-proxies"></a>Werken met Azure Functions-proxy 's

In dit artikel wordt uitgelegd hoe u kunt configureren en werken met Azure Functions-proxy's. Met deze functie kunt u eindpunten op uw functie-app die worden geïmplementeerd door een andere resource. Deze proxy's kunt u een grote API opsplitsen in meerdere functie-apps (zoals in een microservice-architectuur), terwijl u nog steeds één API-oppervlak voor clients.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Standaardfuncties facturering is van toepassing op proxy uitvoeringen. Zie [Prijzen voor Azure Functions](https://azure.microsoft.com/pricing/details/functions/) voor meer informatie.

## <a name="create"></a>Een proxy maken

Deze sectie leest u hoe u een proxy maakt in de Functions-portal.

1. Open de [Azure Portal]en ga vervolgens naar uw functie-app.
2. Selecteer **nieuwe proxy**in het linkerdeel venster.
3. Geef een naam voor de proxy.
4. Configureer het eind punt dat wordt weer gegeven in deze functie-app door de **route sjabloon** en **http-methoden**op te geven. Deze para meters gedragen zich op basis van de regels voor [http-triggers].
5. Stel de **back-end-URL** in op een ander eind punt. Dit eindpunt kan een functie in een andere functie-app, of wordt een andere API. De waarde hoeft niet statisch te zijn en kan verwijzen naar [Toepassings instellingen] en- [para meters van de oorspronkelijke client aanvraag].
6. Klik op **Maken**.

De proxy wordt nu bestaat als een nieuw eindpunt op uw functie-app. Vanuit het clientperspectief van een is het gelijk aan een HttpTrigger in Azure Functions. U kunt uw nieuwe proxy uitproberen door de Proxy-URL kopiëren en testen met uw favoriete HTTP-client.

## <a name="modify-requests-responses"></a>Aanvragen en antwoorden wijzigen

U kunt met Azure Functions-proxy's aanvragen en antwoorden van de back-end wijzigen. Deze trans formaties kunnen variabelen gebruiken zoals gedefinieerd in [variabelen gebruiken].

### <a name="modify-backend-request"></a>De aanvraag voor het maken van een back-end wijzigen

Standaard is de back-end-aanvraag als een kopie van de oorspronkelijke aanvraag geïnitialiseerd. Naast het instellen van de back-end-URL, kunt u wijzigingen aanbrengt aan de HTTP-methode, kopteksten en queryreeksparameters. De gewijzigde waarden kunnen verwijzen naar [Toepassings instellingen] en [para meters van de oorspronkelijke client aanvraag].

Back-end-aanvragen kunnen worden gewijzigd in de portal door de sectie *aanvraag onderdrukking* uit te vouwen op de pagina met proxy Details. 

### <a name="modify-response"></a>Het antwoord wijzigen

Standaard wordt het antwoord van de client als een kopie van het antwoord van de back-end geïnitialiseerd. U kunt wijzigingen aanbrengen aan van de reactie statuscode, reden, headers en hoofdtekst. De gewijzigde waarden kunnen verwijzen naar [Toepassings instellingen], [para meters van de oorspronkelijke client aanvraag]en [para meters uit het back-end-antwoord].

U kunt back-end-aanvragen in de portal wijzigen door de sectie *antwoorden negeren* van de pagina met proxy details uit te vouwen. 

## <a name="using-variables"></a>Variabelen gebruiken

De configuratie van een proxy hoeft niet statisch. U kunt het gebruik van variabelen van de aanvraag van de oorspronkelijke client, de reactie van de back-end, of de toepassingsinstellingen-voorwaarde.

### <a name="reference-localhost"></a>Referentie lokale functies
U kunt `localhost` gebruiken om rechtstreeks naar een functie binnen dezelfde functie-app te verwijzen, zonder een retour proxy-aanvraag.

`"backendurl": "https://localhost/api/httptriggerC#1"` verwijst naar een lokale HTTP-geactiveerde functie op de route `/api/httptriggerC#1`

 
>[!Note]  
>Als uw functie gebruikmaakt van *functie-, beheer-of sys* -autorisatie niveaus, moet u de code en clientId opgeven, conform de oorspronkelijke functie-URL. In dit geval ziet de verwijzing er als volgt uit: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` het is aan te raden deze sleutels op te slaan in [Toepassings instellingen] en te verwijzen naar die in uw proxy's. Zo voor komt u dat geheimen worden opgeslagen in de bron code. 

### <a name="request-parameters"></a>Referentie aanvraag parameters

U kunt parameters van de aanvraag kunt gebruiken als invoer voor de eigenschap van de back-end-URL of als onderdeel van het wijzigen van aanvragen en antwoorden. Sommige parameters kunnen afhankelijk zijn van de Routesjabloon die opgegeven in de basis-proxyconfiguratie en anderen kunnen afkomstig zijn van de eigenschappen van de inkomende aanvraag.

#### <a name="route-template-parameters"></a>Route-Sjabloonparameters
Parameters die worden gebruikt in de Routesjabloon zijn beschikbaar voor naar worden verwezen door de naam. De parameter namen worden tussen accolades ({}) geplaatst.

Als een proxy bijvoorbeeld een route sjabloon heeft, zoals `/pets/{petId}`, kan de back-end-URL de waarde van `{petId}`bevatten, zoals in `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Als de route sjabloon eindigt met een Joker teken, zoals `/api/{*restOfPath}`, is de waarde `{restOfPath}` een reeks representatie van de resterende padsegmenten van de binnenkomende aanvraag.

#### <a name="additional-request-parameters"></a>Aanvullende aanvraagparameters
Naast de parameters van de route, kunnen de volgende waarden worden gebruikt in configuratiewaarden:

* **{Request. Method}** : de HTTP-methode die wordt gebruikt voor de oorspronkelijke aanvraag.
* **{Request. headers.\<header\>}** : een header die kan worden gelezen vanuit de oorspronkelijke aanvraag. Vervang *\<kopnaam\>* door de naam van de koptekst die u wilt lezen. Als de header niet in de aanvraag opgenomen is, wordt de waarde een lege tekenreeks zijn.
* **{Request. query string.\<parameter waarde\>}** : een query reeks parameter die kan worden gelezen vanuit de oorspronkelijke aanvraag. Vervang *\<para meter name\>* door de naam van de para meter die u wilt lezen. Als de parameter niet is opgenomen in de aanvraag, wordt de waarde een lege tekenreeks zijn.

### <a name="response-parameters"></a>Referentie parameters voor back-end

Antwoord parameters kunnen worden gebruikt als onderdeel van het wijzigen van het antwoord op de client. De volgende waarden kunnen worden gebruikt in configuratiewaarden:

* **{back-end. Response. code status}** : de HTTP-status code die wordt geretourneerd tijdens het back-end-antwoord.
* **{back-end. Response. statusReason}** : de WACHTWOORDZIN voor http die wordt geretourneerd tijdens het back-end-antwoord.
* **{back-end. Response. headers.\<-header\>}** : een header die kan worden gelezen vanuit het back-end-antwoord. Vervang *\<kopnaam\>* door de naam van de koptekst die u wilt lezen. Als de header niet in het antwoord opgenomen is, wordt de waarde een lege tekenreeks zijn.

### <a name="use-appsettings"></a>Referentie toepassings instellingen

U kunt ook verwijzen naar [Toepassings instellingen die zijn gedefinieerd voor de functie-app](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) door de naam van de instelling te omgeven door procent tekens (%).

Bijvoorbeeld: een back-end-URL van *https://%ORDER_PROCESSING_HOST%/api/orders* zou '% ORDER_PROCESSING_HOST% ' hebben vervangen door de waarde van de instelling ORDER_PROCESSING_HOST.

> [!TIP] 
> Toepassingsinstellingen voor back-end-hosts gebruiken wanneer u meerdere implementaties of testomgevingen. Op die manier kunt u ervoor zorgen dat u altijd praten dan over naar de juiste back-end voor die omgeving.

## <a name="debugProxies"></a>Problemen met Proxy's oplossen

Door de vlag `"debug":true` toe te voegen aan een proxy in uw `proxies.json` kunt u logboek registratie voor fout opsporing inschakelen. Logboeken worden opgeslagen in `D:\home\LogFiles\Application\Proxies\DetailedTrace` en zijn toegankelijk via de geavanceerde hulp middelen (kudu). HTTP-antwoorden bevatten ook een `Proxy-Trace-Location` header met een URL voor toegang tot het logboek bestand.

U kunt fouten opsporen in een proxy van de client zijde door een `Proxy-Trace-Enabled` header toe te voegen aan `true`. Dit wordt ook een tracering Meld naar het bestandssysteem en de trace-URL als een header in het antwoord retourneren.

### <a name="block-proxy-traces"></a>Proxy-traceringen blokkeren

Uit veiligheidsoverwegingen mag u niet wilt dat iedereen die uw service voor het genereren van een tracering aanroepen. Ze pas weer toegang tot de inhoud van de tracering zonder uw aanmeldingsreferenties, maar de tracering genereren resources verbruikt en wordt aangegeven dat u van functie-proxy's gebruikmaakt.

Schakel traceringen samen uit door `"debug":false` toe te voegen aan een bepaalde proxy in uw `proxies.json`.

## <a name="advanced-configuration"></a>Geavanceerd configuratie

De proxy's die u configureert, worden opgeslagen in een *Proxie. json* -bestand, dat zich in de hoofdmap van een functie-app-map bevindt. U kunt dit bestand hand matig bewerken en implementeren als onderdeel van uw app wanneer u een van de [implementatie methoden](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) gebruikt die door functies worden ondersteund. 

> [!TIP] 
> Als u een van de implementatie methoden niet hebt ingesteld, kunt u ook werken met het bestand *proxies. json* in de portal. Ga naar uw functie-app, selecteer **platform functies**en selecteer vervolgens **app service-editor**. Op deze manier kunt u weergeven van de volledige structuur van uw functie-app en breng vervolgens wijzigingen.

*Proxy's. json* wordt gedefinieerd door een proxy object, dat bestaat uit benoemde proxy's en hun definities. Als uw editor dit ondersteunt, kunt u ook verwijzen naar een [JSON-schema](http://json.schemastore.org/proxies) voor het volt ooien van de code. Een voorbeeld van een bestand kan er als volgt uitzien:

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

Elke proxy heeft een beschrijvende naam, zoals *Proxy1* in het vorige voor beeld. De bijbehorende proxy definition-object wordt gedefinieerd door de volgende eigenschappen:

* **matchCondition**: required-een object dat de aanvragen definieert waarmee de uitvoering van deze proxy wordt geactiveerd. Het bevat twee eigenschappen die worden gedeeld met [http-triggers]:
    * _methoden_: een matrix met de HTTP-methoden waarop de proxy reageert. Als deze niet is opgegeven, wordt de proxy reageert op HTTP-methoden worden in de route.
    * _route_: vereist--definieert de route sjabloon en bepaalt welke aanvraag-url's door uw proxy worden beantwoord. In tegenstelling tot in HTTP-triggers is er geen standaardwaarde.
* **backendUri**: de URL van de back-end-bron waarvoor de aanvraag moet worden geproxy. Deze waarde kan verwijzen naar toepassings- en parameters van de oorspronkelijke clientaanvraag. Als deze eigenschap niet opgenomen is, wordt Azure Functions reageert met een HTTP 200 OK.
* **requestOverrides**: een object dat trans formaties in de back-end-aanvraag definieert. Zie [een requestOverrides-object definiëren].
* **responseOverrides**: een object dat trans formaties voor het client antwoord definieert. Zie [een responseOverrides-object definiëren].

> [!NOTE] 
> De eigenschap *route* in azure functions-proxy's voldoet niet aan de eigenschap *routePrefix* van de configuratie van de functie-app-host. Als u een voor voegsel wilt opnemen, zoals `/api`, moet het worden opgenomen in de eigenschap *route* .

### <a name="disableProxies"></a>Afzonderlijke proxy's uitschakelen

U kunt afzonderlijke proxy's uitschakelen door `"disabled": true` toe te voegen aan de proxy in het `proxies.json` bestand. Dit zorgt ervoor dat aanvragen die aan de matchCondition voldoen, 404 retour neren.
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

### <a name="applicationSettings"></a>Toepassings instellingen

De proxy-gedrag kan worden beheerd door verschillende app-instellingen. Deze worden beschreven in de [Naslag informatie over de app-instellingen van functions](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reservedChars"></a>Gereserveerde tekens (teken reeks opmaak)

Proxy's lezen alle teken reeksen uit een JSON-bestand met behulp van \ als een escape-teken. Proxy's interpreteren ook gekrulde accolades. Bekijk een volledige set met voor beelden hieronder.

|Teken|Escape-teken|Voorbeeld|
|-|-|-|
|{of}|{{of}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="requestOverrides"></a>Een requestOverrides-object definiëren

Het object requestOverrides definieert wijzigingen aangebracht in de aanvraag wanneer de back-end-bron wordt aangeroepen. Het object wordt gedefinieerd door de volgende eigenschappen:

* **back-endserver**: de HTTP-methode die wordt gebruikt om de back-end aan te roepen.
* **back-end. Request. query string.\<parameter name\>** : een query reeks parameter die kan worden ingesteld voor de aanroep van de back-end. Vervang *\<para meter name\>* door de naam van de para meter die u wilt instellen. Als de lege tekenreeks is opgegeven, wordt de parameter is niet opgenomen in de back-end-aanvraag.
* **back-end. Request. headers.\<header\>** : een header die kan worden ingesteld voor de aanroep van de back-enddatabase. Vervang *\<kopnaam\>* door de naam van de koptekst die u wilt instellen. Als u een lege tekenreeks opgeeft, wordt de header is niet opgenomen in de back-end-aanvraag.

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

### <a name="responseOverrides"></a>Een responseOverrides-object definiëren

Het object requestOverrides definieert wijzigingen die worden aangebracht in de reactie die wordt doorgegeven aan de client. Het object wordt gedefinieerd door de volgende eigenschappen:

* **Response. code status**: de HTTP-status code die wordt geretourneerd naar de client.
* **Response. statusReason**: de woord groep met de http-reden die moet worden geretourneerd naar de client.
* **Response. Body**: de teken reeks representatie van de hoofd tekst die wordt geretourneerd naar de client.
* **Response. headers.\<header\>** : een header die kan worden ingesteld voor de reactie op de client. Vervang *\<kopnaam\>* door de naam van de koptekst die u wilt instellen. Als u een lege tekenreeks opgeeft, wordt de header is niet opgenomen in het antwoord.

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
> In dit voor beeld wordt de hoofd tekst van de reactie direct ingesteld, zodat er geen `backendUri` eigenschap nodig is. Het voorbeeld ziet hoe u Azure Functions-proxy's kunt gebruiken voor het simuleren van API's.

[Azure Portal]: https://portal.azure.com
[HTTP-triggers]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Een requestOverrides-object definiëren]: #requestOverrides
[Een responseOverrides-object definiëren]: #responseOverrides
[Toepassings instellingen]: #use-appsettings
[Variabelen gebruiken]: #using-variables
[para meters van de oorspronkelijke client aanvraag]: #request-parameters
[para meters uit het back-end-antwoord]: #response-parameters
