---
title: De juiste prijs categorie kiezen | Microsoft Azure kaarten
description: In dit artikel vindt u informatie over de prijs categorieën die worden aangeboden door Microsoft Azure kaarten.
author: walsehgal
ms.author: v-musehg
ms.date: 01/02/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5ff774f9848db948058075a98504e6c13db5467a
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911753"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Kies de juiste prijs categorie in Azure Maps

Azure Maps biedt twee prijscategorieën. Het doel van dit artikel is om u te helpen bij het kiezen van de juiste prijs categorie voor uw behoeften. Als u de juiste prijs categorie wilt kiezen, kunt u de volgende twee vragen stellen.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Welke georuimtelijke mogelijkheden moet ik gebruiken?
De prijs categorie S0 is geschikt voor u als de belangrijkste georuimtelijke Api's voldoen aan uw service vereisten. Als u meer geavanceerde mogelijkheden voor uw toepassing wilt, kunt u kiezen voor de prijs categorie S1. Voor beelden van mogelijkheden zijn Areal plus hybride installatie kopieën, het route bereik ophalen en batch geocodering. De tabel met **mogelijkheden voor prijs categorieën** die volgt, geeft u een beter idee van de behoeften van uw toepassing. Daarnaast kunt u een prijs categorie kiezen die het meest geschikt is voor uw toepassing.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Hoeveel gelijktijdige gebruikers wilt u ondersteunen? 
De prijs categorieën S0 en S1 verwerken verschillende hoeveel heden gegevens doorvoer. Stel uzelf enkele vragen voordat u een prijs categorie voor Azure Maps kiest. Een voor beeld is ' hoeveel gelijktijdige gebruikers wilt u ondersteunen? ' De prijs categorie S0 verwerkt Maxi maal **50 query's per seconde**. De prijs categorie S1 verwerkt **meer dan 50 query's per seconde**.

### <a name="pricing-tier-capabilities"></a>Mogelijkheden voor prijs Categorieën

| Mogelijkheid                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Zoeken (FWD/REV geocodering, interessante punten)  |        ✓           |     ✓    |
| Batch Geocode ring (preview-versie)              |                   |     ✓    |
| Veelhoeken uit zoek opdracht          |                   |     ✓    |
| Routes                                 |        ✓           |     ✓    |
| Route bereik                    |                   |     ✓    |
| Batch routering (preview-versie)                |                   |     ✓    |
| Matrix routering (preview-versie)               |                   |     ✓    |
| Renderen                                  |        ✓           |     ✓    |
| Afbeeldings-en hybride installatie kopieën    |            |     ✓    |
| Verkeer                                 |        ✓           |     ✓    |
| Tijdzones                              |        ✓           |     ✓    |
| Geolocatie (preview-versie)                |        ✓           |     ✓    |
| Gegevens (preview-versie)               |                   |     ✓    |
| Ruimtelijke (preview-versie)               |                   |     ✓    |
| Geoomheining (preview-versie)               |                   |     ✓    |



Deze aanvullende gegevens punten zijn waard:
* Wat voor soort bedrijf hebt u?
* Hoe kritiek is de toepassing die wordt gebouwd?

Zie de tabel met de **doel klanten van de prijs categorie** voor een beter beeld van de prijs categorieën S0 en S1. Zie [Azure Maps prijzen](https://azure.microsoft.com/pricing/details/azure-maps/)voor meer informatie. 

### <a name="pricing-tier-targeted-customers"></a>Prijs categorie gericht op klanten

| Prijscategorie  |     Doel klanten                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>De prijs categorie S0 is voor klanten die kleine of middel grote ondernemingen zijn. Het is de juiste prijs categorie voor u als u geen hoge volumes van gelijktijdige gebruikers verwacht. Het is ook geschikt als de kern-georuimtelijke Api's die in de voor gaande tabel worden weer gegeven, voldoen aan uw service vereisten. Deze laag is algemeen beschikbaar. Het werkt voor toepassingen in alle stadia van productie: van ontwikkeling van concepten en vroege fase tests op toepassings productie en-implementatie.<p>|
| S1            |    <p>De prijs categorie S1 is voor klanten die ondersteuning moeten bieden voor grootschalige ondernemingen, essentiële toepassingen of hoge volumes van gelijktijdige gebruikers. Het is ook bedoeld voor klanten die geavanceerde georuimtelijke services nodig hebben.</p>|

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het weer geven en wijzigen van prijs Categorieën:

> [!div class="nextstepaction"] 
> [Een prijs categorie beheren](how-to-manage-pricing-tier.md)
