---
title: Train een model voor Aangepaste Spraak - Spraakservice
titleSuffix: Azure Cognitive Services
description: Het trainen van een spraak-naar-tekstmodel kan de herkenningsnauwkeurigheid voor het basislijnmodel van Microsoft of een aangepast model verbeteren. Een model wordt getraind met behulp van transcripties met een menselijk label en verwante tekst.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: a2bc39a35299f56ba52a0143ce123560bd4d88fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77137770"
---
# <a name="train-a-model-for-custom-speech"></a>Een model trainen voor aangepaste spraak

Het trainen van een spraak-naar-tekstmodel kan de herkenningsnauwkeurigheid voor het basislijnmodel van Microsoft verbeteren. Een model wordt getraind met behulp van transcripties met een menselijk label en verwante tekst. Deze gegevenssets samen met eerder geüploade audiogegevens worden gebruikt om het spraak-naar-tekstmodel te verfijnen en te trainen om woorden, zinnen, afkortingen, namen en andere productspecifieke termen te herkennen. Hoe meer in-domain datasets die u verstrekt (gegevens die gerelateerd zijn aan wat gebruikers zullen zeggen en wat u verwacht te herkennen), hoe nauwkeuriger uw model zal zijn, wat resulteert in verbeterde herkenning. Houd er rekening mee dat door niet-gerelateerde gegevens in uw training te voeren, u de nauwkeurigheid van uw model verminderen of schaden.

## <a name="use-training-to-resolve-accuracy-issues"></a>Training gebruiken om nauwkeurigheidsproblemen op te lossen

Als u problemen ondervindt met herkenning smaken met uw model, kan het gebruik van door mensen gelabelde transcripties en gerelateerde gegevens voor aanvullende training helpen om de nauwkeurigheid te verbeteren. Gebruik deze tabel om te bepalen welke gegevensset u wilt gebruiken om uw probleem(en) op te lossen:

| Gebruiksvoorbeeld | Gegevenstype |
| -------- | --------- |
| Verbeter de nauwkeurigheid van de herkenning op branchespecifieke woordenschat en grammatica, zoals medische terminologie of IT-jargon. | Verwante tekst (zinnen/uitingen) |
| Definieer de fonetische en weergegeven vorm van een woord of term met een niet-standaard uitspraak, zoals productnamen of afkortingen. | Verwante tekst (uitspraak) |
| Verbeter de herkenningsnauwkeurigheid op spreekstijlen, accenten of specifieke achtergrondgeluiden. | Audio + transcripties met menselijk label |

> [!IMPORTANT]
> Als u geen gegevensset hebt geüpload, raadpleegt u [Uw gegevens voorbereiden en testen.](how-to-custom-speech-test-data.md) Dit document bevat instructies voor het uploaden van gegevens en richtlijnen voor het maken van gegevenssets van hoge kwaliteit.

## <a name="train-and-evaluate-a-model"></a>Een model trainen en evalueren

De eerste stap om een model te trainen is het uploaden van trainingsgegevens. Gebruik [Uw gegevens voorbereiden en testen](how-to-custom-speech-test-data.md) op stapsgewijze instructies om transcripties en gerelateerde tekst (uitingen en uitspraken) voor te bereiden. Nadat u trainingsgegevens hebt geüpload, volgt u de volgende instructies om uw model te trainen:

1. Meld u aan bij de [portal Aangepaste spraak](https://speech.microsoft.com/customspeech).
2. Navigeer naar **aangepaste > spraaktraining**naar spraak naar tekst >.
3. Klik **op Treinmodel**.
4. Geef vervolgens uw training een **naam** en **beschrijving.**
5. Selecteer in de vervolgkeuzelijst **Scenario en Basislijnmodel** het scenario dat het beste bij uw domein past. Als u niet zeker weet welk scenario u wilt kiezen, selecteert u **Algemeen**. Het basislijnmodel is het startpunt voor de training. Als u geen voorkeur hebt, u de nieuwste gebruiken.
6. Kies op de pagina **Trainingsgegevens selecteren** een of meerdere audio+ transcriptiesets met een mens die u wilt gebruiken voor training.
7. Zodra de training is voltooid, u ervoor kiezen om nauwkeurigheidstests uit te voeren op het nieuw getrainde model. Deze stap is optioneel.
8. Selecteer **Maken** om uw aangepaste model te maken.

In de trainingstabel wordt een nieuw item weergegeven dat overeenkomt met dit nieuw gemaakte model. In de tabel wordt ook de status weergegeven: Verwerking, Geslaagd, Mislukt.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>De nauwkeurigheid van een getraind model evalueren

U de gegevens inspecteren en de modelnauwkeurigheid evalueren met behulp van deze documenten:

- [Uw gegevens inspecteren](how-to-custom-speech-inspect-data.md)
- [Uw gegevens evalueren](how-to-custom-speech-evaluate-data.md)

Als u ervoor kiest om de nauwkeurigheid te testen, is het belangrijk om een akoestische gegevensset te selecteren die verschilt van de gegevens die u met uw model hebt gebruikt om een realistisch beeld te krijgen van de prestaties van het model.

## <a name="next-steps"></a>Volgende stappen

- [Uw model implementeren](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Uw gegevens voorbereiden en testen](how-to-custom-speech-test-data.md)
- [Uw gegevens inspecteren](how-to-custom-speech-inspect-data.md)
- [Uw gegevens evalueren](how-to-custom-speech-evaluate-data.md)
- [Uw model trainen](how-to-custom-speech-train-model.md)
