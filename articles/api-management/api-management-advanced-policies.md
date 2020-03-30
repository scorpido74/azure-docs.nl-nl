---
title: Geavanceerde beleidsregels voor Azure API Management | Microsoft Documenten
description: Meer informatie over het geavanceerde beleid dat beschikbaar is voor gebruik in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: c8ef481fe277d6451923da828f0e7473354c24cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266167"
---
# <a name="api-management-advanced-policies"></a>API Management advanced policies (Geavanceerde beleidsregels API Management)

In dit onderwerp wordt een verwijzing naar het volgende API-beheerbeleid bevat. Zie [Beleid in API-beheer](https://go.microsoft.com/fwlink/?LinkID=398186)voor informatie over het toevoegen en configureren van beleidsregels.

## <a name="advanced-policies"></a><a name="AdvancedPolicies"></a>Geavanceerd beleid

-   [Controlestroom](api-management-advanced-policies.md#choose) - Past beleidsoverzichten voorwaardelijk toe op basis van de resultaten van de evaluatie van Booleaanse [expressies](api-management-policy-expressions.md).
-   [Aanvraag doorsturen](#ForwardRequest) : doorstuurt het verzoek naar de backendservice.
-   [Gelijktijdigheid beperken](#LimitConcurrency) : voorkomt dat gesloten beleid wordt uitgevoerd met meer dan het opgegeven aantal aanvragen tegelijk.
-   [Log uit naar gebeurtenishub](#log-to-eventhub) - Verzendt berichten in de opgegeven indeling naar een gebeurtenishub die is gedefinieerd door een loggerentiteit.
-   [Mock response](#mock-response) - Hiermee wordt de uitvoering van de pijplijn afgebroken en wordt een bespot antwoord rechtstreeks naar de beller geretourneerd.
-   [Opnieuw proberen](#Retry) - Herprobeert de uitvoering van de bijgevoegde beleidsoverzichten opnieuw, als en totdat aan de voorwaarde is voldaan. De uitvoering wordt herhaald met de opgegeven tijdsintervallen en tot het opgegeven aantal opnieuw proberen.
-   [Retourrespons](#ReturnResponse) - Hiermee wordt de uitvoering van de pijplijn afgebroken en wordt het opgegeven antwoord rechtstreeks naar de beller geretourneerd.
-   [Eenenkele reisaanvraag verzenden](#SendOneWayRequest) - Stuurt een aanvraag naar de opgegeven URL zonder te wachten op een antwoord.
-   [Aanvraag verzenden](#SendRequest) - Stuurt een aanvraag naar de opgegeven URL.
-   [HTTP-proxy instellen](#SetHttpProxy) - Hiermee u doorgestuurde aanvragen doorsturen via een HTTP-proxy.
-   [Aanvraagmethode instellen](#SetRequestMethod) - Hiermee u de HTTP-methode voor een aanvraag wijzigen.
-   [Statuscode instellen](#SetStatus) : hiermee wijzigt u de HTTP-statuscode in de opgegeven waarde.
-   [Variabele instellen](api-management-advanced-policies.md#set-variable) : houdt een waarde in een benoemde [contextvariabele](api-management-policy-expressions.md#ContextVariables) voor latere toegang aan.
-   [Trace](#Trace) - Voegt aangepaste traces toe aan de [API Inspector-uitvoer,](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) Application Insights-telemetrieën en diagnostische logboeken.
-   [Wacht](#Wait) - Wacht op ingesloten [aanvraag verzenden](api-management-advanced-policies.md#SendRequest), Waarde ophalen [uit cache](api-management-caching-policies.md#GetFromCacheByKey)of [Voerbeleid beheren](api-management-advanced-policies.md#choose) voordat u verdergaat.

## <a name="control-flow"></a><a name="choose"></a>Regelstroom

Het `choose` beleid past bijgevoegde beleidsverklaringen toe op basis van de evaluatie van Booleaanse expressies, vergelijkbaar met een als-dan-anders of een switchconstructie in een programmeertaal.

### <a name="policy-statement"></a><a name="ChoosePolicyStatement"></a>Beleidsverklaring

```xml
<choose>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <otherwise>
        <!— one or more policy statements to be applied if none of the above conditions are true  -->
</otherwise>
</choose>
```

Het controlestroombeleid moet ten `<when/>` minste één element bevatten. Het `<otherwise/>` element is optioneel. Voorwaarden `<when/>` in elementen worden geëvalueerd in volgorde van hun verschijning binnen het beleid. Beleidsverklaring(en) die is `<when/>` bijgesloten in `true` het eerste element met voorwaardekenmerk is gelijk. Beleid dat `<otherwise/>` in het element is ingesloten, `<when/>` indien aanwezig, `false`wordt toegepast als alle elementvoorwaardekenmerken .

### <a name="examples"></a>Voorbeelden

#### <a name="example"></a><a name="ChooseExample"></a>Voorbeeld

In het volgende voorbeeld wordt een [vast variabel](api-management-advanced-policies.md#set-variable) beleid en twee controlestroombeleid's getoond.

Het ingestelde variabelebeleid bevindt zich in `isMobile` de inkomende sectie en maakt een Booleaanse `iPhone` [contextvariabele](api-management-policy-expressions.md#ContextVariables) die is ingesteld op true als de `User-Agent` aangezochtkop de tekst `iPad` bevat of .

Het eerste besturingselementstroombeleid bevindt zich ook in de inkomende sectie en past voorwaardelijk een `isMobile` van de twee parameterbeleidsregels voor [querytekenreeksen in,](api-management-transformation-policies.md#SetQueryStringParameter) afhankelijk van de waarde van de contextvariabele.

Het tweede besturingselementstroombeleid bevindt zich in de afdeling uitgaande en `isMobile` past `true`het XML converteren voorwaardelijk toe [op JSON-beleid](api-management-transformation-policies.md#ConvertXMLtoJSON) wanneer is ingesteld op .

```xml
<policies>
    <inbound>
        <set-variable name="isMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <set-query-parameter name="mobile" exists-action="override">
                    <value>true</value>
                </set-query-parameter>
            </when>
            <otherwise>
                <set-query-parameter name="mobile" exists-action="override">
                    <value>false</value>
                </set-query-parameter>
            </otherwise>
        </choose>
    </inbound>
    <outbound>
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <xml-to-json kind="direct" apply="always" consider-accept-header="false"/>
            </when>
        </choose>
    </outbound>
</policies>
```

#### <a name="example"></a>Voorbeeld

In dit voorbeeld ziet u hoe u inhoudsfiltering uitvoert door gegevenselementen `Starter` uit het antwoord te verwijderen dat van de backendservice wordt ontvangen wanneer u het product gebruikt. Zie [Cloud Cover Episode 177: Meer API-beheerfuncties met Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) en fast-forward naar 34:30 voor een demonstratie van het configureren en gebruiken van dit beleid. Begin om 31:50 uur om een overzicht te zien van [de Dark Sky Forecast API](https://developer.forecast.io/) die voor deze demo wordt gebruikt.

```xml
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->
<choose>
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">
    <set-body>@{
        var response = context.Response.Body.As<JObject>();
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {
          response.Property (key).Remove ();
        }
        return response.ToString();
      }
    </set-body>
  </when>
</choose>
```

### <a name="elements"></a>Elementen

| Element   | Beschrijving                                                                                                                                                                                                                                                               | Vereist |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Kies    | Hoofdelement.                                                                                                                                                                                                                                                             | Ja      |
| Wanneer      | De voorwaarde voor `if` gebruik `ifelse` voor `choose` de of delen van het beleid. Als `choose` het beleid `when` meerdere secties heeft, worden ze achtereenvolgens geëvalueerd. Zodra `condition` het van een wanneer `true`element `when` evalueert aan, worden geen verdere voorwaarden geëvalueerd. | Ja      |
| Anders | Bevat het beleidsfragment dat moet `when` worden gebruikt `true`als geen van de voorwaarden wordt geëvalueerd op .                                                                                                                                                                               | Nee       |

### <a name="attributes"></a>Kenmerken

| Kenmerk                                              | Beschrijving                                                                                               | Vereist |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| voorwaarde="Booleaanse expressie &#124; Booleaanse constante" | De Booleaanse expressie of constant `when` om te evalueren wanneer de beleidsinstructie wordt geëvalueerd. | Ja      |

### <a name="usage"></a><a name="ChooseUsage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidssecties:** binnenkomend, uitgaand, backend, on-error

-   **Beleidsscopes:** alle scopes

## <a name="forward-request"></a><a name="ForwardRequest"></a>Verzoek doorsturen

Het `forward-request` beleid stuurt de binnenkomende aanvraag door naar de backendservice die is opgegeven in de [context](api-management-policy-expressions.md#ContextVariables)van de aanvraag . De URL van de backendservice is opgegeven in de [API-instellingen](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) en kan worden gewijzigd met behulp van het [beleid voor de backendservice.](api-management-transformation-policies.md)

> [!NOTE]
> Als u dit beleid verwijdert, wordt het verzoek niet doorgestuurd naar de backendservice en worden het beleid in de uitgaande sectie onmiddellijk geëvalueerd na de succesvolle voltooiing van het beleid in de inkomende sectie.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<forward-request timeout="time in seconds" follow-redirects="false | true" buffer-request-body="false | true" fail-on-error-status-code="false | true"/>
```

### <a name="examples"></a>Voorbeelden

#### <a name="example"></a>Voorbeeld

Met het volgende beleid op API-niveau worden alle API-aanvragen doorgestuurd naar de backendservice met een time-outinterval van 60 seconden.

```xml
<!-- api level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="60"/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Voorbeeld

Met dit beleid `base` op bewerkingsniveau wordt het element gebruikt om het backendbeleid over te nemen van het bovenliggende API-niveaubereik.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <base/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Voorbeeld

Met dit beleid op bewerkingsniveau worden alle aanvragen expliciet doorgestuurd naar de backendservice met een time-out van 120 en wordt het backend-beleid op bovenliggende API-niveau niet overgenomen. Als de backendservice reageert met een foutstatuscode van 400 tot 599, wordt de sectie [on-error](api-management-error-handling-policies.md) geactiveerd.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="120" fail-on-error-status-code="true" />
        <!-- effective policy. note the absence of <base/> -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Voorbeeld

Dit beleid op operationeel niveau stuurt geen aanvragen door naar de backendservice.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <!-- no forwarding to backend -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

### <a name="elements"></a>Elementen

| Element         | Beschrijving   | Vereist |
| --------------- | ------------- | -------- |
| termijnaanvraag | Hoofdelement. | Ja      |

### <a name="attributes"></a>Kenmerken

| Kenmerk                                     | Beschrijving                                                                                                                                                                                                                                                                                                    | Vereist | Standaard |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| time-out="geheel getal"                             | De hoeveelheid tijd in seconden om te wachten tot de HTTP-antwoordkoppen worden geretourneerd door de backendservice voordat een time-outfout wordt verhoogd. De minimumwaarde is 0 seconden. Waarden van meer dan 240 seconden worden mogelijk niet gehonoreerd omdat de onderliggende netwerkinfrastructuur na deze periode inactieve verbindingen kan laten vallen. | Nee       | Geen    |
| follow-redirects="false &#124; true"          | Hiermee geeft u op of omleidingen van de backendservice worden gevolgd door de gateway of worden teruggestuurd naar de beller.                                                                                                                                                                                                    | Nee       | false   |
| buffer-request-body="false &#124; true"       | Wanneer ingesteld op "true" aanvraag wordt gebufferd en zal worden hergebruikt bij [het opnieuw proberen](api-management-advanced-policies.md#Retry).                                                                                                                                                                                               | Nee       | false   |
| fail-on-error-status-code="false &#124; true" | Wanneer ingesteld op true triggers [on-error](api-management-error-handling-policies.md) sectie voor responscodes in het bereik van 400 tot 599 inclusief.                                                                                                                                                                      | Nee       | false   |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidssecties:** backend
-   **Beleidsscopes:** alle scopes

## <a name="limit-concurrency"></a><a name="LimitConcurrency"></a>Gelijktijdigheid beperken

Het `limit-concurrency` beleid voorkomt dat gesloten beleid op elk gewenst moment wordt uitgevoerd met meer dan het opgegeven aantal aanvragen. Na het overschrijden van dat aantal, zullen nieuwe aanvragen onmiddellijk mislukken met de statuscode van 429 te veel aanvragen.

### <a name="policy-statement"></a><a name="LimitConcurrencyStatement"></a>Beleidsverklaring

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Voorbeelden

#### <a name="example"></a>Voorbeeld

In het volgende voorbeeld wordt uitgelegd hoe u het aantal aanvragen dat wordt doorgestuurd naar een backend beperken op basis van de waarde van een contextvariabele.

```xml
<policies>
  <inbound>…</inbound>
  <backend>
    <limit-concurrency key="@((string)context.Variables["connectionId"])" max-count="3">
      <forward-request timeout="120"/>
    <limit-concurrency/>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>Elementen

| Element           | Beschrijving   | Vereist |
| ----------------- | ------------- | -------- |
| limietgelijktijdigheid | Hoofdelement. | Ja      |

### <a name="attributes"></a>Kenmerken

| Kenmerk | Beschrijving                                                                                        | Vereist | Standaard |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| sleutel       | Een touwtje. Expressie toegestaan. Hiermee geeft u het gelijktijdigheidsbereik op. Kan worden gedeeld door meerdere beleidsregels. | Ja      | N.v.t.     |
| max-count | Een geheel getal. Hiermee geeft u een maximum aantal aanvragen op dat het beleid mag invoeren.           | Ja      | N.v.t.     |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidssecties:** binnenkomend, uitgaand, backend, on-error

-   **Beleidsscopes:** alle scopes

## <a name="log-to-event-hub"></a><a name="log-to-eventhub"></a>Aanmelden bij gebeurtenishub

Het `log-to-eventhub` beleid verzendt berichten in de opgegeven indeling naar een gebeurtenishub die is gedefinieerd door een loggerentiteit. Zoals de naam al aangeeft, wordt het beleid gebruikt voor het opslaan van geselecteerde informatie over de aanvraag- of antwoordcontext voor online of offline analyse.

> [!NOTE]
> Zie [Api-beheergebeurtenissen met Azure Event Hubs](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/)voor een stapsgewijze handleiding voor het configureren van een gebeurtenishub en logboekgebeurtenissen.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Voorbeeld

Elke tekenreeks kan worden gebruikt als de waarde die moet worden geregistreerd in gebeurtenishubs. In dit voorbeeld worden de datum en tijd, de naam van de implementatieservice, de aanvraag-id, `contoso-logger` het IP-adres en de bedrijfsnaam voor alle binnenkomende oproepen geregistreerd bij de gebeurtenishub Logger die is geregistreerd met de ID

```xml
<policies>
  <inbound>
    <log-to-eventhub logger-id ='contoso-logger'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name) )
    </log-to-eventhub>
  </inbound>
  <outbound>
  </outbound>
</policies>
```

### <a name="elements"></a>Elementen

| Element         | Beschrijving                                                                     | Vereist |
| --------------- | ------------------------------------------------------------------------------- | -------- |
| log-to-eventhub | Hoofdelement. De waarde van dit element is de tekenreeks die u moet aanmelden bij uw gebeurtenishub. | Ja      |

### <a name="attributes"></a>Kenmerken

| Kenmerk     | Beschrijving                                                               | Vereist                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| logger-id     | De ID van de Logger geregistreerd bij uw API Management service.         | Ja                                                                  |
| partitie-id  | Hiermee geeft u de index op van de partitie waar berichten worden verzonden.             | Optioneel. Dit kenmerk mag niet `partition-key` worden gebruikt als het wordt gebruikt. |
| partitiesleutel | Hiermee geeft u de waarde op die wordt gebruikt voor partitietoewijzing wanneer berichten worden verzonden. | Optioneel. Dit kenmerk mag niet `partition-id` worden gebruikt als het wordt gebruikt.  |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidssecties:** binnenkomend, uitgaand, backend, on-error

-   **Beleidsscopes:** alle scopes

## <a name="mock-response"></a><a name="mock-response"></a>Mock-reactie

De `mock-response`, zoals de naam al aangeeft, wordt gebruikt om API's en bewerkingen te bespotten. Het wordt afgebroken normale pijplijn uitvoering en retourneert een bespot antwoord op de beller. Het beleid probeert altijd antwoorden van de hoogste getrouwheid terug te sturen. Het geeft de voorkeur aan voorbeelden van antwoord-inhoud, indien beschikbaar. Het genereert voorbeeldreacties van schema's, wanneer schema's worden verstrekt en voorbeelden niet. Als er geen voorbeelden of schema's worden gevonden, worden antwoorden zonder inhoud geretourneerd.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<mock-response status-code="code" content-type="media type"/>

```

### <a name="examples"></a>Voorbeelden

```xml
<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code. First found content type is used. If no example or schema is found, the content is empty. -->
<mock-response/>

<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code and media type. If no example or schema found, the content is empty. -->
<mock-response status-code='200' content-type='application/json'/>
```

### <a name="elements"></a>Elementen

| Element       | Beschrijving   | Vereist |
| ------------- | ------------- | -------- |
| mock-response | Hoofdelement. | Ja      |

### <a name="attributes"></a>Kenmerken

| Kenmerk    | Beschrijving                                                                                           | Vereist | Standaard |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| statuscode  | Hiermee geeft u de status van de responsopscode op en wordt deze gebruikt om een overeenkomstig voorbeeld of schema te selecteren.                 | Nee       | 200     |
| inhoudstype | Hiermee `Content-Type` geeft u de waarde van de antwoordkopwaarde op en wordt deze gebruikt om een overeenkomstig voorbeeld of schema te selecteren. | Nee       | Geen    |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidssecties:** binnenkomend, uitgaand, op-fout

-   **Beleidsscopes:** alle scopes

## <a name="retry"></a><a name="Retry"></a>Opnieuw

Het `retry` beleid voert het onderliggende beleid één keer uit `condition` `false` en probeert `count` de uitvoering ervan opnieuw totdat de nieuwe poging wordt of opnieuw wordt geprobeerd.

### <a name="policy-statement"></a>Beleidsverklaring

```xml

<retry
    condition="boolean expression or literal"
    count="number of retry attempts"
    interval="retry interval in seconds"
    max-interval="maximum retry interval in seconds"
    delta="retry interval delta in seconds"
    first-fast-retry="boolean expression or literal">
        <!-- One or more child policies. No restrictions -->
</retry>

```

### <a name="example"></a>Voorbeeld

In het volgende voorbeeld wordt het doorschakelen van aanvragen tot tien keer opnieuw geprobeerd met behulp van een exponentieel algoritme voor het opnieuw proberen. Aangezien `first-fast-retry` is ingesteld op false, alle pogingen opnieuw proberen zijn onderworpen aan de exponentiële retry algoritme.

```xml

<retry
    condition="@(context.Response.StatusCode == 500)"
    count="10"
    interval="10"
    max-interval="100"
    delta="10"
    first-fast-retry="false">
        <forward-request buffer-request-body="true" />
</retry>

```

### <a name="elements"></a>Elementen

| Element | Beschrijving                                                         | Vereist |
| ------- | ------------------------------------------------------------------- | -------- |
| retry   | Hoofdelement. Kan elk ander beleid als onderliggende elementen bevatten. | Ja      |

### <a name="attributes"></a>Kenmerken

| Kenmerk        | Beschrijving                                                                                                                                           | Vereist | Standaard |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| Voorwaarde        | Een booleaanse letterlijke of [expressie](api-management-policy-expressions.md) die`false`aangeeft of`true`nieuwe pogingen moeten worden gestopt ( ) of voortgezet ( ).      | Ja      | N.v.t.     |
| count            | Een positief getal dat het maximum aantal pogingen om te proberen opgeeft.                                                                                | Ja      | N.v.t.     |
| interval         | Een positief getal in seconden dat het wachtinterval tussen de pogingen opnieuw probeert.                                                                 | Ja      | N.v.t.     |
| max-interval     | Een positief getal in seconden dat het maximale wachtinterval tussen de pogingen opnieuw probeert. Het wordt gebruikt om een exponentieel retry algoritme te implementeren. | Nee       | N.v.t.     |
| Delta            | Een positief getal in seconden dat de toename van het wachtinterval opgeeft. Het wordt gebruikt om de lineaire en exponentiële retry algoritmen te implementeren.             | Nee       | N.v.t.     |
| first-fast-retry | Als ingesteld `true` op , wordt de eerste poging opnieuw geprobeerd onmiddellijk uitgevoerd.                                                                                  | Nee       | `false` |

> [!NOTE]
> Wanneer alleen `interval` het opgegeven interval is opgegeven, worden **er vaste** intervalpogingen uitgevoerd.
> Wanneer alleen `interval` `delta` het en het opgegeven, wordt een algoritme voor **lineaire** intervalopnieuw proberen gebruikt, waarbij de wachttijd tussen nieuwe pogingen wordt berekend volgens de volgende formule - `interval + (count - 1)*delta`.
> Wanneer `interval`het `max-interval` `delta` **exponentiële** intervalretry-algoritme wordt toegepast, waarbij de wachttijd tussen de nieuwe pogingen `interval` exponentieel `max-interval` groeit van de `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`waarde van de waarde volgens de volgende formule - .

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) . Houd er rekening mee dat beperkingen voor het gebruik van onderliggendbeleid worden overgenomen door dit beleid.

-   **Beleidssecties:** binnenkomend, uitgaand, backend, on-error

-   **Beleidsscopes:** alle scopes

## <a name="return-response"></a><a name="ReturnResponse"></a>Retourreactie

Het `return-response` beleid wordt afgebroken pijplijnuitvoering en retourneert een standaard- of aangepast antwoord op de beller. Standaardrespons `200 OK` is zonder lichaam. Aangepaste respons kan worden opgegeven via een contextvariabele of beleidsoverzichten. Wanneer beide worden verstrekt, wordt het antwoord binnen de contextvariabele gewijzigd door de beleidsoverzichten voordat het wordt teruggestuurd naar de beller.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<return-response response-variable-name="existing context variable">
  <set-header/>
  <set-body/>
  <set-status/>
</return-response>

```

### <a name="example"></a>Voorbeeld

```xml
<return-response>
   <set-status code="401" reason="Unauthorized"/>
   <set-header name="WWW-Authenticate" exists-action="override">
      <value>Bearer error="invalid_token"</value>
   </set-header>
</return-response>

```

### <a name="elements"></a>Elementen

| Element         | Beschrijving                                                                               | Vereist |
| --------------- | ----------------------------------------------------------------------------------------- | -------- |
| return-response | Hoofdelement.                                                                             | Ja      |
| set-header      | Een beleidsinstructie [met set-header.](api-management-transformation-policies.md#SetHTTPheader) | Nee       |
| set-body        | Een [set-body](api-management-transformation-policies.md#SetBody) beleidsverklaring.         | Nee       |
| setstatus      | Een [beleidsverklaring met een vaste status.](api-management-advanced-policies.md#SetStatus)           | Nee       |

### <a name="attributes"></a>Kenmerken

| Kenmerk              | Beschrijving                                                                                                                                                                          | Vereist  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| naam van antwoordvariabele | De naam van de contextvariabele waarnaar wordt verwezen uit bijvoorbeeld een `Response` [upstream-verzendaanvraagbeleid](api-management-advanced-policies.md#SendRequest) en met een object | Optioneel. |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidssecties:** binnenkomend, uitgaand, backend, on-error

-   **Beleidsscopes:** alle scopes

## <a name="send-one-way-request"></a><a name="SendOneWayRequest"></a>Eenenkele manier verzenden

Het `send-one-way-request` beleid stuurt het opgegeven verzoek naar de opgegeven URL zonder te wachten op een antwoord.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<send-one-way-request mode="new | copy">
  <url>...</url>
  <method>...</method>
  <header name="" exists-action="override | skip | append | delete">...</header>
  <body>...</body>
  <authentication-certificate thumbprint="thumbprint" />
</send-one-way-request>

```

### <a name="example"></a>Voorbeeld

In dit voorbeeldbeleid wordt `send-one-way-request` een voorbeeld weergegeven van het gebruik van het beleid om een bericht naar een Slack-chatruimte te verzenden als de HTTP-antwoordcode groter is dan of gelijk is aan 500. Zie Externe services gebruiken [van de Azure API Management-service](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)voor meer informatie over dit voorbeeld.

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>Elementen

| Element                    | Beschrijving                                                                                                 | Vereist                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| verzenden-one-way-request       | Hoofdelement.                                                                                               | Ja                             |
| url                        | De URL van het verzoek.                                                                                     | Geen als mode=kopiëren; anders ja. |
| method                     | De HTTP-methode voor de aanvraag.                                                                            | Geen als mode=kopiëren; anders ja. |
| koptekst                     | Koptekst aanvragen. Gebruik meerdere kopelementen voor meerdere aanvraagkoppen.                                  | Nee                              |
| body                       | Het verzoekorgaan.                                                                                           | Nee                              |
| verificatie-certificaat | [Certificaat dat moet worden gebruikt voor clientverificatie](api-management-authentication-policies.md#ClientCertificate) | Nee                              |

### <a name="attributes"></a>Kenmerken

| Kenmerk     | Beschrijving                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Vereist | Standaard  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="tekenreeks" | Hiermee bepaalt u of dit een nieuw verzoek is of een kopie van de huidige aanvraag. In de uitgaande modus initialiseert mode=copy de aanvraaginstantie niet.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nee       | Nieuw      |
| name          | Hiermee geeft u de naam van de in te stellen header op.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Ja      | N.v.t.      |
| bestaande-actie | Hiermee geeft u op welke actie u moet uitvoeren wanneer de koptekst al is opgegeven. Dit kenmerk moet een van de volgende waarden hebben.<br /><br /> - overschrijven - vervangt de waarde van de bestaande header.<br />- overslaan - vervangt niet de bestaande header waarde.<br />- toevoegen - de waarde toevoegen aan de bestaande kopwaarde.<br />- verwijderen - verwijdert de koptekst uit de aanvraag.<br /><br /> Wanneer u `override` meerdere items met dezelfde naam intrekt, resulteert dit erin dat de koptekst wordt ingesteld op basis van alle vermeldingen (die meerdere keren worden weergegeven); alleen vermelde waarden worden in het resultaat ingesteld. | Nee       | overschrijven |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidssecties:** binnenkomend, uitgaand, backend, on-error

-   **Beleidsscopes:** alle scopes

## <a name="send-request"></a><a name="SendRequest"></a>Verzoek verzenden

Het `send-request` beleid stuurt het opgegeven verzoek naar de opgegeven URL, wachtend niet langer dan de ingestelde time-outwaarde.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<send-request mode="new|copy" response-variable-name="" timeout="60 sec" ignore-error
="false|true">
  <set-url>...</set-url>
  <set-method>...</set-method>
  <set-header name="" exists-action="override|skip|append|delete">...</set-header>
  <set-body>...</set-body>
  <authentication-certificate thumbprint="thumbprint" />
</send-request>

```

### <a name="example"></a>Voorbeeld

In dit voorbeeld wordt een manier weergegeven om een referentietoken met een autorisatieserver te verifiëren. Zie Externe services gebruiken [van de Azure API Management-service](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)voor meer informatie over dit voorbeeld.

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
        <!-- Check active property in response -->
        <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
            <!-- Return 401 Unauthorized with http-problem payload -->
            <return-response>
                <set-status code="401" reason="Unauthorized" />
                <set-header name="WWW-Authenticate" exists-action="override">
                    <value>Bearer error="invalid_token"</value>
                </set-header>
            </return-response>
        </when>
    </choose>
  <base />
</inbound>

```

### <a name="elements"></a>Elementen

| Element                    | Beschrijving                                                                                                 | Vereist                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| verzendverzoek               | Hoofdelement.                                                                                               | Ja                             |
| url                        | De URL van het verzoek.                                                                                     | Geen als mode=kopiëren; anders ja. |
| method                     | De HTTP-methode voor de aanvraag.                                                                            | Geen als mode=kopiëren; anders ja. |
| koptekst                     | Koptekst aanvragen. Gebruik meerdere kopelementen voor meerdere aanvraagkoppen.                                  | Nee                              |
| body                       | Het verzoekorgaan.                                                                                           | Nee                              |
| verificatie-certificaat | [Certificaat dat moet worden gebruikt voor clientverificatie](api-management-authentication-policies.md#ClientCertificate) | Nee                              |

### <a name="attributes"></a>Kenmerken

| Kenmerk                       | Beschrijving                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Vereist | Standaard  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="tekenreeks"                   | Hiermee bepaalt u of dit een nieuw verzoek is of een kopie van de huidige aanvraag. In de uitgaande modus initialiseert mode=copy de aanvraaginstantie niet.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nee       | Nieuw      |
| respons-variabele-naam="tekenreeks" | De naam van de contextvariabele die een antwoordobject ontvangt. Als de variabele niet bestaat, wordt deze gemaakt bij een succesvolle [`context.Variable`](api-management-policy-expressions.md#ContextVariables) uitvoering van het beleid en wordt deze toegankelijk via verzameling.                                                                                                                                                                                                                                                                                                                          | Ja      | N.v.t.      |
| time-out="geheel getal"               | Het time-outinterval in seconden voordat de aanroep naar de URL mislukt.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Nee       | 60       |
| negeren-fout                    | Als true en de aanvraag resulteert in een fout:<br /><br /> - Als de naam van de responsvariabele is opgegeven, bevat deze een null-waarde.<br />- Als de naam van de responsvariabele niet is opgegeven, context. De aanvraag wordt niet bijgewerkt.                                                                                                                                                                                                                                                                                                                                                                                   | Nee       | false    |
| name                            | Hiermee geeft u de naam van de in te stellen header op.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Ja      | N.v.t.      |
| bestaande-actie                   | Hiermee geeft u op welke actie u moet uitvoeren wanneer de koptekst al is opgegeven. Dit kenmerk moet een van de volgende waarden hebben.<br /><br /> - overschrijven - vervangt de waarde van de bestaande header.<br />- overslaan - vervangt niet de bestaande header waarde.<br />- toevoegen - de waarde toevoegen aan de bestaande kopwaarde.<br />- verwijderen - verwijdert de koptekst uit de aanvraag.<br /><br /> Wanneer u `override` meerdere items met dezelfde naam intrekt, resulteert dit erin dat de koptekst wordt ingesteld op basis van alle vermeldingen (die meerdere keren worden weergegeven); alleen vermelde waarden worden in het resultaat ingesteld. | Nee       | overschrijven |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidssecties:** binnenkomend, uitgaand, backend, on-error

-   **Beleidsscopes:** alle scopes

## <a name="set-http-proxy"></a><a name="SetHttpProxy"></a>HTTP-proxy instellen

Met `proxy` het beleid u aanvragen die zijn doorgestuurd naar backends doorsturen via een HTTP-proxy routeren. Alleen HTTP (niet HTTPS) wordt ondersteund tussen de gateway en de proxy. Alleen basis- en NTLM-verificatie.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Voorbeeld

Let op het gebruik van [eigenschappen](api-management-howto-properties.md) als waarden van de gebruikersnaam en het wachtwoord om te voorkomen dat gevoelige informatie in het beleidsdocument wordt opgeslagen.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Elementen

| Element | Beschrijving  | Vereist |
| ------- | ------------ | -------- |
| proxy   | Hoofdelement | Ja      |

### <a name="attributes"></a>Kenmerken

| Kenmerk         | Beschrijving                                            | Vereist | Standaard |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| url="tekenreeks"      | Proxy-URL in http://host:portde vorm van .             | Ja      | N.v.t.     |
| gebruikersnaam="tekenreeks" | Gebruikersnaam die moet worden gebruikt voor verificatie met de proxy. | Nee       | N.v.t.     |
| password="string" | Wachtwoord dat moet worden gebruikt voor verificatie met de proxy. | Nee       | N.v.t.     |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidsonderdelen:** binnenkomend

-   **Beleidsscopes:** alle scopes

## <a name="set-request-method"></a><a name="SetRequestMethod"></a>Aanvraagmethode instellen

Met `set-method` het beleid u de HTTP-aanvraagmethode voor een aanvraag wijzigen.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Voorbeeld

In dit voorbeeldbeleid `set-method` dat het beleid gebruikt, wordt een voorbeeld weergegeven van het verzenden van een bericht naar een Slack-chatruimte als de HTTP-antwoordcode groter is dan of gelijk is aan 500. Zie Externe services gebruiken [van de Azure API Management-service](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)voor meer informatie over dit voorbeeld.

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>Elementen

| Element    | Beschrijving                                                       | Vereist |
| ---------- | ----------------------------------------------------------------- | -------- |
| setmethode | Hoofdelement. De waarde van het element geeft de HTTP-methode aan. | Ja      |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidsonderdelen:** binnenkomend, op-fout

-   **Beleidsscopes:** alle scopes

## <a name="set-status-code"></a><a name="SetStatus"></a>Statuscode instellen

In `set-status` het beleid wordt de HTTP-statuscode ingesteld op de opgegeven waarde.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Voorbeeld

In dit voorbeeld ziet u hoe u een 401-antwoord retourneert als het autorisatietoken ongeldig is. Zie [Externe services gebruiken vanuit de Azure API Management-service](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/) voor meer informatie

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>

```

### <a name="elements"></a>Elementen

| Element    | Beschrijving   | Vereist |
| ---------- | ------------- | -------- |
| setstatus | Hoofdelement. | Ja      |

### <a name="attributes"></a>Kenmerken

| Kenmerk       | Beschrijving                                                | Vereist | Standaard |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| code="geheel"  | De HTTP-statuscode om terug te keren.                            | Ja      | N.v.t.     |
| reden="tekenreeks" | Een beschrijving van de reden voor het retourneren van de statuscode. | Ja      | N.v.t.     |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidssecties:** uitgaand, backend, on-error
-   **Beleidsscopes:** alle scopes

## <a name="set-variable"></a><a name="set-variable"></a>Variabele instellen

Het `set-variable` beleid verklaart een [contextvariabele](api-management-policy-expressions.md#ContextVariables) en wijst deze een waarde toe die is opgegeven via een [expressie](api-management-policy-expressions.md) of een tekenreeks letterlijk. als de expressie een letterlijke tekst bevat, wordt deze omgezet `System.String`in een tekenreeks en wordt het type waarde .

### <a name="policy-statement"></a><a name="set-variablePolicyStatement"></a>Beleidsverklaring

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="example"></a><a name="set-variableExample"></a>Voorbeeld

In het volgende voorbeeld wordt een ingesteld variabel beleid in de inkomende sectie getoond. Met dit ingestelde `isMobile` variabelebeleid wordt een Booleaanse `User-Agent` [contextvariabele](api-management-policy-expressions.md#ContextVariables) gemaakt die is ingesteld op true als de aanknopingskop de tekst `iPad` bevat of `iPhone`.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Elementen

| Element      | Beschrijving   | Vereist |
| ------------ | ------------- | -------- |
| setvariabele | Hoofdelement. | Ja      |

### <a name="attributes"></a>Kenmerken

| Kenmerk | Beschrijving                                                              | Vereist |
| --------- | ------------------------------------------------------------------------ | -------- |
| name      | De naam van de variabele.                                                | Ja      |
| waarde     | De waarde van de variabele. Dit kan een uitdrukking of een letterlijke waarde zijn. | Ja      |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidssecties:** binnenkomend, uitgaand, backend, on-error
-   **Beleidsscopes:** alle scopes

### <a name="allowed-types"></a><a name="set-variableAllowedTypes"></a>Toegestane typen

Expressies die `set-variable` in het beleid worden gebruikt, moeten een van de volgende basistypen retourneren.

-   System.Boolean System.Boolean System.Boolean System.
-   System.SByte System.SByte
-   System.Byte System.Byte
-   System.UInt16
-   System.UInt32
-   System.UInt64
-   System.Int16
-   System.Int32
-   System.Int64
-   Systeem.Decimal
-   System.Single
-   System.Double
-   System.Guid System.Guid
-   System.String
-   System.Char System.Char
-   System.DateTime
-   System.TimeSpan
-   System.Byte?
-   System.UInt16?
-   System.UInt32?
-   System.UInt64?
-   System.Int16?
-   System.Int32?
-   System.Int64?
-   Systeem.Decimal?
-   System.Single?
-   System.Double?
-   System.Guid?
-   System.String?
-   System.Char?
-   System.DateTime?

## <a name="trace"></a><a name="Trace"></a>Trace

Het `trace` beleid voegt een aangepaste tracering toe aan de API Inspector-uitvoer, Application Insights-telemetrieën en/of diagnostische logboeken.

-   Het beleid voegt een aangepaste tracering toe aan de [API Inspector-uitvoer](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) wanneer tracering wordt geactiveerd, d.w.z. `Ocp-Apim-Trace` de aanvraagheader is aanwezig en ingesteld op true- en `Ocp-Apim-Subscription-Key` requestheader is aanwezig en bevat een geldige sleutel waarmee tracering mogelijk is.
-   Het beleid maakt een [Trace](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) telemetrie in Application Insights, wanneer [Application Insights-integratie](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights) is ingeschakeld en het `severity` niveau dat in het beleid is opgegeven, op of hoger is dan het `verbosity` niveau dat is opgegeven in de diagnostische instelling.
-   Het beleid voegt een eigenschap toe in de logboekvermelding wanneer [diagnostische logboeken](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs) zijn ingeschakeld en het in het beleid opgegeven ernstniveau op of hoger is dan het verbositeitsniveau dat is opgegeven in de diagnostische instelling.

### <a name="policy-statement"></a>Beleidsverklaring

```xml

<trace source="arbitrary string literal" severity="verbose|information|error">
    <message>String literal or expressions</message>
    <metadata name="string literal or expressions" value="string literal or expressions"/>
</trace>

```

### <a name="example"></a><a name="traceExample"></a>Voorbeeld

```xml
<trace source="PetStore API" severity="verbose">
    <message>@((string)context.Variables["clientConnectionID"])</message>
    <metadata name="Operation Name" value="New-Order"/>
</trace>
```

### <a name="elements"></a>Elementen

| Element  | Beschrijving                                                                                                                                          | Vereist |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| tracering    | Hoofdelement.                                                                                                                                        | Ja      |
| message  | Een tekenreeks of expressie die moet worden geregistreerd.                                                                                                                 | Ja      |
| metagegevens | Hiermee voegt u een aangepaste eigenschap toe aan de telemetrie van Application Insights [Trace.](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) | Nee       |

### <a name="attributes"></a>Kenmerken

| Kenmerk | Beschrijving                                                                                                               | Vereist | Standaard |
| --------- | ------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| source    | Tekenreeks letterlijk zinvol voor de trace viewer en het opgeven van de bron van het bericht.                                   | Ja      | N.v.t.     |
| ernst  | Hiermee geeft u het ernstniveau van het spoor op. Toegestane waarden `verbose`zijn `information` `error` , (van laag naar hoog). | Nee       | Verbose |
| name      | Naam van het pand.                                                                                                     | Ja      | N.v.t.     |
| waarde     | Waarde van het pand.                                                                                                    | Ja      | N.v.t.     |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

-   **Beleidssecties:** binnenkomend, uitgaand, backend, on-error

-   **Beleidsscopes:** alle scopes

## <a name="wait"></a><a name="Wait"></a>Wachten

Het `wait` beleid voert het beleid voor onmiddellijke onderliggende problemen parallel uit en wacht tot het beleid voor het directe kind of het beleid voor onmiddellijke kinderen is voltooid voordat het is voltooid. Het wachtbeleid kan hebben als het beleid voor onmiddellijk kind [Verzendaanvraag](api-management-advanced-policies.md#SendRequest), [Waarde ophalen uit cache](api-management-caching-policies.md#GetFromCacheByKey)en [Stroombeleid beheren.](api-management-advanced-policies.md#choose)

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Voorbeeld

In het volgende voorbeeld `choose` zijn er twee `wait` beleidsregels als direct kindbeleid van het beleid. Elk van `choose` deze beleidsregels wordt parallel uitgevoerd. Elk `choose` beleid probeert een in de cache opgeslagen waarde op te halen. Als er een cache-misser is, wordt een backendservice ingeschakeld om de waarde te bieden. In dit `wait` voorbeeld wordt het beleid pas voltooid nadat al `for` het beleid `all`voor onmiddellijke onderliggende kenmerken is voltooid, omdat het kenmerk is ingesteld op . In dit voorbeeld worden`execute-branch-one`de `value-one` `execute-branch-two`contextvariabelen `value-two`( , , en ) buiten het toepassingsgebied van dit voorbeeldbeleid gedeclareerd.

```xml
<wait for="all">
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-one="])">
      <cache-lookup-value key="key-one" variable-name="value-one" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-one="))">
          <send-request mode="new" response-variable-name="value-one">
            <set-url>https://backend-one</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-two="])">
      <cache-lookup-value key="key-two" variable-name="value-two" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-two="))">
          <send-request mode="new" response-variable-name="value-two">
            <set-url>https://backend-two</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
</wait>

```

### <a name="elements"></a>Elementen

| Element | Beschrijving                                                                                                   | Vereist |
| ------- | ------------------------------------------------------------------------------------------------------------- | -------- |
| Wacht    | Hoofdelement. Kan alleen `send-request`als onderliggende `choose` elementen en `cache-lookup-value`beleid bevatten. | Ja      |

### <a name="attributes"></a>Kenmerken

| Kenmerk | Beschrijving                                                                                                                                                                                                                                                                                                                                                                                                            | Vereist | Standaard |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| voor       | Hiermee bepaalt `wait` u of het beleid wacht tot alle directe onderliggende beleidsregels zijn voltooid of slechts één. Toegestane waarden zijn:<br /><br /> - `all`- wachten tot alle onmiddellijke kinderbeleid is voltooid<br />- elke - wachten op een onmiddellijke kinderbeleid te voltooien. Zodra het eerste directe onderliggende `wait` beleid is voltooid, wordt het beleid voltooid en uitgevoerd met andere beleid voor onmiddellijke onderliggende gegevens. | Nee       | all     |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidssecties:** binnenkomend, uitgaand, backend
-   **Beleidsscopes:** alle scopes

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over beleid:

-   [Beleid in API-beheer](api-management-howto-policies.md)
-   [Beleidsexpressies](api-management-policy-expressions.md)
-   [Beleidsreferentie](api-management-policy-reference.md) voor een volledige lijst met beleidsoverzichten en hun instellingen
-   [Beleidsvoorbeelden](policy-samples.md)
