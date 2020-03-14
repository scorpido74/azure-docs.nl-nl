---
title: Naslag informatie voor project-URL'S
titlesuffix: Azure Cognitive Services
description: Verwijzing voor het eind punt van de project-URL preview.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: f92c0faaaa3aa0cd2af16a031f3bed4c6b41fc22
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220785"
---
# <a name="project-url-preview-v7-reference"></a>Naslag informatie voor de project-URL preview V7

URL-voor beeld biedt ondersteuning voor korte beschrijvingen van webbronnen voor blog berichten, forum discussies, voorbeeld pagina's, enzovoort. De URL kan elk type Internet bron zijn: webpagina, nieuws, afbeelding of video. De query moet een absolute URL met een HTTP-of https-schema. relatieve Url's of andere schema's zoals ftp://worden niet ondersteund.

Toepassingen die gebruikmaken van URL-voor beelden sturen webaanvragen naar het eind punt met een URL die moet worden weer gegeven in een query parameter. De aanvraag moet de header *OCP-APIM-Subscription-Key* bevatten.

De JSON-reactie kan worden geparseerd voor de preview-gegevens: naam, beschrijving van de resource, *isFamilyFriendly*en koppelingen die toegang bieden tot een representatieve installatie kopie en de volledige resource online.

U moet alleen de gegevens uit het URL-voor beeld gebruiken om voorbeeld fragmenten en miniatuur afbeeldingen weer te geven die zijn Hyper links naar hun bron sites, in door eind gebruikers geïnitieerde URL-uitwisseling op sociale media, chat-bot of vergelijk bare aanbiedingen. U moet alle gegevens die u ontvangt van de project-URL-preview kopiëren, opslaan of bewaren in de cache. U moet alle aanvragen voor het uitschakelen van voor beelden die kunnen worden ontvangen van website-of eigen aren van inhoud, voldoen.

## <a name="endpoint"></a>Eindpunt
Als u de resultaten van een Preview-URL wilt aanvragen, stuurt u een aanvraag naar het volgende eind punt. Gebruik de para meters headers en URL om verdere specificaties te definiëren.

Eind punt ophalen:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

