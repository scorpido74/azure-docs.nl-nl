---
title: 'Snelstartgids: zoeken naar afbeeldingen REST API en python-Bing Image Search'
titleSuffix: Azure Cognitive Services
description: Gebruik deze snelstartgids om aanvragen voor het zoeken van afbeeldingen naar de REST API voor Bing Image Search te verzenden met Python en JSON-antwoorden te ontvangen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 12/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: f3d00688feb0f9d42e80cdbb51753483e53c388d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930732"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>Snelstartgids: zoeken naar afbeeldingen met behulp van de Bing Image Search REST API en python

Gebruik deze quickstart om zoekaanvragen naar de Bing Image Search API te leren sturen. Deze Python-toepassing verzendt een zoekquery naar de API en geeft de URL weer van de eerste afbeelding in de resultaten. Hoewel deze toepassing in Python is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

U kunt dit voorbeeld uitvoeren als een Jupyter-notebook op [MyBinder](https://mybinder.org) door te klikken op de badge launch binder:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


De broncode voor dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py) met extra foutafhandeling en aantekeningen.


## <a name="prerequisites"></a>Vereisten

* [Python 2.x of 3.x](https://www.python.org/)
* De [Python Imaging-bibliotheek (PIL)](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw Python-bestand in uw favoriete IDE of editor en importeer de volgende modules. Maak variabelen voor uw abonnementssleutel, zoekeindpunt en een zoekterm. `search_url` kunnen het globale eind punt hieronder zijn of het [aangepaste subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) -eind punt dat wordt weer gegeven in de Azure portal voor uw resource.

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. Voeg uw abonnementssleutel toe aan de koptekst `Ocp-Apim-Subscription-Key` door een woordenlijst te maken en de sleutel als een waarde toe te voegen. 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>Een zoekaanvraag maken en verzenden

1. Maak een woordenlijst voor de parameters van de zoekaanvraag. Voeg de zoekterm toe aan de `q`-parameter. Gebruik "openbaar" voor de `license`-parameter om te zoeken naar afbeeldingen in het openbare domein. Gebruik "foto" voor de `imageType` om alleen te zoeken naar foto's.

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. Gebruik de `requests`-bibliotheek voor het aanroepen van de Bing Image Search-API. Voeg uw koptekst en parameters toe aan de aanvraag en retourneer de reactie als een JSON-object. Haal de Url's op in verschillende miniatuur afbeeldingen van het `thumbnailUrl` veld van de reactie.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
    ```

## <a name="view-the-response"></a>Bekijk het antwoord

1. Maak een nieuwe afbeelding met vier kolommen en vier rijen met behulp van de bibliotheek matplotlib. 

2. Doorloop de rijen en kolommen van de afbeelding en gebruik de methode `Image.open()` van de PIL-bibiotheek om een miniatuur van de afbeelding toe te voegen aan elke ruimte. 

3. Gebruik `plt.show()` voor het tekenen van de afbeelding en om de afbeeldingen weer te geven.

    ```python
    f, axes = plt.subplots(4, 4)
    for i in range(4):
        for j in range(4):
            image_data = requests.get(thumbnail_urls[i+4*j])
            image_data.raise_for_status()
            image = Image.open(BytesIO(image_data.content))        
            axes[i][j].imshow(image)
            axes[i][j].axis("off")
    plt.show()
    ```


## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord

Antwoorden die afkomstig zijn van de Bing Afbeeldingen zoeken-API worden geretourneerd in de JSON-indeling. Dit voorbeeldantwoord is ingekort zodat één resultaat wordt weergegeven.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie voor app met één pagina voor Bing Image Search](../tutorial-bing-image-search-single-page-app.md)

* [Wat is de Bing Image Search-API?](../overview.md)  
* [Prijsinformatie](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) voor Bing Search-API's. 
* [Gratis Cognitive Services-toegangssleutel ophalen](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Documentatie van Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Naslag voor Bing Afbeeldingen zoeken-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
