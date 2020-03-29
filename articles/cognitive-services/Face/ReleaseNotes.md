---
title: Release notes - Face service
titleSuffix: Azure Cognitive Services
description: Release notes voor de Face-service bevatten een geschiedenis van releasewijzigingen voor verschillende versies.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: 767c9dec373a2bda806d75d602b194edde98c6b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76165872"
---
# <a name="face-release-notes"></a>Gezichtsreleasenotities

Dit artikel heeft betrekking op Face-serviceversie 1.0.

### <a name="release-changes-in-june-2019"></a>Releasewijzigingen in juni 2019

* Een nieuw gezichtsherkenningsmodel toegevoegd met verbeterde nauwkeurigheid op kleine, zijaanzicht, afgesloten en wazige gezichten. Gebruik het via [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) and [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) by specifying the new face detection model name `detection_02` in `detectionModel` parameter. Meer details in [Hoe een detectiemodel op te geven](Face-API-How-to-Topics/specify-detection-model.md).

### <a name="release-changes-in-april-2019"></a>Releasewijzigingen in april 2019

* Verbeterde algehele nauwkeurigheid `age` van `headPose` de en attributen. Het `headPose` kenmerk wordt ook `pitch` bijgewerkt met de waarde die nu is ingeschakeld. Gebruik deze kenmerken door ze `returnFaceAttributes` op te geven in de parameter [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parameter. 

* Verbeterde snelheid van [het gezicht - Detecteren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Voeg Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Voeg Gezicht](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Voeg Gezicht](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) en [LargePersonGroup Person - Face toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2019"></a>Releasewijzigingen in maart 2019

* Een nieuw gezichtsherkenningsmodel toegevoegd met verbeterde nauwkeurigheid. Gebruik het via [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) and [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) by specifying the new face recognition model name `recognition_02` in `recognitionModel` parameter. Meer details in [Hoe een herkenningsmodel op te geven](Face-API-How-to-Topics/specify-recognition-model.md).

### <a name="release-changes-in-january-2019"></a>Releasewijzigingen in januari 2019

* Added Snapshot-functie om gegevensmigratie tussen abonnementen te ondersteunen: [Momentopname](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Meer informatie over [Hoe uw gezichtsgegevens te migreren naar een ander Face-abonnement](Face-API-How-to-Topics/how-to-migrate-face-data.md).

### <a name="release-changes-in-october-2018"></a>Releasewijzigingen in oktober 2018

* Verfijnde beschrijving `status`voor `createdDateTime` `lastActionDateTime`, `lastSuccessfulTrainingDateTime` , en in [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5), en [LargeFaceList - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

### <a name="release-changes-in-may-2018"></a>Releasewijzigingen in mei 2018

* Verbeterd `gender` attribuut aanzienlijk `age`en `glasses` `facialHair`ook `hair` `makeup` verbeterd , , , attributen. Gebruik ze via [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parameter. 

* Verhoogde limiet voor de bestandsgrootte van invoerafbeeldingen van 4 MB naar 6 MB in [Face - Detecteren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)toevoegen , [LargeFaceList - Gezicht toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [Persoonsgroeppersoon - Gezicht toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) en een persoon toevoegen - Gezicht [toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Releasewijzigingen in maart 2018

* Container met miljoenenschaal toegevoegd: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) en [LargePersonGroup.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) Meer details in [Hoe de grootschalige functie te gebruiken.](Face-API-How-to-Topics/how-to-use-large-scale.md)

* Verhoogd [gezicht - Identificeer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` de parameter van [1, 5] tot [1, 100] en standaard naar 10.

### <a name="release-changes-in-may-2017"></a>Releasewijzigingen in mei 2017

* Added `hair` `makeup`, `accessory` `occlusion`, `blur` `exposure`, `noise` , , , and attributes in [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parameter.

* Ondersteunde 10.000 personen in een persoonsgroep en [face - identificeren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Ondersteunde paginatie in [Persoonsgroeppersoon -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) `start` Lijst `top`met optionele parameters: en .

* Ondersteunde gelijktijdigheid bij het toevoegen/verwijderen van gezichten ten opzichte van verschillende FaceLists en verschillende personen in PersonGroup.

### <a name="release-changes-in-march-2017"></a>Releasewijzigingen in maart 2017
* Attribuut `emotion` toegevoegd in [Face - Parameter detecteren.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`

* Fixed the face could not betected with rectangle `targetFace` returned from Face - [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) as in [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) and [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Fixed the detectable face size to make sure it strictly between 36x36 to 4096x4096 pixels.

### <a name="release-changes-in-november-2016"></a>Releasewijzigingen in november 2016
* Toegevoegd Face Storage Standard-abonnement om extra aanhoudende gezichten op te slaan bij het gebruik van [PersonGroup Person - Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) of [FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) toevoegen - Gezicht toevoegen voor identificatie of overeenkomst waarbij overeenkomsten worden overeenkomen. De opgeslagen afbeeldingen worden als volgt in rekening gebracht: $0,5 per 1000 gezichten. Dit tarief is per dag pro rato. Gratis tier abonnementen blijven beperkt tot 1.000 totaal personen.

### <a name="release-changes-in-october-2016"></a>Releasewijzigingen in oktober 2016
* Veranderde de foutmelding van meer dan één gezicht in het doelFace van 'Er zijn meer dan één gezicht in de afbeelding' naar 'Er is meer dan één gezicht in de afbeelding' in [FaceList - Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) en [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Releasewijzigingen in juli 2016
* Ondersteunde verificatie van object face-to-person in [Face - Verifiëren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Optionele `mode` parameter toegevoegd die selectie `matchPerson` van `matchFace` twee werkmodi mogelijk `matchPerson`maakt: en in Face - Vergelijkbare en standaardinstelling [zoeken](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) is .

* Optionele `confidenceThreshold` parameter toegevoegd voor de gebruiker om de drempel waarde in te stellen van de vraag of één gezicht behoort tot een persoonsobject in [Face - Identificeren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Optionele `start` en `top` parameters toegevoegd in [PersonGroup - Lijst](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) om de gebruiker in staat te stellen het beginpunt en het totale aantal persoonsgroepen op te geven dat moet worden vermeld.

### <a name="v10-changes-from-v0"></a>V1.0 wijzigingen ten opzichte van V0
* Bijgewerkt hoofdeindpunt van ```https://westus.api.cognitive.microsoft.com/face/v0/``` ```https://westus.api.cognitive.microsoft.com/face/v1.0/```de service van naar . Wijzigingen toegepast op: [Gezicht - Detecteren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), Gezicht - [Identificeren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), Gezicht - [Zoek vergelijkbaar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) en gezicht [- Groep](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Bijgewerkt de minimale detecteerbare gezicht grootte tot 36x36 pixels. Gezichten kleiner dan 36x36 pixels worden niet gedetecteerd.

* Afgeschaft de persongroup- en persoonsgegevens in Face V0. Deze gegevens zijn niet toegankelijk met de Face V1.0-service.

* Afgeschaft de V0 eindpunt van Face API op 30 juni 2016.
