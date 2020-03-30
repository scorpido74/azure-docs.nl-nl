---
title: Aanbevolen procedures voor Azure Maps Search Service | Microsoft Azure Maps
description: Meer informatie over het toepassen van de aanbevolen procedures bij het gebruik van de zoekservice van Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8d62d7d278323baa0ae49b9e12f46468efb067a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335312"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Aanbevolen procedures voor Azure Maps Search Service

Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) bevat API's die verschillende mogelijkheden bieden. De API voor zoekadres kan bijvoorbeeld points of interest (POI) of gegevens rond een specifieke locatie vinden. 

In dit artikel wordt uitgelegd hoe u geluidspraktijken toepast wanneer u gegevens van Azure Maps Search Service belt. U leert het volgende:

* Query's bouwen om relevante overeenkomsten te retourneren.
* Beperk zoekresultaten.
* Meer informatie over de verschillen tussen resultaattypen.
* Lees de structuur van de zoekreactie van het adres.

## <a name="prerequisites"></a>Vereisten

Als u wilt bellen naar de Azure Maps-service API's, hebt u een Azure Maps-account en een sleutel nodig. Zie [Een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) en een primaire sleutel [opvragen voor](quick-demo-map-app.md#get-the-primary-key-for-your-account)meer informatie. 

Zie [Verificatie beheren in Azure Maps](./how-to-manage-authentication.md)voor informatie over verificatie in Azure Maps.

