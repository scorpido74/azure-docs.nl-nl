---
title: De gegevens kwaliteit van de Custom Speech speech-service controleren
titleSuffix: Azure Cognitive Services
description: Custom Speech biedt hulp middelen waarmee u de herkennings kwaliteit van een model visueel kunt controleren door audio gegevens te vergelijken met het bijbehorende herkennings resultaat. U kunt geüploade audio afspelen en bepalen of het gegeven herkennings resultaat juist is.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: 7a8fec876556d943d29756a38ffc27ae8095e3c4
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/06/2020
ms.locfileid: "84466235"
---
# <a name="inspect-custom-speech-data"></a>Custom Speech-gegevens controleren

> [!NOTE]
> Op deze pagina wordt ervan uitgegaan dat u [test gegevens voorbereiden voor Custom speech](how-to-custom-speech-test-data.md) hebt gelezen en een gegevensset hebt geüpload voor inspectie.

Custom Speech biedt hulp middelen waarmee u de herkennings kwaliteit van een model visueel kunt controleren door audio gegevens te vergelijken met het bijbehorende herkennings resultaat. Vanuit de [Custom speech Portal](https://speech.microsoft.com/customspeech)kunt u geüploade audio afspelen en bepalen of het gegeven herkennings resultaat juist is. Met dit hulp programma kunt u de kwaliteit van het spraak-naar-tekst model van micro soft-basis lijn controleren, een getraind aangepast model controleren of transcriptie vergelijken met twee modellen.

In dit document leert u hoe u de kwaliteit van een model visueel kunt controleren met behulp van de trainings gegevens die u eerder hebt geüpload.

Op deze pagina leert u hoe u de kwaliteit van het spraak-naar-tekst model van micro soft en/of een aangepast model dat u hebt getraind, visueel kunt controleren. U gebruikt de gegevens die u hebt geüpload naar het tabblad **gegevens** om te testen.

## <a name="create-a-test"></a>Een test maken

Volg deze instructies om een test te maken:

1. Meld u aan bij de [Custom speech Portal](https://speech.microsoft.com/customspeech).
2. Navigeer naar **> voor spraak naar tekst Custom Speech > tests**uit te voeren.
3. Klik op **test toevoegen**.
4. Selecteer **kwaliteit controleren (alleen audio gegevens)**. Geef de test een naam, beschrijving en selecteer uw audio gegevensset.
5. Selecteer Maxi maal twee modellen die u wilt testen.
6. Klik op **Create**.

Nadat een test is gemaakt, kunt u zien hoe een model de door u opgegeven audio gegevensset transcribeert of resultaten vergelijken van twee modellen naast elkaar.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Vergelijking van modellen naast elkaar

Wanneer de test status is _geslaagd_, klikt u op de naam van het test item om de details van de test te bekijken. Deze detail pagina bevat een lijst met alle uitingen in uw gegevensset en toont de herkennings resultaten van de twee modellen die u vergelijkt.

Als u de gelijktijdige vergelijking wilt controleren, kunt u verschillende fout typen met inbegrip van invoegen, verwijderen en vervangen. Als u naar de audio luistert en de herkennings resultaten in elke kolom vergelijkt (met transcriptie en de resultaten van twee spraak-naar-tekst modellen), kunt u bepalen welk model aan uw behoeften voldoet en waar verbeteringen nodig zijn.

Het is handig om te controleren welk spraakherkennings model het meest geschikt is voor een toepassing. Volg de instructies in [evalueren nauw keurigheid](how-to-custom-speech-evaluate-data.md)voor een objectieve maat regel voor nauw keurigheid, voor het vereisen van getranscribeerde audio.

## <a name="next-steps"></a>Volgende stappen

- [Uw gegevens evalueren](how-to-custom-speech-evaluate-data.md)
- [Uw model trainen](how-to-custom-speech-train-model.md)
- [Uw model verbeteren](how-to-custom-speech-improve-accuracy.md)
- [Uw model implementeren](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Test gegevens voorbereiden voor Custom Speech](how-to-custom-speech-test-data.md)
