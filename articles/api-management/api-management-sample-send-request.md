---
title: API-beheerservice gebruiken om HTTP-aanvragen te genereren
description: Informatie over het gebruik van aanvraag- en antwoordbeleid in API-beheer om externe services uit uw API aan te roepen
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 1c86570850894a47f57a2d3587811411cc9a76eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190015"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Externe services gebruiken vanuit de Azure API Management-service
Het beleid dat beschikbaar is in de Azure API Management-service kan een breed scala aan nuttig werk doen, puur op basis van de binnenkomende aanvraag, de uitgaande respons en basisconfiguratiegegevens. De mogelijkheid om vanuit API-beheerbeleid met externe services te communiceren, biedt echter veel meer mogelijkheden.

U hebt eerder gezien hoe u communiceren met de [Azure Event Hub-service voor logboekregistratie, bewaking en analyse.](api-management-log-to-eventhub-sample.md) In dit artikel wordt beleid weergegeven waarmee u communiceren met een externe OP HTTP gebaseerde service. Dit beleid kan worden gebruikt voor het activeren van externe gebeurtenissen of voor het ophalen van informatie die wordt gebruikt om het oorspronkelijke verzoek en antwoord op een bepaalde manier te manipuleren.

## <a name="send-one-way-request"></a>Verzend-One-Way-Request
Misschien wel de eenvoudigste externe interactie is de fire-and-forget stijl van verzoek die het mogelijk maakt een externe dienst te worden aangemeld van een soort van belangrijke gebeurtenis. Het controlestroombeleid `choose` kan worden gebruikt om elke vorm van aandoening te detecteren waarin u geïnteresseerd bent.  Als aan de voorwaarde is voldaan, u een externe [HTTP-aanvraag](/azure/api-management/api-management-advanced-policies#SendOneWayRequest) indienen met behulp van het beleid voor het verzenden van een enkele reis aanvraag. Dit kan een verzoek zijn voor een berichtensysteem zoals Hipchat of Slack, of een e-mail-API zoals SendGrid of MailChimp, of voor kritieke ondersteuningsincidenten zoals PagerDuty. Al deze berichtensystemen hebben eenvoudige HTTP API's die kunnen worden aangeroepen.

### <a name="alerting-with-slack"></a>Waarschuwen met Slack
In het volgende voorbeeld wordt uitgelegd hoe u een bericht naar een Slack-chatruimte verzendt als de HTTP-antwoordstatuscode groter is dan of gelijk is aan 500. Een fout in het bereik van 500 duidt op een probleem met de backend-API dat de client van de API niet zelf kan oplossen. Het vereist meestal een soort van interventie op API Management deel.  

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

Slack heeft het idee van inkomende webhaken. Bij het configureren van een inkomende webhaak genereert Slack een speciale URL, waarmee u een eenvoudig POST-verzoek doen en een bericht doorgeven aan het Slack-kanaal. De JSON-body die u maakt, is gebaseerd op een indeling die is gedefinieerd door Slack.

![Slack-webhaak](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Is vuur en vergeet goed genoeg?
Er zijn bepaalde afwegingen bij het gebruik van een fire-and-forget stijl van verzoek. Als om de een of andere reden het verzoek mislukt, wordt de fout niet gemeld. In deze specifieke situatie is de complexiteit van het hebben van een secundair systeem voor het melden van fouten en de extra prestatiekosten van het wachten op de reactie niet gerechtvaardigd. Voor scenario's waarbij het essentieel is om het antwoord te controleren, is het [verzendbeleid](/azure/api-management/api-management-advanced-policies#SendRequest) een betere optie.

## <a name="send-request"></a>Verzendaanvraag
Met `send-request` het beleid kan het gebruik van een externe service complexe verwerkingsfuncties uitvoeren en gegevens worden terugsturen naar de API-beheerservice die kan worden gebruikt voor verdere beleidsverwerking.

### <a name="authorizing-reference-tokens"></a>Referentietokens autoriseren
Een belangrijke functie van API Management is het beschermen van backend resources. Als de autorisatieserver die door uw API wordt gebruikt [JWT-tokens](https://jwt.io/) maakt als onderdeel van `validate-jwt` de OAuth2-stroom, zoals Azure Active [Directory](../active-directory/hybrid/whatis-hybrid-identity.md) doet, u het beleid gebruiken om de geldigheid van het token te verifiëren. Sommige autorisatieservers maken zogenaamde [referentietokens](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) die niet kunnen worden geverifieerd zonder een terugroep naar de autorisatieserver.

### <a name="standardized-introspection"></a>Gestandaardiseerde introspectie
In het verleden is er geen gestandaardiseerde manier geweest om een referentietoken met een autorisatieserver te verifiëren. Een onlangs voorgestelde standaard [RFC 7662](https://tools.ietf.org/html/rfc7662) is echter gepubliceerd door de IETF die bepaalt hoe een resourceserver de geldigheid van een token kan verifiëren.

### <a name="extracting-the-token"></a>Het token extraheren
De eerste stap is om het token uit de koptekst Autorisatie te halen. De kopwaarde moet worden `Bearer` opgemaakt met het autorisatieschema, een enkele spatie en vervolgens het autorisatietoken volgens [RFC 6750](https://tools.ietf.org/html/rfc6750#section-2.1). Helaas zijn er gevallen waarin de vergunningsregeling wordt weggelaten. Om dit te verantwoorden bij het ontleden, splitst API Management de kopwaarde op een spatie en selecteert het de laatste tekenreeks uit de geretourneerde reeks tekenreeksen. Dit biedt een tijdelijke oplossing voor slecht geformatteerde autorisatiekoppen.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Het validatieverzoek indienen
Zodra API Management het autorisatietoken heeft, kan API Management het verzoek indienen om het token te valideren. RFC 7662 noemt dit proces introspectie en vereist dat u `POST` een HTML-formulier aan de introspectie bron. Het HTML-formulier moet ten minste een sleutel/waardepaar met de sleutel `token`bevatten. Dit verzoek aan de autorisatieserver moet ook worden geverifieerd om ervoor te zorgen dat kwaadwillende clients niet kunnen gaan trawleren voor geldige tokens.

```xml
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
```

### <a name="checking-the-response"></a>Het antwoord controleren
Het `response-variable-name` kenmerk wordt gebruikt om toegang te geven tot het geretourneerde antwoord. De naam die in deze eigenschap is gedefinieerd, kan worden gebruikt als sleutel in het `context.Variables` woordenboek om toegang te krijgen tot het `IResponse` object.

Uit het antwoordobject u de hoofdtekst ophalen en rfc 7622 vertelt API Management dat het `active` antwoord een JSON-object moet zijn en ten minste een eigenschap moet bevatten die een booleaanse waarde is. Wanneer `active` is waar dan wordt het token als geldig beschouwd.

Als de autorisatieserver het veld 'actief' niet bevat om aan te geven of het token geldig is, gebruikt u een hulpprogramma zoals Postman om te bepalen welke eigenschappen zijn ingesteld in een geldig token. Als een geldig tokenantwoord bijvoorbeeld een eigenschap bevat die 'expires_in' wordt genoemd, controleert u of deze eigenschapsnaam op deze manier in het antwoord van de autorisatieserver bestaat:

<wanneer de context van condition="@((((IResponse). Variabelen["tokenstate"]). Body.As<JObject>( ). Eigenschap("expires_in") == null)">

### <a name="reporting-failure"></a>Meldingsfout
U `<choose>` een beleid gebruiken om te detecteren of het token ongeldig is en zo ja, een 401-antwoord retourneren.

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

Volgens [RFC 6750,](https://tools.ietf.org/html/rfc6750#section-3) waarin `bearer` wordt beschreven hoe tokens moeten `WWW-Authenticate` worden gebruikt, retourneert API Management ook een header met de 401-respons. De WWW-Authenticate is bedoeld om een klant te instrueren over het samenstellen van een correct geautoriseerde aanvraag. Vanwege de grote verscheidenheid aan benaderingen die mogelijk zijn met het OAuth2-framework, is het moeilijk om alle benodigde informatie te communiceren. Gelukkig zijn er inspanningen aan de gang om klanten te helpen [ontdekken hoe ze aanvragen naar een resourceserver correct kunnen autoriseren.](https://tools.ietf.org/html/draft-jones-oauth-discovery-00)

### <a name="final-solution"></a>Definitieve oplossing
Aan het einde krijgt u het volgende beleid:

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
              <return-response response-variable-name="existing response variable">
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

Dit is slechts een van de `send-request` vele voorbeelden van hoe het beleid kan worden gebruikt om nuttige externe services te integreren in het proces van aanvragen en antwoorden die via de API-beheerservice stromen.

## <a name="response-composition"></a>Reactiesamenstelling
Het `send-request` beleid kan worden gebruikt voor het verbeteren van een primaire aanvraag voor een backend-systeem, zoals u in het vorige voorbeeld hebt gezien, of het kan worden gebruikt als een volledige vervanging voor de backend-oproep. Met deze techniek u eenvoudig samengestelde resources maken die zijn samengevoegd met meerdere verschillende systemen.

### <a name="building-a-dashboard"></a>Een dashboard bouwen
Soms wilt u informatie die in meerdere backendsystemen bestaat, bijvoorbeeld om een dashboard te besturen, kunnen blootleggen. De KPI's komen uit alle verschillende back-ends, maar u zou liever niet directe toegang tot hen te bieden en het zou leuk zijn als alle informatie kan worden opgehaald in een enkel verzoek. Misschien een deel van de backend informatie moet wat snijden en snijden en een beetje ontsmetten eerste! In staat zijn om cache die samengestelde bron zou een nuttig zijn om de backend belasting te verminderen als je weet dat gebruikers hebben een gewoonte van hameren op de F5-toets om te zien of hun slecht presterende statistieken kunnen veranderen.    

### <a name="faking-the-resource"></a>Faken van de bron
De eerste stap voor het bouwen van de dashboardbron is het configureren van een nieuwe bewerking in de Azure-portal. Dit is een tijdelijke aanduidingsbewerking die wordt gebruikt om een compositiebeleid te configureren om de dynamische resource te bouwen.

![Dashboardbewerking](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Het maken van de verzoeken
Zodra de bewerking is gemaakt, u een beleid speciaal voor die bewerking configureren. 

![Dashboardbewerking](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

De eerste stap is om queryparameters uit de binnenkomende aanvraag te extraheren, zodat u deze doorsturen naar de backend. In dit voorbeeld toont het dashboard informatie op basis van `fromDate` `toDate` een bepaalde periode en heeft daarom een en parameter. U `set-variable` het beleid gebruiken om de informatie uit de url van de aanvraag te extraheren.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Zodra u deze informatie hebt, u verzoeken indienen bij alle backend-systemen. Elke aanvraag construeert een nieuwe URL met de parameterinformatie en roept de betreffende server aan en slaat het antwoord op in een contextvariabele.

```xml
<send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
  <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
  <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Deze aanvragen worden achter elkaar uitgevoerd, wat niet ideaal is. 

### <a name="responding"></a>Reageren
Als u het samengestelde antwoord wilt samenstellen, u het [retourresponsbeleid](/azure/api-management/api-management-advanced-policies#ReturnResponse) gebruiken. Het `set-body` element kan een expressie `JObject` gebruiken om een nieuwe te maken met alle componentvoorstellingen die zijn ingesloten als eigenschappen.

```xml
<return-response response-variable-name="existing response variable">
  <set-status code="200" reason="OK" />
  <set-header name="Content-Type" exists-action="override">
    <value>application/json</value>
  </set-header>
  <set-body>
    @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                  new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                  new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                  new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                  ).ToString())
  </set-body>
</return-response>
```

Het volledige beleid ziet er als volgt uit:

```xml
<policies>
    <inbound>

  <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
  <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
</policies>
```

In de configuratie van de tijdelijke aanduidingkunt u de dashboardbron configureren die ten minste een uur in de cache moet worden opgeslagen. 

## <a name="summary"></a>Samenvatting
Azure API Management-service biedt flexibel beleid dat selectief kan worden toegepast op HTTP-verkeer en de samenstelling van backendservices mogelijk maakt. Of u nu uw API-gateway wilt verbeteren met waarschuwingsfuncties, verificatie, validatiemogelijkheden of nieuwe `send-request` samengestelde resources wilt maken op basis van meerdere backendservices, het beleid en het bijbehorende beleid openen een wereld aan mogelijkheden.

