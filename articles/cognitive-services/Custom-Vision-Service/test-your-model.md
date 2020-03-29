---
title: Een model testen en omscholen - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: In dit artikel ziet u hoe u een afbeelding testen en vervolgens gebruiken om uw model om te scholen in de Custom Vision-service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: c6ed8869f6d33609381a42fd22d728e9e5542802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73721195"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Een model testen en omscholen met Custom Vision Service

Nadat u uw model hebt getraind, u het snel testen met behulp van een lokaal opgeslagen afbeelding of een online afbeelding. De test maakt gebruik van de meest recent getrainde iteratie.

## <a name="test-your-model"></a>Uw model testen

1. Selecteer uw project op de [webpagina Aangepast zicht.](https://customvision.ai) Selecteer **Snelle test** rechts van de bovenste menubalk. Met deze actie wordt een venster geopend met het label **Snelle test**.

    ![De knop Snelle test wordt in de rechterbovenhoek van het venster weergegeven.](./media/test-your-model/quick-test-button.png)

2. Klik in het venster **Snelle test** in het veld **Afbeelding verzenden** en voer de URL in van de afbeelding die u voor uw test wilt gebruiken. Als u in plaats daarvan een lokaal opgeslagen afbeelding wilt gebruiken, klikt u op de knop **Lokale bestanden bladeren** en selecteert u een lokaal afbeeldingsbestand.

    ![Afbeelding van de afbeeldingspagina verzenden](./media/test-your-model/submit-image.png)

De afbeelding die u selecteert, wordt in het midden van de pagina weergegeven. Vervolgens worden de resultaten onder de afbeelding weergegeven in de vorm van een tabel met twee kolommen met het label **Tags** en **Vertrouwen**. Nadat u de resultaten hebt bekeken, u het venster **Snelle test** sluiten.

U deze testafbeelding nu aan uw model toevoegen en vervolgens uw model omscholen.

## <a name="use-the-predicted-image-for-training"></a>De voorspelde afbeelding gebruiken voor training

Als u de afbeelding die eerder is ingediend voor training wilt gebruiken, gebruikt u de volgende stappen:

1. Als u afbeeldingen wilt weergeven die naar de classificatie zijn verzonden, opent u de [webpagina Aangepast zicht](https://customvision.ai) en selecteert u het tabblad __Voorspellingen.__

    ![Afbeelding van het tabblad Voorspellingen](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > De standaardweergave toont afbeeldingen van de huidige iteratie. U het __vervolgkeuzeveld Iteratie__ gebruiken om afbeeldingen weer te geven die tijdens eerdere iteraties zijn ingediend.

2. Plaats de plaats in de boventoon van een afbeelding om de tags te zien die door de classificatie zijn voorspeld.

    > [!TIP]
    > Afbeeldingen worden gerangschikt, zodat de afbeeldingen die de meeste winst naar de classificatie kunnen brengen, bovenaan staan. Als u een andere sortering wilt selecteren, gebruikt u de sectie __Sorteren.__

    Als u een afbeelding aan uw trainingsgegevens wilt toevoegen, selecteert u de afbeelding, selecteert u de tag en selecteert u __Opslaan en sluiten__. De afbeelding wordt verwijderd uit __Voorspellingen__ en toegevoegd aan de trainingsafbeeldingen. U het bekijken door het tabblad __Trainingsafbeeldingen te__ selecteren.

    ![Afbeelding van de tagpagina](./media/test-your-model/tag-image.png)

3. Gebruik de __knop Trein__ om de classificatie om te scholen.

## <a name="next-steps"></a>Volgende stappen

[De classificatie verbeteren](getting-started-improving-your-classifier.md)
