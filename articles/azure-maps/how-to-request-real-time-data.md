---
title: Real-time gegevens over het openbaar vervoer aanvragen | Microsoft Azure Maps
description: Vraag realtime gegevens over het openbaar vervoer aan met de Microsoft Azure Maps Mobility Service.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4743fbe84f5d41b4659e13d96868d2f64a473e4b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086074"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-service"></a>Realtime gegevens over openbaar vervoer aanvragen met de Azure Maps Mobility Service

In dit artikel ziet u hoe u Azure Maps [Mobility Service kunt](https://aka.ms/AzureMapsMobilityService) gebruiken om realtime gegevens over het openbaar vervoer op te vragen.

In dit artikel leert u hoe u de volgende real-time aankomsten aanvragen voor alle lijnen die bij een bepaalde stop aankomen

## <a name="prerequisites"></a>Vereisten

U moet eerst een Azure Maps-account en een abonnementssleutel hebben om te bellen naar de Azure Maps-API's voor openbaar vervoer. Volg instructies voor informatie in [Een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) om een Azure Maps-account te maken. Volg de stappen in [de primaire sleutel](quick-demo-map-app.md#get-the-primary-key-for-your-account) om de primaire sleutel voor uw account te verkrijgen. Zie Verificatie beheren in Azure [Maps voor](./how-to-manage-authentication.md)meer informatie over verificatie in Azure Maps.

In dit artikel wordt de [Postman-app](https://www.getpostman.com/apps) gebruikt om REST-oproepen te maken. U elke API-ontwikkelomgeving gebruiken die u verkiest.

## <a name="request-real-time-arrivals-for-a-stop"></a>Real-time aankomsten aanvragen voor een stop

Als u realtime aankomstgegevens van een bepaalde ov-stop wilt aanvragen, moet u een verzoek indienen bij de [Real-time Arrivals API](https://aka.ms/AzureMapsMobilityRealTimeArrivals) van de Azure Maps [Mobility Service.](https://aka.ms/AzureMapsMobilityService) Je hebt de **metroID** en **stopID** nodig om het verzoek te voltooien. Voor meer informatie over het aanvragen van deze parameters, zie onze gids over het [aanvragen van openbaar vervoer routes](https://aka.ms/AMapsHowToGuidePublicTransitRouting).

Laten we "522" gebruiken als onze metro-ID, de metro-ID voor het "Seattle-Tacoma-Bellevue, WA" gebied. Gebruik "522---2060603" als de halte-ID, deze bushalte is op "Ne 24th st & 162nd Ave Ne, Bellevue WA". Voer de volgende stappen uit om de volgende vijf realtime aankomstgegevens op te vragen voor alle volgende live aankomsten bij deze halte:

1. Open de Postman-app en maak een verzameling om de aanvragen op te slaan. Selecteer Boven aan de Postman-app de optie **Nieuw**. Selecteer **Verzameling** in het **Collection**venster Nieuw maken .  Geef de verzameling een naam en selecteer de knop **Maken.**

2. Als u de aanvraag wilt maken, selecteert u Opnieuw **nieuw.** Selecteer **Verzoek in**het venster **Nieuw maken** . Voer een **aanvraagnaam** in voor de aanvraag. Selecteer de verzameling die u in de vorige stap hebt gemaakt, als de locatie om de aanvraag op te slaan. Selecteer vervolgens **Opslaan**.

    ![Een aanvraag maken in Postbode](./media/how-to-request-transit-data/postman-new.png)

3. Selecteer de **METHODE HTTP OPHALEN** op het tabblad Bouwer en voer de volgende URL in om een GET-aanvraag te maken. Vervang `{subscription-key}`, met uw primaire azure maps-sleutel.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Na een geslaagd verzoek ontvangt u het volgende antwoord.  Merk op dat parameter 'scheduleType' bepaalt of de geschatte aankomsttijd is gebaseerd op realtime of statische gegevens.

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

Meer informatie over het aanvragen van transitgegevens met Mobility Service:

> [!div class="nextstepaction"]
> [Transitgegevens aanvragen](how-to-request-transit-data.md)

Bekijk de API-documentatie van Azure Maps Mobility Service:

> [!div class="nextstepaction"]
> [Documentatie van Mobility Service API](https://aka.ms/AzureMapsMobilityService)
