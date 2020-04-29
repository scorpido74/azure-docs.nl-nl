---
title: Release opmerkingen-face service
titleSuffix: Azure Cognitive Services
description: Release opmerkingen voor de face-service bevatten een geschiedenis van release wijzigingen voor verschillende versies.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: 767c9dec373a2bda806d75d602b194edde98c6b5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76165872"
---
# <a name="face-release-notes"></a>Opmerkingen bij de release van het gezicht

Dit artikel heeft betrekking op Service versie 1,0.

### <a name="release-changes-in-june-2019"></a>Release wijzigingen in juni 2019

* Er is een nieuw gezichts detectie model toegevoegd met verbeterde nauw keurigheid van kleine, weer gave-, occluded-en wazige gezichten. Gebruik deze methode [via gezichts detectie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList-add](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)gezicht, [PersonGroup persoon-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) gezicht toevoegen en [LargePersonGroup persoon-gezicht](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) toevoegen door de naam `detection_02` van het nieuwe gezichts detectie model op `detectionModel` te geven in de para meter. Meer informatie [over het opgeven van een detectie model](Face-API-How-to-Topics/specify-detection-model.md).

### <a name="release-changes-in-april-2019"></a>Release wijzigingen in april 2019

* Verbeterde algehele nauw keurigheid `age` van `headPose` de kenmerken en. Het `headPose` kenmerk wordt ook bijgewerkt met de `pitch` waarde nu ingeschakeld. Gebruik deze kenmerken door ze op te geven `returnFaceAttributes` in de para meter van de para meter [Face-detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` . 

* Verbeterde snelheid van [gezichts detectie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList:](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)LargeFaceList toevoegen, [gezicht](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)toevoegen, [PersonGroup persoon-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) gezicht toevoegen en [LargePersonGroup persoon-gezicht](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)toevoegen.

### <a name="release-changes-in-march-2019"></a>Release wijzigingen in maart 2019

* Er is een nieuw gezichts herkennings model toegevoegd met verbeterde nauw keurigheid. Gebruik deze methode [via gezichts detectie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList maken](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [PersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) en [LargePersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) door de nieuwe naam `recognition_02` van het gezichts erkennings model op te geven in `recognitionModel` de para meter. Meer informatie [over het opgeven van een herkennings model](Face-API-How-to-Topics/specify-recognition-model.md).

### <a name="release-changes-in-january-2019"></a>Release wijzigingen in januari 2019

* De momentopname functie is toegevoegd voor de ondersteuning van gegevens migratie tussen abonnementen: [moment opname](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Meer informatie [over het migreren van uw gezichts gegevens naar een ander gezichts abonnement](Face-API-How-to-Topics/how-to-migrate-face-data.md).

### <a name="release-changes-in-october-2018"></a>Release wijzigingen in oktober 2018

* Verfijnde `status`beschrijving `createdDateTime`voor `lastActionDateTime`,, `lastSuccessfulTrainingDateTime` , en in [PersonGroup: trainings status ophalen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup-trainings status ophalen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)en [LargeFaceList-trainings status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf)ophalen.

### <a name="release-changes-in-may-2018"></a>Release wijzigingen in mei 2018

* Verbeterd `gender` kenmerk aanzienlijk en ook verbeterd `age`, `glasses`, `facialHair`, `hair`, `makeup` kenmerken. U kunt ze gebruiken via de para meter voor de [detectie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` van het gezicht. 

* De maximale grootte van het invoer afbeeldings bestand is van 4 MB tot 6 MB in een [gezichts detectie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList: add face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList-add](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)face, [PersonGroup persoon-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) add gezicht en [LargePersonGroup person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Release wijzigingen in maart 2018

* Er is een $ scale-container toegevoegd: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) en [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Meer informatie [over het gebruik van de grootschalige functie](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Verhoogde ' [Face-identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` ' para meter van [1, 5] tot [1, 100] en standaard ingesteld op 10.

### <a name="release-changes-in-may-2017"></a>Release wijzigingen in mei 2017

* Toegevoegde `hair`, `makeup`, `accessory` `exposure` `noise` [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` ,,, en kenmerken in de para meter voor de detectie van het gezichts kenmerk. `occlusion` `blur`

* Ondersteunde 10.000 personen in een PersonGroup en [Face-identificeren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Ondersteunde paginering in [PersonGroup person-List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) met optionele para `start` meters `top`: en.

* Ondersteunde gelijktijdigheid bij het toevoegen/verwijderen van gezichten voor verschillende FaceLists en verschillende personen in PersonGroup.

### <a name="release-changes-in-march-2017"></a>Release wijzigingen in maart 2017
* Het `emotion` kenmerk is toegevoegd in de para meter [Face-detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` .

* Het vlak kan niet opnieuw worden gedetecteerd met een rechthoek die is geretourneerd door een [gezichts detectie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) , zoals `targetFace` in [FaceList-add face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) en [PersonGroup persoon](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)toevoegen.

* De detectie grootte is opgelost om ervoor te zorgen dat deze zich tussen 36x36 en 4096x4096 pixels bevindt.

### <a name="release-changes-in-november-2016"></a>Release wijzigingen in november 2016
* Gezichtsopslag standaard abonnement toegevoegd voor het opslaan van extra persistente gezichten bij het gebruik van [PersonGroup person-add face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) of [FaceList-add gezicht](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) voor identificatie of gelijkenis met overeenkomst. De opgeslagen afbeeldingen worden als volgt in rekening gebracht: $0,5 per 1000 gezichten. Dit tarief is per dag pro rato. Abonnementen met een gratis laag blijven beperkt tot 1.000 totaal aantal personen.

### <a name="release-changes-in-october-2016"></a>Release wijzigingen in oktober 2016
* Het fout bericht van meer dan één gezicht in het targetFace is gewijzigd vanuit ' er zijn meer dan één gezicht in de afbeelding ' naar ' er is meer dan één gezicht in de afbeelding ' in [FaceList-add gezicht](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) en [PersonGroup persoon-add face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Release wijzigingen in juli 2016
* Ondersteuning van het gezichts object voor persoons objecten in [Face-verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* `mode` Optionele para meter voor het inschakelen van de selectie van `matchPerson` twee `matchFace` werk modi: en in [gezicht-Find lijkt op soort gelijke](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) en standaard waarde `matchPerson`.

* De optionele `confidenceThreshold` para meter voor de gebruiker is toegevoegd om de drempel waarde in te stellen van het feit of een gezicht deel uitmaakt van een persoons object in [Face-identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Optionele `start` en `top` para meters toegevoegd aan de [PersonGroup-lijst](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) om de gebruiker in staat te stellen het begin punt en het totale PersonGroups-nummer op te geven.

### <a name="v10-changes-from-v0"></a>V 1.0 wijzigingen van v0
* Het service basis eindpunt is ```https://westus.api.cognitive.microsoft.com/face/v0/``` bijgewerkt ```https://westus.api.cognitive.microsoft.com/face/v1.0/```van naar. Wijzigingen toegepast op: [gezichts detectie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [gezichts identificatie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face-Zoek vergelijk bare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) en [gezichts groep](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* De minimale Detecteer bare face-grootte is bijgewerkt naar 36x36 pixels. Vlakken die kleiner zijn dan 36x36 pixels, worden niet gedetecteerd.

* De PersonGroup en persoons gegevens in gezicht v0 zijn afgeschaft. Deze gegevens zijn niet toegankelijk met de face V 1.0-service.

* Het eind punt v0 van Face-API is afgeschaft op 30 juni 2016.
