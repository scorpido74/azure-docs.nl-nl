---
title: Zoekcategorieën voor de Bing Local Business Search API
titleSuffix: Azure Cognitive Services
description: Gebruik dit artikel voor meer informatie over het opgeven van zoekcategorieën voor het doelpunt voor zoeken naar bing Local Business Search API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 56b94d66eb0929d2fd0ca74a1a631d229330adfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "69906401"
---
# <a name="search-categories-for-the-bing-local-business-search-api"></a>Zoekcategorieën voor de Bing Local Business Search API

Met de Bing Local Business Search API u zoeken naar lokale bedrijfsentiteiten in verschillende categorieën, waarbij prioriteit wordt gegeven aan resultaten die de locatie van een gebruiker sluiten. U deze zoekopdrachten opnemen in `localCircularView` `localMapView` zoekopdrachten samen met de parameters en [parameters.](specify-geographic-search.md)


## <a name="toplevel-categories"></a>Toplevel-categorieën 

In de volgende typen worden belangrijke zoekcategorieën gedefinieerd.  Er kunnen meer dan één categorie worden opgegeven met `localCategories` behulp van een door komma's afgebakende lijst die aan de parameter is toegewezen.  
- EatDrink 
- SeeDo 
- Winkel 
- HotelsEnMotels 
- Banken en kredietunies 
- Parkeren 
- Ziekenhuizen 

## <a name="sub-categories"></a>Subcategorieën
Subcategorieën worden op dezelfde `localCategories`manier doorgegeven als . Subcategorieën zijn meer specifieke categorieën. Ze zijn ondergeschikt in de zin dat als u een categorie C en een van de subcategorieën S opgeeft in dezelfde lijst met komma's, u dezelfde resultaten ontvangt als wanneer u C alleen hebt opgegeven.

### <a name="eat-drink"></a>Eet drank 
|  |  |  |  |
| - | - | - | - |
| BrouwerijenEnBrewPubs | CocktailLounges | AfricanRestaurants |
| AmericanRestaurants | Bagels | BarbecueRestaurants |
| Tavernes | SportsBars (Sportbars) | Bars |
| BarsGrillsAndPubs BarsGrillsAndPubs | BuffetRestaurants| BelgianRestaurants | 
| BritishRestaurants | CafeRestaurants | CaribbeanRestaurants |
| ChineesRestaurants | KoffieAndTea | Delicatessenwinkels | 
| DeliveryService | Diners | DiscountStores | 
| Donuts | Fastfood | FransRestaurants | 
| BevrorenYoghurt | GermanRestaurants | Supermarkten | 
| GreekRestaurants | Kruideniers | HawaiianRestaurants | 
| HongaarseRestaurants | IJsen bevrorendesserts | IndianRestaurants | 
| ItalianRestaurants | JapansRestaurants | Sappen | 
| KoreanRestaurants | Slijterijen | MexicaanRestaurants |
| Midden-OostenRestaurants | Pizza | PolishRestaurants | 
| PortugeesRestaurants | Pretzels | Restaurants | 
| Russisch- en Oekraïensrestaurants | Sandwiches | SeafoodRestaurants | 
| SpaansRestaurants | SteakHouseRestaurants | SushiRestaurants | 
| Afhaalmaaltijden | ThaiRestaurants | TurkishRestaurants | 
| Vegetarische en veganistische restaurants | VietnamezenRestaurants|  |
 
### <a name="see-do"></a>Zie Do 
|  |  |  |
| -- | -- | -- |
| Pretparken | Attracties | Carnavals |
| Casinos | LandmarksAndHistoricalSites | Miniaturegolfcursussen |
| Bioscopen | Musea | Parken |
| SightseeingTours | TouristInformation | Dierentuinen |
 
