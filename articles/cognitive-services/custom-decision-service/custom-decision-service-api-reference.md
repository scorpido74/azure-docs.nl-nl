---
title: API-verwijzing-Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Een volledige API-hand leiding voor Custom Decision Service.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 569a1c83562a995f15e12013c864ef4c0447d963
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161691"
---
# <a name="api"></a>API

Azure Custom Decision Service biedt twee Api's die voor elke beslissing worden aangeroepen: de [classificatie-API](#ranking-api) voor het invoeren van de rang orde van acties en de [belonings-API](#reward-api) voor het uitvoeren van de beloning. Daarnaast geeft u een [Actions set API](#action-set-api-customer-provided) op om de acties op te geven voor Azure Custom decision service. In dit artikel komen de volgende drie Api's aan bod. Hieronder ziet u een typisch scenario dat wordt weer gegeven wanneer Custom Decision Service de rang orde van artikelen optimaliseert.

## <a name="ranking-api"></a>Classificatie-API

De classificatie-API maakt gebruik van een standaard [Jsonp](https://en.wikipedia.org/wiki/JSONP)-communicatie patroon om latentie te optimaliseren en het [beleid van dezelfde oorsprong](https://en.wikipedia.org/wiki/Same-origin_policy)over te slaan. De laatste verbieden dat Java script gegevens ophaalt van buiten de oorsprong van de pagina.

Voeg dit fragment in de HTML-kop van uw front page in (waarbij een persoonlijke lijst met artikelen wordt weer gegeven):

```html
// define the "callback function" to render UI
<script> function callback(data) { … } </script>
// "data" provides the ranking of URLs, see example below.

// call to Ranking API
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>?<parameters>" async></script>
// action set id is the name of the corresponding RSS/Atom feed.

// same call with multiple action sets:
// <script src="https://ds.microsoft.com/api/v2/<appId>/rank/<A1>/<A2>/.../<An>?<parameters>" async></script>
```

> [!IMPORTANT]
> De call back-functie moet worden gedefinieerd vóór de aanroep van de classificatie-API.

> [!TIP]
> Om latentie te verbeteren, wordt de classificatie-API weer gegeven via HTTP in plaats van HTTPS, zoals in `https://ds.microsoft.com/api/v2/<appId>/rank/*`.
> Een HTTPS-eind punt moet echter worden gebruikt als de front page wordt bediend via HTTPS.

Wanneer para meters niet worden gebruikt, is het HTTP-antwoord van de classificatie-API een JSONP teken reeks:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

Deze tekenreeks wordt vervolgens in de browser uitgevoerd als een aanroep naar de functie `callback()`.

De para meter voor de functie call back in het voor gaande voor beeld heeft het volgende schema:

- `ranking` biedt de rang schikking van Url's die moeten worden weer gegeven.
- `eventId` wordt intern gebruikt door Custom Decision Service om deze classificatie te vergelijken met de bijbehorende klikken.
- `appId` kunt de call back-functie gebruiken om onderscheid te maken tussen meerdere toepassingen van Custom Decision Service die op dezelfde webpagina worden uitgevoerd.
- `actionSets` een lijst met elke actie die in de classificatie-API-aanroep wordt gebruikt, samen met de UTC-tijds tempel van de laatste geslaagde vernieuwing. Custom Decision Service de Actions-feeds regel matig vernieuwt. Als sommige actie sets bijvoorbeeld niet actueel zijn, moet de call back-functie mogelijk terugvallen op de standaard positie.

> [!IMPORTANT]
> De opgegeven actie sets worden verwerkt en kunnen eventueel worden verwijderd om de standaard positie van artikelen te vormen. De standaard volgorde wordt vervolgens opnieuw geordend en geretourneerd in het HTTP-antwoord. De standaard classificatie wordt hier gedefinieerd:
>
> - Binnen elke Actieset worden de artikelen verwijderd naar de 15 meest recente artikelen (indien meer dan 15 worden geretourneerd).
> - Wanneer er meerdere actie sets worden opgegeven, worden deze samengevoegd in dezelfde volg orde als in de API-aanroep. De oorspronkelijke volg orde van de artikelen wordt binnen elke actie set bewaard. Dubbele waarden worden verwijderd uit de eerdere exemplaren.
> - De eerste `n` artikelen worden opgeslagen in de samengevoegde lijst met artikelen, waarbij `n=20` standaard is.

### <a name="ranking-api-with-parameters"></a>Classificatie-API met para meters

Met de classificatie-API kunt u deze para meters:

- `details=1` en `details=2` voegen extra informatie toe over elk artikel dat wordt vermeld in `ranking`.
- met `limit=<n>` geeft u het maximale aantal artikelen in de standaard volgorde op. `n` moet tussen `2` en `30` zijn (of anders wordt het afgekapt op respectievelijk `2` of `30`).
- `dnt=1` schakelt gebruikers cookies uit.

Para meters kunnen worden gecombineerd met de syntaxis van de query teken reeks, bijvoorbeeld `details=2&dnt=1`.

> [!IMPORTANT]
> De standaard instelling in Europa moet `dnt=1` totdat de klant akkoord gaat met de cookie banner. Dit moet ook de standaard instelling zijn voor websites die gericht zijn op minder jarigen. Zie de [gebruiksrecht overeenkomst](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804)voor meer informatie.

Het `details=1` element voegt de `guid`van elk artikel in, als het wordt bediend door de Action set API. Het HTTP-antwoord:

```json
callback({
   "ranking":[{"id":"url1","details":[{"guid":"123"}]},
              {"id":"url2","details":[{"guid":"456"}]},
              {"id":"url3","details":[{"guid":"789"}]}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

Het `details=2`-element voegt meer details toe die Custom Decision Service kunnen worden geëxtraheerd uit de [parametrisatie-code](https://github.com/Microsoft/mwt-ds/tree/master/Crawl)van het bestand SEO Meta Tags:

- `title` van `<meta property="og:title" content="..." />` of `<meta property="twitter:title" content="..." />` of `<title>...</title>`
- `description` van `<meta property="og:description" ... />` of `<meta property="twitter:description" content="..." />` of `<meta property="description" content="..." />`
- `image` van `<meta property="og:image" content="..." />`
- `ds_id` van `<meta name="microsoft:ds_id" content="..." />`

Het HTTP-antwoord:

```json
callback({
   "ranking":[{"id":"url1","details":<details>},
              {"id":"url2","details":<details>},
              {"id":"url3","details":<details>}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

Het element `<details>`:

```json
[{"guid":"123"}, {"description":"some text", "ds_id":"234", "image":"ImageUrl1", "title":"some text"}]
```

## <a name="reward-api"></a>Belonings-API

Custom Decision Service gebruikt alleen klikken op de bovenste sleuf. Elke klik wordt geïnterpreteerd als een beloning van 1. Het ontbreken van een klik wordt geïnterpreteerd als een beloning van 0. Klikken worden in overeenstemming met de bijbehorende classificaties met behulp van gebeurtenis-Id's die worden gegenereerd door de [classificatie-API](#ranking-api) -aanroep. Indien nodig kunnen gebeurtenis-Id's worden door gegeven via sessie cookies.

Als u een klik op de bovenste sleuf wilt afhandelen, plaatst u deze code op uw front page:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Hier `data` is het argument voor de functie `callback()`, zoals eerder is beschreven. Het gebruik van `data` in de code voor het klikken op afhandelen vereist enige zorg. In deze [zelf studie](custom-decision-service-tutorial-news.md#use-the-apis)wordt een voor beeld weer gegeven.

Voor alleen testen kan de belonings-API via [krul](https://en.wikipedia.org/wiki/CURL)worden aangeroepen:

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

Het verwachte effect is een HTTP-antwoord van 200 (OK). U kunt de beloning van 1 voor deze gebeurtenis zien in het logboek (als er een Azure Storage-account is opgegeven op de portal).

## <a name="action-set-api-customer-provided"></a>Action set-API (door de klant opgegeven)

Op hoog niveau retourneert de Action set-API een lijst met artikelen (acties). Elk artikel wordt opgegeven door de URL en (optioneel) de artikel titel en de publicatie datum. U kunt verschillende actie sets opgeven op de portal. U moet voor elke Actieset een andere actions set API gebruiken als een afzonderlijke URL.

Elke actions set-API kan op twee manieren worden geïmplementeerd: als een RSS-feed of als een Atom-feed. Een van beide moet voldoen aan de standaard en retourneert een juist XML-bestand. Hier volgt een voor beeld van RSS:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <guid>guid (could be a your internal database id)</guid>
      <pubDate>date</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Elk `<item>` element op het hoogste niveau beschrijft een actie:

- `<link>` is verplicht en wordt gebruikt als actie-ID.
- `<date>` wordt genegeerd als het minder is dan of gelijk is aan 15 items; anders is het verplicht.
  - Als er meer dan 15 items zijn, worden de vijf tien meest recente bestanden gebruikt.
  - Het moet de standaard indeling voor RSS of Atom hebben:
    - [RFC 822](https://tools.ietf.org/html/rfc822) voor RSS: bijvoorbeeld `"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) voor Atom: bijvoorbeeld `"2016-12-19T16:39:57-08:00"`
- `<title>` is optioneel en wordt gebruikt voor het genereren van functies die het artikel beschrijven.
- `<guid>` is optioneel en door het systeem door gegeven aan de call back-functie (als de para meter `?details` is opgegeven in de aanroep van de classificatie-API).

Andere elementen in een `<item>` worden genegeerd.

De versie van de Atom-feed maakt gebruik van dezelfde XML-syntaxis en conventies.

> [!TIP]
> Als uw systeem zijn eigen artikel-Id's gebruikt, kunnen ze worden door gegeven aan de call back-functie met behulp van `<guid>`.
