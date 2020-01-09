---
title: Efficiënt zoeken met behulp van de Azure Maps Search-service | Microsoft Docs
description: Meer informatie over het gebruik van aanbevolen procedures voor zoeken met behulp van de Azure Maps Search-service
author: walsehgal
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 6a51d764b8e42419bc331e3d4731ef5c5f511f91
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75408722"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Aanbevolen procedures voor het gebruik van Azure Maps Search Service

Azure Maps [Search service](https://docs.microsoft.com/rest/api/maps/search) bevat api's met verschillende mogelijkheden, bijvoorbeeld van adres zoeken om te zoeken naar POI-gegevens (Point of interest) rond een specifieke locatie. In dit artikel delen we de aanbevolen procedures voor het aanroepen van gegevens via Azure Maps Search-Services. U leert het volgende:

* Query's bouwen om relevante overeenkomsten te retour neren
* Zoek resultaten beperken
* Meer informatie over het verschil tussen verschillende resultaat typen
* Lees de structuur van het antwoord op de zoek opdracht


## <a name="prerequisites"></a>Vereisten

Voor het aanroepen van de Maps service Api's, hebt u een Maps-account en-sleutel nodig. Volg de instructies in [een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) voor meer informatie over het maken van een account en volg de stappen in [primaire sleutel ophalen](quick-demo-map-app.md#get-the-primary-key-for-your-account) om een primaire sleutel (abonnement) voor uw account op te halen. Zie [verificatie beheren in azure Maps](./how-to-manage-authentication.md)voor meer informatie over verificatie in azure Maps.

> [!Tip]
> Als u een query wilt uitvoeren op de zoek service, kunt u de [app postman](https://www.getpostman.com/apps) gebruiken om rest-aanroepen te bouwen of kunt u elke gewenste API-ontwikkel omgeving gebruiken.


## <a name="best-practices-for-geocoding"></a>Aanbevolen procedures voor geocodering

Wanneer u een volledig of gedeeltelijk adres zoekt met Azure Maps Search Service, wordt de zoek term opgehaald en worden de lengte-en breedte coördinaten van het adres geretourneerd. Dit proces wordt geocodering genoemd. De mogelijkheid tot Geocode in een land is afhankelijk van de dekking van de weg en de geocoderings nauwkeurigheid van de geocoderings service.

Zie [geocoderings dekking](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) voor meer informatie over Azure Maps mogelijkheden voor geocodering per land/regio.

### <a name="limit-search-results"></a>Zoek resultaten beperken

   In deze sectie leert u hoe u Azure Maps Search-Api's kunt gebruiken om de zoek resultaten te beperken. 

   > [!Note]
   > Niet alle zoek-Api's bieden volledige ondersteuning voor de hieronder vermelde para meters

   **Zoek resultaten geo-bias**

   Als u de resultaten van uw gebruiker naar het relevante gebied wilt verduidelijken, moet u altijd de Maxi maal mogelijke gedetailleerde locatie-invoer toevoegen. Als u de zoek resultaten wilt beperken, kunt u overwegen de volgende invoer typen toe te voegen:

   1. Stel de para meter `countrySet` in, bijvoorbeeld ' VS, FR '. Het standaard zoek gedrag is het doorzoeken van de hele wereld, waardoor mogelijk onnodige resultaten worden geretourneerd. Als uw query `countrySet` para meter niet bevat, kan de zoek opdracht onnauwkeurige resultaten retour neren. Als u bijvoorbeeld zoekt naar een stad met de naam **Bellevue** , worden de resultaten uit VS en Frank rijk geretourneerd, omdat er steden zijn met de naam **Bellevue** in Frank rijk en in de Verenigde Staten.

   2. U kunt de para meters `btmRight` en `topleft` gebruiken om het begrenzingsvak in te stellen om de zoek opdracht te beperken tot een bepaald gebied op de kaart.

   3. Als u het relevantie gebied voor de resultaten wilt beïnvloeden, kunt u de para meters `lat`en `lon` coördinaat definiëren en de RADIUS van het zoek gebied instellen met behulp van de `radius` para meter.


   **Zoek parameters voor fuzzy**

   1. Met de `minFuzzyLevel` en `maxFuzzyLevel`kunt u relevante overeenkomsten retour neren, zelfs wanneer de query parameters niet precies overeenkomen met de gewenste gegevens. De meeste zoek query's zijn standaard `minFuzzyLevel=1` en `maxFuzzyLevel=2` voor het verkrijgen van prestaties en het verminderen van ongebruikelijke resultaten. Bekijk een voor beeld van een zoek term ' restrant ', die overeenkomt met ' restaurant ' wanneer de `maxFuzzyLevel` is ingesteld op 2. De standaard niveaus voor fuzzy kunnen worden overschreven volgens de behoeften van de aanvraag. 

   2. U kunt ook de exacte set resultaat typen opgeven die moeten worden geretourneerd met behulp van de para meter `idxSet`. Voor dit doel kunt u een lijst met door komma's gescheiden indexen verzenden, maar de volg orde van de items niet van belang is. De volgende indexen worden ondersteund:

       * `Addr` - adresbereiken: voor sommige straten zijn er adres punten die worden geïnterpoleerd vanaf het begin en het einde van de straat. deze punten worden weer gegeven als adresbereiken.
       * `Geo` - **geographs**: gebieden op een kaart die de administratieve divisie van een land vertegenwoordigen, dat wil zeggen land, staat, plaats.
       * `PAD` - **punt adres**: punten op een kaart waarbij een specifiek adres met een straat naam en nummer kan worden gevonden in een index, bijvoorbeeld Soquel Dr 2501. Het is het hoogste nauwkeurigheids niveau dat beschikbaar is voor adressen.  
       * `POI` - e interessante **punten**: punten op een kaart die aandacht best Eden en mogelijk interessant zijn.  [Zoek adres ophalen](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) retourneert niet poi's.  
       * `Str` - **Streets**: de weer gave van straten op de kaart.
       * `XStr` - **Kruis straten/snij punten**: weer gave van verbindings lijnen; plaatsen waar twee Streets INTERSECT.


       **Voor beelden van gebruik**:

       * idxSet = POI (alleen zoeken naar interessante punten) 

       * idxSet = PAD, addr (alleen adressen zoeken, PAD = punt adres, addr = adres bereik)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Filter voor reverse Geocode en geografie van het entiteits type

Bij het uitvoeren van een reverse Geocode Search met [Search Address reverse API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), heeft de service de mogelijkheid om veelhoeken te retour neren voor de administratieve gebieden. Door de para meter op te geven `entityType` in de aanvraag, kunt u de zoek actie beperken voor opgegeven geografie typen. De resulterende reactie bevat de geografie-ID en het entiteits type dat overeenkomt. Als u meer dan één entiteit opgeeft, wordt de **kleinste beschik bare entiteit**door het eind punt geretourneerd. De geretourneerde geometrie-ID kan worden gebruikt om de geometrie van die geografie op te halen via de service voor het [ophalen van polygoon](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Voorbeeld aanvraag:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Antwoord:**

```JSON
{
    "summary": {
        "queryTime": 8,
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
                "freeformAddress": "Redmond, WA",
                "boundingBox": {
                    "northEast": "47.717105,-122.034537",
                    "southWest": "47.627016,-122.164998",
                    "entity": "position"
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

Met de para meter `language` kunt u instellen in welke taal Zoek resultaten moeten worden geretourneerd. Als de taal niet is ingesteld in de aanvraag, wordt de zoek service automatisch ingesteld op de meest voorkomende taal in het land/de regio. Wanneer de gegevens in de opgegeven taal niet beschikbaar zijn, wordt ook de standaard taal gebruikt. Zie [ondersteunde talen](https://docs.microsoft.com/azure/azure-maps/supported-languages) voor een lijst met ondersteunde talen ten aanzien van Azure Maps services per land/regio.


### <a name="predictive-mode-auto-suggest"></a>Voorspellende modus (automatische suggesties)

`typeahead` para meter moet worden ingesteld op ' True ' om meer overeenkomsten voor gedeeltelijke query's te vinden. De query wordt geïnterpreteerd als een gedeeltelijke invoer en de voor Spellings modus wordt in de zoek opdracht weer geven. Anders gaat de service ervan uit dat alle relevante gegevens zijn door gegeven in.

In de onderstaande voorbeeld query ziet u dat de Search-adres service wordt doorzocht op ' Microsoft ', waarbij de para meter `typeahead` is ingesteld op **True**. Als u het antwoord ziet, kunt u zien dat de zoek service de query heeft geïnterpreteerd als gedeeltelijke query en antwoord resultaten bevat voor automatisch voorgestelde query's.

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
        "queryTime": 25,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/10294417",
            "score": 2.594,
            "dist": 327.546040632591,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980526399,980528300",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63989,
                "lon": -122.12509
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309
                },
                "btmRightPoint": {
                    "lat": 47.64223,
                    "lon": -122.13061
                }
            }
        },
        ...,
        ...,
        ...,
        ...,
        {
            "type": "Street",
            "id": "US/STR/p0/9063400",
            "score": 2.075,
            "dist": 3655467.6406921702,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "postalCode": "28217",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28217",
                "countrySubdivisionName": "North Carolina"
            },
            "position": {
                "lat": 35.14279,
                "lon": -80.91814
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814
                },
                "btmRightPoint": {
                    "lat": 35.14279,
                    "lon": -80.91824
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>URI-code ring voor het afhandelen van speciale tekens 

U kunt als volgt Cross Street-adressen zoeken: "1ste punt & vakbonds straat Seattle", het speciale teken ' & ' moet worden versleuteld voordat de aanvraag wordt verzonden. We raden aan om teken gegevens te coderen in een URI, waarbij alle tekens worden gecodeerd met een%-teken en een hexadecimale waarde van twee tekens die overeenkomt met het UTF-8-teken.

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
        "queryTime": 206,
        "numResults": 3,
        "offset": 0,
        "totalResults": 742169,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/245813",
            "score": 5.663,
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
                "url": "www.chevron.com",
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
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2444 Bel Red Rd, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63223,
                        "lon": -122.13311
                    }
                }
            ]
        },
        ...,
        {
            "type": "POI",
            "id": "US/POI/p0/7727106",
            "score": 5.662,
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
                "url": "www.texaco.com/",
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
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980525511",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "15248 Bel Red Rd, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62826,
                        "lon": -122.13626
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
        "queryTime": 129,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/43076024",
            "score": 9.894,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, South Lake Union, Lower Queen Anne",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "400 Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/2440854",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Westlake, South Lake Union",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "extendedPostalCode": "981094347,981094700,981094701,981094702",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62553,
                "lon": -122.33936
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62545,
                    "lon": -122.33861
                },
                "btmRightPoint": {
                    "lat": 47.62574,
                    "lon": -122.33974
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8450985",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109,98121",
                "extendedPostalCode": "981094991,981211117,981211237,981213206",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.61691,
                "lon": -122.35251
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61502,
                    "lon": -122.35041
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.35484
                }
            }
        },
        ...,
        ...,
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/3816818",
            "score": 6.759,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street & Valley Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62574,
                "lon": -122.33861
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62664,
                    "lon": -122.33994
                },
                "btmRightPoint": {
                    "lat": 47.62484,
                    "lon": -122.33728
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometrie

Wanneer het antwoord type **geometrie**is, kan het de geometrie-id bevatten die wordt geretourneerd in het object **Data sources** onder "geometrie" en "ID". Met de service voor het [ophalen van polygoon](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) kunt u bijvoorbeeld de geometrie gegevens opvragen in de geojson-indeling, zoals een plaats of een overzicht van de lucht haven voor een set entiteiten. U kunt deze grens gegevens gebruiken voor [geoomheining](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) of [Zoeken in poi's binnen de geometrie](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


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
