---
title: Fout afhandeling in azure API Management-beleid | Microsoft Docs
description: Meer informatie over hoe u kunt reageren op fout condities die zich kunnen voordoen tijdens het verwerken van aanvragen in azure API Management.
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
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902445"
---
# <a name="error-handling-in-api-management-policies"></a>Fout afhandeling in API Management beleid

Door een `ProxyError`-object op te geven, kunnen uitgevers door Azure API Management reageren op fout voorwaarden die kunnen optreden tijdens het verwerken van aanvragen. Het `ProxyError`-object wordt geopend via de [context. ](api-management-policy-expressions.md#ContextVariables)De eigenschap Last error en kan worden gebruikt door beleid in de sectie `on-error` Policy. In dit artikel vindt u informatie over de mogelijkheden voor het afhandelen van fouten in azure API Management.

## <a name="error-handling-in-api-management"></a>Fout afhandeling in API Management

Beleids regels in azure API Management worden onderverdeeld in de secties `inbound`, `backend`, `outbound`en `on-error`, zoals in het volgende voor beeld wordt weer gegeven.

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

Tijdens de verwerking van een aanvraag worden de ingebouwde stappen uitgevoerd, samen met alle beleids regels die binnen het bereik van de aanvraag vallen. Als er een fout optreedt, gaat de verwerking direct naar de sectie `on-error` Policy.
De sectie voor het `on-error` beleid kan worden gebruikt in elk bereik. API-uitgevers kunnen aangepaste gedragingen configureren, zoals het vastleggen van de fout in Event hubs of het maken van een nieuw antwoord om terug te keren naar de aanroeper.

> [!NOTE]
> De sectie `on-error` is standaard niet aanwezig in het beleid. Als u de sectie `on-error` wilt toevoegen aan een beleid, bladert u naar het gewenste beleid in de beleids editor en voegt u het toe. Zie [beleid in API Management](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/)voor meer informatie over het configureren van beleid.
>
> Als er geen `on-error` sectie is, ontvangen bellers 400-of 500-HTTP-antwoord berichten als er een fout optreedt.

### <a name="policies-allowed-in-on-error"></a>Beleid dat is toegestaan in on-error

De volgende beleids regels kunnen worden gebruikt in de sectie `on-error` Policy.

-   [desgewenst](api-management-advanced-policies.md#choose)
-   [set-variable](api-management-advanced-policies.md#set-variable)
-   [zoeken en vervangen](api-management-transformation-policies.md#Findandreplacestringinbody)
-   [retour-antwoord](api-management-advanced-policies.md#ReturnResponse)
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)
-   [set-methode](api-management-advanced-policies.md#SetRequestMethod)
-   [set-status](api-management-advanced-policies.md#SetStatus)
-   [send-request](api-management-advanced-policies.md#SendRequest)
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)
-   [aanmelden bij eventhub](api-management-advanced-policies.md#log-to-eventhub)
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)

## <a name="lasterror"></a>LastError

Als er een fout optreedt en er wordt overgeschakeld naar de `on-error`-beleids sectie, wordt de fout opgeslagen in de [context. ](api-management-policy-expressions.md#ContextVariables)De eigenschap Last error, die kan worden geopend door beleid in de sectie `on-error`. Last error heeft de volgende eigenschappen.

| Name       | Type   | Beschrijving                                                                                               | Verplicht |
| ---------- | ------ | --------------------------------------------------------------------------------------------------------- | -------- |
| `Source`   | string | De naam van het element waarop de fout is opgetreden. Dit kan een beleids regel of een ingebouwde pijplijn naam zijn.      | Ja      |
| `Reason`   | string | Machine vriendelijke fout code, die kan worden gebruikt voor het afhandelen van fouten.                                       | Nee       |
| `Message`  | string | Beschrijving van door de mens lees bare fout.                                                                         | Ja      |
| `Scope`    | string | De naam van het bereik waarin de fout is opgetreden en kan een van de volgende zijn: ' Global ', ' product ', ' API ' of ' Operation ' | Nee       |
| `Section`  | string | Sectie naam waarin fout is opgetreden. Mogelijke waarden: ' binnenkomend ', ' back-end ', ' outbound ' of ' on-error '.      | Nee       |
| `Path`     | string | Hiermee geeft u genest beleid op, bijvoorbeeld ' Kies [3]/when [2] '.                                                 | Nee       |
| `PolicyId` | string | Waarde van het kenmerk `id`, indien opgegeven door de klant, op het beleid waar fout is opgetreden             | Nee       |

> [!TIP]
> U kunt de status code openen met behulp van context. Response. status code.

> [!NOTE]
> Alle beleids regels hebben een optioneel `id` kenmerk dat kan worden toegevoegd aan het hoofd element van het beleid. Als dit kenmerk aanwezig is in een beleid als er een fout optreedt, kan de waarde van het kenmerk worden opgehaald met behulp van de eigenschap `context.LastError.PolicyId`.

## <a name="predefined-errors-for-built-in-steps"></a>Vooraf gedefinieerde fouten voor ingebouwde stappen

De volgende fouten zijn vooraf gedefinieerd voor fout situaties die kunnen optreden tijdens de evaluatie van de ingebouwde verwerkings stappen.

| Bron        | Voorwaarde                                 | Reden                  | Bericht                                                                                                                |
| ------------- | ----------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| configuratie | De URI komt niet overeen met een API of bewerking | OperationNotFound       | Kan de inkomende aanvraag niet overeenkomen met een bewerking.                                                                      |
| authorization | De abonnements sleutel is niet opgegeven             | SubscriptionKeyNotFound | De toegang is geweigerd vanwege een ontbrekende abonnements sleutel. Zorg ervoor dat u de abonnements sleutel opneemt bij het maken van aanvragen voor deze API. |
| authorization | De waarde van de abonnements sleutel is ongeldig         | SubscriptionKeyInvalid  | De toegang is geweigerd vanwege een ongeldige abonnements sleutel. Zorg ervoor dat u een geldige sleutel opgeeft voor een actief abonnement.            |
| veelvoud | De downstream-verbinding (van een client naar een API Management Gateway) is afgebroken door de client terwijl de aanvraag in behandeling was | ClientConnectionFailure | veelvoud |
| veelvoud | De upstream-verbinding (van een API Management gateway naar een back-end-service) is niet tot stand gebracht of is afgebroken door de back-end | BackendConnectionFailure | veelvoud |
| veelvoud | Er is een runtime-uitzonde ring opgetreden tijdens de evaluatie van een bepaalde expressie | ExpressionValueEvaluationFailure | veelvoud |

## <a name="predefined-errors-for-policies"></a>Vooraf gedefinieerde fouten voor beleids regels

De volgende fouten zijn vooraf gedefinieerd voor fout situaties die kunnen optreden tijdens de beleids evaluatie.

| Bron       | Voorwaarde                                                       | Reden                    | Bericht                                                                                                                              |
| ------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| frequentie limiet   | Frequentie limiet overschreden                                             | RateLimitExceeded         | Frequentie limiet is overschreden                                                                                                               |
| quota        | Quotum overschreden                                                  | QuotaExceeded             | Volumequotum buiten bereik. Het quotum wordt aangevuld in xx: xx: xx. -of-verouderd quotum voor de band breedte. Het quotum wordt aangevuld in xx: xx: xx. |
| jsonp        | Waarde van call back-para meter is ongeldig (bevat onjuiste tekens) | CallbackParameterInvalid  | De waarde van de call back-para meter {call back-para meter-name} is geen geldige java script-id.                                          |
| ip-filter    | Parseren van beller-IP uit aanvraag mislukt                          | FailedToParseCallerIP     | Kan geen IP-adres voor de oproepende functie instellen. Toegang geweigerd.                                                                        |
| ip-filter    | Het IP-adres van de aanroeper is niet in de lijst met toegestane adressen                                | CallerIpNotAllowed        | Het IP-adres van de oproepende functie {IP-Address} is niet toegestaan. Toegang geweigerd.                                                                        |
| ip-filter    | IP van beller bevindt zich in de lijst met geblokkeerde adressen                                    | CallerIpBlocked           | Het IP-adres van de beller is geblokkeerd. Toegang geweigerd.                                                                                         |
| check-header | De vereiste header is niet weer gegeven of de waarde ontbreekt               | HeaderNotFound            | Header {header-name} is niet gevonden in de aanvraag. Toegang geweigerd.                                                                    |
| check-header | De vereiste header is niet weer gegeven of de waarde ontbreekt               | HeaderValueNotAllowed     | Header {header-name} waarde {header-value} is niet toegestaan. Toegang geweigerd.                                                          |
| validate-jwt | JWT-token ontbreekt in aanvraag                                 | TokenNotFound             | De JWT is niet gevonden in de aanvraag. Toegang geweigerd.                                                                                         |
| validate-jwt | Validatie van hand tekening is mislukt                                     | TokenSignatureInvalid     | < bericht van de JWT-bibliotheek\>. Toegang geweigerd.                                                                                          |
| validate-jwt | Ongeldige doel groep                                                | TokenAudienceNotAllowed   | < bericht van de JWT-bibliotheek\>. Toegang geweigerd.                                                                                          |
| validate-jwt | Ongeldige verlener                                                  | TokenIssuerNotAllowed     | < bericht van de JWT-bibliotheek\>. Toegang geweigerd.                                                                                          |
| validate-jwt | Token is verlopen                                                   | TokenExpired              | < bericht van de JWT-bibliotheek\>. Toegang geweigerd.                                                                                          |
| validate-jwt | De handtekening sleutel is niet omgezet op basis van de ID                            | TokenSignatureKeyNotFound | < bericht van de JWT-bibliotheek\>. Toegang geweigerd.                                                                                          |
| validate-jwt | De vereiste claims ontbreken in het token                          | TokenClaimNotFound        | In het JWT-token ontbreken de volgende claims: < C1\>, < C2\>,... Toegang geweigerd.                                                            |
| validate-jwt | Claim waarden komen niet overeen                                           | TokenClaimValueNotAllowed | Claim {claim naam} waarde {claim-value} is niet toegestaan. Toegang geweigerd.                                                             |
| validate-jwt | Andere validatie fouten                                       | JwtInvalid                | < bericht van de JWT-bibliotheek\>                                                                                                          |
| aanvraag voor door sturen of verzenden | De status code van het HTTP-antwoord is niet ontvangen van de back-end binnen de geconfigureerde time-out | Time-out | veelvoud |

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

en het verzenden van een niet-geautoriseerde aanvraag leidt tot het volgende antwoord:

![Niet-geautoriseerd fout antwoord](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het gebruik van beleid:

-   [Beleid in API Management](api-management-howto-policies.md)
-   [Api's transformeren](transform-api.md)
-   [Beleids verwijzing](api-management-policy-reference.md) voor een volledige lijst met beleids instructies en hun instellingen
-   [Voor beelden van beleid](policy-samples.md)
