---
title: 'Zelf studie: afbeeldings gegevens extra heren met C# de rest API en-Bing Image Search'
titleSuffix: Azure Cognitive Services
description: Gebruik deze zelf studie om een C# toepassing te maken die afbeeldings Details extraheert met behulp van de Bing afbeeldingen zoeken-API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 12/06/2019
ms.author: aahi
ms.openlocfilehash: 69c5fc9805bed8fdfde3dd208e7fb66254e82c3c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75383872"
---
# <a name="tutorial-extract-image-details-using-the-bing-image-search-api-and-c"></a>Zelfstudie: Afbeeldingsdetails extraheren met de Bing Afbeeldingen zoeken-API en C#

Er zijn meerdere [eindpunten](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint) beschikbaar via de Bing Afbeeldingen zoeken-API. Het eindpunt `/details` accepteert een POST-aanvraag met een afbeelding en kan uiteenlopende details over de afbeelding retourneren. Deze C#-toepassing verzendt een afbeelding met behulp van deze API en toont de details die door Bing worden geretourneerd. Daarbij gaat het om JSON-objecten, zoals de volgende:

![[JSON-resultaten]](media/cognitive-services-bing-images-api/jsonResult.jpg)

In deze zelfstudie wordt het volgende uitgelegd:

> [!div class="checklist"]
> * Het eindpunt `/details` voor Afbeeldingen zoeken gebruiken in een `POST`-aanvraag
> * Headers voor de aanvraag opgeven
> * URL-parameters gebruiken om resultaten op te geven
> * De afbeeldingsgegevens uploaden en de `POST`-aanvraag verzenden
> * De JSON-resultaten op de console afdrukken

De broncode voor dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingGetSimilarImages.cs).

## <a name="prerequisites"></a>Vereisten

* Een versie van [Visual studio 2017 of hoger](https://visualstudio.microsoft.com/downloads/).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="construct-an-image-details-search-request"></a>Een zoekaanvraag voor afbeeldingsdetails opstellen

Hieronder gaat het op het eindpunt `/details`, dat POST-aanvragen accepteert met afbeeldingsgegevens in de hoofdtekst van de aanvraag. U kunt het volgende globale eind punt gebruiken of het [aangepaste subdomein](../../cognitive-services/cognitive-services-custom-subdomains.md) -eind punt dat wordt weer gegeven in de Azure portal voor uw resource.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```

Bij het opstellen van de zoekaanvraag-URL volgt de parameter `modules` het bovenstaande eindpunt. De parameter specificeert ook het type details dat de resultaten zullen bevatten:

* `modules=All`
* `modules=RecognizedEntities` (zichtbare personen of plaatsen in de afbeelding)

Geef `modules=All` in de POST-aanvraag op om JSON-tekst te krijgen die het volgende bevat:

* `bestRepresentativeQuery` - een Bing-query die afbeeldingen retourneert die lijken op de geüploade afbeelding
* `detectedObjects` - gevonden objecten in de afbeelding
* `image` - metagegevens voor de afbeelding
* `imageInsightsToken` - een token voor latere GET-aanvragen die `RecognizedEntities` (zichtbare personen of plaatsen in de afbeelding) krijgen vanuit de afbeelding.
* `imageTags` - tags voor de afbeelding
* `pagesIncluding` - webpagina's die de afbeelding bevatten
* `relatedSearches` - zoekopdrachten op basis van details in de afbeelding
* `visuallySimilarImages` - vergelijkbare afbeeldingen op het web

Geef `modules=RecognizedEntities` op in de POST-aanvraag om alleen `imageInsightsToken` te krijgen, wat gebruikt kan worden in een daaropvolgende GET-aanvraag om personen of plaatsen in de afbeelding te identificeren.

## <a name="create-a-webclient-object-and-set-headers-for-the-api-request"></a>Een WebClient-object maken en headers voor de API-aanvraag instellen

Maak een `WebClient`-object en stel de headers in. Alle aanvragen aan de Bing Zoeken-API vereisen een `Ocp-Apim-Subscription-Key`. Een `POST`-aanvraag voor het uploaden van een afbeelding moet ook `ContentType: multipart/form-data` opgeven.

```javascript
WebClient client = new WebClient();
client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
client.Headers["ContentType"] = "multipart/form-data";
```

## <a name="upload-the-image-and-display-the-results"></a>De afbeelding uploaden en de resultaten weergeven

De methode `UpLoadFile()` van de klasse `WebClient` maakt gegevens op voor de `POST`-aanvraag, inclusief het opmaken van `RequestStream` en het aanroepen van `HttpWebRequest`.

Roep `WebClient.UpLoadFile()` aan met het eindpunt `/details` en het afbeeldingsbestand dat moet worden geüpload. Gebruik het JSON-antwoord om een instantie van de `SearchResult`-structuur te initialiseren, en sla het antwoord op.

```javascript        
const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";
// The image to upload. Replace with your file and path.
const string imageFile = "your-image.jpg";
byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
var json = System.Text.Encoding.Default.GetString(resp);
// Create result object for return
var searchResult = new SearchResult()
{
    jsonResult = json,
    relevantHeaders = new Dictionary<String, String>()
};
```
Dit JSON-antwoord kan vervolgens worden afgedrukt op de console.

## <a name="use-an-image-insights-token-in-a-request"></a>Een image insights token gebruiken in een aanvraag

Om de `ImageInsightsToken` te gebruiken die met de resultaten van een `POST` is geretourneerd, kunt u deze toevoegen aan een `GET`-aanvraag. Bijvoorbeeld:

```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```

Als de afbeelding identificeerbare personen of plaatsen bevat, zal deze aanvraag informatie daarover retourneren.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Afbeeldingen en zoekopties weergeven in een webtoepassing van één pagina ](tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Zie ook

* [Naslag voor Bing Afbeeldingen zoeken-API](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
