---
title: Beleid voor toegangsbeperking azure API Management | Microsoft Documenten
description: Meer informatie over het toegangsbeperkingsbeleid dat beschikbaar is voor gebruik in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 3ba620d66b84e6724751b2024059e8ecd66888cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266115"
---
# <a name="api-management-access-restriction-policies"></a>API Management access restriction policies (Beleid voor toegangsbeperking API Management)

In dit onderwerp wordt een verwijzing naar het volgende API-beheerbeleid bevat. Zie [Beleid in API-beheer](https://go.microsoft.com/fwlink/?LinkID=398186)voor informatie over het toevoegen en configureren van beleidsregels.

## <a name="access-restriction-policies"></a><a name="AccessRestrictionPolicies"></a>Beleid voor toegangsbeperkingen

-   [Http-header controleren](api-management-access-restriction-policies.md#CheckHTTPHeader) - Hiermee wordt het bestaan en/of de waarde van een HTTP-koptekst afgedwongen.
-   [Belsnelheid per abonnement beperken](api-management-access-restriction-policies.md#LimitCallRate) - Voorkomt pieken in API-gebruik door de gesprekssnelheid per abonnement te beperken.
-   [Beperk de aanroepsnelheid per sleutel](#LimitCallRateByKey) - Voorkomt pieken in API-gebruik door de gesprekssnelheid per sleutel te beperken.
-   [Beller-IP's beperken](api-management-access-restriction-policies.md#RestrictCallerIPs) - Filters (staat/weigert) oproepen van specifieke IP-adressen en/of adresbereiken.
-   [Gebruiksquotum instellen op abonnementsbasis](api-management-access-restriction-policies.md#SetUsageQuota) - Hiermee u een contractvolume voor hernieuwbare of levenslange oproepen en/of bandbreedtequota per abonnement afdwingen.
-   [Gebruiksquotum instellen op sleutel](#SetUsageQuotaByKey) - Hiermee u een contractvolume voor hernieuwbare of levenslange oproepen en/of bandbreedtequota per sleutel afdwingen.
-   [JWT valideren](api-management-access-restriction-policies.md#ValidateJWT) - Dwingt het bestaan en de geldigheid van een JWT af die is geëxtraheerd uit een opgegeven HTTP-koptekst of een opgegeven queryparameter.

> [!TIP]
> U toegangsrestrictiebeleid in verschillende scopes gebruiken voor verschillende doeleinden. U bijvoorbeeld de hele API beveiligen met `validate-jwt` AAD-verificatie door het beleid op API-niveau toe `claims` te passen of u deze toepassen op het niveau van de API-bewerking en gebruiken voor meer gedetailleerde besturing.

## <a name="check-http-header"></a><a name="CheckHTTPHeader"></a>HTTP-header controleren

Gebruik `check-header` het beleid om af te dwingen dat een aanvraag een opgegeven HTTP-header heeft. U optioneel controleren of de koptekst een specifieke waarde heeft of controleren op een reeks toegestane waarden. Als de controle mislukt, wordt de verwerking van aanvragen beëindigd en wordt de HTTP-statuscode en het foutbericht geretourneerd dat door het beleid is opgegeven.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>Voorbeeld

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>Elementen

| Name         | Beschrijving                                                                                                                                   | Vereist |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| check-header | Hoofdelement.                                                                                                                                 | Ja      |
| waarde        | Toegestane HTTP-headerwaarde. Wanneer meerdere waardeelementen zijn opgegeven, wordt de controle als een succes beschouwd als een van de waarden een overeenkomst is. | Nee       |

### <a name="attributes"></a>Kenmerken

| Name                       | Beschrijving                                                                                                                                                            | Vereist | Standaard |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| foutbericht mislukt-check-fout | Foutbericht om terug te keren in de HTTP-antwoordtekst als de koptekst niet bestaat of een ongeldige waarde heeft. Dit bericht moet een speciale tekens goed ontsnapt. | Ja      | N.v.t.     |
| httpcode mislukt      | HTTP-statuscode om terug te keren als de koptekst niet bestaat of een ongeldige waarde heeft.                                                                                        | Ja      | N.v.t.     |
| koptekstnaam                | De naam van de HTTP-koptekst die u wilt controleren.                                                                                                                                  | Ja      | N.v.t.     |
| negeren-case                | Kan worden ingesteld op Waar of Onwaar. Als ingesteld op True-aanvraag wordt genegeerd wanneer de kopwaarde wordt vergeleken met de set acceptabele waarden.                                    | Ja      | N.v.t.     |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidsonderdelen:** binnenkomend, uitgaand

-   **Beleidsscopes:** alle scopes

## <a name="limit-call-rate-by-subscription"></a><a name="LimitCallRate"></a>Gesprekstarief beperken per abonnement

Het `rate-limit` beleid voorkomt dat API-gebruikspieken per abonnementsbasis voorkomen door het aanroeppercentage te beperken tot een opgegeven nummer per een bepaalde periode. Wanneer dit beleid wordt geactiveerd, ontvangt de beller een `429 Too Many Requests` statuscode voor het antwoord.

> [!IMPORTANT]
> Dit beleid kan slechts één keer per beleidsdocument worden gebruikt.
>
> [Beleidsuitingen](api-management-policy-expressions.md) kunnen niet worden gebruikt in een van de beleidskenmerken voor dit beleid.

> [!CAUTION]
> Vanwege de gedistribueerde aard van throttling architectuur, tarief beperking is nooit volledig nauwkeurig. Het verschil tussen geconfigureerde en het werkelijke aantal toegestane aanvragen is afhankelijk van het aanvraagvolume en de snelheid, de latentie van backend en andere factoren.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</rate-limit>
```

### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementen

| Name       | Beschrijving                                                                                                                                                                                                                                                                                              | Vereist |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| tarieflimiet | Hoofdelement.                                                                                                                                                                                                                                                                                            | Ja      |
| api        | Voeg een of meer van deze elementen toe om een gesprekstarieflimiet op te leggen aan API's in het product. Product- en API-oproepsnelheidlimieten worden onafhankelijk van elkaar toegepast. API kan worden verwezen `name` `id`via of . Als beide kenmerken zijn `id` verstrekt, `name` zal worden gebruikt en zal worden genegeerd.                    | Nee       |
| Bewerking  | Voeg een of meer van deze elementen toe om een gesprekstarieflimiet op te leggen voor bewerkingen binnen een API. Product-, API- en bewerkingsgespreksfrequentielimieten worden onafhankelijk toegepast. De werking kan worden `name` `id`verwezen via of . Als beide kenmerken zijn `id` verstrekt, `name` zal worden gebruikt en zal worden genegeerd. | Nee       |

### <a name="attributes"></a>Kenmerken

| Name           | Beschrijving                                                                                           | Vereist | Standaard |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | De naam van de API waarvoor de tarieflimiet moet worden toegepast.                                                | Ja      | N.v.t.     |
| Oproepen          | Het maximum aantal oproepen dat is toegestaan tijdens `renewal-period`het tijdsinterval dat is opgegeven in de . | Ja      | N.v.t.     |
| verlengingsperiode | De periode in seconden waarna het quotum wordt gereset.                                              | Ja      | N.v.t.     |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidsonderdelen:** binnenkomend

-   **Beleidsscopes:** product, api, bewerking

## <a name="limit-call-rate-by-key"></a><a name="LimitCallRateByKey"></a>Gesprekssnelheid per sleutel beperken

> [!IMPORTANT]
> Deze functie is niet beschikbaar in de **verbruikslaag** van API-beheer.

Het `rate-limit-by-key` beleid voorkomt dat API-gebruikspieken per sleutel worden door de aanroepsnelheid te beperken tot een opgegeven getal per een bepaalde periode. De sleutel kan een willekeurige tekenreekswaarde hebben en wordt meestal geleverd met behulp van een beleidsexpressie. Optionele incrementvoorwaarde kan worden toegevoegd om aan te geven welke aanvragen moeten worden meegeteld voor de limiet. Wanneer dit beleid wordt geactiveerd, ontvangt de beller een `429 Too Many Requests` statuscode voor het antwoord.

Zie [Geavanceerde aanvraagbeperking met Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/)voor meer informatie en voorbeelden van dit beleid.

> [!CAUTION]
> Vanwege de gedistribueerde aard van throttling architectuur, tarief beperking is nooit volledig nauwkeurig. Het verschil tussen geconfigureerde en het werkelijke aantal toegestane aanvragen is afhankelijk van het aanvraagvolume en de snelheid, de latentie van backend en andere factoren.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### <a name="example"></a>Voorbeeld

In het volgende voorbeeld wordt de tarieflimiet ingetoetst door het IP-adres van de beller.

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementen

| Name              | Beschrijving   | Vereist |
| ----------------- | ------------- | -------- |
| rate-limit-by-key | Hoofdelement. | Ja      |

### <a name="attributes"></a>Kenmerken

| Name                | Beschrijving                                                                                           | Vereist | Standaard |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| Oproepen               | Het maximum aantal oproepen dat is toegestaan tijdens `renewal-period`het tijdsinterval dat is opgegeven in de . | Ja      | N.v.t.     |
| tegentoets         | De sleutel om te gebruiken voor het tarieflimietbeleid.                                                             | Ja      | N.v.t.     |
| increment-voorwaarde | De booleaanse expressie die aangeeft of de`true`aanvraag moet worden meegeteld voor het quotum ( ).        | Nee       | N.v.t.     |
| verlengingsperiode      | De periode in seconden waarna het quotum wordt gereset.                                              | Ja      | N.v.t.     |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidsonderdelen:** binnenkomend

-   **Beleidsscopes:** alle scopes

## <a name="restrict-caller-ips"></a><a name="RestrictCallerIPs"></a>Beller-IP's beperken

De `ip-filter` beleidsfilters (staat/weigert) oproepen van specifieke IP-adressen en/of adresbereiken.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Voorbeeld

In het volgende voorbeeld staat het beleid alleen aanvragen toe die afkomstig zijn van het enkele IP-adres of het bereik van ip-adressen dat is opgegeven

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Elementen

| Name                                      | Beschrijving                                         | Vereist                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filter                                 | Hoofdelement.                                       | Ja                                                            |
| adres                                   | Hiermee geeft u één IP-adres op waarop u moet filteren.   | Ten minste `address` `address-range` één element of element is vereist. |
| adresbereik van="adres" tot="adres" | Hiermee geeft u een bereik van IP-adres op waarop moet worden gefilterd. | Ten minste `address` `address-range` één element of element is vereist. |

### <a name="attributes"></a>Kenmerken

| Name                                      | Beschrijving                                                                                 | Vereist                                           | Standaard |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| adresbereik van="adres" tot="adres" | Een reeks IP-adressen om toegang toe te staan of te weigeren.                                        | Vereist wanneer `address-range` het element wordt gebruikt. | N.v.t.     |
| ip-filter actie ="toestaan &#124; verbieden"    | Hiermee geeft u op of aanroepen al dan niet moeten worden toegestaan voor de opgegeven IP-adressen en -bereiken. | Ja                                                | N.v.t.     |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidsonderdelen:** binnenkomend
-   **Beleidsscopes:** alle scopes

## <a name="set-usage-quota-by-subscription"></a><a name="SetUsageQuota"></a>Gebruiksquotum instellen op abonnement

Het `quota` beleid dwingt een contractvolume voor hernieuwbare of levenslange oproepen en/of bandbreedtequota af, per abonnementsbasis.

> [!IMPORTANT]
> Dit beleid kan slechts één keer per beleidsdocument worden gebruikt.
>
> [Beleidsuitingen](api-management-policy-expressions.md) kunnen niet worden gebruikt in een van de beleidskenmerken voor dit beleid.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementen

| Name      | Beschrijving                                                                                                                                                                                                                                                                                  | Vereist |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| quota     | Hoofdelement.                                                                                                                                                                                                                                                                                | Ja      |
| api       | Voeg een of meer van deze elementen toe om oproepquota op te leggen aan API's binnen het product. Product- en API-aanroepquota worden onafhankelijk toegepast. API kan worden verwezen `name` `id`via of . Als beide kenmerken zijn `id` verstrekt, `name` zal worden gebruikt en zal worden genegeerd.                    | Nee       |
| Bewerking | Voeg een of meer van deze elementen toe om oproepquota op te leggen aan bewerkingen binnen een API. Product-, API- en bewerkingsoproepquota worden onafhankelijk toegepast. De werking kan worden `name` `id`verwezen via of . Als beide kenmerken zijn `id` verstrekt, `name` zal worden gebruikt en zal worden genegeerd. | Nee       |

### <a name="attributes"></a>Kenmerken

| Name           | Beschrijving                                                                                               | Vereist                                                         | Standaard |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | De naam van de API of bewerking waarvoor het quotum van toepassing is.                                             | Ja                                                              | N.v.t.     |
| Bandbreedte      | Het maximum aantal kilobytes dat is toegestaan tijdens `renewal-period`het tijdsinterval dat is opgegeven in de . | Ofwel `calls` `bandwidth`, of beide samen moeten worden opgegeven. | N.v.t.     |
| Oproepen          | Het maximum aantal oproepen dat is toegestaan tijdens `renewal-period`het tijdsinterval dat is opgegeven in de .     | Ofwel `calls` `bandwidth`, of beide samen moeten worden opgegeven. | N.v.t.     |
| verlengingsperiode | De periode in seconden waarna het quotum wordt gereset.                                                  | Ja                                                              | N.v.t.     |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidsonderdelen:** binnenkomend
-   **Beleidsscopes:** product

## <a name="set-usage-quota-by-key"></a><a name="SetUsageQuotaByKey"></a>Gebruiksquotum instellen op sleutel

> [!IMPORTANT]
> Deze functie is niet beschikbaar in de **verbruikslaag** van API-beheer.

Het `quota-by-key` beleid dwingt een hernieuwbaar of levenslange gespreksvolume en/of bandbreedtequotum af, per sleutel. De sleutel kan een willekeurige tekenreekswaarde hebben en wordt meestal geleverd met behulp van een beleidsexpressie. Optionele incrementvoorwaarde kan worden toegevoegd om aan te geven welke aanvragen moeten worden meegeteld voor het quotum. Als meerdere beleidsregels dezelfde sleutelwaarde zouden verhogen, wordt deze slechts één keer per aanvraag verhoogd. Wanneer de gesprekslimiet is bereikt, ontvangt de beller een `403 Forbidden` statuscode voor het antwoord.

Zie [Geavanceerde aanvraagbeperking met Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/)voor meer informatie en voorbeelden van dit beleid.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Voorbeeld

In het volgende voorbeeld wordt het quotum ingetoetst door het IP-adres van de beller.

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementen

| Name  | Beschrijving   | Vereist |
| ----- | ------------- | -------- |
| quota | Hoofdelement. | Ja      |

### <a name="attributes"></a>Kenmerken

| Name                | Beschrijving                                                                                               | Vereist                                                         | Standaard |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| Bandbreedte           | Het maximum aantal kilobytes dat is toegestaan tijdens `renewal-period`het tijdsinterval dat is opgegeven in de . | Ofwel `calls` `bandwidth`, of beide samen moeten worden opgegeven. | N.v.t.     |
| Oproepen               | Het maximum aantal oproepen dat is toegestaan tijdens `renewal-period`het tijdsinterval dat is opgegeven in de .     | Ofwel `calls` `bandwidth`, of beide samen moeten worden opgegeven. | N.v.t.     |
| tegentoets         | De sleutel om te gebruiken voor het quotabeleid.                                                                      | Ja                                                              | N.v.t.     |
| increment-voorwaarde | De booleaanse expressie die aangeeft of de`true`aanvraag moet worden meegeteld voor het quotum ( )             | Nee                                                               | N.v.t.     |
| verlengingsperiode      | De periode in seconden waarna het quotum wordt gereset.                                                  | Ja                                                              | N.v.t.     |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidsonderdelen:** binnenkomend
-   **Beleidsscopes:** alle scopes

## <a name="validate-jwt"></a><a name="ValidateJWT"></a>JWT valideren

Het `validate-jwt` beleid dwingt het bestaan en de geldigheid af van een JWT die is geëxtraheerd uit een opgegeven HTTP-koptekst of een opgegeven queryparameter.

> [!IMPORTANT]
> Het `validate-jwt` beleid vereist `exp` dat de geregistreerde claim is opgenomen `require-expiration-time` in het JWT-token, tenzij het kenmerk is opgegeven en is ingesteld op `false`.
> Het `validate-jwt` beleid ondersteunt HS256 en RS256 ondertekening algoritmen. Voor HS256 moet de sleutel inline worden verstrekt binnen het beleid in het basis64 gecodeerde formulier. Voor RS256 moet de sleutel worden verstrekt via een Open ID configuratie eindpunt.
> Het `validate-jwt` beleid ondersteunt tokens versleuteld met symmetrische sleutels met behulp van de volgende encryptie-algoritmen A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
</validate-jwt>

```

### <a name="examples"></a>Voorbeelden

#### <a name="simple-token-validation"></a>Eenvoudige tokenvalidatie

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Azure Active Directory-tokenvalidatie

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="azure-active-directory-b2c-token-validation"></a>Azure Active Directory B2C-tokenvalidatie

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Toegang tot bewerkingen autoriseren op basis van tokenclaims

In dit voorbeeld ziet u hoe u het [JWT-beleid valideren](api-management-access-restriction-policies.md#ValidateJWT) gebruiken om toegang tot bewerkingen te autoriseren op basis van de waarde van tokenclaims.

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

### <a name="elements"></a>Elementen

| Element             | Beschrijving                                                                                                                                                                                                                                                                                                                                           | Vereist |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | Hoofdelement.                                                                                                                                                                                                                                                                                                                                         | Ja      |
| Publiek           | Bevat een lijst met acceptabele doelgroepclaims die op het token aanwezig kunnen zijn. Als er meerdere doelgroepwaarden aanwezig zijn, wordt elke waarde geprobeerd totdat ze allemaal zijn uitgeput (in welk geval validatie mislukt) of totdat een waarde slaagt. Er moet ten minste één doelgroep worden opgegeven.                                                                     | Nee       |
| issuer-signing-keys | Een lijst met base64-gecodeerde beveiligingssleutels die worden gebruikt om ondertekende tokens te valideren. Als er meerdere beveiligingssleutels aanwezig zijn, wordt elke sleutel geprobeerd totdat ze allemaal zijn uitgeput (in welk geval validatie mislukt) of totdat een validatie slaagt (handig voor tokenrollover). Belangrijke elementen hebben `id` een optioneel `kid` kenmerk dat wordt gebruikt om aan te passen aan claim.               | Nee       |
| decryptiesleutels     | Een lijst met Base64-gecodeerde sleutels die worden gebruikt om de tokens te decoderen. Als er meerdere beveiligingssleutels aanwezig zijn, wordt elke sleutel geprobeerd totdat alle sleutels zijn uitgeput (in welk geval validatie mislukt) of totdat een sleutel slaagt. Belangrijke elementen hebben `id` een optioneel `kid` kenmerk dat wordt gebruikt om aan te passen aan claim.                                                 | Nee       |
| Emittenten             | Een lijst met acceptabele opdrachtgevers die het token hebben uitgegeven. Als er meerdere emittentwaarden aanwezig zijn, wordt elke waarde geprobeerd totdat ze allemaal zijn uitgeput (in welk geval validatie mislukt) of totdat een waarde slaagt.                                                                                                                                         | Nee       |
| openid-config       | Het element dat wordt gebruikt voor het opgeven van een compatibel eindpunt van de Open ID-configuratie waaruit ondertekeningssleutels en uitgever kunnen worden verkregen.                                                                                                                                                                                                                        | Nee       |
| vereiste claims     | Bevat een lijst met claims die naar verwachting aanwezig zullen zijn op het token om deze als geldig te beschouwen. Wanneer `match` het kenmerk `all` is ingesteld op elke claimwaarde in het beleid, moet deze aanwezig zijn in het token om validatie te laten slagen. Wanneer `match` het kenmerk `any` is ingesteld op ten minste één claim, moet deze aanwezig zijn in het token om validatie te laten slagen. | Nee       |

### <a name="attributes"></a>Kenmerken

| Name                            | Beschrijving                                                                                                                                                                                                                                                                                                                                                                                                                                            | Vereist                                                                         | Standaard                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| klokscheeftrekken                      | Tijdspanne. Hiermee u het maximale verwachte tijdsverschil opgeven tussen de systeemklokken van de tokenuitgever en de instantie API-beheer.                                                                                                                                                                                                                                                                                                               | Nee                                                                               | 0 seconden                                                                         |
| foutbericht mislukt-validatie-fout | Foutbericht om terug te keren in de HTTP-antwoordtekst als de JWT de validatie niet doorstaat. Dit bericht moet een speciale tekens goed ontsnapt.                                                                                                                                                                                                                                                                                                 | Nee                                                                               | Standaard foutmelding is afhankelijk van validatie probleem, bijvoorbeeld "JWT niet aanwezig." |
| httpcode mislukt-validatie      | HTTP-statuscode om terug te keren als de JWT niet door validatie voldoet.                                                                                                                                                                                                                                                                                                                                                                                         | Nee                                                                               | 401                                                                               |
| koptekstnaam                     | De naam van de HTTP-header die het token vasthoudt.                                                                                                                                                                                                                                                                                                                                                                                                         | Een `header-name`van `query-parameter-name` `token-value` , of moet worden gespecificeerd. | N.v.t.                                                                               |
| queryparameter-naam            | De naam van de queryparameter die het token vasthoudt.                                                                                                                                                                                                                                                                                                                                                                                                     | Een `header-name`van `query-parameter-name` `token-value` , of moet worden gespecificeerd. | N.v.t.                                                                               |
| tokenwaarde                     | Expressie die een tekenreeks met JWT-token retourt                                                                                                                                                                                                                                                                                                                                                                                                     | Een `header-name`van `query-parameter-name` `token-value` , of moet worden gespecificeerd. | N.v.t.                                                                               |
| id                              | Met `id` het `key` kenmerk op het element u de `kid` tekenreeks opgeven die wordt gekoppeld aan de claim in het token (indien aanwezig) om de juiste sleutel te vinden die u moet gebruiken voor handtekeningvalidatie.                                                                                                                                                                                                                                           | Nee                                                                               | N.v.t.                                                                               |
| Overeenkomen met                           | Het `match` kenmerk `claim` op het element geeft aan of elke claimwaarde in het beleid aanwezig moet zijn in het token om validatie te laten slagen. Mogelijke waarden zijn:<br /><br /> - `all`- elke claimwaarde in het beleid moet aanwezig zijn in het token voor validatie om te slagen.<br /><br /> - `any`- ten minste één claimwaarde moet aanwezig zijn in het token om validatie te laten slagen.                                                       | Nee                                                                               | all                                                                               |
| vereisen-vervaldatum         | Booleaanse. Hiermee geeft u op of een vervaldatumclaim vereist is in het token.                                                                                                                                                                                                                                                                                                                                                                               | Nee                                                                               | waar                                                                              |
| vereisen                  | De naam van het tokenschema, bijvoorbeeld "Drager". Wanneer dit kenmerk is ingesteld, zorgt het beleid ervoor dat de opgegeven regeling aanwezig is in de kopwaarde Autorisatie.                                                                                                                                                                                                                                                                                    | Nee                                                                               | N.v.t.                                                                               |
| vereisen-ondertekend-tokens           | Booleaanse. Hiermee geeft u op of een token moet worden ondertekend.                                                                                                                                                                                                                                                                                                                                                                                           | Nee                                                                               | waar                                                                              |
| Scheidingsteken                       | Tekenreeks. Hiermee geeft u een scheidingsteken op (bijvoorbeeld "") die moet worden gebruikt voor het extraheren van een set waarden uit een claim met meerdere waarden.                                                                                                                                                                                                                                                                                                                                          | Nee                                                                               | N.v.t.                                                                               |
| url                             | Open ID-configuratieeindpunt-URL van waaruit open id-configuratiemetagegevens kunnen worden verkregen. Het antwoord moet worden volgens specs zoals`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`gedefinieerd op URL: . Gebruik voor Azure Active Directory `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` de volgende URL: vervang de `contoso.onmicrosoft.com`naam van uw directorytenant, bijvoorbeeld . | Ja                                                                              | N.v.t.                                                                               |
| naam uitvoer-token-variabele      | Tekenreeks. Naam van contextvariabele die tokenwaarde ontvangt [`Jwt`](api-management-policy-expressions.md) als object van type bij succesvolle tokenvalidatie                                                                                                                                                                                                                                                                                     | Nee                                                                               | N.v.t.                                                                               |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidsonderdelen:** binnenkomend
-   **Beleidsscopes:** alle scopes

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over beleid:

-   [Beleid in API-beheer](api-management-howto-policies.md)
-   [API's transformeren](transform-api.md)
-   [Beleidsreferentie](api-management-policy-reference.md) voor een volledige lijst met beleidsoverzichten en hun instellingen
-   [Beleidsvoorbeelden](policy-samples.md)
