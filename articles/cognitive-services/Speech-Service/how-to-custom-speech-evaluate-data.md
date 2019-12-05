---
title: Nauw keurigheid evalueren voor de Custom Speech Speech-Service
titleSuffix: Azure Cognitive Services
description: In dit document leert u hoe u de kwaliteit van het spraak-naar-tekst model of uw aangepaste model kwantitatief kunt meten. Audio en menselijk gelabelde transcriptie-gegevens zijn vereist om nauw keurigheid te testen en er moet een representatieve audio van 30 minuten tot 5 uur worden opgegeven.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: f710b8bfdd4dcfd3b7a63aa0b457036ab7037016
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806093"
---
# <a name="evaluate-custom-speech-accuracy"></a>Nauw keurigheid van Custom Speech evalueren

In dit document leert u hoe u de kwaliteit van het spraak-naar-tekst model van micro soft of uw aangepaste model kwantitatief kunt meten. Audio en menselijk gelabelde transcriptie-gegevens zijn vereist om nauw keurigheid te testen en er moet een representatieve audio van 30 minuten tot 5 uur worden opgegeven.

## <a name="what-is-word-error-rate-wer"></a>Wat is een woord fout (WER)?

De industrie standaard om de nauw keurigheid van het model te meten is een *woord fout* (wer). WER telt het aantal onjuiste woorden dat is geïdentificeerd tijdens de herkenning. vervolgens wordt gedeeld door het totale aantal woorden dat is opgegeven in de transcripten met menselijke labels. Ten slotte wordt dat aantal vermenigvuldigd met 100% om de WER te berekenen.

![WER-formule](./media/custom-speech/custom-speech-wer-formula.png)

Onjuist geïdentificeerde woorden vallen in drie categorieën:

* Invoegen (I): woorden die onjuist zijn toegevoegd in de transcripten voor hypo Thesen
* Verwijdering (D): woorden die niet worden gedetecteerd in de transcripten van hypo Thesen
* Vervanging (en): woorden die zijn vervangen tussen verwijzing en hypo these

Hier volgt een voorbeeld:

![Voor beeld van onjuist geïdentificeerde woorden](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Fouten oplossen en WER verbeteren

U kunt de WER van de resultaten van de computer herkenning gebruiken om de kwaliteit te evalueren van het model dat u gebruikt met uw app, hulp programma of product. Een WER van 5%-10% wordt beschouwd als een goede kwaliteit en is klaar voor gebruik. Een WER van 20% is acceptabel, maar u kunt ook extra training overwegen. Een WER van 30% of meer heeft een slechte kwaliteit en vereist aanpassing en training.

Hoe de fouten worden gedistribueerd, is belang rijk. Wanneer er veel verwijderings fouten optreden, is dit meestal het gevolg van een zwakke geluids signaal sterkte. Om dit probleem op te lossen, moet u audio gegevens dichter bij de bron verzamelen. Invoeg fouten betekenen dat de audio is vastgelegd in een rustige omgeving en dat er crosstalk mogelijk aanwezig zijn, waardoor herkennings problemen ontstaan. Vervangings fouten worden vaak aangetroffen wanneer er een ontoereikend voor beeld van specifieke domein termen is opgegeven als een transcripties of gerelateerde tekst.

Door afzonderlijke bestanden te analyseren, kunt u bepalen welk type fouten bestaan en welke fouten uniek zijn voor een bepaald bestand. Als u problemen ondervindt op bestands niveau, kunt u verbeteringen aanrichten.

## <a name="create-a-test"></a>Een test maken

Als u de kwaliteit van het spraak-naar-tekst basislijn model van micro soft of een aangepast model dat u hebt getraind wilt testen, kunt u twee modellen naast elkaar vergelijken om de nauw keurigheid te evalueren. De vergelijking omvat WER-en herkennings resultaten. Normaal gesp roken wordt een aangepast model vergeleken met het basis model van micro soft.

Modellen naast elkaar evalueren:

1. Meld u aan bij de [Custom speech Portal](https://speech.microsoft.com/customspeech).
2. Navigeer naar **> voor spraak naar tekst Custom Speech > tests**uit te voeren.
3. Klik op **test toevoegen**.
4. Selecteer **nauw keurigheid evalueren**. Geef een naam en beschrijving op voor de test en selecteer uw transcriptie-gegevensset voor audio + met Human label.
5. Selecteer Maxi maal twee modellen die u wilt testen.
6. Klik op **Maken**.

Nadat de test is gemaakt, kunt u de resultaten naast elkaar vergelijken.

## <a name="side-by-side-comparison"></a>Side-by-side-vergelijking

Zodra de test is voltooid, wordt aangegeven dat de status is gewijzigd in *geslaagd*. u vindt hier een wer-nummer voor beide modellen die in de test zijn opgenomen. Klik op de naam van de test om de detail pagina van de test te bekijken. Deze detail pagina bevat een lijst met alle uitingen in uw gegevensset, waarmee de herkennings resultaten van de twee modellen naast de transcriptie van de verzonden gegevensset worden aangegeven. Als u de gelijktijdige vergelijking wilt controleren, kunt u verschillende fout typen met inbegrip van invoegen, verwijderen en vervangen. Als u naar de audio luistert en de herkennings resultaten in elke kolom vergelijkt, waarin de transcriptie en de resultaten van twee spraak-naar-tekst modellen worden weer gegeven, kunt u bepalen welk model aan uw behoeften voldoet en waar extra training en verbeteringen zijn Vereist.

## <a name="next-steps"></a>Volgende stappen

* [Uw model trainen](how-to-custom-speech-train-model.md)
* [Uw model implementeren](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Aanvullende bronnen

* [Uw gegevens voorbereiden en testen](how-to-custom-speech-test-data.md)
* [Uw gegevens controleren](how-to-custom-speech-inspect-data.md)
