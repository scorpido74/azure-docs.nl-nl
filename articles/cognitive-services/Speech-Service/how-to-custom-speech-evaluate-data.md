---
title: Nauwkeurigheid evalueren voor aangepaste spraak - Spraakservice
titleSuffix: Azure Cognitive Services
description: In dit document leert u hoe u kwantitatief de kwaliteit van ons spraak-naar-tekstmodel of uw aangepaste model meten. Audio + door mensen gelabelde transcriptiegegevens zijn vereist om de nauwkeurigheid te testen en er moet 30 tot 5 uur representatieve audio worden verstrekt.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: f710b8bfdd4dcfd3b7a63aa0b457036ab7037016
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806093"
---
# <a name="evaluate-custom-speech-accuracy"></a>De nauwkeurigheid van aangepaste spraak evalueren

In dit document leert u hoe u kwantitatief de kwaliteit van het spraak-naar-tekstmodel van Microsoft of uw aangepaste model meten. Audio + door mensen gelabelde transcriptiegegevens zijn vereist om de nauwkeurigheid te testen en er moet 30 tot 5 uur representatieve audio worden verstrekt.

## <a name="what-is-word-error-rate-wer"></a>Wat is Word Error Rate (WER)?

De industriestandaard om modelnauwkeurigheid te meten is *Word Error Rate* (WER). WER telt het aantal onjuiste woorden dat tijdens de herkenning is geïdentificeerd en verdeelt vervolgens door het totale aantal woorden in het transcript met het menselijk label. Ten slotte wordt dat aantal vermenigvuldigd met 100% om het WER te berekenen.

![WER-formule](./media/custom-speech/custom-speech-wer-formula.png)

Verkeerd geïdentificeerde woorden vallen in drie categorieën:

* Invoeging (I): Woorden die ten onrechte zijn toegevoegd in het hypothesetranscript
* Verwijdering (D): Woorden die niet zijn gedetecteerd in het hypothesetranscript
* Substitutie (S): Woorden die werden vervangen tussen referentie en hypothese

Hier volgt een voorbeeld:

![Voorbeeld van verkeerd geïdentificeerde woorden](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Fouten oplossen en WER verbeteren

U het WER gebruiken op basis van de resultaten van de machineherkenning om de kwaliteit van het model dat u gebruikt te evalueren met uw app, gereedschap of product. Een WER van 5%-10% wordt beschouwd als een goede kwaliteit en is klaar voor gebruik. Een WER van 20% is acceptabel, maar u aanvullende training overwegen. Een WER van 30% of meer signaleert slechte kwaliteit en vereist maatwerk en training.

Hoe de fouten worden verdeeld is belangrijk. Wanneer veel verwijderingsfouten worden aangetroffen, is dit meestal te wijten aan zwakke audiosignaalsterkte. Als u dit probleem wilt oplossen, moet u audiogegevens dichter bij de bron verzamelen. Invoegfouten betekenen dat de audio is opgenomen in een rumoerige omgeving en crosstalk aanwezig kan zijn, waardoor herkenningsproblemen ontstaan. Substitutiefouten worden vaak aangetroffen wanneer een onvoldoende voorbeeld van domeinspecifieke termen is verstrekt als transcripties met een menselijk label of verwante tekst.

Door afzonderlijke bestanden te analyseren, u bepalen welk type fouten er bestaan en welke fouten uniek zijn voor een specifiek bestand. Als u problemen op bestandsniveau begrijpt, u verbeteringen targeten.

## <a name="create-a-test"></a>Een test maken

Als u de kwaliteit van microsoft's spraak-naar-tekst basislijnmodel of een aangepast model wilt testen dat u hebt getraind, u twee modellen naast elkaar vergelijken om de nauwkeurigheid te evalueren. De vergelijking omvat WER en herkenningsresultaten. Een aangepast model wordt doorgaans vergeleken met het basislijnmodel van Microsoft.

Modellen naast elkaar evalueren:

1. Meld u aan bij de [portal Aangepaste spraak](https://speech.microsoft.com/customspeech).
2. Navigeer naar **spraak-naar-tekst > aangepaste spraak > testen**.
3. Klik **op Test toevoegen**.
4. Selecteer **Nauwkeurigheid evalueren**. Geef de test een naam, beschrijving en selecteer uw audio + met een mens gelabelde transcriptiegegevensset.
5. Selecteer maximaal twee modellen die u wilt testen.
6. Klik **op Maken**.

Nadat uw test is gemaakt, u de resultaten naast elkaar vergelijken.

## <a name="side-by-side-comparison"></a>Side-by-side vergelijking

Zodra de test is voltooid, aangegeven door de statuswijziging *in Geslaagd,* vindt u een WER-nummer voor beide modellen die in uw test zijn opgenomen. Klik op de testnaam om de pagina met testdetails te bekijken. Deze detailpagina bevat alle uitingen in uw gegevensset, met vermelding van de herkenningsresultaten van de twee modellen naast de transcriptie van de ingediende gegevensset. Als u de vergelijking naast elkaar wilt inspecteren, u verschillende fouttypen inschakelen, waaronder invoegen, verwijderen en vervangen. Door te luisteren naar de audio en herkenningsresultaten in elke kolom te vergelijken, die de door de mens gelabelde transcriptie en de resultaten voor twee spraak-naar-tekstmodellen toont, u bepalen welk model aan uw behoeften voldoet en waar aanvullende training en verbeteringen Vereist.

## <a name="next-steps"></a>Volgende stappen

* [Uw model trainen](how-to-custom-speech-train-model.md)
* [Uw model implementeren](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Aanvullende bronnen

* [Uw gegevens voorbereiden en testen](how-to-custom-speech-test-data.md)
* [Uw gegevens inspecteren](how-to-custom-speech-inspect-data.md)
