---
title: Controleer de gegevenskwaliteit voor aangepaste spraak - Spraakservice
titleSuffix: Azure Cognitive Services
description: Aangepaste spraak biedt tools waarmee u de herkenningskwaliteit van een model visueel inspecteren door audiogegevens te vergelijken met het bijbehorende herkenningsresultaat. U geüploade audio afspelen en bepalen of het opgegeven herkenningsresultaat juist is.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: 60b415f69800885f37ee53cc4f090098f5ebf1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806076"
---
# <a name="inspect-custom-speech-data"></a>Aangepaste spraakgegevens inspecteren

> [!NOTE]
> Op deze pagina wordt ervan uitgegaan dat u [testgegevens voorbereiden voor aangepaste spraak](how-to-custom-speech-test-data.md) hebt gelezen en een gegevensset hebt geüpload voor inspectie.

Aangepaste spraak biedt tools waarmee u de herkenningskwaliteit van een model visueel inspecteren door audiogegevens te vergelijken met het bijbehorende herkenningsresultaat. Vanuit de [aangepaste spraakportal](https://speech.microsoft.com/customspeech)u geüploade audio afspelen en bepalen of het opgegeven herkenningsresultaat juist is. Met deze tool u snel de kwaliteit van microsoft's spraak-naar-tekstmodel of een getraind aangepast model inspecteren zonder dat u audiogegevens hoeft te transcriberen.

In dit document leert u hoe u de kwaliteit van een model visueel inspecteren met behulp van de trainingsgegevens die u eerder hebt geüpload.

Op deze pagina leert u hoe u de kwaliteit van het spraak-naar-tekstmodel van Microsoft visueel inspecteren en/of een aangepast model dat u hebt getraind. U gebruikt de gegevens die u hebt geüpload naar het tabblad **Gegevens** om te testen.

## <a name="create-a-test"></a>Een test maken

Volg deze instructies om een test te maken:

1. Meld u aan bij de [portal Aangepaste spraak](https://speech.microsoft.com/customspeech).
2. Navigeer naar **spraak-naar-tekst > aangepaste spraak > testen**.
3. Klik **op Test toevoegen**.
4. Selecteer **Kwaliteit inspecteren (alleen audiogegevens)**. Geef de test een naam, beschrijving en selecteer uw audiogegevensset.
5. Selecteer maximaal twee modellen die u wilt testen.
6. Klik **op Maken**.

Nadat een test is gemaakt, u de modellen naast elkaar vergelijken.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Vergelijkingen naast elkaar

Wanneer de teststatus is _geslaagd,_ klikt u in de naam van het testitem om de details van de test te bekijken. Deze detailpagina bevat alle uitingen in uw gegevensset, met vermelding van de herkenningsresultaten van de twee modellen naast de transcriptie van de ingediende gegevensset.

Als u de vergelijking naast elkaar wilt inspecteren, u verschillende fouttypen inschakelen, waaronder invoegen, verwijderen en vervangen. Door te luisteren naar de audio en herkenningsresultaten in elke kolom te vergelijken (met een door de mens gelabelde transcriptie en de resultaten van twee spraak-naar-tekstmodellen), u bepalen welk model aan uw behoeften voldoet en waar verbeteringen nodig zijn.

Het inspecteren van kwaliteitstests is handig om te valideren of de kwaliteit van een eindpunt voor spraakherkenning voldoende is voor een toepassing. Volg de instructies in [De nauwkeurigheid evalueren](how-to-custom-speech-evaluate-data.md)voor een objectieve nauwkeurigheidsmeting die getranscribeerde audio vereist.

## <a name="next-steps"></a>Volgende stappen

- [Uw gegevens evalueren](how-to-custom-speech-evaluate-data.md)
- [Uw model trainen](how-to-custom-speech-train-model.md)
- [Uw model implementeren](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Testgegevens voorbereiden op aangepaste spraak](how-to-custom-speech-test-data.md)
