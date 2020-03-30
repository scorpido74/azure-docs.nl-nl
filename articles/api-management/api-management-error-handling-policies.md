---
title: Foutafhandeling in Azure API Management-beleid | Microsoft Documenten
description: Meer informatie over het reageren op foutvoorwaarden die kunnen optreden tijdens de verwerking van aanvragen in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 2c021a6d10c95b58ac444de8ea895ca01371a2b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902445"
---
# <a name="error-handling-in-api-management-policies"></a>Fout bij het verwerken van API Management-beleid

Door een `ProxyError` object op te geven, stelt Azure API Management uitgevers in staat om te reageren op foutvoorwaarden, die kunnen optreden tijdens de verwerking van aanvragen. Het `ProxyError` object is toegankelijk via de [context. LastError eigenschap](api-management-policy-expressions.md#ContextVariables) en kan worden `on-error` gebruikt door het beleid in de sectie beleid. In dit artikel vindt u een referentie voor de mogelijkheden voor foutafhandeling in Azure API Management.

## <a name="error-handling-in-api-management"></a>Foutafhandeling in API-beheer

Beleidsregels in Azure API `inbound` `backend`Management `outbound`zijn `on-error` onderverdeeld in , , en secties zoals weergegeven in het volgende voorbeeld.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is
         forwarded to the backend service go here -->
    </backend>
    <outbound>
      <!-- statements to be applied to the response go here -->
    </outbound>
    <on-error>
        <!-- statements to be applied if there is an error
             condition go here -->
  </on-error>
</policies>
```

Tijdens de verwerking van een aanvraag worden ingebouwde stappen uitgevoerd, samen met alle beleidsregels die in het bereik van de aanvraag zijn. Als er een fout optreedt, `on-error` wordt de verwerking onmiddellijk naar de beleidssectie overgegaan.
De `on-error` beleidssectie kan op elk gewenst gebied worden gebruikt. API-uitgevers kunnen aangepast gedrag configureren, zoals het registreren van de fout in gebeurtenishubs of het maken van een nieuw antwoord om terug te keren naar de beller.

> [!NOTE]
> De `on-error` sectie is standaard niet aanwezig in beleid. Als u `on-error` de sectie aan een beleid wilt toevoegen, bladert u naar het gewenste beleid in de beleidseditor en voegt u deze toe. Zie [Beleid in API-beheer](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/)voor meer informatie over het configureren van beleidsregels.
>
> Als er `on-error` geen sectie is, ontvangen bellers 400 of 500 HTTP-antwoordberichten als er een foutvoorwaarde optreedt.

### <a name="policies-allowed-in-on-error"></a>Beleid toegestaan in on-error

Het volgende beleid kan `on-error` worden gebruikt in de beleidssectie.

-   [Kies](api-management-advanced-policies.md#choose)
-   [setvariabele](api-management-advanced-policies.md#set-variable)
-   [zoeken en vervangen](api-management-transformation-policies.md#Findandreplacestringinbody)
-   [return-response](api-management-advanced-policies.md#ReturnResponse)
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)
-   [setmethode](api-management-advanced-policies.md#SetRequestMethod)
-   [setstatus](api-management-advanced-policies.md#SetStatus)
-   [verzendverzoek](api-management-advanced-policies.md#SendRequest)
-   [verzenden-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)

## <a name="lasterror"></a>LastError

Wanneer er een fout optreedt `on-error` en de controle naar de beleidssectie springt, wordt de fout in [context opgeslagen. Eigenschap LastError,](api-management-policy-expressions.md#ContextVariables) die toegankelijk is via `on-error` beleidsregels in de sectie. LastError heeft de volgende eigenschappen.

| Name       | Type   | Beschrijving                                                                                               | Vereist |
| ---------- | ------ | --------------------------------------------------------------------------------------------------------- | -------- |
| `Source`   | tekenreeks | Geeft het element waar de fout is opgetreden een naam. Kan beleid of een ingebouwde pijplijnstapnaam zijn.      | Ja      |
| `Reason`   | tekenreeks | Machinevriendelijke foutcode, die kan worden gebruikt bij foutafhandeling.                                       | Nee       |
| `Message`  | tekenreeks | Door de mens leesbare foutbeschrijving.                                                                         | Ja      |
| `Scope`    | tekenreeks | Naam van het bereik waar de fout is opgetreden en kan een van 'globaal', 'product', 'api' of 'bewerking' zijn | Nee       |
| `Section`  | tekenreeks | Sectienaam waarbij er fout is opgetreden. Mogelijke waarden: "binnenkomend", "backend", "outbound" of "on-error".      | Nee       |
| `Path`     | tekenreeks | Hiermee geeft u genest beleid op, bijvoorbeeld "kies[3]/wanneer[2]".                                                 | Nee       |
| `PolicyId` | tekenreeks | Waarde van `id` het kenmerk, indien opgegeven door de klant, op het beleid waar fout optrad             | Nee       |

> [!TIP]
> U hebt toegang tot de statuscode via de context. Response.StatusCode.

> [!NOTE]
> Alle beleidsregels `id` hebben een optioneel kenmerk dat kan worden toegevoegd aan het hoofdelement van het beleid. Als dit kenmerk aanwezig is in een beleid wanneer een foutvoorwaarde optreedt, `context.LastError.PolicyId` kan de waarde van het kenmerk worden opgehaald met de eigenschap.

## <a name="predefined-errors-for-built-in-steps"></a>Vooraf gedefinieerde fouten voor ingebouwde stappen

De volgende fouten zijn vooraf gedefinieerd voor foutvoorwaarden die kunnen optreden tijdens de evaluatie van ingebouwde verwerkingsstappen.

| Bron        | Voorwaarde                                 | Reden                  | Bericht                                                                                                                |
| ------------- | ----------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| configuratie | Uri komt niet overeen met een API of Bewerking | OperationNotFound (OperationNotFound)       | Kan binnenkomend verzoek niet koppelen aan een bewerking.                                                                      |
| autorisatie | Abonnementssleutel niet geleverd             | AbonnementKeyNotFound | Toegang geweigerd vanwege ontbrekende abonnementssleutel. Zorg ervoor dat u de abonnementssleutel opneemt bij het indienen van aanvragen voor deze API. |
| autorisatie | De waarde van abonnementssleutel is ongeldig         | AbonnementkeyOngeldig  | Toegang geweigerd vanwege ongeldige abonnementssleutel. Zorg ervoor dat u een geldige sleutel voor een actief abonnement opgeeft.            |
| veelvoud | Downstream-verbinding (van een client naar een API-beheergateway) werd afgebroken door de client terwijl de aanvraag in behandeling was | ClientConnectionFailure | veelvoud |
| veelvoud | Upstream-verbinding (van een API Management-gateway naar een backend-service) is niet ingesteld of afgebroken door de backend | BackendConnectionMislukt | veelvoud |
| veelvoud | Runtime uitzondering had plaatsgevonden tijdens de evaluatie van een bepaalde uitdrukking | ExpressionValueEvaluationFailure ExpressionValueEvaluationFailure ExpressionValueEvaluationFailure ExpressionValue | veelvoud |

## <a name="predefined-errors-for-policies"></a>Vooraf gedefinieerde fouten voor beleid

De volgende fouten zijn vooraf gedefinieerd voor foutvoorwaarden die kunnen optreden tijdens de beleidsevaluatie.

| Bron       | Voorwaarde                                                       | Reden                    | Bericht                                                                                                                              |
| ------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| tarieflimiet   | Tarieflimiet overschreden                                             | RateLimitoverschreden         | Tarieflimiet wordt overschreden                                                                                                               |
| quota        | Quotum overschreden                                                  | Quotumoverschren             | Volumequotum buiten bereik. Het quotum wordt aangevuld in xx:xx:xx:xx. -of- Buiten bandbreedtequotum. Het quotum wordt aangevuld in xx:xx:xx:xx. |
| jsonp        | De parameterwaarde van terugbellen is ongeldig (bevat verkeerde tekens) | CallbackParameterOngeldig  | Waarde van callbackparameter {callback-parameter-name} is geen geldige JavaScript-id.                                          |
| ip-filter    | Kan het IP-adres van beller niet ontleeden van de aanvraag                          | FailedToParseCallerIP     | Kan geen IP-adres voor de beller vaststellen. Toegang geweigerd.                                                                        |
| ip-filter    | Het IP-nummer van de beller staat niet in de lijst met toegestane                                | BellerIpNotA toegestaan        | Het IP-adres van de beller {ip-adres} is niet toegestaan. Toegang geweigerd.                                                                        |
| ip-filter    | Het IP-nummer van de beller staat in de geblokkeerde lijst                                    | CallerIpBlocked           | Het IP-adres van de beller wordt geblokkeerd. Toegang geweigerd.                                                                                         |
| check-header | Vereiste koptekst niet gepresenteerd of waarde ontbreekt               | HeaderNotFound            | Header {header-name} is niet gevonden in de aanvraag. Toegang geweigerd.                                                                    |
| check-header | Vereiste koptekst niet gepresenteerd of waarde ontbreekt               | HeaderValueNotA toegestaan     | Header {header-name} waarde van {header-value} is niet toegestaan. Toegang geweigerd.                                                          |
| validate-jwt | Jwt-token ontbreekt in aanvraag                                 | TokenNotFound             | JWT niet gevonden in het verzoek. Toegang geweigerd.                                                                                         |
| validate-jwt | Handtekeningvalidatie is mislukt                                     | TokenSignatureOngeldig     | <bericht van jwt bibliotheek\>. Toegang geweigerd.                                                                                          |
| validate-jwt | Ongeldig publiek                                                | TokenAudienceNotA toegestaan   | <bericht van jwt bibliotheek\>. Toegang geweigerd.                                                                                          |
| validate-jwt | Ongeldige uitgever                                                  | TokenIssuerNotA toegestaan     | <bericht van jwt bibliotheek\>. Toegang geweigerd.                                                                                          |
| validate-jwt | Token is verlopen                                                   | TokenExpired              | <bericht van jwt bibliotheek\>. Toegang geweigerd.                                                                                          |
| validate-jwt | Handtekeningsleutel is niet opgelost door id                            | TokenSignatureKeyNotFound | <bericht van jwt bibliotheek\>. Toegang geweigerd.                                                                                          |
| validate-jwt | Vereiste claims ontbreken op token                          | TokenClaimNotFound        | JWT token mist de volgende claims: <\>\>c1, <c2 , ... Toegang geweigerd.                                                            |
| validate-jwt | Mismatch claimwaarden                                           | TokenClaimValueNotA toegestaan | Claim {claim-name} waarde van {claim-value} is niet toegestaan. Toegang geweigerd.                                                             |
| validate-jwt | Andere validatiefouten                                       | JwtInvalid                | <bericht van jwt bibliotheek\>                                                                                                          |
| forward-request of send-request | HTTP-antwoordstatuscode en -koppen zijn niet ontvangen vanaf de backend binnen de geconfigureerde time-out | Time-out | veelvoud |

## <a name="example"></a>Voorbeeld

Een API-beleid instellen op:

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <set-header name="ErrorSource" exists-action="override">
            <value>@(context.LastError.Source)</value>
        </set-header>
        <set-header name="ErrorReason" exists-action="override">
            <value>@(context.LastError.Reason)</value>
        </set-header>
        <set-header name="ErrorMessage" exists-action="override">
            <value>@(context.LastError.Message)</value>
        </set-header>
        <set-header name="ErrorScope" exists-action="override">
            <value>@(context.LastError.Scope)</value>
        </set-header>
        <set-header name="ErrorSection" exists-action="override">
            <value>@(context.LastError.Section)</value>
        </set-header>
        <set-header name="ErrorPath" exists-action="override">
            <value>@(context.LastError.Path)</value>
        </set-header>
        <set-header name="ErrorPolicyId" exists-action="override">
            <value>@(context.LastError.PolicyId)</value>
        </set-header>
        <set-header name="ErrorStatusCode" exists-action="override">
            <value>@(context.Response.StatusCode.ToString())</value>
        </set-header>
        <base />
    </on-error>
</policies>
```

en het verzenden van een ongeautoriseerd verzoek zal resulteren in het volgende antwoord:

![Ongeautoriseerde foutreactie](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over beleid:

-   [Beleid in API-beheer](api-management-howto-policies.md)
-   [API's transformeren](transform-api.md)
-   [Beleidsreferentie](api-management-policy-reference.md) voor een volledige lijst met beleidsoverzichten en hun instellingen
-   [Beleidsvoorbeelden](policy-samples.md)