```

De aanvraag moet het HTTPS-protocol gebruiken en de volgende query parameter bevatten:

q-de query waarmee de URL voor de preview-versie wordt geïdentificeerd

De volgende secties bevatten technische gegevens over de antwoord objecten, query parameters en kopteksten die van invloed zijn op de zoek resultaten.

Zie [kopteksten](#headers)voor informatie over kopteksten die aanvragen moeten bevatten.

Zie [query parameters](#query-parameters)voor meer informatie over query parameters die aanvragen moeten bevatten.

Zie [Response Objects](#response-objects)(Engelstalig) voor meer informatie over de JSON-objecten die het antwoord bevat.

De maximale lengte van de query-URL is 2.048 tekens. Om ervoor te zorgen dat de lengte van de URL de limiet niet overschrijdt, moet de maximum lengte van de query parameters kleiner zijn dan 1.500 tekens. Als de URL langer is dan 2.048 tekens, retourneert de server 404 niet gevonden.

Zie [vereisten voor gebruik en weer gave](use-display-requirements.md)voor meer informatie over het toegestane gebruik en de weer gave van resultaten.

> [!NOTE]
> Sommige aanvraag headers die relevant zijn voor andere zoek-Api's, hebben geen invloed op de URL-preview
> - Pragma: de aanroeper heeft geen controle over of het URL-voor beeld een cache gebruikt
> - User-agent – voor nu biedt URL preview-API geen andere antwoorden voor aanroepen van een PC, laptop of mobiel.
> 
> Sommige para meters zijn momenteel niet relevant voor URL-preview-API, maar kunnen in de toekomst worden gebruikt voor verbeterde globalisering.

## <a name="headers"></a>Headers
Hieronder ziet u de kopteksten die een aanvraag en antwoord kan bevatten.

|Header|Beschrijving|
|------------|-----------------|
|<a name="market" />BingAPIs-Market|Antwoordheader.<br /><br /> De markt die wordt gebruikt door de aanvraag. De notatie is \<languageCode\>-\<countryCode\>. Bijvoorbeeld: nl-NL.|
|<a name="traceid" />BingAPIs-TraceId|Antwoordheader.<br /><br /> De id van de logboekvermelding die de details van de aanvraag bevat. Registreer deze id wanneer er een fout optreedt. Als u het probleem niet kunt vaststellen en oplossen, neemt u deze id op bij de andere informatie die u aan het ondersteuningsteam verstrekt.|
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Vereiste aanvraagheader.<br /><br /> De abonnementssleutel die u hebt ontvangen toen u zich hebt geregistreerd voor deze service in [Cognitive Services](https://www.microsoft.com/cognitive-services/).|
|<a name="clientid" />X-MSEdge-ClientID|Optionele aanvraag- en antwoord-header.<br /><br /> Bing gebruikt deze header om gebruikers consistent gedrag te bieden bij Bing API-aanroepen. Bing introduceert nieuwe functies en verbeteringen vaak in flights, en gebruikt de client-ID als sleutel voor het toewijzen van verkeer aan verschillende flights. Als u niet dezelfde client-id gebruikt voor een gebruiker voor meerdere aanvragen, kan Bing de gebruiker toewijzen aan meerdere conflicterende flights. Toewijzing aan meerdere conflicterende flights kan leiden tot een inconsistente gebruikerservaring. Als de tweede aanvraag bijvoorbeeld een andere flighttoewijzing heeft dan de eerste, kan de ervaring onverwacht zijn. Bing kan de client-id ook gebruiken om webresultaten aan te passen aan de zoekgeschiedenis van die client-id, waardoor de gebruiker een rijkere ervaring krijgt.<br /><br /> Bing gebruikt deze header ook om de rangschikking van resultaten te verbeteren door de activiteit te analyseren die wordt gegenereerd door een client-id. De relevantie-verbeteringen helpen de kwaliteit van de resultaten die door Bing-API's worden geleverd te verbeteren, en maken op hun beurt hogere doorklikpercentages mogelijk voor de API-consument.<br /><br />Hieronder volgen de basisgebruiksregels die van toepassing zijn op deze header.<br /><ul><li>Elke gebruiker die uw toepassing op het apparaat gebruikt, moet een unieke door Bing gegenereerde client-id hebben.<br /><br/>Als u deze header niet in de aanvraag opneemt, genereert Bing een id en retourneert deze in de X-MSEdge-ClientID-antwoordheader. De enige keer dat u deze header NIET in een aanvraag moet opnemen, is de eerste keer dat de gebruiker uw app op dat apparaat gebruikt.<br /><br/></li><li>Gebruik de client-id voor elke Bing-API-aanvraag die uw app voor deze gebruiker op het apparaat doet.<br /><br/></li><li>**Attentie:** U moet ervoor zorgen dat deze client-ID niet kan worden gekoppeld aan de gegevens van een authenticatable-gebruikers account.</li><br/><li>Maak de client-id persistent. Gebruik in een browser-app een persistent HTTP-cookie om ervoor te zorgen dat de id in alle sessies wordt gebruikt. Gebruik geen sessiecookie. Voor andere apps, zoals mobiele apps, gebruikt u de persistente opslag van het apparaat om de id persistent te maken.<br /><br/>De volgende keer dat de gebruiker uw app op dat apparaat gebruikt, haalt u de client-id op die u persistent hebt gemaakt.</li></ul><br /> **OPMERKING:** Bing-antwoorden kunnen deze header al dan niet bevatten. Als het antwoord deze header bevat, registreert u de client-id en gebruikt u deze voor alle volgende Bing-aanvragen voor de gebruiker op dat apparaat.<br /><br /> **OPMERKING:** als u de X-MSEdge-ClientID opneemt, moet u geen cookies opnemen in de aanvraag.|
|<a name="clientip" />X-MSEdge-ClientIP|Optionele aanvraagheader.<br /><br /> Het IPv4- of IPv6-adres van het clientapparaat. Het IP-adres wordt gebruikt voor het detecteren van de locatie van de gebruiker. Bing gebruikt de locatie-informatie om het gedrag van Veilig Zoeken te bepalen.<br /><br /> Verdoezel het adres niet (bijvoorbeeld door het laatste octet te wijzigen in 0). Wanneer u het adres verdoezelt, is de locatie totaal niet in de buurt van de werkelijke locatie van het apparaat, wat ertoe kan leiden dat Bing onjuiste resultaten geeft.|

## <a name="query-parameters"></a>Queryparameters
De aanvraag kan de volgende query parameters bevatten. Zie de vereiste kolom voor de vereiste para meters. U moet de URL van de query parameters coderen. De query moet een absolute URL met een HTTP-of https-schema. We ondersteunen geen relatieve Url's of andere schema's zoals ftp://

|Naam|Waarde|Type|Vereist|
|----------|-----------|----------|--------------|
|<a name="mkt" />mkt|De markt waaruit de resultaten afkomstig zijn. <br /><br />Zie markt codes voor een lijst met mogelijke markt waarden.<br /><br /> **Opmerking:** De URL-preview-API ondersteunt momenteel alleen Geografie-en Engelse taal.<br /><br />|Tekenreeks|Ja|
|<a name="query" />q|De URL voor de preview-versie|Tekenreeks|Ja|
|<a name="responseformat" />responseFormat|Het media type dat voor het antwoord moet worden gebruikt. Hier volgen de mogelijke niet-hoofdletter gevoelige waarden.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> De standaard waarde is JSON. Zie [Response Objects](#response-objects)(Engelstalig) voor informatie over de JSON-objecten die het antwoord bevat.<br /><br />Als u JsonLd opgeeft, bevat de antwoord tekst JSON-LD objecten die de zoek resultaten bevatten. Zie [JSON-LD](https://json-ld.org/)voor meer informatie over de JSON-ld.|Tekenreeks|Nee|
|<a name="safesearch"/>safeSearch|Illegale inhoud voor volwassenen, of illegale inhoud, is geblokkeerd met fout code 400 en de vlag *isFamilyFriendly* wordt niet geretourneerd. <p>Voor juridische inhoud voor volwassenen is hieronder het gedrag. De status code retourneert 200 en de vlag *isFamilyFriendly* is ingesteld op false.<ul><li>safeSearch = strict: titel, beschrijving, URL en afbeelding worden niet geretourneerd.</li><li>safeSearch = gemiddeld; Titel, URL en beschrijving ophalen, maar niet de beschrijvende afbeelding.</li><li>safeSearch = uit; Alle antwoord objecten/elementen – titel, URL, beschrijving en afbeelding ophalen.</li></ul> |Tekenreeks|Niet vereist. </br> De standaard waarde is safeSearch = strict.|

## <a name="response-objects"></a>Antwoord objecten
Het antwoord schema is een [webpagina] of ErrorResponse, zoals in de Webzoekopdrachten-API. Als de aanvraag mislukt, is het object op het hoogste niveau het [ErrorResponse](#errorresponse) -object.

|Object|Beschrijving|
|------------|-----------------|
|[Webpagina](#webpage)|JSON-object op het hoogste niveau dat kenmerken van de preview bevat.|

### <a name="error"></a>Fout
Hiermee definieert u de fout die is opgetreden.

|Element|Beschrijving|Type|
|-------------|-----------------|----------|
|<a name="error-code" />code|De fout code waarmee de fout categorie wordt aangeduid. Zie [fout codes](#error-codes)voor een lijst met mogelijke codes.|Tekenreeks|
|<a name="error-message" />bericht|Een beschrijving van de fout.|Tekenreeks|
|<a name="error-moredetails" />moreDetails|Een beschrijving die extra informatie biedt over de fout.|Tekenreeks|
|<a name="error-parameter" />para meter|De query parameter in de aanvraag die de fout heeft veroorzaakt.|Tekenreeks|
|<a name="error-subcode" />subcode|De fout code die de fout identificeert. Als `code` bijvoorbeeld InvalidRequest is, kan `subCode` ParameterInvalid of ParameterInvalidValue zijn. |Tekenreeks|
|<a name="error-value" />waarde|De waarde van de query parameter die ongeldig is.|Tekenreeks|

### <a name="errorresponse"></a>ErrorResponse
Het object op het hoogste niveau dat het antwoord bevat wanneer de aanvraag is mislukt.

|Naam|Waarde|Type|
|----------|-----------|----------|
|_type|Type hint.|Tekenreeks|
|<a name="errors" />fouten|Een lijst met fouten die de redenen beschrijven waarom de aanvraag is mislukt.|[Fout](#error)[]|

### <a name="webpage"></a>WebPage
Definieert informatie over een webpagina in de preview-versie.

|Naam|Waarde|Type|
|----------|-----------|----------|
|naam|De pagina titel, niet noodzakelijkerwijs de HTML-titel|Tekenreeks|
|url|De URL die werkelijk is verkend (aanvraag is mogelijk doorlopende omleidingen)|Tekenreeks|
|description|Korte beschrijving van de pagina en inhoud|Tekenreeks|
|isFamilyFriendly|De meest nauw keurige voor items in de web-index; bij realtime ophalen worden deze detectie alleen gebaseerd op de URL en niet op de pagina-inhoud|booleaans|
|primaryImageOfPage/contentUrl|De URL naar een representatieve afbeelding die moet worden meegenomen in de preview-versie|Tekenreeks|

### <a name="identifiable"></a>Persoonlijke
|Naam|Waarde|Type|
|-------------|-----------------|----------|
|id|Een resource-id|Tekenreeks|

## <a name="error-codes"></a>Foutcodes

Hier volgen de mogelijke HTTP-status codes die een aanvraag retourneert.

|Statuscode|Beschrijving|
|-----------------|-----------------|
|200|Geslaagd.|
|400|Een van de query parameters ontbreekt of is ongeldig.|
|400|Server Error, subcode ResourceError: de aangevraagde URL kan niet worden bereikt|
|400|Server Error, subcode ResourceError: de aangevraagde URL heeft geen succes code geretourneerd (inclusief als HTTP 404 is geretourneerd)|
|400|InvalidRequest, subcode geblokkeerd: de aangevraagde URL bevat mogelijk inhoud voor volwassenen en is geblokkeerd|
|401|De abonnements sleutel ontbreekt of is ongeldig.|
|403|De gebruiker is geverifieerd (bijvoorbeeld omdat er een geldige abonnements sleutel is gebruikt), maar ze hebben geen machtiging voor de aangevraagde resource.<br /><br /> Bing kan ook deze status retour neren als de aanroeper het quotum voor query's per maand heeft overschreden.|
|410|De aanvraag heeft HTTP gebruikt in plaats van het HTTPS-protocol. HTTPS is het enige ondersteunde protocol.|
|429|De aanroeper heeft het quotum van de query's per seconde overschreden.|
|500|Onverwachte server fout.|

Als de aanvraag mislukt, bevat het antwoord een [ErrorResponse](#errorresponse) -object, dat een lijst bevat met [fout](#error) objecten die beschrijven wat de oorzaak van het probleem is. Als de fout is gerelateerd aan een para meter, identificeert het `parameter` veld de para meter die het probleem vormt. En als de fout is gerelateerd aan een parameter waarde, identificeert het `value` veld de waarde die niet geldig is.

```json
{
  "_type": "ErrorResponse",
  "errors": [
    {
      "code": "InvalidRequest",
      "subCode": "ParameterMissing",
      "message": "Required parameter is missing.",
      "parameter": "q"
    }
  ]
}

