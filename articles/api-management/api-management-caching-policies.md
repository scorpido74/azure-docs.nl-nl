---
title: Azure API Management-cachingbeleid | Microsoft Documenten
description: Meer informatie over het caching-beleid dat beschikbaar is voor gebruik in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 06c4ede12f939e48973d3e0b502d90b848d199bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280298"
---
# <a name="api-management-caching-policies"></a>API Management caching policies (Cachebeleid API Management)
In dit onderwerp wordt een verwijzing naar het volgende API-beheerbeleid bevat. Zie [Beleid in API-beheer](https://go.microsoft.com/fwlink/?LinkID=398186)voor informatie over het toevoegen en configureren van beleidsregels.

## <a name="caching-policies"></a><a name="CachingPolicies"></a>Caching-beleid

- Beleid voor het kazelig beleid voor antwoorden
    - [Haal uit de cache](api-management-caching-policies.md#GetFromCache) - Voer cache opzoeken uit en retourneer een geldige antwoorden in de cache indien beschikbaar.
    - [Store to cache](api-management-caching-policies.md#StoreToCache) - Caches reacties volgens de opgegeven cache controle configuratie.
- Beleid voor het plaatsen van waarden
    - [Haal waarde uit cache](#GetFromCacheByKey) - Haal een item in de cache op per sleutel.
    - [Winkelwaarde in cache](#StoreToCacheByKey) - Sla een item op de cache op per sleutel.
    - [Waarde uit cache verwijderen](#RemoveCacheByKey) - Een item in de cache per sleutel verwijderen.

## <a name="get-from-cache"></a><a name="GetFromCache"></a>Uit de cache halen
Gebruik `cache-lookup` het beleid om cache op te zoeken en een geldig antwoord in de cache terug te sturen indien beschikbaar. Dit beleid kan worden toegepast in gevallen waarin de responsinhoud over een bepaalde periode statisch blijft. Response caching vermindert bandbreedte- en verwerkingsvereisten die worden opgelegd aan de backendwebserver en verlaagt de latentie die api-consumenten ervaren.

> [!NOTE]
> Dit beleid moet een bijbehorend [opslagbeleid](api-management-caching-policies.md#StoreToCache) voor cache hebben.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
  <vary-by-header>Accept</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Accept-Charset</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Authorization</vary-by-header>
  <!-- should be present when allow-private-response-caching is "true"-->
  <vary-by-header>header name</vary-by-header>
  <!-- optional, can repeated several times -->
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>
  <!-- optional, can repeated several times -->
</cache-lookup>
```

### <a name="examples"></a>Voorbeelden

#### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Voorbeeld van beleidsexpressies
In dit voorbeeld ziet u hoe u de duur van api-beheerresponscache configureert die `Cache-Control` overeenkomt met de responscache van de backendservice zoals opgegeven door de richtlijn van de back-service. Zie [Cloud Cover Episode 177: Meer API-beheerfuncties met Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) en fast-forward naar 25:25 voor een demonstratie van het configureren en gebruiken van dit beleid.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Zie [Beleidsexpressies](api-management-policy-expressions.md) en [Contextvariabele](api-management-policy-expressions.md#ContextVariables)voor meer informatie.

### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|
|----------|-----------------|--------------|
|cache-lookup|Hoofdelement.|Ja|
|per kop|Begin met het in cache plaatsen van reacties per waarde van een opgegeven koptekst, zoals Accepteren, Accepteren-Charset, Accepteren-Codering, Accepteren-taal, Autorisatie, Verwachten, Van, Host, If-Match.|Nee|
|per query-parameter variëren|Begin met het incacheeren van antwoorden per waarde van opgegeven queryparameters. Voer één of meerdere parameters in. Gebruik puntkomma als scheidingspunt. Als deze niet is opgegeven, worden alle queryparameters gebruikt.|Nee|

### <a name="attributes"></a>Kenmerken

| Name                           | Beschrijving                                                                                                                                                                                                                                                                                                                                                 | Vereist | Standaard           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| allow-private-response-caching | Wanneer ingesteld `true`op, caching van aanvragen die een autorisatie header bevatten.                                                                                                                                                                                                                                                                        | Nee       | false             |
| caching-type               | Kies tussen de volgende waarden van het kenmerk:<br />- `internal`om de ingebouwde API Management-cache te gebruiken,<br />- `external`om de externe cache te gebruiken zoals beschreven in [Een externe Azure-cache voor Redis gebruiken in Azure API-beheer](api-management-howto-cache-external.md),<br />- `prefer-external`om externe cache te gebruiken als deze is geconfigureerd of als de interne cache anders is geconfigureerd. | Nee       | `prefer-external` |
| downstream-caching-type        | Dit kenmerk moet zijn ingesteld op een van de volgende waarden.<br /><br /> - geen - downstream caching is niet toegestaan.<br />- privé - downstream privé-caching is toegestaan.<br />- openbare - private en gedeelde downstream caching is toegestaan.                                                                                                          | Nee       | geen              |
| must-revalidate                | Wanneer downstream caching is ingeschakeld, schakelt `must-revalidate` dit kenmerk de richtlijn cachebeheer in gatewayreacties in of uit.                                                                                                                                                                                                                      | Nee       | waar              |
| variëren per ontwikkelaar              | Ingesteld `true` op cachereacties per [abonnementssleutel.](https://docs.microsoft.com/azure/api-management/api-management-subscriptions)                                                                                                                                                                                                                                                                                                         | Ja      |         False          |
| variëren per ontwikkelaar-groep       | Ingesteld `true` op cachereacties per [gebruikersgroep.](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups)                                                                                                                                                                                                                                                                                                             | Ja      |       False            |

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Beleidsonderdelen:** binnenkomend
- **Beleidsscopes:** alle scopes

## <a name="store-to-cache"></a><a name="StoreToCache"></a>Opslaan naar cache
Het `cache-store` beleid caches reacties volgens de opgegeven cache-instellingen. Dit beleid kan worden toegepast in gevallen waarin de responsinhoud over een bepaalde periode statisch blijft. Response caching vermindert bandbreedte- en verwerkingsvereisten die worden opgelegd aan de backendwebserver en verlaagt de latentie die api-consumenten ervaren.

> [!NOTE]
> Dit beleid moet een [overeenkomstige Get from cache-beleid](api-management-caching-policies.md#GetFromCache) hebben.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<cache-store duration="seconds" />
```

### <a name="examples"></a>Voorbeelden

#### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">
            <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
        </cache-lookup>
    </inbound>
    <outbound>
        <base />
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Voorbeeld van beleidsexpressies
In dit voorbeeld ziet u hoe u de duur van api-beheerresponscache configureert die `Cache-Control` overeenkomt met de responscache van de backendservice zoals opgegeven door de richtlijn van de back-service. Zie [Cloud Cover Episode 177: Meer API-beheerfuncties met Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) en fast-forward naar 25:25 voor een demonstratie van het configureren en gebruiken van dit beleid.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Zie [Beleidsexpressies](api-management-policy-expressions.md) en [Contextvariabele](api-management-policy-expressions.md#ContextVariables)voor meer informatie.

### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|
|----------|-----------------|--------------|
|cache-store|Hoofdelement.|Ja|

### <a name="attributes"></a>Kenmerken

| Name             | Beschrijving                                                                                                                                                                                                                                                                                                                                                 | Vereist | Standaard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| duur         | Time-to-live van de in de cache opgeslagen items, die in enkele seconden zijn opgegeven.                                                                                                                                                                                                                                                                                                   | Ja      | N.v.t.               |

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Beleidssecties:** uitgaand
- **Beleidsscopes:** alle scopes

## <a name="get-value-from-cache"></a><a name="GetFromCacheByKey"></a>Haal waarde uit cache
Gebruik `cache-lookup-value` het beleid om cache opzoeken per sleutel uit te voeren en een in de cache opgeslagen waarde terug te geven. De sleutel kan een willekeurige tekenreekswaarde hebben en wordt meestal geleverd met behulp van een beleidsexpressie.

> [!NOTE]
> Dit beleid moet een overeenkomstige [winkelwaarde in cachebeleid](#StoreToCacheByKey) hebben.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Voorbeeld
Zie [Aangepaste caching in Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/)voor meer informatie en voorbeelden van dit beleid.

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|
|----------|-----------------|--------------|
|cache-lookup-waarde|Hoofdelement.|Ja|

### <a name="attributes"></a>Kenmerken

| Name             | Beschrijving                                                                                                                                                                                                                                                                                                                                                 | Vereist | Standaard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | Kies tussen de volgende waarden van het kenmerk:<br />- `internal`om de ingebouwde API Management-cache te gebruiken,<br />- `external`om de externe cache te gebruiken zoals beschreven in [Een externe Azure-cache voor Redis gebruiken in Azure API-beheer](api-management-howto-cache-external.md),<br />- `prefer-external`om externe cache te gebruiken als deze is geconfigureerd of als de interne cache anders is geconfigureerd. | Nee       | `prefer-external` |
| standaardwaarde    | Een waarde die aan de variabele wordt toegewezen als de cachesleutel opzoekt tot een misser. Als dit kenmerk niet `null` is opgegeven, wordt deze toegewezen.                                                                                                                                                                                                           | Nee       | `null`            |
| sleutel              | Cachesleutelwaarde die u in de opzoekwaarde gebruiken.                                                                                                                                                                                                                                                                                                                       | Ja      | N.v.t.               |
| variabele naam    | Naam van de [contextvariabele](api-management-policy-expressions.md#ContextVariables) waaraan de opgezochte waarde wordt toegewezen, als het opzoeken succesvol is. Als het opzoeken resulteert in een misser, krijgt `default-value` de `null`variabele `default-value` de waarde van het kenmerk toegewezen of , als het kenmerk wordt weggelaten.                                       | Ja      | N.v.t.               |

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Beleidssecties:** binnenkomend, uitgaand, backend, on-error
- **Beleidsscopes:** alle scopes

## <a name="store-value-in-cache"></a><a name="StoreToCacheByKey"></a>Winkelwaarde in cache
De `cache-store-value` cacheopslag per sleutel uitvoert. De sleutel kan een willekeurige tekenreekswaarde hebben en wordt meestal geleverd met behulp van een beleidsexpressie.

> [!NOTE]
> Dit beleid moet een overeenkomstige [waarde ophalen uit cachebeleid](#GetFromCacheByKey) hebben.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Voorbeeld
Zie [Aangepaste caching in Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/)voor meer informatie en voorbeelden van dit beleid.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|
|----------|-----------------|--------------|
|cache-store-waarde|Hoofdelement.|Ja|

### <a name="attributes"></a>Kenmerken

| Name             | Beschrijving                                                                                                                                                                                                                                                                                                                                                 | Vereist | Standaard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | Kies tussen de volgende waarden van het kenmerk:<br />- `internal`om de ingebouwde API Management-cache te gebruiken,<br />- `external`om de externe cache te gebruiken zoals beschreven in [Een externe Azure-cache voor Redis gebruiken in Azure API-beheer](api-management-howto-cache-external.md),<br />- `prefer-external`om externe cache te gebruiken als deze is geconfigureerd of als de interne cache anders is geconfigureerd. | Nee       | `prefer-external` |
| duur         | De waarde wordt in de cache opgeslagen voor de opgegeven duurwaarde, die in seconden is opgegeven.                                                                                                                                                                                                                                                                                 | Ja      | N.v.t.               |
| sleutel              | Cachesleutel de waarde wordt opgeslagen onder.                                                                                                                                                                                                                                                                                                                   | Ja      | N.v.t.               |
| waarde            | De waarde die in de cache moet worden opgeslagen.                                                                                                                                                                                                                                                                                                                                     | Ja      | N.v.t.               |
### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Beleidssecties:** binnenkomend, uitgaand, backend, on-error
- **Beleidsscopes:** alle scopes

### <a name="remove-value-from-cache"></a><a name="RemoveCacheByKey"></a>Waarde uit cache verwijderen
Het `cache-remove-value` verwijdert een item in de cache dat is geïdentificeerd door de sleutel. De sleutel kan een willekeurige tekenreekswaarde hebben en wordt meestal geleverd met behulp van een beleidsexpressie.

#### <a name="policy-statement"></a>Beleidsverklaring

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Voorbeeld

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|
|----------|-----------------|--------------|
|waarde cache-verwijderen|Hoofdelement.|Ja|

#### <a name="attributes"></a>Kenmerken

| Name             | Beschrijving                                                                                                                                                                                                                                                                                                                                                 | Vereist | Standaard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | Kies tussen de volgende waarden van het kenmerk:<br />- `internal`om de ingebouwde API Management-cache te gebruiken,<br />- `external`om de externe cache te gebruiken zoals beschreven in [Een externe Azure-cache voor Redis gebruiken in Azure API-beheer](api-management-howto-cache-external.md),<br />- `prefer-external`om externe cache te gebruiken als deze is geconfigureerd of als de interne cache anders is geconfigureerd. | Nee       | `prefer-external` |
| sleutel              | De sleutel van de eerder in de cache opgeslagen waarde die uit de cache moet worden verwijderd.                                                                                                                                                                                                                                                                                        | Ja      | N.v.t.               |

#### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

- **Beleidssecties:** binnenkomend, uitgaand, backend, on-error
- **Beleidsscopes:** alle scopes

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over beleid:

+ [Beleid in API-beheer](api-management-howto-policies.md)
+ [API's transformeren](transform-api.md)
+ [Beleidsreferentie](api-management-policy-reference.md) voor een volledige lijst met beleidsoverzichten en hun instellingen
+ [Beleidsvoorbeelden](policy-samples.md)
