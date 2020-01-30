---
title: Efficiënt zoeken met behulp van de Azure Maps Search Service | Microsoft Azure kaarten
description: Meer informatie over het Toep assen van best practices voor de Search-service met behulp van Microsoft Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 68c7408f13027ded7beaabf46fb663217a90c52b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845747"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Aanbevolen procedures voor het gebruik van Azure Maps Search Service

Azure Maps [Search service](https://docs.microsoft.com/rest/api/maps/search) bevat api's met verschillende mogelijkheden. Bijvoorbeeld, de zoek-API voor adressen wordt gebruikt om te zoeken naar belang rijke punten (POI) of gegevens over een specifieke locatie. In dit artikel worden de geluids procedures beschreven die moeten worden toegepast wanneer u gegevens aanroept vanuit Azure Maps-Zoek Services. U leert het volgende:

* Query's bouwen om relevante overeenkomsten te retour neren
* Zoek resultaten beperken
* Meer informatie over het verschil tussen verschillende resultaat typen
* Lees de structuur van het antwoord op de zoek opdracht


## <a name="prerequisites"></a>Vereisten

Als u wilt aanroepen naar de Maps service-Api's, hebt u een Azure Maps-account en een sleutel nodig. Als dat het geval is, volgt u de instructies in [een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) en [een primaire sleutel ophalen](quick-demo-map-app.md#get-the-primary-key-for-your-account). Zie [verificatie beheren in azure Maps](./how-to-manage-authentication.md)voor meer informatie over verificatie in azure Maps.

> [!Tip]
> Als u een query wilt uitvoeren op de zoek service, kunt u de [app postman](https://www.getpostman.com/apps) gebruiken om rest-aanroepen te bouwen of kunt u elke gewenste API-ontwikkel omgeving gebruiken.


## <a name="best-practices-for-geocoding-address-search"></a>Aanbevolen procedures voor geocodering (adres zoekopdracht)

Wanneer u zoekt naar een volledig of gedeeltelijk adres met behulp van Azure Maps Search Service, leest de API tref woorden uit uw zoek opdracht en worden de lengte-en breedte coördinaten van het adres geretourneerd. Dit proces wordt geocodering genoemd. De mogelijkheid tot Geocode in een land is afhankelijk van de dekking van de weg en de geocoderings nauwkeurigheid van de geocoderings service.

Zie [geocoderings dekking](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) voor meer informatie over Azure Maps mogelijkheden voor geocodering per land/regio.

### <a name="limit-search-results"></a>Zoek resultaten beperken

 Azure Maps Search-API kan u helpen de zoek resultaten op de juiste manier te beperken, zodat u relevante gegevens kunt weer geven voor uw gebruikers.

   > [!Note]
   > Niet alle door Search Api's ondersteunde para meters worden hieronder weer gegeven

   **Zoek resultaten geo-bias**

   Als u de resultaten van uw gebruiker naar het relevante gebied wilt verduidelijken, moet u altijd de Maxi maal mogelijke gedetailleerde locatie-invoer toevoegen. Als u de zoek resultaten wilt beperken, kunt u overwegen de volgende invoer typen toe te voegen:

   1. Stel de para meter `countrySet` in, bijvoorbeeld ' VS, FR '. Het standaard zoek gedrag is het doorzoeken van de hele wereld, waardoor mogelijk onnodige resultaten worden geretourneerd. Als uw query de para meter `countrySet` heeft, kan de zoek opdracht onnauwkeurige resultaten retour neren. Als u bijvoorbeeld zoekt naar een stad met de naam **Bellevue** , worden de resultaten uit VS en Frank rijk geretourneerd, omdat er steden zijn met de naam **Bellevue** in de Frank rijk en de Verenigde Staten.

   2. U kunt de para meters `btmRight` en `topleft` gebruiken om het begrenzingsvak in te stellen om de zoek opdracht te beperken tot een bepaald gebied op de kaart.

   3. Als u het relevantie gebied voor de resultaten wilt beïnvloeden, kunt u de para meters `lat`en `lon` coördinaat definiëren en de RADIUS van het zoek gebied instellen met behulp van de `radius` para meter.


   **Zoek parameters voor fuzzy**
   
  Azure Maps [fuzzy Search-API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) is de aanbevolen service die u kunt gebruiken wanneer u niet weet wat uw gebruikers invoer zijn voor een zoek opdracht. De API combineert POI-Zoek resultaten (Point of interest) en Geocode ring in een canonieke *Zoek opdracht met één regel*.

   1. De `minFuzzyLevel` en `maxFuzzyLevel` helpen relevante overeenkomsten te retour neren, zelfs wanneer de query parameters niet precies overeenkomen met de gewenste gegevens. Voor het verkrijgen van prestaties en het verminderen van ongebruikelijke resultaten, standaard zoekopdracht query's voor `minFuzzyLevel=1` en `maxFuzzyLevel=2`. Bekijk een voor beeld van een zoek term ' restrant ', die overeenkomt met ' restaurant ' wanneer de `maxFuzzyLevel` is ingesteld op 2. De standaard niveaus voor fuzzy kunnen worden overschreven als dat nodig is.  

   2. U kunt ook prioriteit geven aan de exacte set resultaat typen die moeten worden geretourneerd met behulp van de para meter `idxSet`. U kunt voor dit doel een door komma's gescheiden lijst met indexen verzenden. de volg orde van de items is niet van belang. De volgende indexen worden ondersteund:

       * `Addr` - adresbereiken: voor sommige straten zijn er adres punten, die worden geïnterpoleerd vanaf het begin en het einde van de straat. Deze punten worden weer gegeven als adresbereiken.
       * `Geo` - **geographs**: gebieden op een kaart die de administratieve divisie van een land vertegenwoordigen, dat wil zeggen land, staat, plaats.
       * `PAD` - **punt adres**: punten op een kaart waarbij een specifiek adres met een straat naam en nummer kan worden gevonden in een index, bijvoorbeeld Soquel Dr 2501. Deze idxSet waarde is het hoogste nauwkeurigheids niveau dat beschikbaar is voor adressen.  
       * `POI` - e interessante **punten**: punten op een kaart die aandacht best Eden en mogelijk interessant zijn.  [Zoek adres ophalen](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) retourneert niet poi's.  
       * `Str` - **Streets**: de weer gave van straten op de kaart.
       * `XStr` - **Kruis straten/snij punten**: weer gave van verbindings lijnen; plaatsen waar twee Streets INTERSECT.


       **Voor beelden van gebruik**:

       * idxSet = POI (alleen zoeken naar interessante punten) 

       * idxSet = PAD, addr (alleen adressen zoeken, PAD = punt adres, addr = adres bereik)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Filter voor reverse Geocode en geografie van het entiteits type

Bij het uitvoeren van een reverse Geocode Search met [Search Address reverse API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), heeft de service de mogelijkheid om veelhoeken te retour neren voor de administratieve gebieden. Door de para meter op te geven `entityType` in de aanvraag, kunt u de zoek actie beperken voor opgegeven geografie typen. De resulterende reactie bevat de geografie-ID en het entiteits type dat overeenkomt. Als u meer dan één entiteit opgeeft, wordt de **kleinste beschik bare entiteit**door het eind punt geretourneerd. De geretourneerde geometrie-ID kan worden gebruikt om de geometrie van die geografie op te halen via de service voor het [ophalen van polygoon](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Voorbeeld aanvraag:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Antwoord:**

```JSON
{
    "summary": {
        "queryTime": 14,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                },
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="search-results-language"></a>Taal van zoek resultaten

Met de para meter `language` kunt u de taal van de resultaten selecteren die wordt geretourneerd door de API. Als de taal niet is ingesteld in de aanvraag, wordt de zoek service automatisch ingesteld op de meest voorkomende taal in het land/de regio. Wanneer de gegevens in de opgegeven taal niet beschikbaar zijn, wordt ook de standaard taal gebruikt. Zie [ondersteunde talen](https://docs.microsoft.com/azure/azure-maps/supported-languages) voor een lijst met ondersteunde talen van Azure Maps services per land/regio.


### <a name="predictive-mode-autosuggest"></a>Voorspellende modus (Automatische suggestie)

`typeahead` para meter moet worden ingesteld op ' True ' om meer overeenkomsten voor gedeeltelijke query's te vinden. De query wordt geïnterpreteerd als een gedeeltelijke invoer en de voor Spellings modus wordt in de zoek opdracht weer geven. Anders gaat de service ervan uit dat alle relevante gegevens zijn door gegeven in.

In de onderstaande voorbeeld query kunt u zien dat de Search-adres service wordt opgevraagd voor ' Microsoft ', waarbij de para meter `typeahead` is ingesteld op **True**. Als u het antwoord ziet, kunt u zien dat de zoek service de query als gedeeltelijke query heeft geïnterpreteerd. Het antwoord bevat resultaten voor de automatisch voorgestelde query.

**Voorbeeldquery:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**Antwoord:**

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>URI-code ring voor het afhandelen van speciale tekens 

U moet de URI coderen voor het afhandelen van speciale tekens in het adres om Cross Street-adressen te vinden. Bekijk dit voor beeld van dit adres: "1ste stap & vakbonds straat Seattle". Het speciale teken ' & ' moet worden versleuteld voordat de aanvraag wordt verzonden. We raden aan om teken gegevens te coderen in een URI, waarbij alle tekens worden gecodeerd met een%-teken en een hexadecimale waarde van twee tekens die overeenkomt met het UTF-8-teken.

**Voor beelden van gebruik**:

Zoek adres ophalen:

```
query=1st Avenue & E 111th St, New York
```

 moet worden gecodeerd als:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


Hier volgen de verschillende methoden voor het gebruik van verschillende talen: 

Java script-type script:
```Javascript
encodeURIComponent(query)
```

C#/VB:
```csharp
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python:
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++:
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP:
```PHP
urlencode(query)
```

Ruby
```Ruby
CGI::escape(query) 
```

Swift
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Gebleven
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Aanbevolen procedures voor POI Search

Met POI-Zoek opdrachten (Points of interest) kunt u POI-resultaten aanvragen op naam, bijvoorbeeld zoek naar bedrijf op naam. We raden u ten zeerste aan om de para meter `countrySet` te gebruiken voor het opgeven van landen waar uw toepassing behoefte heeft. het standaard gedrag is om de hele wereld te doorzoeken, mogelijk onnodige resultaten te retour neren en/of tot een langere Zoek tijd.

### <a name="brand-search"></a>Merk zoeken

Ter verbetering van de relevantie van de resultaten en de informatie in het antwoord, POI-Zoek antwoord (Point of interest) bevat de merk gegevens die verder kunnen worden gebruikt om de reactie te parseren.

U kunt ook een door komma's gescheiden lijst met merk namen verzenden in de aanvraag. U kunt de lijst gebruiken om de resultaten te beperken tot specifieke merken met behulp van de para meter `brandSet`. De volg orde van items is niet van belang. Wanneer er meerdere merken worden opgegeven, worden alleen de resultaten geretourneerd die horen bij (ten minste) een van de opgegeven lijsten.

We gaan een zoek aanvraag voor een [POI categorie](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) maken voor de stations in de buurt van micro soft campus (REDMOND, WA). Als u het antwoord ziet, kunt u de merk gegevens voor elke geretourneerde POI bekijken.


**Voorbeeldquery:**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**Antwoord:**

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Vlieg veld zoeken

POI Search biedt ondersteuning voor het zoeken naar lucht havens door gebruik te maken van officiële luchthaven codes. Bijvoorbeeld **Sea** (Seattle-Tacoma internationale lucht haven). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>In de buurt zoeken

Als u alleen POI resultaten wilt ophalen rond een specifieke locatie, is de [zoek-API](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) in de buurt mogelijk de juiste keuze. Dit eind punt retourneert alleen POI-resultaten en levert geen zoek query parameter op. Als u de resultaten wilt beperken, is het raadzaam om de RADIUS in te stellen.

## <a name="understanding-the-responses"></a>Meer informatie over de antwoorden

We gaan een aanvraag voor het zoeken naar een adres aanvragen bij de Azure Maps [Search-service](https://docs.microsoft.com/rest/api/maps/search) voor een adres in Seattle. Als u de onderstaande aanvraag-URL zorgvuldig bekijkt, moet u de para meter `countrySet` op **ons** instellen om te zoeken naar het adres in de Verenigde Staten van Amerika.

**Voorbeeldquery:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Hieronder vindt u een overzicht van de onderstaande antwoord structuur. De resultaat typen van de resultaat objecten in het antwoord wijken af. Als u voorzichtig bent, kunt u zien dat er drie verschillende typen resultaat objecten zijn: "punt adres", "straat" en "Cross Street". U ziet dat bij het zoeken naar adressen geen Poi's wordt geretourneerd. De para meter `Score` voor elk antwoord object geeft de relatieve overeenkomende score aan voor scores van andere objecten in hetzelfde antwoord. Zie [Zoek adres ophalen](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) voor meer informatie over de para meters van het Response-object.

**Ondersteunde typen resultaat:**

* **Punt adres:** Punten op een kaart met een specifiek adres met een straat naam en nummer. Het hoogste nauwkeurigheids niveau dat beschikbaar is voor adressen. 

* **Adres bereik:**  Voor sommige straten zijn er adres punten die worden geïnterpoleerd vanaf het begin en het einde van de straat. deze punten worden weer gegeven als adresbereiken. 

* **Geografie:** Gebieden op een kaart die de administratieve divisie van een land vertegenwoordigen, dat wil zeggen land, staat, plaats. 

* **POI-(interessante punten):** Punten op een kaart die aandacht best Eden en mogelijk interessant zijn.

* **Straat:** Weer gave van straten op de kaart. Adressen worden omgezet naar de breedte graad/lengte graad van de straat die het adres bevat. Het huis nummer mag niet worden verwerkt. 

* **Kruis Straat:** Overschrijdingen. Representaties van koppelingen; plaatsen waar twee Streets INTERSECT.

**Antwoord:**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometrie

Wanneer het antwoord type **geometrie**is, kan het de geometrie-id bevatten die wordt geretourneerd in het object **Data sources** onder "geometrie" en "ID". Met de service voor het [ophalen van polygoon](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) kunt u bijvoorbeeld de geometrie gegevens opvragen in de geojson-indeling. Zoals een plaats of een overzicht van de lucht haven voor een set entiteiten. U kunt deze grens gegevens gebruiken voor [geoomheining](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) of [Zoeken in poi's binnen de geometrie](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


[Zoek adres](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) of [Zoek acties op fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) -API kunnen de **geometrie-id** bevatten die wordt geretourneerd in het object data sources onder "geometrie" en "ID".


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het bouwen van Azure Maps zoek service aanvragen](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Verken de documentatie van de Azure Maps [Search Service-API](https://docs.microsoft.com/rest/api/maps/search). 
