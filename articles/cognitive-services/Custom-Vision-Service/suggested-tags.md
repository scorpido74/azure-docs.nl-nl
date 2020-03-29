---
title: Afbeeldingen sneller labelen met Smart Labeler
titleSuffix: Azure Cognitive Services
description: In deze handleiding leert u hoe u Smart Labeler gebruiken om voorgestelde tags voor afbeeldingen te genereren. Hiermee u een groot aantal afbeeldingen sneller labelen bij het trainen van een Custom Vision-model.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647755"
---
# <a name="label-images-faster-with-smart-labeler"></a>Afbeeldingen sneller labelen met Smart Labeler

In deze handleiding leert u hoe u Smart Labeler gebruiken om voorgestelde tags voor afbeeldingen te genereren. Hiermee u een groot aantal afbeeldingen sneller labelen bij het trainen van een Custom Vision-model.

Wanneer u afbeeldingen tagt voor een Custom Vision-model, gebruikt de service de nieuwste getrainde iteratie van het model om de labels van niet-gelabelde afbeeldingen te voorspellen. Het toont vervolgens deze voorspellingen als voorgestelde tags, op basis van de geselecteerde betrouwbaarheidsdrempel en voorspellingsonzekerheid. U de suggesties vervolgens bevestigen of wijzigen, waardoor het proces van het handmatig taggen van de afbeeldingen voor training wordt versneld.

## <a name="when-to-use-smart-labeler"></a>Wanneer smart labeler gebruiken

Houd rekening met de volgende beperkingen:

* U moet alleen voorgestelde tags aanvragen voor afbeeldingen waarvan de inhoud al een keer is getraind. Krijg geen suggesties voor een nieuwe tag die je net begint te trainen.

> [!IMPORTANT]
> De Smart Labeler-functie gebruikt hetzelfde [prijsmodel](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) als reguliere voorspellingen. De eerste keer dat u voorgestelde tags voor een reeks afbeeldingen activeert, wordt hetzelfde in rekening gebracht als voor voorspellingsoproepen. Daarna slaat de service de resultaten voor de geselecteerde afbeeldingen gedurende 30 dagen op in een database en u hebt er binnen die periode altijd gratis toegang toe. Na 30 dagen worden er kosten in rekening gebracht als je hun voorgestelde tags opnieuw aanvraagt.

## <a name="smart-labeler-workflow"></a>Smart Labeler-werkstroom

In de volgende stappen ziet u hoe u Smart Labeler gebruikt:

1. Upload al je trainingsafbeeldingen naar je Custom Vision-project.
1. Label een deel van uw gegevensset en kies een gelijk aantal afbeeldingen voor elke tag.
    > [!TIP]
    > Zorg ervoor dat u alle tags gebruikt waarvoor u later suggesties wilt.
1. Start het trainingsproces.
1. Wanneer de training is voltooid, navigeert u naar de **weergave Niet-gelabeld en** selecteert u de knop **Voorgestelde tags opdevorige plaatsen** in het linkerdeelvenster.
    > [!div class="mx-imgBorder"]
    > ![De knop voorgestelde tags wordt weergegeven onder het tabblad niet-gelabelde afbeeldingen.](./media/suggested-tags/suggested-tags-button.png)
1. Stel in het pop-upvenster dat wordt weergegeven het aantal afbeeldingen in waarvoor u suggesties wilt. U moet alleen eerste tagsuggesties krijgen voor een deel van de niet-gelabelde afbeeldingen. Je krijgt betere tag suggesties als je herhalen door dit proces.
1. Bevestig de voorgestelde tags en repareer alle tags die niet correct zijn.
    > [!TIP]
    > Afbeeldingen met voorgestelde tags worden gesorteerd op hun voorspellingsonzekerheid (lagere waarden geven een hoger vertrouwen aan). U de sorteervolgorde wijzigen met de optie **Sorteren op onzekerheid.** Als u de volgorde op **hoog naar laag**stelt, u eerst de voorspellingen met grote onzekerheid corrigeren en vervolgens snel de lage onzekerheid bevestigen.
    * In afbeeldingsclassificatieprojecten u tags in batches selecteren en bevestigen. Filter de weergave op een bepaalde voorgestelde tag, schakel afbeeldingen uit die verkeerd zijn getagd en bevestig de rest in een batch.
        > [!div class="mx-imgBorder"]
        > ![Voorgestelde tags worden weergegeven in batchmodus voor IC met filters.](./media/suggested-tags/ic-batch-mode.png)

        U ook voorgestelde tags gebruiken in de individuele afbeeldingsmodus door een afbeelding in de galerie te selecteren.

        ![Voorgestelde tags worden weergegeven in de individuele beeldmodus voor IC.](./media/suggested-tags/ic-individual-image-mode.png)
    * In objectdetectieprojecten worden batchbevestigingen niet ondersteund, maar u nog steeds filteren en sorteren op voorgestelde tags voor een meer georganiseerde labelervaring. Miniaturen van uw niet-gelabelde afbeeldingen tonen een overlay van selectiekaders die de locaties van voorgestelde tags aangeven. Als u geen voorgesteld tagfilter selecteert, worden al uw niet-gelabelde afbeeldingen weergegeven zonder de selectievakken te overbelasten.
        > [!div class="mx-imgBorder"]
        > ![Voorgestelde tags worden weergegeven in batchmodus voor OD met filters.](./media/suggested-tags/od-batch-mode.png)

        Als u objectdetectietags wilt bevestigen, moet u deze toepassen op elke afzonderlijke afbeelding in de galerie.

        ![Voorgestelde tags worden weergegeven in de individuele afbeeldingsmodus voor OD.](./media/suggested-tags/od-individual-image-mode.png)
1. Start het trainingsproces opnieuw.
1. Herhaal de voorgaande stappen totdat u tevreden bent met de suggestiekwaliteit.

## <a name="next-steps"></a>Volgende stappen

Volg een snelle start om aan de slag te gaan met het maken en trainen van een Custom Vision-project.

* [Een classificatie maken](getting-started-build-a-classifier.md)
* [Een objectdetector maken](get-started-build-detector.md)