> [!TIP]
> Als u de zoekservice wilt opvragen, u de [Postman-app](https://www.getpostman.com/apps) gebruiken om REST-oproepen te maken. Of u elke API-ontwikkelomgeving gebruiken die u verkiest.

## <a name="best-practices-to-geocode-addresses"></a>Aanbevolen procedures voor geocodeadressen

Wanneer u met Azure Maps Search Service naar een volledig of gedeeltelijk adres zoekt, leest de API zoekwoorden uit uw zoekopdracht. Dan retourneert de lengte- en breedtegraadcoördinaten van het adres. Dit proces wordt *geocodering*genoemd. 

De mogelijkheid om te geocoderen in een land is afhankelijk van de beschikbaarheid van weggegevens en de precisie van de geocoderingsdienst. Zie [Geocoderingsdekking](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage)voor meer informatie over geocoderingsmogelijkheden voor Azure Maps per land of regio.

### <a name="limit-search-results"></a>Zoekresultaten beperken

 Azure Maps Search API kan u helpen de zoekresultaten op de juiste manier te beperken. U beperkt de resultaten zodat u relevante gegevens aan uw gebruikers weergeven.

> [!NOTE]
> De zoek-API's ondersteunen meer parameters dan alleen die welke in dit artikel worden besproken.

#### <a name="geobiased-search-results"></a>Geobevooroordeelde zoekresultaten

Om geobias resultaten aan het relevante gebied voor uw gebruiker, altijd zo veel locatiegegevens mogelijk. U de zoekresultaten beperken door enkele invoertypen op te geven:

* Stel `countrySet` de parameter in. U het `US,FR`instellen op, bijvoorbeeld. Standaard zoekt de API de hele wereld, zodat het onnodige resultaten kan retourneren. Als uw query `countrySet` geen parameter heeft, kan de zoekopdracht onjuiste resultaten opleveren. Een zoekopdracht naar een stad met de naam *Bellevue* retourneert bijvoorbeeld resultaten uit de VS en Frankrijk omdat beide landen een stad met de naam *Bellevue*bevatten.

* U `btmRight` de `topleft` parameters en parameters gebruiken om het selectiekader in te stellen. Deze parameters beperken de zoekopdracht tot een specifiek gebied op de kaart.

* Als u het relevantiegebied voor de `lat` resultaten `lon` wilt beïnvloeden, definieert u de parameters en coördineert u de parameters. Gebruik `radius` de parameter om de straal van het zoekgebied in te stellen.


#### <a name="fuzzy-search-parameters"></a>Vage zoekparameters

We raden u aan de Azure Maps [Search Fuzzy API te](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) gebruiken wanneer u uw gebruikersinvoer voor een zoekopdracht niet kent. De API combineert POI zoeken en geocodering in een canonieke *single-line zoeken:* 

* De `minFuzzyLevel` `maxFuzzyLevel` parameters en parameters helpen relevante overeenkomsten terug te sturen, zelfs wanneer queryparameters niet precies overeenkomen met de informatie die de gebruiker wil. Als u de prestaties wilt maximaliseren en ongebruikelijke `minFuzzyLevel=1` resultaten `maxFuzzyLevel=2`wilt verminderen, stelt u zoekopdrachten in op standaardwaarden van en . 

    Wanneer de `maxFuzzyLevel` parameter bijvoorbeeld is ingesteld op 2, wordt de *restrant* van de zoekterm gekoppeld aan *restaurant*. U de standaard fuzzy niveaus overschrijven wanneer dat nodig is. 

* Gebruik `idxSet` de parameter om prioriteit te geven aan de exacte set resultaattypen. Als u prioriteit wilt geven aan een exacte reeks resultaten, u een door komma's gescheiden lijst met indexen indienen. In uw lijst doet de objectorder er niet toe. Azure Maps ondersteunt de volgende indexen:

* `Addr` - **Adresbereiken**: Adrespunten die vanaf het begin en einde van de straat worden geïnterpoleerd. Deze punten worden weergegeven als adresbereiken.
* `Geo` - **Geografische gebieden**: Administratieve verdelingen van het land. Een geografie kan bijvoorbeeld een land, staat of stad zijn.
* `PAD` - **Adresadressen**: Adressen met een straatnaam en -nummer. Puntadressen zijn te vinden in een index. Een voorbeeld is *Soquel Dr 2501*. Een puntadres biedt het hoogste niveau van nauwkeurigheid dat beschikbaar is voor adressen.  
* `POI` - **Bezienswaardigheden**: Punten op een kaart die worden beschouwd als de moeite waard aandacht of dat kan interessant zijn. De [API voor zoekadres](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) retourneert geen POI's.  
* `Str` - **Straten**: Straten op de kaart.
* `XStr` - **Kruis straten of kruispunten**: Kruispunten of plaatsen waar twee straten elkaar kruisen.


#### <a name="usage-examples"></a>Gebruiksvoorbeelden

* `idxSet=POI`- Zoek alleen POI's. 

* `idxSet=PAD,Addr`- Alleen adressen doorzoeken. `PAD`geeft het puntadres `Addr` aan en geeft het adresbereik aan.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Reverse-geocode en filter voor een type geografische entiteit

Wanneer u een reverse-geocode-zoekopdracht doet in de [Reverse API voor zoekadres,](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)kan de service veelhoeken retourneren voor beheergebieden.Als u de zoekopdracht wilt beperken tot `entityType` specifieke typen van geografische entiteiten, neemt u de parameter op in uw aanvragen. 

Het resulterende antwoord bevat de geografische id en het entiteitstype dat is gekoppeld. Als u meer dan één entiteit opgeeft, retourneert het eindpunt de *kleinste entiteit die beschikbaar is.* U de geretourneerde geometrie-id gebruiken om de geometrie van de geografie te krijgen via de [service Veelhoek zoeken.](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)

#### <a name="sample-request"></a>Voorbeeldaanvraag

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>Antwoord

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

### <a name="set-the-results-language"></a>De resultatentaal instellen

Gebruik `language` de parameter om de taal in te stellen voor de geretourneerde zoekresultaten. Als de aanvraag de taal niet instelt, gebruikt de zoekservice standaard de meest voorkomende taal in het land of de regio. Wanneer er geen gegevens beschikbaar zijn in de opgegeven taal, wordt de standaardtaal gebruikt. 

Zie [Ondersteunde talen met Azure Maps](https://docs.microsoft.com/azure/azure-maps/supported-languages)voor meer informatie.


### <a name="use-predictive-mode-automatic-suggestions"></a>De voorspellende modus gebruiken (automatische suggesties)

Als u meer overeenkomsten voor gedeeltelijke `typeahead` query's wilt vinden, stelt u de parameter in op `true`. Deze query wordt geïnterpreteerd als een gedeeltelijke invoer en de zoekopdracht gaat in de voorspellende modus. Als u de `typeahead` parameter niet `true`instelt op , gaat de service ervan uit dat alle relevante informatie is doorgegeven.

In de volgende voorbeeldquery wordt de zoekadresservice opgevraagd voor *Microso*. Hier is `typeahead` de `true`parameter ingesteld op . Het antwoord laat zien dat de zoekservice de query heeft geïnterpreteerd als gedeeltelijke query. Het antwoord bevat resultaten voor een automatisch voorgestelde query.

#### <a name="sample-query"></a>Voorbeeldquery

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>Antwoord

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


### <a name="encode-a-uri-to-handle-special-characters"></a>Een URI coderen om speciale tekens te verwerken 

Als u adressen voor kruisende adressen wilt vinden, moet u de URI coderen om speciale tekens in het adres te verwerken. Neem dit adres voorbeeld: *1st Avenue & Union Street, Seattle*. Codeer hier het ampersand-teken (`&`) voordat u het verzoek verzendt. 

We raden u aan tekengegevens in een URI te coderen. In een URI codeert u alle tekens`%`met behulp van een percentageteken ( ) en een hexadecimale waarde van twee tekens die overeenkomt met de UTF-8-code van de tekens.

#### <a name="usage-examples"></a>Gebruiksvoorbeelden

Begin met dit adres:

```
query=1st Avenue & E 111th St, New York
```

Codeer het adres:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

U de volgende methoden gebruiken.

JavaScript of TypeScript:
```Javascript
encodeURIComponent(query)
```

C# of Visual Basic:
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

Php:
```PHP
urlencode(query)
```

Ruby:
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Gaan:
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>Aanbevolen procedures voor POI-zoeken

In een POI-zoekopdracht u POI-resultaten op naam aanvragen. U bijvoorbeeld zoeken naar een bedrijf op naam. 

We raden u ten `countrySet` zeerste aan de parameter te gebruiken om landen aan te geven waar uw toepassing dekking nodig heeft. Het standaardgedrag is om de hele wereld te doorzoeken. Deze brede zoekopdracht kan onnodige resultaten opleveren en het zoeken kan lang duren.

### <a name="brand-search"></a>Merkzoeken

Om de relevantie van de resultaten en de informatie in de reactie te verbeteren, bevat een POI-zoekreactie merkinformatie. U deze informatie gebruiken om het antwoord verder te parseren.

In een aanvraag u een door komma's gescheiden lijst met merknamen indienen. Gebruik de lijst om de resultaten te `brandSet` beperken tot specifieke merken door de parameter in te stellen. In uw lijst doet de objectvolgorde er niet toe. Wanneer u meerdere merklijsten opgeeft, moeten de geretourneerde resultaten tot ten minste één van uw lijsten behoren.

Om het zoeken naar merken te verkennen, laten we een [POI-categorie zoekverzoek](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) indienen. In het volgende voorbeeld zoeken we naar benzinestations in de buurt van de Microsoft-campus in Redmond, Washington. De reactie toont merkinformatie voor elke POI die is geretourneerd.

#### <a name="sample-query"></a>Voorbeeldquery

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>Antwoord

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


### <a name="airport-search"></a>Zoeken op de luchthaven

Door gebruik te maken van de Search POI API u luchthavens zoeken met behulp van hun officiële code. U bijvoorbeeld *SEA* gebruiken om de Seattle-Tacoma International Airport te vinden: 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Zoeken in de buurt

Als u POI-resultaten rond een specifieke locatie wilt ophalen, u de [API Zoeken in](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)de buurt proberen. Het eindpunt retourneert alleen POI-resultaten. Er wordt geen queryparameter in het zoekquery opnemen. 

Om de resultaten te beperken, raden we u aan de straal in te stellen.

## <a name="understanding-the-responses"></a>Inzicht in de antwoorden

Een adres zoeken in Seattle door een adreszoekverzoek in te dienen bij de Azure Maps Search Service. In de volgende aanvraag-URL `countrySet` stellen `US` we de parameter in op het zoeken naar het adres in de VS.

### <a name="sample-query"></a>Voorbeeldquery

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>Ondersteunde soorten resultaten

* **Adresadres:** punten op een kaart met een specifiek adres met een straatnaam en -nummer. Adres biedt het hoogste niveau van nauwkeurigheid voor adressen. 

* **Adresbereik:** het bereik van adrespunten dat vanaf het begin en einde van de straat is geïnterpoleerd.  

* **Geografie:** gebieden op een kaart die administratieve verdelingen van een land vertegenwoordigen, bijvoorbeeld land, staat of stad. 

* **POI**: Punten op een kaart die de aandacht waard zijn en dat kan interessant zijn.

* **Straat**: Straten op de kaart. Adressen worden opgelost tot de breedte- en lengtegraadcoördinaten van de straat die het adres bevat. Het huisnummer wordt mogelijk niet verwerkt. 

* **Cross Street**: Kruispunten. Dwarsstraten vertegenwoordigen kruispunten waar twee straten elkaar kruisen.

### <a name="response"></a>Antwoord

Laten we eens kijken naar de reactiestructuur. In het antwoord dat volgt, zijn de typen van de resultaatobjecten verschillend. Als u goed kijkt, ziet u drie soorten resultaatobjecten:

* Adres
* Straat
* Cross Straat

Merk op dat de adreszoekopdracht geen POI's retourneert.  

De `Score` parameter voor elk antwoordobject geeft aan hoe de overeenkomende score zich verhoudt tot de scores van andere objecten in dezelfde respons. Zie [Zoekadres opvragen](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)voor meer informatie over parameters van antwoordobjecten.

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

Een reactietype *Geometrie* kan de geometrie-id bevatten die `dataSources` wordt `geometry` `id`geretourneerd in het object onder en . U bijvoorbeeld de [opdracht Veelhoek zoeken](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) gebruiken om de geometriegegevens in een GeoJSON-indeling op te vragen. Met deze indeling u een overzicht van een stad of luchthaven krijgen voor een reeks entiteiten. U deze grensgegevens vervolgens gebruiken om een geofence of [zoekPOI's in de geometrie in](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)te [stellen.](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence)


Antwoorden voor de [Search Address](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) API of de [Search Fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API kunnen `dataSources` de `geometry` `id`geometrie-id bevatten die wordt geretourneerd in het object onder en:


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie:

> [!div class="nextstepaction"]
> [Azure Maps-zoekserviceaanvragen maken](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

> [!div class="nextstepaction"]
> [Documentatie van de API-functie voor zoekservice](https://docs.microsoft.com/rest/api/maps/search)