{
  "_type": "ErrorResponse",
  "errors": [
    {
      "code": "InvalidAuthorization",
      "subCode": "AuthorizationMissing",
      "message": "Authorization is required.",
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

Hier volgen de mogelijke fout code en waarden voor de subfout code.

|Code|SubCode|Beschrijving
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Niet geïmplementeerd|HTTP-status code is 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>#|Bing retourneert InvalidRequest wanneer een deel van de aanvraag ongeldig is. Een vereiste para meter ontbreekt bijvoorbeeld of een parameter waarde is niet geldig.<br/><br/>Als de fout ParameterMissing of ParameterInvalidValue is, is de HTTP-status code 400.<br/><br/>Als u het HTTP-protocol gebruikt in plaats van HTTPS, retourneert Bing HttpNotAllowed en de HTTP-status code is 410.
|RateLimitExceeded|Geen subcodes|Bing retourneert RateLimitExceeded wanneer u het quotum voor query's per seconde (QPS) of query's per maand (QPM) overschrijdt.<br/><br/>Als u QPS overschrijdt, retourneert Bing de HTTP-status code 429 en als u QPM overschrijdt, retourneert Bing 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing retourneert InvalidAuthorization wanneer Bing de oproepende functie niet kan verifiëren. De `Ocp-Apim-Subscription-Key`-header ontbreekt bijvoorbeeld of de abonnements sleutel is niet geldig.<br/><br/>Redundantie treedt op als u meer dan één verificatie methode opgeeft.<br/><br/>Als de fout InvalidAuthorization is, is de HTTP-status code 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing retourneert InsufficientAuthorization wanneer de aanroeper geen machtigingen heeft voor toegang tot de resource. Dit kan gebeuren als de abonnements sleutel is uitgeschakeld of is verlopen. <br/><br/>Als de fout InsufficientAuthorization is, is de HTTP-status code 403.

## <a name="next-steps"></a>Volgende stappen
- [Snelstartgids voor C#](csharp.md)
- [Snelstartgids voor Java](java-quickstart.md)
- [Snelstartgids voor JavaScript](javascript.md)
- [Snelstart voor Node](node-quickstart.md)
- [Snelstart voor Python](python-quickstart.md)
