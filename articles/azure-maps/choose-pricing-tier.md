---
title: De juiste prijs categorie kiezen | Microsoft Azure kaarten
description: In dit artikel vindt u informatie over de prijs categorieën die worden aangeboden door Microsoft Azure kaarten.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: a8bf7ff9bacd4fe84ee5b64d0aed5cb271ce06f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335661"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Kies de juiste prijs categorie in Azure Maps

Azure Maps biedt twee prijs Categorieën: S0 en S1. Het doel van dit artikel is om u te helpen bij het kiezen van de juiste prijs categorie voor uw behoeften. Als u de juiste prijs categorie wilt kiezen, kunt u de volgende twee vragen stellen.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Hoeveel gelijktijdige gebruikers wilt u ondersteunen? 
De prijs categorieën S0 en S1 verwerken verschillende hoeveel heden gegevens doorvoer. De prijs categorie S0 verwerkt Maxi maal **50 query's per seconde**. Terwijl de S1-laag **meer dan 50 query's per seconde**verwerkt.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Welke georuimtelijke mogelijkheden moet ik gebruiken?
De prijs categorie S0 is geschikt voor u als de belangrijkste georuimtelijke Api's voldoen aan uw service vereisten. Als u meer geavanceerde mogelijkheden voor uw toepassing wilt, kunt u kiezen voor de prijs categorie S1. Geavanceerde mogelijkheden zijn: lucht foto plus hybride afbeelding, route bereik ophalen en batch geocodering. Raadpleeg de tabel met mogelijkheden voor de **prijs categorie** om de prijs categorie te selecteren die het meest geschikt is voor uw toepassing.

### <a name="pricing-tier-capabilities"></a>Mogelijkheden voor prijs Categorieën

| Mogelijkheid                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Kaart weergave                              | ✓                   | ✓       |
| Satelliet afbeelding                       |                     | ✓        |
| Search                                  | ✓                    | ✓        |
| Zoeken in batch                            |                     | ✓        |
| Route                                   | ✓                    |✓        |
| Batch routering                            |                    | ✓        |
| Matrix routering                          |                     | ✓        |
| Route bereik (van isochronen)                |                     | ✓        |
| Verkeer                                |✓                    |✓        |
| Tijdzone                               |✓                    |✓        |
| Geolocatie (preview-versie)                    |✓                   |✓        |
| Ruimtelijke bewerkingen                        |                    |✓        |
| Geofencing                                |                    |✓        |
| Azure Maps gegevens (preview-versie)                |                     | ✓        |
| Mobiliteit (preview-versie)                       |                     | ✓        |
| Weer (preview-versie)                        |✓                    |✓        |

Houd rekening met de volgende extra punten:
* Welk type onderneming hebt u?
* Hoe kritiek is uw toepassing?

### <a name="pricing-tier-targeted-customers"></a>Prijs categorie gericht op klanten

Zie de tabel met de **doel klanten van de prijs categorie** voor een beter beeld van de prijs categorieën S0 en S1. Zie [Azure Maps prijzen](https://azure.microsoft.com/pricing/details/azure-maps/)voor meer informatie. 

| Prijscategorie  |     Doel klanten                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>De prijs categorie S0 werkt voor toepassingen in alle stadia van productie: van ontwikkeling van concepten en vroege fase tests op toepassings productie en-implementatie. Deze laag is echter ontworpen voor kleinschalige ontwikkeling, of klanten met weinig gelijktijdige gebruikers, of beide. <p>|
| S1            |    <p>De prijs categorie S1 is voor klanten met grootschalige bedrijfs toepassingen, essentiële toepassingen of hoge volumes van gelijktijdige gebruikers. Het is ook bedoeld voor klanten die geavanceerde georuimtelijke services nodig hebben.</p>|

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het weer geven en wijzigen van prijs Categorieën:

> [!div class="nextstepaction"] 
> [Een prijs categorie beheren](how-to-manage-pricing-tier.md)
