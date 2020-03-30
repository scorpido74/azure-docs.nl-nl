---
title: Transitgegevens aanvragen | Microsoft Azure Maps
description: In dit artikel leert u hoe u openbaarvervoergegevens opvraagt met de Microsoft Azure Maps Mobility Service.
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: f60b66790342874620971c8f15a1e8ace9a3c7cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335458"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Openbaar vervoergegevens aanvragen met de Azure Maps Mobility Service 

In dit artikel ziet u hoe u Azure Maps [Mobility Service kunt](https://aka.ms/AzureMapsMobilityService) gebruiken om gegevens over het openbaar vervoer op te vragen. Transitgegevens omvatten transitstops, route-informatie en reistijdschattingen.

In dit artikel leer je hoe je:

* Krijg een metro-id met behulp van de [API Metro Area](https://aka.ms/AzureMapsMobilityMetro)
* Vraag de doorvoer in de buurt aan met [de doorvoerservice Onderweg in de buurt.](https://aka.ms/AzureMapsMobilityNearbyTransit)
* Query [Download de API voor transitroutes](https://aka.ms/AzureMapsMobilityTransitRoute) om een route te plannen met het openbaar vervoer.
* Vraag de geometrie van de transitroute en het gedetailleerde schema voor de route aan met behulp van de [API Transitroute opvragen.](https://aka.ms/https://azure.microsoft.com/services/azure-maps/)


## <a name="prerequisites"></a>Vereisten

U moet eerst een Azure Maps-account en een abonnementssleutel hebben om te bellen naar de Azure Maps-API's voor openbaar vervoer. Volg instructies voor informatie in [Een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) om een Azure Maps-account te maken. Volg de stappen in [de primaire sleutel](quick-demo-map-app.md#get-the-primary-key-for-your-account) om de primaire sleutel voor uw account te verkrijgen. Zie Verificatie beheren in Azure [Maps voor](./how-to-manage-authentication.md)meer informatie over verificatie in Azure Maps.


In dit artikel wordt de [Postman-app](https://www.getpostman.com/apps) gebruikt om REST-oproepen te maken. U elke API-ontwikkelomgeving gebruiken die u verkiest.


## <a name="get-a-metro-area-id"></a>Een metro-id

Om transitinformatie op te vragen voor een bepaald `metroId` grootstedelijk gebied, hebt u het van dat gebied nodig. Met [de API Voor Metro Area-informatie](https://aka.ms/AzureMapsMobilityMetro) u metrogebieden aanvragen, waarin de Azure Maps Mobility Service beschikbaar is. Het antwoord bevat details `metroId` `metroName`zoals de , en de weergave van de metrogebied geometrie in GeoJSON formaat.

Laten we een verzoek indienen om de Metro Area te krijgen voor de Seattle-Tacoma metro gebied ID. Voer de volgende stappen uit om een id voor een metrogebied aan te vragen:

1. Open de Postman-app en maak een verzameling om de aanvragen op te slaan. Selecteer Boven aan de Postman-app de optie **Nieuw**. Selecteer **Verzameling** in het **Collection**venster Nieuw maken .  Geef de verzameling een naam en selecteer de knop **Maken.**

2. Als u de aanvraag wilt maken, selecteert u Opnieuw **nieuw.** Selecteer **Verzoek in**het venster **Nieuw maken** . Voer een **aanvraagnaam** in voor de aanvraag. Selecteer de verzameling die u in de vorige stap hebt gemaakt als de locatie om de aanvraag op te slaan. Selecteer vervolgens **Opslaan**.
    
    ![Een aanvraag maken in Postbode](./media/how-to-request-transit-data/postman-new.png)

3. Selecteer de **METHODE HTTP OPHALEN** op het tabblad Bouwer en voer de volgende URL in om een GET-aanvraag te maken. Vervang `{subscription-key}`, met uw primaire azure maps-sleutel.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. Na een geslaagd verzoek ontvangt u het volgende antwoord:

    ```JSON
    {
        "results": [
            {
                "metroId": 522,
                "metroName": "Seattle–Tacoma–Bellevue, WA",
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                },
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                -121.99604,
                                47.16147
                            ],
                            [
                                -121.97051,
                                47.17222
                            ],
                            [
                                -121.96308,
                                47.17671
                            ],
                            ...,
                            ...,
                            ...,
                            [
                                -122.01525,
                                47.16008
                            ],
                            [
                                -122.00553,
                                47.15919
                            ],
                            [
                                -121.99604,
                                47.16147
                            ]
                        ]
                    ]
                }
            }
        ]
    }
    ```

5. Kopieer `metroId`de , we moeten het later gebruiken.

## <a name="request-nearby-transit-stops"></a>Aanvraag transitstops in de buurt

Met de Azure Maps [Get Nearby Transit-service](https://aka.ms/AzureMapsMobilityNearbyTransit) u transitobjecten doorzoeken.  de API retourneert de details van het transitobject, zoals haltes voor openbaar vervoer en gedeelde fietsen rond een bepaalde locatie. Vervolgens zullen we een verzoek indienen bij de service om te zoeken naar nabijgelegen ov-haltes binnen een straal van 300 meter rond de opgegeven locatie. In het verzoek moeten we `metroId` de eerder opgehaalde gegevens opnemen.

Volg de onderstaande stappen om een aanvraag in te dienen voor de transit in de [buurt:](https://aka.ms/AzureMapsMobilityNearbyTransit)

1. Klik in Postman op **Nieuw verzoek** | **GET-aanvraag** en geef de naam **Get Nearby stops**.

2. Selecteer op het tabblad Bouwer de METHODE **HTTP OPHALEN,** voer de volgende aanvraag-URL in voor uw API-eindpunt en klik op **Verzenden**.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. Na een succesvolle aanvraag moet de antwoordstructuur er uitzien als hieronder:

    ```JSON
    {
        "results": [
            {
                "id": "522---2060603",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632413,
                        "longitude": -122.12659
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594,
                        "longitude": -122.123959
                    }
                }
            },
            {
                "id": "522---2061020",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "68372",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318,
                        "longitude": -122.128451
                    },
                    "btmRightPoint": {
                        "latitude": 47.630499,
                        "longitude": -122.12582
                    }
                }
            },
            {
                "id": "522---2060604",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "71310",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474,
                        "longitude": -122.129124
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655,
                        "longitude": -122.126492
                    }
                }
            }
        ]
    }   
    ```

Als u de reactiestructuur zorgvuldig observeert, ziet u dat deze parameters bevat voor elk doorgangsobject. Elk doorvoerobject heeft `id`parameters `type` `stopName`zoals `mainTransitType` `mainAgencyName`, , , en de positie, in coördinaten, van het object.

Om te leren, gebruiken we `id` een van een bushaltes als de oorsprong, voor onze route in het volgende gedeelte.  


## <a name="request-a-transit-route"></a>Een transitroute aanvragen

Met de API Azure Maps [Get Transit Routes](https://aka.ms/AzureMapsMobilityTransitRoute) u reisplanning plannen. Het retourneert de best mogelijke routeopties van een oorsprong naar een bestemming. De service biedt verschillende soorten reismodi, waaronder wandelen, fietsen en openbaar vervoer. Vervolgens zoeken we een route van de dichtstbijzijnde bushalte naar de Space Needle tower in Seattle.

### <a name="get-location-coordinates-for-destination"></a>Locatiecoördinaten voor bestemming opvragen

Als u de locatiecoördinaten van de spaceneedle-toren wilt verkrijgen, u de Azure Maps [Fuzzy Search Service](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)gebruiken.

Voer de onderstaande stappen uit om een verzoek in te dienen bij de fuzzy zoekservice:

1. Klik in Postman op **Nieuw verzoek** | **GET-aanvraag** en geef deze de naam **Locatiecoördinaten op.**

2.  Selecteer op het tabblad Bouwer de METHODE **HTTP OPHALEN,** voer de volgende aanvraag-URL in en klik op **Verzenden**.
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. Als u het antwoord zorgvuldig bekijkt, bevat het meerdere locaties in de resultaten voor de zoekfunctie ruimtenaald. Elk resultaat bevat de locatiecoördinaten onder de **positie**. Kopieer `lat` de `lon` en onder de **positie** van het eerste resultaat.
    
   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 35,
            "numResults": 8,
            "offset": 0,
            "totalResults": 11,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/6993440",
                "score": 4.67369,
                "info": "search:ta:840539001406144-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "categorySet": [
                        {
                            "id": 7376009
                        }
                    ],
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "tower"
                    ],
                    "classifications": [
                        {
                            "code": "IMPORTANT_TOURIST_ATTRACTION",
                            "names": [
                                {
                                    "nameLocale": "en-US",
                                    "name": "important tourist attraction"
                                },
                                {
                                    "nameLocale": "en-US",
                                    "name": "tower"
                                }
                            ]
                        }
                    ]
                },
                "address": {
                    "streetNumber": "400",
                    "streetName": "Broad St",
                    "municipalitySubdivision": "South Lake Union, Seattle, Lower Queen Anne",
                    "municipality": "Seattle",
                    "countrySecondarySubdivision": "King",
                    "countryTertiarySubdivision": "Seattle",
                    "countrySubdivision": "WA",
                    "postalCode": "98109",
                    "countryCode": "US",
                    "country": "United States",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
                    "localName": "Seattle",
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
            ...,
            ...,
            ...
        ]
    }
    ``` 
    

### <a name="request-route"></a>Route aanvragen

Voer de onderstaande stappen uit om een routeaanvraag te doen:

1. Klik in Postman op **Nieuw verzoek** | **GET-aanvraag** en geef deze de naam **Route-informatie**.

2. Selecteer op het tabblad Bouwer de METHODE **HTTP OPHALEN,** voer de volgende aanvraag-URL in voor uw API-eindpunt en klik op **Verzenden**.

    We vragen openbaar vervoer routes voor een `modeType` bus `transitType` door het opgeven van de en parameters. De URL van de aanvraag bevat de locaties die in de vorige secties zijn opgehaald. Voor `originType`de , we hebben nu een **stopId**. En voor `destionationType`de , we hebben de **positie**.

    Bekijk de [lijst met URI-parameters](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) die u gebruiken in uw aanvraag voor de [API TransitRoutes opvragen.](https://aka.ms/AzureMapsMobilityTransitRoute) 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. Op een succesvol verzoek moet de responsstructuur er uitzien als hieronder:

    ```JSON
    {
        "results": [
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:0---522",
                "departureTime": "2019-09-07T01:01:50Z",
                "arrivalTime": "2019-09-07T02:16:33Z",
                "travelTimeInSeconds": 4483,
                "numberOfLegs": 8,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:01:50Z",
                        "caption": "249"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:26:00Z",
                        "caption": "249",
                        "lengthInMeters": 9139
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:26:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "255"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:57:21Z",
                        "caption": "255",
                        "lengthInMeters": 13136
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:57:22Z",
                        "legEndTime": "2019-09-07T02:01:27Z",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T02:01:27Z",
                        "legEndTime": "2019-09-07T02:06:33Z",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:06:33Z",
                        "legEndTime": "2019-09-07T02:12:41Z",
                        "caption": "8",
                        "lengthInMeters": 1060
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:12:42Z",
                        "legEndTime": "2019-09-07T02:16:33Z",
                        "lengthInMeters": 251
                    }
                ]
            },
            ...,
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:2---522",
                "departureTime": "2019-09-07T00:49:32Z",
                "arrivalTime": "2019-09-07T02:20:06Z",
                "travelTimeInSeconds": 5434,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T00:49:32Z",
                        "caption": "226"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T01:15:00Z",
                        "caption": "226",
                        "lengthInMeters": 6792
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:15:00Z",
                        "legEndTime": "2019-09-07T01:20:00Z",
                        "caption": "241"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:20:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "241",
                        "lengthInMeters": 3397
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:33:00Z",
                        "caption": "550"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:33:00Z",
                        "legEndTime": "2019-09-07T01:58:00Z",
                        "caption": "550",
                        "lengthInMeters": 12899
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:58:01Z",
                        "legEndTime": "2019-09-07T01:59:21Z",
                        "caption": "4th Avenue South",
                        "lengthInMeters": 99
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:59:21Z",
                        "legEndTime": "2019-09-07T02:01:00Z",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:01:00Z",
                        "legEndTime": "2019-09-07T02:13:29Z",
                        "caption": "33,24",
                        "lengthInMeters": 2447
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:13:30Z",
                        "legEndTime": "2019-09-07T02:20:06Z",
                        "lengthInMeters": 457
                    }
                ]
            }
        ]
    }
    ```

4. Als u zorgvuldig observeren, zijn er meerdere **busroutes** in de reactie. Elke route heeft een unieke **route-ID** en een samenvatting die elk deel van de route beschrijft. Een routepoot is het deel van de route tussen twee stopwaypoints. Vervolgens vragen we details voor de snelste `itineraryId` route met behulp van de in het antwoord.

## <a name="request-fastest-route-itinerary"></a>Verzoek snelste routeroute

Met de Azure Maps [Get Transit-routeservice](https://aka.ms/AzureMapsMobilityTransitItinerary) u gegevens voor een bepaalde route aanvragen met behulp van de **route-id** van de route die is geretourneerd door de [API-service Transitroutes downloaden.](https://aka.ms/AzureMapsMobilityTransitRoute) Voer de onderstaande stappen uit om een aanvraag in te dienen:

1. Klik in Postman op **Nieuw verzoek** | **GET-aanvraag** en geef deze naam **Transitinfo**.

2. Selecteer op het tabblad Bouwer de METHODE **HTTP-functie DOWNLOADEN.** Voer de volgende aanvraag-URL in voor uw API-eindpunt en klik op **Verzenden**.

    We stellen de `detailType` parameter in op **geometrie,** zodat de respons stopinformatie bevat voor het openbaar vervoer en turn-by-turn navigatie voor loop- en fietsbenen van de route.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. Op een succesvol verzoek moet de responsstructuur er uitzien als hieronder:

    ```JSON
    {
        "departureTime": "2019-09-07T01:01:50Z",
        "arrivalTime": "2019-09-07T02:16:33Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:01:50Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:26:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2060604",
                        "stopKey": "71310",
                        "stopName": "NE 24th St & 160th Ave NE",
                        "stopCode": "71310",
                        "position": {
                            "latitude": 47.631565,
                            "longitude": -122.127808
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    ...,
                    ...,
                    {
                        "stopId": "522---2061704",
                        "stopKey": "74451",
                        "stopName": "Northup Way & NE 33rd Pl",
                        "stopCode": "74451",
                        "position": {
                            "latitude": 47.640911,
                            "longitude": -122.194443
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.12527,
                            47.63143
                        ],
                        [
                            -122.12529,
                            47.63143
                        ],
                        [
                            -122.12561,
                            47.63144
                        ],
                        [
                            -122.12701,
                            47.63148
                        ],
                        ...,
                        ...,
                        ...,
                        [
                            -122.19601,
                            47.64304
                        ],
                        [
                            -122.19584,
                            47.64315
                        ],
                        [
                            -122.19677,
                            47.6438
                        ]
                    ]
                }
            },
            ...,
            ...,
            ...,
            {
                "legType": "Walk",
                "legStartTime": "2019-09-07T02:12:42Z",
                "legEndTime": "2019-09-07T02:16:33Z",
                "steps": [
                    {
                        "direction": {
                            "relativeDirection": "depart"
                        },
                        "streetName": "Denny Way"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "4th Avenue North"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "Broad Street"
                    }
                ],
                "origin": {
                    "position": {
                        "latitude": 47.618578,
                        "longitude": -122.348058
                    }
                },
                "destination": {
                    "position": {
                        "latitude": 47.62039,
                        "longitude": -122.34928
                    }
                },
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.34806,
                            47.61857
                        ],
                        [
                            -122.3481,
                            47.61857
                        ],
                        [
                            -122.34894,
                            47.61858
                        ],
                        [
                            -122.34892,
                            47.61964
                        ],
                        [
                            -122.34877,
                            47.61975
                        ],
                        [
                            -122.3492,
                            47.62001
                        ],
                        [
                            -122.34918,
                            47.62003
                        ],
                        [
                            -122.34917,
                            47.62006
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34928,
                            47.62039
                        ]
                    ]
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het aanvragen van realtime gegevens met Mobility Service:

> [!div class="nextstepaction"]
> [Realtime gegevens opvragen](how-to-request-real-time-data.md)

Ontdek de API-documentatie van Azure Maps Mobility Service API

> [!div class="nextstepaction"]
> [Documentatie van Mobility Service API](https://aka.ms/AzureMapsMobilityService)

