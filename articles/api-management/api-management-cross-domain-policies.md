---
title: Azure API Management-crossdomeinbeleid | Microsoft Documenten
description: Meer informatie over het domeinbeleid dat beschikbaar is voor gebruik in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: b72abf4e208c57987375a105865046f194460058
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265985"
---
# <a name="api-management-cross-domain-policies"></a>API Management-beleid voor meerdere domeinen
In dit onderwerp wordt een verwijzing naar het volgende API-beheerbeleid bevat. Zie [Beleid in API-beheer](https://go.microsoft.com/fwlink/?LinkID=398186)voor informatie over het toevoegen en configureren van beleidsregels.

## <a name="cross-domain-policies"></a><a name="CrossDomainPolicies"></a>Domeinbeleid voor meerdere domeinen

- [Cross-domain calls toestaan](api-management-cross-domain-policies.md#AllowCrossDomainCalls) - Maakt de API toegankelijk vanaf Adobe Flash- en Microsoft Silverlight-browsergebaseerde clients.
- [CORS](api-management-cross-domain-policies.md#CORS) - Voegt CORS-ondersteuning (cross-origin resource sharing) toe aan een bewerking of een API om cross-domain calls van browsergebaseerde clients toe te staan.
- [JSONP](api-management-cross-domain-policies.md#JSONP) - Voegt JSON toe met ondersteuning voor opvulling (JSONP) aan een bewerking of een API om cross-domain calls van JavaScript-browsergebaseerde clients toe te staan.

## <a name="allow-cross-domain-calls"></a><a name="AllowCrossDomainCalls"></a>Cross-domeingesprekken toestaan
Gebruik `cross-domain` het beleid om de API toegankelijk te maken voor adobe flash- en microsoft silverlight-browsergebaseerde clients.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>Voorbeeld

```xml
<cross-domain>
    <cross-domain>
        <allow-http-request-headers-from domain='*' headers='*' />
    </cross-domain>
</cross-domain>
```

### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|
|----------|-----------------|--------------|
|cross-domein|Hoofdelement. Onderliggende elementen moeten voldoen aan de [specificatie van het adobe-domeinbeleidsbestand](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Ja|

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Beleidsonderdelen:** binnenkomend
- **Beleidsscopes:** alle scopes

## <a name="cors"></a><a name="CORS"></a>CORS (CORS)
Het `cors` beleid voegt cors-ondersteuning (cross-origin resource sharing) toe aan een bewerking of een API om cross-domain calls van browsergebaseerde clients toe te staan.

Cors stelt een browser en een server in staat om te communiceren en te bepalen of specifieke cross-origin-verzoeken (d.w.z. XMLHttpRequests-oproepen vanuit JavaScript op een webpagina naar andere domeinen) al dan niet moeten worden toegestaan. Dit zorgt voor meer flexibiliteit dan alleen het toestaan van verzoeken van dezelfde oorsprong, maar is veiliger dan het toestaan van alle cross-origin aanvragen.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<cors allow-credentials="false|true">
    <allowed-origins>
        <origin>origin uri</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="number of seconds">
        <method>http verb</method>
    </allowed-methods>
    <allowed-headers>
        <header>header name</header>
    </allowed-headers>
    <expose-headers>
        <header>header name</header>
    </expose-headers>
</cors>
```

### <a name="example"></a>Voorbeeld
In dit voorbeeld wordt uitgelegd hoe u pre-flight aanvragen ondersteunen, zoals aanvragen met aangepaste kopteksten of andere methoden dan GET en POST. Als u aangepaste kopteksten en aanvullende `allowed-methods` `allowed-headers` HTTP-werkwoorden wilt ondersteunen, gebruikt u de secties en secties zoals weergegeven in het volgende voorbeeld.

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <!-- Localhost useful for development -->
        <origin>http://localhost:8080/</origin>
        <origin>http://example.com/</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="300">
        <method>GET</method>
        <method>POST</method>
        <method>PATCH</method>
        <method>DELETE</method>
    </allowed-methods>
    <allowed-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
        <header>x-zumo-version</header>
        <header>x-zumo-auth</header>
        <header>content-type</header>
        <header>accept</header>
    </allowed-headers>
    <expose-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
    </expose-headers>
</cors>
```

### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|cors cors|Hoofdelement.|Ja|N.v.t.|
|toegestane oorsprong|Bevat `origin` elementen die de toegestane oorsprong voor cross-domeinaanvragen beschrijven. `allowed-origins`kan één `origin` element bevatten `*` dat aangeeft dat elke `origin` oorsprong of een of meer elementen die een URI bevatten, moet worden toegestaan.|Ja|N.v.t.|
|origin|De waarde kan `*` zijn om alle oorsprong toe te staan, of een URI die één oorsprong aangeeft. De URI moet een schema, gastheer en poort bevatten.|Ja|Als de poort wordt weggelaten in een URI, wordt poort 80 gebruikt voor HTTP en wordt poort 443 gebruikt voor HTTPS.|
|toegestane methoden|Dit element is vereist als andere methoden dan GET of POST zijn toegestaan. Bevat `method` elementen die de ondersteunde HTTP-werkwoorden opgeven. De `*` waarde geeft alle methoden aan.|Nee|Als deze sectie niet aanwezig is, worden GET en POST ondersteund.|
|method|Hiermee geeft u een HTTP-werkwoord op.|Er is `method` ten minste `allowed-methods` één element vereist als de sectie aanwezig is.|N.v.t.|
|toegestane kop-headers|Dit element `header` bevat elementen die namen opgeven van de kopteksten die in de aanvraag kunnen worden opgenomen.|Nee|N.v.t.|
|expose-headers|Dit element `header` bevat elementen die namen opgeven van de kopteksten die toegankelijk zijn voor de client.|Nee|N.v.t.|
|koptekst|Hiermee geeft u een koptekstnaam op.|Ten minste `header` één element `allowed-headers` `expose-headers` is vereist in of als de sectie aanwezig is.|N.v.t.|

### <a name="attributes"></a>Kenmerken

|Name|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|toegestane referenties|De `Access-Control-Allow-Credentials` koptekst in het preflight-antwoord wordt ingesteld op de waarde van dit kenmerk en heeft invloed op de mogelijkheid van de client om referenties in te dienen in cross-domain-aanvragen.|Nee|false|
|preflight-resultaat-max-leeftijd|De `Access-Control-Max-Age` koptekst in de preflight-respons wordt ingesteld op de waarde van dit kenmerk en heeft invloed op de mogelijkheid van de gebruikersagent om de respons van vóór de vlucht in de cache te plaatsen.|Nee|0|

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Beleidsonderdelen:** binnenkomend
- **Beleidsscopes:** alle scopes

## <a name="jsonp"></a><a name="JSONP"></a>JSONP (JSONP)
Het `jsonp` beleid voegt JSON met ondersteuning voor opvulling (JSONP) toe aan een bewerking of een API om cross-domain calls van JavaScript-browsergebaseerde clients toe te staan. JSONP is een methode die wordt gebruikt in JavaScript-programma's om gegevens op te vragen bij een server in een ander domein. JSONP omzeilt de beperking die wordt afgedwongen door de meeste webbrowsers waar de toegang tot webpagina's zich in hetzelfde domein moet bevinden.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Voorbeeld

```xml
<jsonp callback-parameter-name="cb" />
```

Als u de methode aanroept zonder de callbackparameter ?cb=XXX, wordt gewoon JSON (zonder functiecallwrapper) teruggestuurd.

Als u de callbackparameter `?cb=XXX` toevoegt, wordt een JSONP-resultaat weergegeven, waardoor de oorspronkelijke JSON-resultaten rond de callback-functie worden verpakt zoals`XYZ('<json result goes here>');`

### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|
|----------|-----------------|--------------|
|jsonp|Hoofdelement.|Ja|

### <a name="attributes"></a>Kenmerken

|Name|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|callback-parameter-naam|De javascript-functievoor domeinoverschrijdende functie vooraf gekoppeld aan de volledig gekwalificeerde domeinnaam waar de functie zich bevindt.|Ja|N.v.t.|

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Beleidssecties:** uitgaand
- **Beleidsscopes:** alle scopes

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over beleid:

+ [Beleid in API-beheer](api-management-howto-policies.md)
+ [API's transformeren](transform-api.md)
+ [Beleidsreferentie](api-management-policy-reference.md) voor een volledige lijst met beleidsoverzichten en hun instellingen
+ [Beleidsvoorbeelden](policy-samples.md)
