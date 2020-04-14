---
title: Azure API Management-transformatiebeleid | Microsoft Documenten
description: Meer informatie over het transformatiebeleid dat beschikbaar is voor gebruik in Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: 27bb6abb7ae8eae46bc4dea3708270ecb4b731a6
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260901"
---
# <a name="api-management-transformation-policies"></a>Transformatiebeleid API Management
In dit onderwerp wordt een verwijzing naar het volgende API-beheerbeleid bevat. Zie [Beleid in API-beheer](https://go.microsoft.com/fwlink/?LinkID=398186)voor informatie over het toevoegen en configureren van beleidsregels.

##  <a name="transformation-policies"></a><a name="TransformationPolicies"></a>Transformatiebeleid

-   [Json converteren naar XML](api-management-transformation-policies.md#ConvertJSONtoXML) - Converteert aanvraag- of antwoordtekst van JSON naar XML.

-   [XML converteren naar JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - Converteert aanvraag- of antwoordtekst van XML naar JSON.

-   [String zoeken en vervangen in de hoofdtekst](api-management-transformation-policies.md#Findandreplacestringinbody) - Hiermee zoekt en vervangt u een aanvraag- of antwoordsubtekenreeks en vervangt u deze door een andere subtekenreeks.

-   [Url's in inhoud maskeren](api-management-transformation-policies.md#MaskURLSContent) - Koppelingen (maskers) opnieuw schrijven in de reactiebody, zodat ze via de gateway naar de gelijkwaardige koppeling verwijzen.

-   [Backendservice instellen](api-management-transformation-policies.md#SetBackendService) : wijzigt de backendservice voor een binnenkomende aanvraag.

-   [Hoofdtekst instellen](api-management-transformation-policies.md#SetBody) : hiermee stelt u de berichttekst in voor binnenkomende en uitgaande aanvragen.

-   [HTTP-header instellen](api-management-transformation-policies.md#SetHTTPheader) : hiermee wijst u een waarde toe aan een bestaande antwoord- en/of aanvraagheader of voegt u een nieuwe reactie- en/of aanvraagkop toe.

-   [Querytekenreeksparameter instellen](api-management-transformation-policies.md#SetQueryStringParameter) - Hiermee wordt de waarde van de querytekenreeksparameter van queryreeksen toegevoegd, vervangen of verwijderd.

-   [URL herschrijven](api-management-transformation-policies.md#RewriteURL) - Converteert een aanvraag-URL van het openbare formulier naar het formulier dat wordt verwacht door de webservice.

-   [XML transformeren met een XSLT](api-management-transformation-policies.md#XSLTransform) - Past een XSL-transformatie toe op XML in de aanvraag- of antwoordtekst.

##  <a name="convert-json-to-xml"></a><a name="ConvertJSONtoXML"></a>JSON converteren naar XML
 Het `json-to-xml` beleid converteert een aanvraag- of antwoordtekst van JSON naar XML.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<json-to-xml apply="always | content-type-json" consider-accept-header="true | false" parse-date="true | false"/>
```

### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <json-to-xml apply="always" consider-accept-header="false" parse-date="false"/>
    </outbound>
</policies>
```

### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|
|----------|-----------------|--------------|
|json-to-xml|Hoofdelement.|Ja|

### <a name="attributes"></a>Kenmerken

|Name|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|toepassen|Het kenmerk moet zijn ingesteld op een van de volgende waarden.<br /><br /> - altijd - altijd conversie toepassen.<br />- content-type-json - converteer alleen als de header Content-Type van reactie de aanwezigheid van JSON aangeeft.|Ja|N.v.t.|
|consider-accept-header|Het kenmerk moet zijn ingesteld op een van de volgende waarden.<br /><br /> - true - conversie toepassen als XML wordt aangevraagd in de koptekst Accepteren.<br />- false -always apply conversion.|Nee|waar|
|parse-datum|Wanneer waarden `false` zijn ingesteld op datum worden ze gewoon gekopieerd tijdens de transformatie|Nee|waar|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidssecties:** binnenkomend, uitgaand, op-fout

-   **Beleidsscopes:** alle scopes

##  <a name="convert-xml-to-json"></a><a name="ConvertXMLtoJSON"></a>XML converteren naar JSON
 Het `xml-to-json` beleid converteert een aanvraag- of antwoordtekst van XML naar JSON. Dit beleid kan worden gebruikt om API's te moderniseren op basis van XML-backend webservices.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>
```

### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|
|----------|-----------------|--------------|
|xml-to-json|Hoofdelement.|Ja|

### <a name="attributes"></a>Kenmerken

|Name|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|Soort|Het kenmerk moet zijn ingesteld op een van de volgende waarden.<br /><br /> - javascript-vriendelijk - de geconverteerde JSON heeft een formulier vriendelijk voor JavaScript ontwikkelaars.<br />- direct - de geconverteerde JSON weerspiegelt de structuur van het oorspronkelijke XML-document.|Ja|N.v.t.|
|toepassen|Het kenmerk moet zijn ingesteld op een van de volgende waarden.<br /><br /> - altijd - altijd converteren.<br />- inhoud-type-xml - converteer alleen als de header Content-Type van respons de aanwezigheid van XML aangeeft.|Ja|N.v.t.|
|consider-accept-header|Het kenmerk moet zijn ingesteld op een van de volgende waarden.<br /><br /> - true - conversie toepassen als JSON wordt aangevraagd in de koptekst Accepteren.<br />- false -always apply conversion.|Nee|waar|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidssecties:** binnenkomend, uitgaand, op-fout

-   **Beleidsscopes:** alle scopes

##  <a name="find-and-replace-string-in-body"></a><a name="Findandreplacestringinbody"></a>Tekenreeks in de behuizing zoeken en vervangen
 Het `find-and-replace` beleid vindt een aanvraag- of antwoordsubtekenreeks en vervangt deze door een andere subtekenreeks.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>Voorbeeld

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|
|----------|-----------------|--------------|
|zoeken en vervangen|Hoofdelement.|Ja|

### <a name="attributes"></a>Kenmerken

|Name|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|from|De tekenreeks waarnaar moet worden gezocht.|Ja|N.v.t.|
|tot|De vervangende tekenreeks. Geef een vervangende tekenreeks met nullengte op om de zoektekenreeks te verwijderen.|Ja|N.v.t.|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidssecties:** binnenkomend, uitgaand, backend, on-error

-   **Beleidsscopes:** alle scopes

##  <a name="mask-urls-in-content"></a><a name="MaskURLSContent"></a>URL's maskeren in inhoud
 Het `redirect-content-urls` beleid herschrijft (maskers) links in de reactie body, zodat ze wijzen op de gelijkwaardige link via de gateway. Gebruik in de uitgaande sectie om de koppelingen naar de antwoordtekst opnieuw te schrijven om ze naar de gateway te laten wijzen. Gebruik in de inkomende sectie voor een tegenovergesteld effect.

> [!NOTE]
>  Dit beleid wijzigt geen koptekstwaarden zoals `Location` kopteksten. Als u kopwaarden wilt wijzigen, gebruikt u het beleid [voor set-headers.](api-management-transformation-policies.md#SetHTTPheader)

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<redirect-content-urls />
```

### <a name="example"></a>Voorbeeld

```xml
<redirect-content-urls />
```

### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|
|----------|-----------------|--------------|
|redirect-content-urls|Hoofdelement.|Ja|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidsonderdelen:** binnenkomend, uitgaand

-   **Beleidsscopes:** alle scopes

##  <a name="set-backend-service"></a><a name="SetBackendService"></a>Backend-service instellen
 Gebruik `set-backend-service` het beleid om een binnenkomende aanvraag om te leiden naar een andere back-end dan die welke is opgegeven in de API-instellingen voor die bewerking. Met dit beleid wordt de URL van de backendservicebasis van de binnenkomende aanvraag gewijzigd in de URL die in het beleid is opgegeven.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<set-backend-service base-url="base URL of the backend service" />
```

of

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> Backend-entiteiten kunnen worden beheerd via [beheer-API](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/backend) en [PowerShell.](https://www.powershellgallery.com/packages?q=apimanagement)

### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <choose>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2013-05")">
                <set-backend-service base-url="http://contoso.com/api/8.2/" />
            </when>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2014-03")">
                <set-backend-service base-url="http://contoso.com/api/9.1/" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
In dit voorbeeld worden aanvragen voor de set backendservicebeleids routes op basis van de versiewaarde die in de querytekenreeks wordt doorgegeven aan een andere backendservice dan die welke in de API is opgegeven.

In eerste instantie wordt de URL van de backendservicebasis afgeleid van de API-instellingen. De URL `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` van `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` `http://contoso.com/api/10.4/` de aanvraag wordt dus waar de URL van de backendservice is opgegeven in de API-instellingen.

Wanneer de [<beleidsinstructie kiezen\> ](api-management-advanced-policies.md#choose) wordt toegepast, kan de URL van de backendservicebasis opnieuw worden gewijzigd in `http://contoso.com/api/8.2` of `http://contoso.com/api/9.1`, afhankelijk van de waarde van de parameter van de queryparameter versieaanvraag. Als de waarde bijvoorbeeld `"2013-15"` de uiteindelijke `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`aanvraag-URL wordt .

Als verdere transformatie van de aanvraag gewenst is, kan ander [transformatiebeleid](api-management-transformation-policies.md#TransformationPolicies) worden gebruikt. Als u bijvoorbeeld de parameter versiequery wilt verwijderen nu de aanvraag wordt doorgestuurd naar een versiespecifieke backend, kan het [parameterbeleid querytekenreeks instellen](api-management-transformation-policies.md#SetQueryStringParameter) worden gebruikt om het nu redundante versiekenmerk te verwijderen.

### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <set-backend-service backend-id="my-sf-service" sf-partition-key="@(context.Request.Url.Query.GetValueOrDefault("userId","")" sf-replica-type="primary" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
In dit voorbeeld leidt het beleid de aanvraag naar een backend van de servicefabric, waarbij de userId-querytekenreeks als partitiesleutel wordt gebruikt en de primaire replica van de partitie wordt gebruikt.

### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|
|----------|-----------------|--------------|
|set-backend-service|Hoofdelement.|Ja|

### <a name="attributes"></a>Kenmerken

|Name|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|base-url|Nieuwe URL van backendservicebasis.|Een `base-url` van `backend-id` of moet aanwezig zijn.|N.v.t.|
|backend-id|Id van de backend naar route naar. (Backend-entiteiten worden beheerd via [API](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/backend) en [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).)|Een `base-url` van `backend-id` of moet aanwezig zijn.|N.v.t.|
|sf-partitie-toets|Alleen van toepassing wanneer de backend een Service Fabric-service is en is opgegeven met 'backend-id'. Wordt gebruikt om een specifieke partitie op te lossen vanuit de naamomzettingsservice.|Nee|N.v.t.|
|sf-replica-type|Alleen van toepassing wanneer de backend een Service Fabric-service is en is opgegeven met 'backend-id'. Hiermee bepaalt u of de aanvraag naar de primaire of secundaire replica van een partitie moet gaan. |Nee|N.v.t.|
|sf-resolve-voorwaarde|Alleen van toepassing wanneer de backend een Service Fabric-service is. Voorwaarde die identificeert of de call to Service Fabric backend moet worden herhaald met een nieuwe resolutie.|Nee|N.v.t.|
|sf-service-instance-name|Alleen van toepassing wanneer de backend een Service Fabric-service is. Hiermee u service-exemplaren wijzigen tijdens runtime. |Nee|N.v.t.|
|sf-listener-naam|Alleen van toepassing wanneer de backend een Service Fabric-service is en is opgegeven met 'backend-id'. Met Service Fabric Reliable Services u meerdere luisteraars in een service maken. Dit kenmerk wordt gebruikt om een specifieke listener te selecteren wanneer een back-end Reliable Service meer dan één listener heeft. Als dit kenmerk niet is opgegeven, probeert API-beheer een listener zonder naam te gebruiken. Een listener zonder naam is typisch voor Betrouwbare Services die slechts één listener hebben. |Nee|N.v.t.|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidssecties:** binnenkomend, backend

-   **Beleidsscopes:** alle scopes

##  <a name="set-body"></a><a name="SetBody"></a>Lichaam instellen
 Gebruik `set-body` het beleid om de berichttekst in te stellen voor binnenkomende en uitgaande aanvragen. Om toegang te krijgen tot `context.Request.Body` de `context.Response.Body`berichttekst u de eigenschap of de eigenschap gebruiken, afhankelijk van of het beleid zich in de inkomende of uitgaande sectie bevindt.

> [!IMPORTANT]
>  Houd er rekening mee dat standaard `context.Request.Body` `context.Response.Body`wanneer u de berichttekst gebruikt of de oorspronkelijke berichttekst gebruikt, verloren gaat en moet worden ingesteld door de tekst terug te sturen in de expressie. Als u de inhoud `preserveContent` van `true` de hoofdtekst wilt behouden, stelt u de parameter in op wanneer u het bericht wilt openen. Als `preserveContent` is `true` ingesteld op en een ander lichaam wordt geretourneerd door de uitdrukking, het geretourneerde lichaam wordt gebruikt.
>
>  Houd rekening met de `set-body` volgende overwegingen bij het gebruik van het beleid.
>
> - Als u het `set-body` beleid gebruikt om een nieuwe of bijgewerkte `preserveContent` instantie `true` terug te sturen, hoeft u dit niet in te stellen omdat u de nieuwe inhoud van het lichaam expliciet levert.
>   -   Het behoud van de inhoud van een reactie in de binnenkomende pijplijn heeft geen zin omdat er nog geen reactie is.
>   -   Het bewaren van de inhoud van een aanvraag in de uitgaande pijplijn heeft geen zin omdat de aanvraag op dit moment al naar de backend is verzonden.
>   -   Als dit beleid wordt gebruikt wanneer er geen berichttekst is, bijvoorbeeld in een binnenkomende GET, wordt een uitzondering gemaakt.

 Zie voor meer `context.Request.Body`informatie `context.Response.Body`de `IMessage` , en de secties in de variabele tabel [Context.](api-management-policy-expressions.md#ContextVariables)

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Voorbeelden

#### <a name="literal-text-example"></a>Voorbeeld van letterlijke tekst

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Voorbeeld toegang tot de body als een string. Houd er rekening mee dat we de oorspronkelijke aanvraaginstantie bewaren, zodat we er later in de pijplijn toegang toe hebben.

```xml
<set-body>
@{ 
    string inBody = context.Request.Body.As<string>(preserveContent: true); 
    if (inBody[0] =='c') { 
        inBody[0] = 'm'; 
    } 
    return inBody; 
}
</set-body>
```

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Voorbeeld toegang tot de behuizing als een JObject. Aangezien we de oorspronkelijke aanvraaginstantie niet reserveren, zal de toegang tot de oorspronkelijke aanvraaginstantie resulteren in een uitzondering.

```xml
<set-body> 
@{ 
    JObject inBody = context.Request.Body.As<JObject>(); 
    if (inBody.attribute == <tag>) { 
        inBody[0] = 'm'; 
    } 
    return inBody.ToString(); 
} 
</set-body>

```

#### <a name="filter-response-based-on-product"></a>Filterrespons op basis van product
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

### <a name="using-liquid-templates-with-set-body"></a>Vloeibare sjablonen gebruiken met setbody
Het `set-body` beleid kan worden geconfigureerd om de [vloeibare](https://shopify.github.io/liquid/basics/introduction/) templating taal te gebruiken om de hoofdtekst van een aanvraag of antwoord te transformeren. Dit kan zeer effectief zijn als u het formaat van uw bericht volledig opnieuw moet vormgeven.

> [!IMPORTANT]
> De implementatie van Liquid `set-body` die in het beleid wordt gebruikt, is geconfigureerd in 'C#-modus'. Dit is vooral belangrijk bij het doen van dingen zoals filteren. Als voorbeeld vereist het gebruik van een datumfilter het gebruik van de Pascal-behuizing en de C#-datumopmaak, bijvoorbeeld:
>
> {{body.foo.startDateTime| Datum:"yyyyMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Gebruik een `set-header` beleid om Inhoudstype in te stellen op toepassing/xml, tekst/xml (of een type dat eindigt met +xml) om u correct te binden aan een XML-hoofd met behulp van de vloeistofsjabloon. voor een JSON-body moet het toepassing/json, tekst/json (of een type dat eindigt met +json) zijn.

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>JSON converteren naar SOAP met een sjabloon Liquid
```xml
<set-body template="liquid">
    <soap:Envelope xmlns="http://tempuri.org/" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
        <soap:Body>
            <GetOpenOrders>
                <cust>{{body.getOpenOrders.cust}}</cust>
            </GetOpenOrders>
        </soap:Body>
    </soap:Envelope>
</set-body>
```

#### <a name="transform-json-using-a-liquid-template"></a>JSON transformeren met behulp van een sjabloon Liquid
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|
|----------|-----------------|--------------|
|set-body|Hoofdelement. Bevat de hoofdtekst of een expressie die een hoofdtekst retourneert.|Ja|

### <a name="properties"></a>Eigenschappen

|Name|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|sjabloon|Wordt gebruikt om de templating-modus te wijzigen waarin het ingestelde hoofdbeleid wordt uitgevoerd. Momenteel is de enige ondersteunde waarde:<br /><br />- vloeistof - het vaste carrosseriebeleid zal de vloeistoftemplating motor gebruiken |Nee||

Voor toegang tot informatie over de aanvraag en het antwoord kan de sjabloon Liquid zich binden aan een contextobject met de volgende eigenschappen: <br />
<pre>context.
    Request.
        Url
        Method
        OriginalMethod
        OriginalUrl
        IpAddress
        MatchedParameters
        HasBody
        ClientCertificates
        Headers

    Response.
        StatusCode
        Method
        Headers
Url.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString

OriginalUrl.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString
</pre>



### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidssecties:** binnenkomend, uitgaand, backend

-   **Beleidsscopes:** alle scopes

##  <a name="set-http-header"></a><a name="SetHTTPheader"></a>HTTP-koptekst instellen
 Het `set-header` beleid wijst een waarde toe aan een bestaande reactie- en/of aanvraagheader of voegt een nieuwe reactie- en/of aanvraagheader toe.

 Hiermee voegt u een lijst met HTTP-koppen in een HTTP-bericht in. Wanneer dit beleid in een binnenkomende pijplijn wordt geplaatst, worden de HTTP-koppen ingesteld voor de aanvraag die wordt doorgegeven aan de doelservice. Wanneer dit beleid in een uitgaande pijplijn wordt geplaatst, worden de HTTP-headers ingesteld voor het antwoord dat naar de client van de gateway wordt verzonden.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Voorbeelden

#### <a name="example---adding-header-override-existing"></a>Voorbeeld - koptekst toevoegen, bestaande overschrijven

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```
#### <a name="example---removing-header"></a>Voorbeeld - koptekst verwijderen

```xml
 <set-header name="some header name" exists-action="delete" />
```



#### <a name="forward-context-information-to-the-backend-service"></a>Contextinformatie doorsturen naar de backendservice
 In dit voorbeeld ziet u hoe u beleid op API-niveau toepassen om contextinformatie te leveren aan de backendservice. Zie [Cloud Cover Episode 177: Meer API-beheerfuncties met Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) en fast-forward naar 10:30 voor een demonstratie van het configureren en gebruiken van dit beleid. Om 12:10 is er een demo van het aanroepen van een bewerking in de ontwikkelaarsportal waar u het beleid aan het werk zien.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Zie [Beleidsexpressies](api-management-policy-expressions.md) en [Contextvariabele](api-management-policy-expressions.md#ContextVariables)voor meer informatie.

> [!NOTE]
> Meerdere waarden van een koptekst worden samengevoegd aan een CSV-tekenreeks, bijvoorbeeld:`headerName: value1,value2,value3`
>
> Uitzonderingen zijn gestandaardiseerde kopteksten, welke waarden:
> - kan komma's`User-Agent` `WWW-Authenticate`bevatten `Proxy-Authenticate`( , , )
> - kan datum`Cookie`( `Set-Cookie` `Warning`, , ) bevatten ;
> - datum (`Date` `Expires`, `If-Modified-Since` `If-Unmodified-Since`, `Last-Modified` `Retry-After`, , , ) bevatten .
>
> In het geval van deze uitzonderingen worden meerdere kopwaarden niet in één tekenreeks samengevoegd en worden ze als afzonderlijke kopteksten doorgegeven, bijvoorbeeld:`User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|
|----------|-----------------|--------------|
|set-header|Hoofdelement.|Ja|
|waarde|Hiermee geeft u de waarde van de in te stellen header op. Voeg voor meerdere kopteksten `value` met dezelfde naam extra elementen toe.|Nee|

### <a name="properties"></a>Eigenschappen

|Name|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|bestaande-actie|Hiermee geeft u op welke actie u moet uitvoeren wanneer de koptekst al is opgegeven. Dit kenmerk moet een van de volgende waarden hebben.<br /><br /> - overschrijven - vervangt de waarde van de bestaande header.<br />- overslaan - vervangt niet de bestaande header waarde.<br />- toevoegen - de waarde toevoegen aan de bestaande kopwaarde.<br />- verwijderen - verwijdert de koptekst uit de aanvraag.<br /><br /> Wanneer u `override` meerdere items met dezelfde naam intrekt, resulteert dit erin dat de koptekst wordt ingesteld op basis van alle vermeldingen (die meerdere keren worden weergegeven); alleen vermelde waarden worden in het resultaat ingesteld.|Nee|overschrijven|
|name|Hiermee geeft u de naam op van de koptekst die moet worden ingesteld.|Ja|N.v.t.|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidssecties:** binnenkomend, uitgaand, backend, on-error

-   **Beleidsscopes:** alle scopes

##  <a name="set-query-string-parameter"></a><a name="SetQueryStringParameter"></a>Parameter querytekenreeks instellen
 Het `set-query-parameter` beleid voegt waarde van of verwijdert de parameter querytekenreeks van aanvragen. Kan worden gebruikt om queryparameters te doorgeven die worden verwacht door de backendservice die optioneel zijn of nooit aanwezig zijn in de aanvraag.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

#### <a name="example"></a>Voorbeeld

```xml

<set-query-parameter name="api-key" exists-action="skip">
  <value>12345678901</value>
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>Contextinformatie doorsturen naar de backendservice
 In dit voorbeeld ziet u hoe u beleid op API-niveau toepassen om contextinformatie te leveren aan de backendservice. Zie [Cloud Cover Episode 177: Meer API-beheerfuncties met Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) en fast-forward naar 10:30 voor een demonstratie van het configureren en gebruiken van dit beleid. Om 12:10 is er een demo van het aanroepen van een bewerking in de ontwikkelaarsportal waar u het beleid aan het werk zien.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Zie [Beleidsexpressies](api-management-policy-expressions.md) en [Contextvariabele](api-management-policy-expressions.md#ContextVariables)voor meer informatie.

### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|
|----------|-----------------|--------------|
|set-query-parameter|Hoofdelement.|Ja|
|waarde|Hiermee geeft u de waarde van de in te stellen queryparameter op. Voeg extra `value` elementen toe voor meerdere queryparameters met dezelfde naam.|Ja|

### <a name="properties"></a>Eigenschappen

|Name|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|bestaande-actie|Hiermee geeft u op welke actie moet worden uitgevoerd wanneer de querytekenreeks al is opgegeven. Dit kenmerk moet een van de volgende waarden hebben.<br /><br /> - overschrijven - vervangt de waarde van de bestaande parameter.<br />- overslaan - vervangt de bestaande waarde van de queryparameter niet.<br />- toevoegen - voegt de waarde toe aan de bestaande waarde van de queryparameter.<br />- verwijderen - verwijdert de queryparameter uit de aanvraag.<br /><br /> Wanneer u `override` meerdere vermeldingen met dezelfde naam intrekt, resulteert dit in de queryparameter die wordt ingesteld op basis van alle vermeldingen (die meerdere keren worden weergegeven); alleen vermelde waarden worden in het resultaat ingesteld.|Nee|overschrijven|
|name|Hiermee geeft u de naam op van de queryparameter die moet worden ingesteld.|Ja|N.v.t.|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidssecties:** binnenkomend, backend

-   **Beleidsscopes:** alle scopes

##  <a name="rewrite-url"></a><a name="RewriteURL"></a>URL opnieuw schrijven
 Het `rewrite-uri` beleid converteert een aanvraag-URL van het openbare formulier naar het formulier dat wordt verwacht door de webservice, zoals in het volgende voorbeeld wordt weergegeven.

- Openbare URL -`http://api.example.com/storenumber/ordernumber`

- URL aanvragen -`http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  Dit beleid kan worden gebruikt wanneer een menselijke en/of browservriendelijke URL moet worden omgezet in de URL-indeling die door de webservice wordt verwacht. Dit beleid hoeft alleen te worden toegepast bij het blootstellen van een alternatieve URL-indeling, zoals schone URL's, RESTful URL's, gebruiksvriendelijke URL's of SEO-vriendelijke URL's die zuiver structurele URL's zijn die geen querytekenreeks bevatten en in plaats daarvan alleen het pad van de resource bevatten (na de regeling en de autoriteit). Dit wordt vaak gedaan voor esthetische, bruikbaarheid, of zoekmachine optimalisatie (SEO) doeleinden.

> [!NOTE]
>  U alleen querytekenreeksparameters toevoegen met behulp van het beleid. U geen extra sjabloonpadparameters toevoegen aan de URL herschrijven.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<rewrite-uri template="uri template" copy-unmatched-params="true | false" />
```

### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put?c=d -->
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" copy-unmatched-params="false" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put -->
```

### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|
|----------|-----------------|--------------|
|herschrijven-uri|Hoofdelement.|Ja|

### <a name="attributes"></a>Kenmerken

|Kenmerk|Beschrijving|Vereist|Standaard|
|---------------|-----------------|--------------|-------------|
|sjabloon|De werkelijke URL van de webservice met parameters voor querytekenreeksen. Bij het gebruik van expressies moet de hele waarde een expressie zijn.|Ja|N.v.t.|
|copy-unmatched-params|Hiermee geeft u op of queryparameters in de binnenkomende aanvraag die niet in de oorspronkelijke URL-sjabloon aanwezig zijn, worden toegevoegd aan de URL die is gedefinieerd door de sjabloon voor herschrijven|Nee|waar|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidsonderdelen:** binnenkomend

-   **Beleidsscopes:** alle scopes

##  <a name="transform-xml-using-an-xslt"></a><a name="XSLTransform"></a>XML transformeren met een XSLT
 Het `Transform XML using an XSLT` beleid past een XSL-transformatie toe op XML in de aanvraag- of antwoordtekst.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<xsl-transform>
    <parameter name="User-Agent">@(context.Request.Headers.GetValueOrDefault("User-Agent","non-specified"))</parameter>
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:output method="xml" indent="yes" />
        <xsl:param name="User-Agent" />
        <xsl:template match="* | @* | node()">
            <xsl:copy>
                <xsl:if test="self::* and not(parent::*)">
                    <xsl:attribute name="User-Agent">
                        <xsl:value-of select="$User-Agent" />
                    </xsl:attribute>
                </xsl:if>
                <xsl:apply-templates select="* | @* | node()" />
            </xsl:copy>
        </xsl:template>
    </xsl:stylesheet>
  </xsl-transform>
```

### <a name="example"></a>Voorbeeld

```xml
<policies>
  <inbound>
      <base />
  </inbound>
  <outbound>
      <base />
      <xsl-transform>
        <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
            <xsl:output omit-xml-declaration="yes" method="xml" indent="yes" />
            <!-- Copy all nodes directly-->
            <xsl:template match="node()| @*|*">
                <xsl:copy>
                    <xsl:apply-templates select="@* | node()|*" />
                </xsl:copy>
            </xsl:template>
        </xsl:stylesheet>
    </xsl-transform>
  </outbound>
</policies>
```

### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|
|----------|-----------------|--------------|
|xsl-transformatie|Hoofdelement.|Ja|
|parameter|Wordt gebruikt om variabelen te definiëren die in de transformatie worden gebruikt|Nee|
|xsl:stylesheet|Hoofdstijlbladelement. Alle elementen en kenmerken die binnen de standaard [XSLT-specificatie](https://www.w3.org/TR/xslt) zijn gedefinieerd, volgen|Ja|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidsonderdelen:** binnenkomend, uitgaand

-   **Beleidsscopes:** alle scopes

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie:

+ [Beleid in API-beheer](api-management-howto-policies.md)
+ [Beleidsreferentie](api-management-policy-reference.md) voor een volledige lijst met beleidsoverzichten en hun instellingen
+ [Beleidsvoorbeelden](policy-samples.md)
