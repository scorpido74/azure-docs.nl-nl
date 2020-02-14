---
title: Gegevens voor real-time doorvoer aanvragen | Microsoft Azure kaarten
description: Real-time gegevens aanvragen met behulp van de Microsoft Azure Maps Mobility-service.
author: walsehgal
ms.author: v-musehg
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 053e6c84f69e8b3d3fed0a90a8b632aa4eb311cb
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198153"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Real-time gegevens aanvragen met behulp van de Azure Maps Mobility-service

In dit artikel wordt beschreven hoe u Azure Maps [Mobility-service](https://aka.ms/AzureMapsMobilityService) kunt gebruiken om gegevens in realtime-transit te aanvragen.

In dit artikel leert u het volgende:


 * De volgende realtime aankomsten aanvragen voor alle regels die bij een bepaalde beëindiging arriveren
 * Real-time informatie aanvragen voor een gegeven fiets docking station.


## <a name="prerequisites"></a>Vereisten

U moet eerst een Azure Maps account en een abonnements sleutel hebben om de Azure Maps open bare doorvoer-Api's te kunnen aanroepen. Volg de instructies in [een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) om een Azure Maps-account te maken voor meer informatie. Volg de stappen in [primaire sleutel ophalen](quick-demo-map-app.md#get-the-primary-key-for-your-account) om de primaire sleutel voor uw account te verkrijgen. Zie [verificatie beheren in azure Maps](./how-to-manage-authentication.md)voor meer informatie over verificatie in azure Maps.


In dit artikel wordt gebruikgemaakt van de [app postman](https://www.getpostman.com/apps) om rest-aanroepen te bouwen. U kunt elke gewenste API-ontwikkel omgeving gebruiken.


## <a name="request-real-time-arrivals-for-a-stop"></a>Dagelijkse aankomsten aanvragen voor een stop

Als u gegevens in realtime wilt ontvangen van een bepaalde open bare doorvoer stop, moet u een aanvraag indienen bij de API voor de [real-time-ontvangst](https://aka.ms/AzureMapsMobilityRealTimeArrivals) van de Azure Maps [Mobility-service](https://aka.ms/AzureMapsMobilityService). U hebt de **metroID** en **stopID** nodig om de aanvraag te volt ooien. Zie voor meer informatie over het aanvragen van deze para meters onze hand leiding voor het [aanvragen van open bare Transit routes](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

We gebruiken ' 522 ' als onze metro-ID. Dit is de metro-ID voor het gebied ' Seattle – Tacoma – Bellevue, WA '. Gebruik "522---2060603" als de stop-ID, deze bus stopt op "ne 24 St & 162nd Ave ne, Bellevue WA". Voer de volgende stappen uit om de volgende vijf gegevens over de dagelijkse duur te aanvragen voor alle volgende Live-aankomsten:

1. Open de Postman-app, zodat u een verzameling kunt maken om de aanvragen op te slaan. Selecteer in de buurt van de Postman-app de optie **Nieuw**. Selecteer **verzameling**in het venster **Nieuw maken** .  Geef de verzameling een naam en selecteer de knop **maken** .

2. Als u de aanvraag wilt maken, selecteert u **Nieuw** opnieuw. Selecteer **aanvraag**in het venster **Nieuw maken** . Voer een **aanvraag naam** in voor de aanvraag. Selecteer de verzameling die u in de vorige stap hebt gemaakt, als de locatie waar u de aanvraag wilt opslaan. Selecteer vervolgens **Opslaan**.

    ![Een aanvraag maken in postman](./media/how-to-request-transit-data/postman-new.png)

3. Selecteer de methode http **ophalen** op het tabblad opbouw functie en voer de volgende URL in om een GET-aanvraag te maken. Vervang `{subscription-key}`door uw Azure Maps primaire sleutel.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Na een geslaagde aanvraag ontvangt u het volgende antwoord.  U ziet dat de para meter ' Schedule Type ' definieert of de geschatte aankomst tijd is gebaseerd op realtime of statische gegevens.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Real-time gegevens voor het fietsen docking-station

Met de informatie over het ophalen van de [gegevens voor transit docken](https://aka.ms/AzureMapsMobilityTransitDock) kunnen gebruikers statische en real-time informatie opvragen. Gebruikers kunnen bijvoorbeeld Beschik baarheid en vacature gegevens aanvragen voor een fiets of een scooter docking station. De informatie over het ophalen van de [gegevens voor transit Dock](https://aka.ms/AzureMapsMobilityTransitDock) maken maakt ook deel uit van de Azure Maps [Mobility-service](https://aka.ms/AzureMapsMobilityService).

Als u een aanvraag wilt indienen voor de [gegevens-API Get Transit Dock](https://aka.ms/AzureMapsMobilityTransitDock), hebt u de **dockId** voor dat station nodig. U kunt de Dock-ID ophalen door een zoek aanvraag te maken naar de API voor in gebruik zijnde [tussenliggende Transit](https://aka.ms/AzureMapsMobilityNearbyTransit) met de para meter **object type** toegewezen aan bikeDock. Volg de onderstaande stappen om real-time gegevens van een docking-station voor fietsen te verkrijgen.


### <a name="get-dock-id"></a>Dock-ID ophalen

Als u **dockID**wilt ophalen, volgt u de onderstaande stappen om een aanvraag in te stellen voor de API voor het ophalen van de nabije Transit:

1. Klik in postman op **nieuwe aanvraag** | **Get-aanvraag** en geef deze de naam **Dock-id ophalen**.

2.  Selecteer op het tabblad opbouw functie de methode http **ophalen** , voer de volgende aanvraag-URL in en klik op **verzenden**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Na een geslaagde aanvraag ontvangt u het volgende antwoord. U ziet nu dat we de **id** in het antwoord hebben, die later kunnen worden gebruikt als een query parameter in de aanvraag voor de gegevens-API Get Transit Dock ophalen.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 31,
                    "lastUpdated": "2019-09-07T00:55:19Z",
                    "operatorInfo": {
                        "id": "121---80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>Real-time Bike-Dock status ophalen

Volg de onderstaande stappen om een aanvraag in te stellen voor de API Get Transit Dock info om realtime gegevens voor de geselecteerde Dock te verkrijgen.

1. Klik in postman op **nieuwe aanvraag** | **Get-aanvraag** en geef **aan dat de gegevens in realtime worden gedokt**.

2.  Selecteer op het tabblad opbouw functie de methode http **ophalen** , voer de volgende aanvraag-URL in en klik op **verzenden**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Na een geslaagde aanvraag ontvangt u een reactie van de volgende structuur:

    ```JSON
    {
        "availableVehicles": 0,
        "vacantLocations": 31,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-09-07T00:55:19Z",
        "operatorInfo": {
            "id": "121---80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het aanvragen van doorvoer gegevens met de Mobility-service:

> [!div class="nextstepaction"]
> [Transit gegevens aanvragen](how-to-request-transit-data.md)

Bekijk de documentatie voor de Azure Maps Mobility Service API:

> [!div class="nextstepaction"]
> [API-documentatie voor Mobility service](https://aka.ms/AzureMapsMobilityService)
