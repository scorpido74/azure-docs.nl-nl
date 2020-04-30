---
title: Gegevens in realtime openbaar door Voer aanvragen | Microsoft Azure kaarten
description: Vraag realtime open bare doorvoer gegevens met behulp van de Microsoft Azure Maps Mobility service.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4743fbe84f5d41b4659e13d96868d2f64a473e4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086074"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-service"></a>Real-time gegevens van open bare door Voer aanvragen met behulp van de Azure Maps Mobility-service

In dit artikel wordt beschreven hoe u Azure Maps [Mobility-service](https://aka.ms/AzureMapsMobilityService) kunt gebruiken om gegevens in realtime open bare door voer te aanvragen.

In dit artikel leert u hoe u de volgende realtime aankomsten kunt aanvragen voor alle regels die bij een bepaalde Stop arriveren

## <a name="prerequisites"></a>Vereisten

U moet eerst een Azure Maps account en een abonnements sleutel hebben om de Azure Maps open bare doorvoer-Api's te kunnen aanroepen. Volg de instructies in [een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) om een Azure Maps-account te maken voor meer informatie. Volg de stappen in [primaire sleutel ophalen](quick-demo-map-app.md#get-the-primary-key-for-your-account) om de primaire sleutel voor uw account te verkrijgen. Zie [verificatie beheren in azure Maps](./how-to-manage-authentication.md)voor meer informatie over verificatie in azure Maps.

In dit artikel wordt gebruikgemaakt van de [app postman](https://www.getpostman.com/apps) om rest-aanroepen te bouwen. U kunt elke gewenste API-ontwikkel omgeving gebruiken.

## <a name="request-real-time-arrivals-for-a-stop"></a>Dagelijkse aankomsten aanvragen voor een stop

Als u gegevens in realtime wilt ontvangen van een bepaalde open bare doorvoer stop, moet u een aanvraag indienen bij de API voor de [real-time-ontvangst](https://aka.ms/AzureMapsMobilityRealTimeArrivals) van de Azure Maps [Mobility-service](https://aka.ms/AzureMapsMobilityService). U hebt de **metroID** en **stopID** nodig om de aanvraag te volt ooien. Zie voor meer informatie over het aanvragen van deze para meters onze hand leiding voor het [aanvragen van open bare Transit routes](https://aka.ms/AMapsHowToGuidePublicTransitRouting).

We gebruiken ' 522 ' als onze metro-ID. Dit is de metro-ID voor het gebied ' Seattle – Tacoma – Bellevue, WA '. Gebruik "522---2060603" als de stop-ID, deze bus stopt op "ne 24 st & 162nd Ave ne, Bellevue WA". Voer de volgende stappen uit om de volgende vijf gegevens over de dagelijkse duur te aanvragen voor alle volgende Live-aankomsten:

1. Open de Postman-app, zodat u een verzameling kunt maken om de aanvragen op te slaan. Selecteer in de buurt van de Postman-app de optie **Nieuw**. Selecteer **verzameling**in het venster **Nieuw maken** .  Geef de verzameling een naam en selecteer de knop **maken** .

2. Als u de aanvraag wilt maken, selecteert u **Nieuw** opnieuw. Selecteer **aanvraag**in het venster **Nieuw maken** . Voer een **aanvraag naam** in voor de aanvraag. Selecteer de verzameling die u in de vorige stap hebt gemaakt, als de locatie waar u de aanvraag wilt opslaan. Selecteer vervolgens **Opslaan**.

    ![Een aanvraag maken in postman](./media/how-to-request-transit-data/postman-new.png)

3. Selecteer de methode http **ophalen** op het tabblad opbouw functie en voer de volgende URL in om een GET-aanvraag te maken. Vervang `{subscription-key}`door de primaire sleutel van Azure Maps.

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

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het aanvragen van doorvoer gegevens met de Mobility-service:

> [!div class="nextstepaction"]
> [Transit gegevens aanvragen](how-to-request-transit-data.md)

Bekijk de documentatie voor de Azure Maps Mobility Service API:

> [!div class="nextstepaction"]
> [API-documentatie voor Mobility service](https://aka.ms/AzureMapsMobilityService)
