---
title: Een model trainen voor Custom Speech-Speech-Service
titleSuffix: Azure Cognitive Services
description: Als u een spraak-naar-tekst model wilt trainen, kunt u de nauw keurigheid van de herkenning voor het basislijn model van micro soft of een aangepast model verbeteren. Een model wordt getraind met behulp van transcripties en gerelateerde tekst.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: a2bc39a35299f56ba52a0143ce123560bd4d88fa
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77137770"
---
# <a name="train-a-model-for-custom-speech"></a>Een model trainen voor Custom Speech

Training van een model voor spraak naar tekst kan de nauw keurigheid van de herkenning voor het basislijn model van micro soft verbeteren. Een model wordt getraind met behulp van transcripties en gerelateerde tekst. Deze gegevens sets en eerder geüploade audio gegevens worden gebruikt voor het verfijnen en trainen van het spraak-naar-tekst model om woorden, zinsdelen, acroniemen, namen en andere productspecifieke voor waarden te herkennen. Hoe meer gegevens sets in het domein zijn die u opgeeft (wat betreft wat gebruikers zeggen en wat u verwacht te herkennen), des te nauw keuriger is uw model, wat resulteert in verbeterde herkenning. Wanneer u niet-gerelateerde gegevens indeelt in uw training, kunt u de nauw keurigheid van uw model verminderen of onnauwkeuriger maken.

## <a name="use-training-to-resolve-accuracy-issues"></a>Training gebruiken om nauw keurige problemen op te lossen

Als u herkennings problemen ondervindt met uw model, kunt u de nauw keurigheid verbeteren door gebruik te maken van transcripten met menselijke labels en gerelateerde gegevens voor extra training. Gebruik deze tabel om te bepalen welke gegevensset moet worden gebruikt voor het oplossen van uw probleem (en):

| Gebruiksvoorbeeld | Gegevenstype |
| -------- | --------- |
| Verbeter nauw keurigheid van herkenning op branchespecifieke woorden lijst en grammatica, zoals medische terminologie of het jargon. | Gerelateerde tekst (zinnen/uitingen) |
| Definieer de fonetische en weer gegeven vorm van een woord of term met een niet-standaard uitspraak, zoals product namen of acroniemen. | Gerelateerde tekst (uitspraak) |
| Verbeter de herkennings nauwkeurigheid op spraak stijlen, accenten of specifieke achtergrond ruis. | Audio en Transcripten met menselijke labels |

> [!IMPORTANT]
> Als u geen gegevensset hebt geüpload, raadpleegt u [uw gegevens voorbereiden en testen](how-to-custom-speech-test-data.md). In dit document vindt u instructies voor het uploaden van gegevens en richt lijnen voor het maken van sets met hoge kwaliteit.

## <a name="train-and-evaluate-a-model"></a>Een model trainen en evalueren

De eerste stap voor het trainen van een model is het uploaden van trainings gegevens. Gebruik voor [bereiding en test uw gegevens](how-to-custom-speech-test-data.md) voor stapsgewijze instructies voor het voorbereiden van transcripties en gerelateerde tekst met menselijke labels (uitingen en uitspraak). Nadat u de trainings gegevens hebt geüpload, volgt u deze instructies om te beginnen met het trainen van uw model:

1. Meld u aan bij de [Custom speech Portal](https://speech.microsoft.com/customspeech).
2. Navigeer naar **spraak-naar-tekst > Custom Speech > training**.
3. Klik op **model trainen**.
4. Geef vervolgens uw training een **naam** en **Beschrijving**.
5. Selecteer in de vervolg keuzelijst **scenario en basislijn model** het scenario dat het meest geschikt is voor uw domein. Als u niet zeker weet welk scenario u moet kiezen, selecteert u **Algemeen**. Het basis model is het begin punt voor training. Als u geen voor keur hebt, kunt u de nieuwste gebruiken.
6. Kies op de pagina **trainings gegevens selecteren** een of meer transcriptie-gegevens sets met audio en menselijke labels die u wilt gebruiken voor de training.
7. Zodra de training is voltooid, kunt u ervoor kiezen om nauw keuriger tests uit te voeren op het nieuwe getrainde model. Deze stap is optioneel.
8. Selecteer **maken** om uw aangepaste model te maken.

In de tabel training wordt een nieuw item weer gegeven dat overeenkomt met dit nieuwe model. In de tabel wordt ook de volgende status weer gegeven: verwerken, geslaagd, mislukt.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>De nauw keurigheid van een getraind model evalueren

U kunt de gegevens controleren en de model nauwkeurigheid evalueren met behulp van deze documenten:

- [Uw gegevens controleren](how-to-custom-speech-inspect-data.md)
- [Uw gegevens evalueren](how-to-custom-speech-evaluate-data.md)

Als u ervoor hebt gekozen om nauw keurigheid te testen, is het belang rijk dat u een akoestische gegevensset selecteert die afwijkt van het model dat u hebt gebruikt voor de prestaties van het model.

## <a name="next-steps"></a>Volgende stappen

- [Uw model implementeren](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Uw gegevens voorbereiden en testen](how-to-custom-speech-test-data.md)
- [Uw gegevens controleren](how-to-custom-speech-inspect-data.md)
- [Uw gegevens evalueren](how-to-custom-speech-evaluate-data.md)
- [Uw model trainen](how-to-custom-speech-train-model.md)