### <a name="shop"></a>Winkel 
|  |  |  |
| -- | -- | -- |
| Antiekwinkels | Boekhandels | CDAndRecordStores |
| KinderkledingWinkels | Sigaren- en tabakswinkels | ComicBookStores |
| Warenhuizen | DiscountStores | Vlooienmarkten en bazaars |
| Meubelwinkels | HomeImprovementStores | JewelryAndWatchesStores |
| KeukengereiWinkels | Slijterijen | WinkelcentraEnwinkelcentra |
| Herenkledingwinkels | MusicStores | OutletStores OutletStores |
| PetShops | PetSupplyStores | SchoolandOfficeSupplyStores |
| Schoenenwinkels | SportingGoodsStores | ToyAndGameStores |
| VitaminandSupplementStores | WomensClothingStores |  |


## <a name="examples-of-local-categories-search"></a>Voorbeelden van zoeken naar lokale rubrieken

In de volgende voorbeelden worden `localCategories` resultaten op basis van de parameter gegeven:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=HotelsAndMotels`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=EatDrink`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Shop`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Hospitals`

De volgende query beperkt het aantal 'ziekenhuis' resultaten tot de eerste drie geretourneerd uit de Bing Local Business Search API:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localCategories=Hospitals&count=3&offset=0`

Het volgende voorbeeld JSON reactie omvat drie ziekenhuizen in de grotere omgeving van Seattle:

```json
BingAPIs-TraceId: 68AFB51807C6485CAB8AAF20E232EFFF
BingAPIs-SessionId: F89E7B8539B34BF58AAF811485E83B20
X-MSEdge-ClientID: 1C44E64DBFAA6BCA1270EADDBE7D6A22
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 68AFB51807C6485CAB8AAF20E232EFFF Ref B: CO1EDGE0108 Ref C: 2018-10-22T18:52:28Z

{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": ""
   },
   "places": {
      "readLink": "https:\/\/www.bingapis.com\/api\/v7\/places\/search?q=",
      "totalEstimatedMatches": 0,
      "value": [
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.0",
            "name": "Overlake Hospital Medical Center",
            "url": "http:\/\/www.overlakehospital.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6204986572266,
               "longitude": -122.185829162598
            },
            "routablePoint": {
               "latitude": 47.6204986572266,
               "longitude": -122.185668945312
            },
            "address": {
               "streetAddress": "1035 116th Ave NE",
               "addressLocality": "Bellevue",
               "addressRegion": "WA",
               "postalCode": "98004",
               "addressCountry": "US",
               "neighborhood": "",
               "text": "1035 116th Ave NE, Bellevue, WA, 98004"
            },
            "telephone": "(425) 688-5000"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.1",
            "name": "Virginia Mason University Village Medical Center",
            "url": "https:\/\/virginiamason.org\/Seattle",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6095390319824,
               "longitude": -122.327941894531
            },
            "routablePoint": {
               "latitude": 47.6093978881836,
               "longitude": -122.328277587891
            },
            "address": {
               "streetAddress": "1100 9th Ave",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98101",
               "addressCountry": "US",
               "neighborhood": "University District",
               "text": "1100 9th Ave, Seattle, WA, 98101"
            },
            "telephone": "(206) 223-6600"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.2",
            "name": "Seattle Children’s Hospital",
            "url": "http:\/\/www.seattlechildrens.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6625061035156,
               "longitude": -122.283760070801
            },
            "routablePoint": {
               "latitude": 47.6631507873535,
               "longitude": -122.284614562988
            },
            "address": {
               "streetAddress": "4800 Sand Point Way NE",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98105",
               "addressCountry": "US",
               "neighborhood": "Laurelhurst",
               "text": "4800 Sand Point Way NE, Seattle, WA, 98105"
            },
            "telephone": "(206) 987-2000"
         }
      ],
      "searchAction": {

      }
   }
}
```

## <a name="next-steps"></a>Volgende stappen
- [Geografische zoekgrenzen](specify-geographic-search.md)
- [Query en antwoord](local-search-query-response.md)
- [Snel starten in C #](quickstarts/local-quickstart.md)
