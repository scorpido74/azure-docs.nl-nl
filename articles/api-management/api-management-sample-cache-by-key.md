---
title: Custom caching in Azure API Management (Aangepast opslaan in Azure API Management)
description: Meer informatie over het cacheen van items per sleutel in Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 922ab731ccd76e6a1336d61abe4b0251e358beb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60780817"
---
# <a name="custom-caching-in-azure-api-management"></a>Custom caching in Azure API Management (Aangepast opslaan in Azure API Management)
Azure API Management-service heeft ingebouwde ondersteuning voor [HTTP-responscaching](api-management-howto-cache.md) met behulp van de bron-URL als de sleutel. De sleutel kan worden gewijzigd door `vary-by` het aanvragen van headers met behulp van de eigenschappen. Dit is handig voor het incachen van hele HTTP-antwoorden (ook bekend als representaties), maar soms is het handig om gewoon een deel van een representatie in de cache te plaatsen. Het nieuwe beleid [met cache-lookup-value](/azure/api-management/api-management-caching-policies#GetFromCacheByKey) en [cache-store-value](/azure/api-management/api-management-caching-policies#StoreToCacheByKey) biedt de mogelijkheid om willekeurige gegevens op te slaan en op te halen binnen beleidsdefinities. Deze mogelijkheid voegt ook waarde toe aan het eerder ingevoerde [beleid voor het aanvragen van verzenden,](/azure/api-management/api-management-advanced-policies#SendRequest) omdat u nu reacties van externe services cachen.

## <a name="architecture"></a>Architectuur
API Management-service maakt gebruik van een gedeelde gegevenscache per tenant, zodat u, wanneer u opschaalt naar meerdere eenheden, nog steeds toegang krijgt tot dezelfde gegevens in de cache. Echter, bij het werken met een multi-regio implementatie zijn er onafhankelijke caches binnen elk van de regio's. Het is belangrijk om de cache niet te behandelen als een gegevensarchief, waar het de enige bron van een stukje informatie is. Als u dat wel deed, en later besloten om te profiteren van de multi-regio implementatie, dan kunnen klanten met gebruikers die reizen toegang verliezen tot die gegevens in de cache.

## <a name="fragment-caching"></a>Fragment caching
Er zijn bepaalde gevallen waarin antwoorden worden geretourneerd een deel van de gegevens bevatten dat duur is om te bepalen en toch voor een redelijke hoeveelheid tijd vers blijft. Denk bijvoorbeeld aan een service die is gebouwd door een luchtvaartmaatschappij die informatie verstrekt over vluchtreserveringen, vluchtstatus, enz. Als de gebruiker lid is van het puntenprogramma van de luchtvaartmaatschappijen, heeft hij of zij ook informatie over hun huidige status en geaccumuleerde kilometers. Deze gebruikersgerelateerde informatie kan worden opgeslagen in een ander systeem, maar het kan wenselijk zijn om deze op te nemen in reacties die zijn geretourneerd over de vluchtstatus en reserveringen. Dit kan worden gedaan met behulp van een proces genaamd fragment caching. De primaire weergave kan worden geretourneerd van de oorsprongsserver met behulp van een soort token om aan te geven waar de gebruikersgerelateerde informatie moet worden ingevoegd. 

Overweeg de volgende JSON-reactie van een backend-API.

```json
{
  "airline" : "Air Canada",
  "flightno" : "871",
  "status" : "ontime",
  "gate" : "B40",
  "terminal" : "2A",
  "userprofile" : "$userprofile$"
}  
```

En secundaire `/userprofile/{userid}` bron op dat lijkt op,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Om de juiste gebruikersinformatie te bepalen, moet API Management identificeren wie de eindgebruiker is. Dit mechanisme is uitvoeringsafhankelijk. Als voorbeeld gebruik ik `Subject` de claim `JWT` van een token. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

API Management `enduserid` slaat de waarde op in een contextvariabele voor later gebruik. De volgende stap is om te bepalen of een eerdere aanvraag de gebruikersgegevens al heeft opgehaald en opgeslagen in de cache. Hiervoor maakt API Management `cache-lookup-value` gebruik van het beleid.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Als er geen invoer in de cache is die `userprofile` overeenkomt met de sleutelwaarde, wordt er geen contextvariabele gemaakt. API Management controleert het succes van `choose` de opzoeking met behulp van het controlestroombeleid.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Als `userprofile` de contextvariabele niet bestaat, moet API-beheer een HTTP-verzoek indienen om deze op te halen.

```xml
<send-request
  mode="new"
  response-variable-name="userprofileresponse"
  timeout="10"
  ignore-error="true">

  <!-- Build a URL that points to the profile for the current end-user -->
  <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
      (string)context.Variables["enduserid"]).AbsoluteUri)
  </set-url>
  <set-method>GET</set-method>
</send-request>
```

API Management `enduserid` gebruikt de URL om de URL te construeren naar de bron van het gebruikersprofiel. Zodra API Management de respons heeft, haalt het de hoofdtekst uit de respons en slaat deze weer op in een contextvariabele.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Om te voorkomen dat API Management dit HTTP-verzoek opnieuw indient, u, wanneer dezelfde gebruiker een ander verzoek doet, opgeven om het gebruikersprofiel in de cache op te slaan.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

API Management slaat de waarde in de cache op met exact dezelfde sleutel waarmee API Management deze oorspronkelijk probeerde op te halen. De duur die API Management kiest om de waarde op te slaan, moet worden gebaseerd op hoe vaak de informatie verandert en hoe tolerant gebruikers zijn ten opzichte van verouderde informatie. 

Het is belangrijk om te beseffen dat ophalen uit de cache is nog steeds een out-of-process, netwerk aanvraag en potentieel kan nog tientallen milliseconden toe te voegen aan de aanvraag. De voordelen komen bij het bepalen van de gebruikersprofielinformatie duurt langer dan die als gevolg van de noodzaak om database query's of geaggregeerde informatie uit meerdere back-ends te doen.

De laatste stap in het proces is het bijwerken van de geretourneerde reactie met de gebruikersprofielinformatie.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

U ervoor kiezen om de aanhalingstekens op te nemen als onderdeel van het token, zodat zelfs wanneer de vervanging niet plaatsvindt, het antwoord nog steeds een geldige JSON is.  

Zodra u al deze stappen samencombineert, is het eindresultaat een beleid dat eruitziet als het volgende beleid.

```xml
<policies>
    <inbound>
        <!-- How you determine user identity is application dependent -->
        <set-variable
          name="enduserid"
          value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

        <!--Look for userprofile for this user in the cache -->
        <cache-lookup-value
          key="@("userprofile-" + context.Variables["enduserid"])"
          variable-name="userprofile" />

        <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                <!-- Make HTTP request to get user profile -->
                <send-request
                  mode="new"
                  response-variable-name="userprofileresponse"
                  timeout="10"
                  ignore-error="true">

                   <!-- Build a URL that points to the profile for the current end-user -->
                    <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>

                <!-- Store response body in context variable -->
                <set-variable
                  name="userprofile"
                  value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                <!-- Store result in cache -->
                <cache-store-value
                  key="@("userprofile-" + context.Variables["enduserid"])"
                  value="@((string)context.Variables["userprofile"])"
                  duration="100000" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <!-- Update response body with user profile-->
        <find-and-replace
              from='"$userprofile$"'
              to="@((string)context.Variables["userprofile"])" />
        <base />
    </outbound>
</policies>
```

Deze caching-benadering wordt voornamelijk gebruikt op websites waar HTML is samengesteld aan de serverzijde, zodat deze als één pagina kan worden weergegeven. Het kan ook nuttig zijn in API's waar clients http-caching aan clientzijde niet kunnen doen of het wenselijk is om die verantwoordelijkheid niet op de klant te leggen.

Ditzelfde soort fragment caching kan ook worden gedaan op de backend webservers met behulp van een Redis caching server, echter, met behulp van de API Management service om dit werk uit te voeren is handig wanneer de cache fragmenten afkomstig zijn van verschillende back-ends dan de primaire Reacties.

## <a name="transparent-versioning"></a>Transparante versiebeheer
Het is gebruikelijk dat meerdere verschillende implementatieversies van een API op een bepaald moment worden ondersteund. Bijvoorbeeld om verschillende omgevingen te ondersteunen (dev, test, productie, enz.) of om oudere versies van de API te ondersteunen om API-consumenten de tijd te geven om te migreren naar nieuwere versies. 

Een benadering van de behandeling van dit, in plaats `/v1/customers` `/v2/customers` van te eisen dat clientontwikkelaars de URL's van naar zijn gewijzigd, is het opslaan in de profielgegevens van de consument welke versie van de API ze momenteel willen gebruiken en de juiste backend-URL aanroepen. Om de juiste backend-URL te bepalen om een bepaalde client te bellen, is het noodzakelijk om bepaalde configuratiegegevens op te vragen. Door deze configuratiegegevens in cache te plaatsen, kan API Management de prestatiestraf voor het uitvoeren van deze opzoeking minimaliseren.

De eerste stap is het bepalen van de id die wordt gebruikt om de gewenste versie te configureren. In dit voorbeeld heb ik ervoor gekozen om de versie te koppelen aan de productabonnementscode. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

API Management doet vervolgens een cache lookup om te zien of het al de gewenste clientversie heeft opgehaald.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Vervolgens controleert API Management of het niet in de cache is gevonden.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Als API Management het niet heeft gevonden, haalt API Management het op.

```xml
<send-request
    mode="new"
    response-variable-name="clientconfiguresponse"
    timeout="10"
    ignore-error="true">
            <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
            <set-method>GET</set-method>
</send-request>
```

Haal de tekst van de antwoordtekst uit het antwoord.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Bewaar het terug in de cache voor toekomstig gebruik.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

En ten slotte de back-end URL bijwerken om de versie van de service gewenst door de client te selecteren.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

Het volledige beleid is als volgt:

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">
            <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
            </send-request>
            <!-- Store response body in context variable -->
            <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
            <!-- Store result in cache -->
            <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
        </when>
    </choose>
    <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
</inbound>
```

Api-consumenten in staat stellen om op transparante wijze te bepalen welke backendversie door clients wordt benaderd zonder clients bij te werken en opnieuw te implementeren, is een elegante oplossing die veel API-versieproblemen aanpakt.

## <a name="tenant-isolation"></a>Huurder Isolatie
In grotere implementaties met meerdere tenants maken sommige bedrijven afzonderlijke groepen tenants op afzonderlijke implementaties van backend-hardware. Dit minimaliseert het aantal klanten dat wordt beïnvloed door een hardwareprobleem op de backend. Het maakt het ook mogelijk om nieuwe softwareversies gefaseerd uit te rollen. Idealiter moet deze backend-architectuur transparant zijn voor API-consumenten. Dit kan worden bereikt op een vergelijkbare manier als transparante versiebeheer, omdat het is gebaseerd op dezelfde techniek van het manipuleren van de backend URL met behulp van configuratiestatus per API-sleutel.  

In plaats van een voorkeursversie van de API voor elke abonnementssleutel terug te sturen, retourneert u een id die een tenant heeft, terug naar de toegewezen hardwaregroep. Die id kan worden gebruikt om de juiste backend-URL te construeren.

## <a name="summary"></a>Samenvatting
De vrijheid om de Azure API-beheercache te gebruiken voor het opslaan van elke vorm van gegevens maakt efficiënte toegang tot configuratiegegevens mogelijk die van invloed kunnen zijn op de manier waarop een binnenkomende aanvraag wordt verwerkt. Het kan ook worden gebruikt om gegevensfragmenten op te slaan die reacties kunnen vergroten, geretourneerd uit een backend-API.
