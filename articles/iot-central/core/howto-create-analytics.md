---
title: Analyseer uw apparaatgegevens in uw Azure IoT Central-toepassing | Microsoft Docs
description: In dit artikel wordt beschreven hoe u uw apparaatgegevens in uw Azure IoT Central-toepassing kunt analyseren met behulp van query's en visualisaties.
author: lmasieri
ms.author: lmasieri
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a467e0e6e8967cf963ad099f83de6718330aa43f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827984"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Analyse gebruiken om uw apparaatgegevens te analyseren

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

Azure IoT Central biedt uitgebreide analyse mogelijkheden om inzicht te geven in grote hoeveel heden gegevens van uw apparaten. Ga naar **Analytics** in het linkerdeel venster om aan de slag te gaan.

## <a name="querying-your-data"></a>Query's uitvoeren op uw gegevens

U moet een **apparaatset**kiezen, een **filter** toevoegen (optioneel) en een **tijds periode** selecteren om aan de slag te gaan. Wanneer u klaar bent, selecteert u **resultaten weer geven** om uw gegevens te visualiseren.

* **Apparaatsets:** Een [apparaatset](howto-use-device-sets.md) is een door de gebruiker gedefinieerde groep van uw apparaten. Bijvoorbeeld alle koel kasten in Oakland, of alle Rev 2,0 wikkeling-turbines.

* **Filters:** U kunt eventueel filters toevoegen aan uw zoek opdracht om uw gegevens te deselecteren. U kunt Maxi maal 10 filters tegelijk toevoegen. Bijvoorbeeld, in alle koel kasten in Oakland, zoek naar die met een Tempe ratuur van meer dan 60 graden.
* **Tijds periode:** Standaard worden gegevens opgehaald uit de afgelopen 10 minuten. U kunt deze waarde wijzigen in een van de vooraf gedefinieerde Peri Oden of een aangepaste tijds periode selecteren.

  ![Analytics-query](media/howto-create-analytics/analytics-query.png)

## <a name="visualizing-your-data"></a>Uw gegevens visualiseren

Zodra u een query hebt uitgevoerd op uw gegevens, kunt u deze eerst visualiseren. U kunt metingen weer geven/verbergen, de manier wijzigen waarop gegevens worden geaggregeerd en de gegevens verder splitsen op basis van andere Apparaateigenschappen.  

* **Splitsen op:** Door gegevens te splitsen op apparaat-eigenschappen kunt u verder inzoomen op uw gegevens. U kunt bijvoorbeeld uw resultaten splitsen op apparaat-ID of locatie.

* **Metingen:** U kunt kiezen of u Maxi maal 10 verschillende telemetriegegevens wilt weer geven of verbergen die door uw apparaten worden gerapporteerd. Metingen zijn dingen als Tempe ratuur en vochtigheid.

* **Aggregatie:** Standaard verzamelen we gegevens op basis van het gemiddelde, maar u kunt ervoor kiezen om de gegevens aggregatie te wijzigen in iets anders dat aan uw behoeften voldoet.

   ![Visualisatie van analytische elementen splitsen door](media/howto-create-analytics/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interactie met uw gegevens

U hebt verschillende manieren om uw query resultaten te wijzigen zodat deze voldoen aan uw visualisatie behoeften. U kunt wisselen tussen een grafiek weergave en een raster weergave, in-en uitzoomen, uw gegevensset vernieuwen en wijzigen hoe lijnen worden weer gegeven.

* **Raster weer geven:** De resultaten zijn beschikbaar in een tabel indeling, zodat u de specifieke waarde voor elk gegevens punt kunt weer geven. Deze weer gave voldoet ook aan de toegankelijkheids normen.
* **Grafiek weer geven:** De resultaten worden weer gegeven in een regel indeling om u te helpen bij het identificeren van een opwaartse of neerwaartse trends en afwijkingen.

  ![De raster weergave voor uw analyse weer geven](media/howto-create-analytics/analytics-showgrid.png)

Als u inzoomt, kunt u op uw gegevens thuis. Als u een periode vindt waarop u zich wilt richten in de resultatenset, gebruikt u de cursor om het gebied te halen waarop u wilt inzoomen en gebruikt u de beschik bare besturings elementen om een van de volgende acties uit te voeren:

* **Inzoomen:** Wanneer u een periode hebt geselecteerd, inzoomen is ingeschakeld en kunt u inzoomen op uw gegevens.
* **Uitzoomen:** Met dit besturings element kunt u één niveau uit de laatste zoomen uitzoomen. Als u bijvoorbeeld drie keer inzoomt op uw gegevens, keert uitzoomen u één stap per keer terug.
* **Zoom instelling opnieuw instellen:** Wanneer u verschillende zoom niveaus hebt uitgevoerd, kunt u het besturings element zoom reset gebruiken om terug te keren naar de oorspronkelijke resultatenset.

  ![Zoomen op uw gegevens uitvoeren](media/howto-create-analytics/analytics-zoom.png)

U kunt de lijn stijl wijzigen zodat deze aan uw behoeften voldoet. U hebt vier opties:

* **Regel:** Een vlakke lijn tussen elk van de gegevens punten.
* **Glad:** Een gekromde lijn tussen elk punt.
* **Stap:** De lijn tussen elk punt in de grafiek is een stap.
* **Sprei ding:** Alle punten worden weer gegeven in de grafiek zonder lijnen die er verbinding mee maken.

  ![Verschillende regel typen die beschikbaar zijn in Analytics](media/howto-create-analytics/analytics-linetypes.png)

Ten slotte kunt u uw gegevens op de Y-as rangschikken door te kiezen uit een van de drie modi:

* **Gestapeld:** Een grafiek voor elke meting wordt gestapeld en alle grafieken hebben hun eigen Y-as. Gestapelde grafieken zijn handig wanneer u meerdere metingen hebt geselecteerd en een unieke weer gave van deze metingen wilt hebben.
* Niet **gestapeld:** Een grafiek voor elke meting wordt op één Y-as getekend, maar de waarden voor de Y-as worden gewijzigd op basis van de gemarkeerde meting. Niet-gestapelde grafieken zijn handig wanneer u meerdere metingen wilt bedekken en patronen wilt zien over deze metingen voor hetzelfde tijds bereik.
* **Gedeelde Y-as:** Alle grafieken delen dezelfde Y-as en de waarden voor de as worden niet gewijzigd. Gedeelde Y-as-grafieken zijn handig wanneer u een enkele meting wilt bekijken terwijl u de gegevens met splitsen-op wilt segmenteren.

  ![Gegevens rangschikken op de y-as met verschillende visualisatie modi](media/howto-create-analytics/analytics-yaxis.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u aangepaste analyses voor uw Azure IoT Central-toepassing maakt, is de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Een node. js-toepassing voorbereiden en verbinden](howto-connect-nodejs.md)