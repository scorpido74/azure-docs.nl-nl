---
title: Kies de juiste prijscategorie | Microsoft Azure Maps
description: In dit artikel vindt u meer informatie over prijsniveaus die worden aangeboden door Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: a8bf7ff9bacd4fe84ee5b64d0aed5cb271ce06f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335661"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>De juiste prijscategorie kiezen in Azure Maps

Azure Maps biedt twee prijsniveaus, S0 en S1. Het doel van dit artikel is om u te helpen bij het kiezen van de juiste prijscategorie voor uw behoeften. Als u de juiste prijscategorie wilt kiezen, stelt u zich de volgende twee vragen.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Hoeveel gelijktijdige gebruikers ben ik van plan te ondersteunen? 
De prijsniveaus S0 en S1 verwerken verschillende hoeveelheden gegevensdoorvoer. De prijscategorie S0 verwerkt maximaal **50 query's per seconde**. Terwijl de S1-laag **meer dan 50 query's per seconde verwerkt**.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Welke georuimtelijke mogelijkheden ben ik van plan te gebruiken?
De S0-prijscategorie is geschikt voor u, als de belangrijkste georuimtelijke API's voldoen aan uw servicevereisten. Als u meer geavanceerde mogelijkheden voor uw toepassing wilt, u overwegen om te kiezen voor de s1-prijscategorie. Geavanceerde mogelijkheden zijn: Aerial plus hybride beelden, het verkrijgen van routebereik en batchgeocodering. Bekijk de tabel met de mogelijkheden voor **de prijscategorie** om de prijscategorie te selecteren die het meest geschikt is voor uw toepassing.

### <a name="pricing-tier-capabilities"></a>Mogelijkheden voor prijsniveaus

| Mogelijkheid                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Kaartrender                              | ✓                   | ✓       |
| Satellietbeelden                       |                     | ✓        |
| Search                                  | ✓                    | ✓        |
| Zoeken in batch                            |                     | ✓        |
| Route                                   | ✓                    |✓        |
| Batchroutering                            |                    | ✓        |
| Matrixroutering                          |                     | ✓        |
| Routebereik (Isochrones)                |                     | ✓        |
| Verkeer                                |✓                    |✓        |
| Tijdzone                               |✓                    |✓        |
| Geolocatie (voorbeeld)                    |✓                   |✓        |
| Ruimtelijke bewerkingen                        |                    |✓        |
| Geofencing (Geofencing)                                |                    |✓        |
| Azure Maps-gegevens (voorbeeld)                |                     | ✓        |
| Mobiliteit (Preview)                       |                     | ✓        |
| Weer (preview)                        |✓                    |✓        |

Houd rekening met deze aanvullende punten:
* Wat voor soort onderneming heb je?
* Hoe kritisch is uw toepassing?

### <a name="pricing-tier-targeted-customers"></a>Prijscategorie gerichte klanten

Bekijk de tabel met gerichte klanten voor **de prijsniveauom** een beter beeld te krijgen van de prijsniveaus S0 en S1. Zie [Azure Maps-prijzen voor](https://azure.microsoft.com/pricing/details/azure-maps/)meer informatie. 

| Prijscategorie  |     Gerichte klanten                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>De S0-prijscategorie werkt voor toepassingen in alle productiestadia: van proof-of-concept-ontwikkeling en testen in een vroeg stadium tot productie en implementatie van toepassingen. Deze laag is echter ontworpen voor kleinschalige ontwikkeling of klanten met een lage gelijktijdige gebruiker of beide. <p>|
| S1            |    <p>De S1-prijscategorie is voor klanten met grootschalige bedrijfstoepassingen, bedrijfskritieke toepassingen of grote hoeveelheden gelijktijdige gebruikers. Het is ook voor klanten die geavanceerde geospatiale diensten nodig hebben.</p>|

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het weergeven en wijzigen van prijsniveaus:

> [!div class="nextstepaction"] 
> [Een prijscategorie beheren](how-to-manage-pricing-tier.md)
