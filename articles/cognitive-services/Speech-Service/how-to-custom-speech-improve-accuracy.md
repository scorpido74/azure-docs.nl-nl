---
title: Een model voor Custom Speech speech-service verbeteren
titleSuffix: Azure Cognitive Services
description: Bepaalde soorten transcripties en gerelateerde tekst met menselijke labels kunnen nauw keurigheid van herkenning verbeteren voor een spraak-naar-tekst model op basis van het uitsprekende scenario.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: v-demjoh
ms.openlocfilehash: bb904482f3cb5900b724803816269f1b10ab3720
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727876"
---
# <a name="improve-custom-speech-accuracy"></a>Custom Speech nauw keurigheid verbeteren

In dit artikel leert u hoe u de kwaliteit van uw aangepaste model kunt verbeteren door audio, transcripten met menselijke labels en gerelateerde tekst toe te voegen.

## <a name="accuracy-in-different-scenarios"></a>Nauw keurigheid in verschillende scenario's

Scenario's voor spraak herkenning variëren per audio kwaliteit en taal (woordenlijst en spraak stijl). In de volgende tabel worden vier algemene scenario's besproken:

| Scenario | Audio kwaliteit | Vocabulaire | Stijl van spraak |
|----------|---------------|------------|----------------|
| Callcenter | 8 kHz kan twee mensen op 1 audio kanaal zijn, kan worden gecomprimeerd | Beperkt, uniek voor domein en producten | Gesprek, los gestructureerd |
| Voice Assistant (zoals Cortana of een station-through-venster) | Hoog, 16 kHz | Entiteits zwaar (song titels, producten, locaties) | Duidelijk geformuleerde woorden en zinsdelen |
| Dicteren (chat bericht, notities, zoeken) | Hoog, 16 kHz | Variabele | Notities maken |
| Video closed captioning | Variërend, inclusief gebruik van een microfoon, toegevoegde muziek | Variërend, van vergaderingen, geciteerde spraak, muzikale Song teksten | Lezen, voor bereid of los gestructureerd |

In verschillende scenario's worden verschillende kwaliteits resultaten gegenereerd. In de volgende tabel ziet u hoe de inhoud van deze vier scenario's de snelheid van het [woord fout (wer)](how-to-custom-speech-evaluate-data.md)in beslag nemen. In de tabel ziet u welke fout typen het meest worden gebruikt in elk scenario.

| Scenario | Kwaliteit van spraak herkenning | Invoeg fouten | Verwijderings fouten | Vervangings fouten |
|----------|----------------------------|------------------|-----------------|---------------------|
| Callcenter | Gemiddeld (< 30% WER) | Laag, behalve wanneer anderen op de achtergrond praten | Kan hoog zijn. Call centers kunnen ruis opdoen en overlappende luid sprekers kunnen het model verwarren | Gemiddeld. De namen van producten en personen kunnen deze fouten veroorzaken |
| Spraakassistent | Hoog (kan worden < 10% WER) | Laag | Laag | Gemiddeld, vanwege de titels van het nummer, product namen of locaties |
| Dicteren | Hoog (kan worden < 10% WER) | Laag | Laag | Hoog |
| Video closed captioning | Is afhankelijk van het video type (kan < 50% WER) | Laag | Kan hoog zijn als gevolg van muziek, ruis, microfoon kwaliteit | Jargon kan deze fouten veroorzaken |

Door de onderdelen van de WER te bepalen (aantal invoeg-, verwijderings-en vervangings fouten) kunt u bepalen welk soort gegevens u wilt toevoegen om het model te verbeteren. Gebruik de [Custom speech Portal](https://speech.microsoft.com/customspeech) om de kwaliteit van een basis model weer te geven. De portal rapporteert Invoeg-, vervangings-en verwijderings fout tarieven die worden gecombineerd in het WER-kwaliteits tarief.

## <a name="improve-model-recognition"></a>Model herkenning verbeteren

U kunt herkennings fouten verminderen door trainings gegevens toe te voegen aan de [Custom speech Portal](https://speech.microsoft.com/customspeech). 

Plan het aangepaste model hand matig door bron materiaal toe te voegen. Uw aangepaste model vereist extra training om u te informeren over wijzigingen aan uw entiteiten. U hebt bijvoorbeeld updates nodig voor product namen, namen van nummers of nieuwe service locaties.

In de volgende secties wordt beschreven hoe elk soort aanvullende trainings gegevens fouten kan verminderen.

### <a name="add-related-text-sentences"></a>Verwante tekst zinnen toevoegen

Aanvullende verwante zinnen kunnen hoofd zakelijk vervangings fouten met betrekking tot de fout herkenning van veelvoorkomende woorden en toepassingsspecifieke woorden verminderen door ze in context weer te geven. Domein-specifieke woorden kunnen ongebruikelijk of opgemaakte woorden zijn, maar de uitspraak moet duidelijk zijn om te worden herkend.

> [!NOTE]
> Vermijd Verwante tekst zinnen die ruis bevatten, zoals niet-herken bare tekens of woorden.

### <a name="add-audio-with-human-labeled-transcripts"></a>Audio toevoegen met transcripten met menselijke labels

Audio met transcripten met menselijke labels biedt de grootste verbeteringen als de audio afkomstig is van de doel use case. Voor beelden moeten het volledige bereik van spraak hebben. Bijvoorbeeld, een Call Center voor een Retail Store krijgt de meeste oproepen over Swimwear en zonnebril tijdens zomer maanden. Controleer of uw voor beeld het volledige spraak bereik bevat dat u wilt detecteren.

Houd rekening met de volgende details:

* Custom Speech kan alleen een Word-context vastleggen om vervangings fouten, geen invoeg-of verwijderings fouten, te verminderen.
* Vermijd voor beelden die transcriptie-fouten bevatten, maar neem een verscheidenheid aan audio kwaliteit op.
* Vermijd zinnen die geen verband houden met uw probleem domein. Niet-verwante zinnen kunnen schadelijk zijn voor uw model.
* Wanneer de kwaliteit van transcripten verschilt, kunt u uitzonderlijk goede zinnen dupliceren (zoals uitstekende transcripties die sleutel zinnen bevatten) om het gewicht te verg Roten.

### <a name="add-new-words-with-pronunciation"></a>Nieuwe woorden met uitspraak toevoegen

Woorden die zijn gemaakt of die zeer gespecialiseerd zijn, kunnen unieke uitspraak hebben. Deze woorden kunnen worden herkend als het woord kan worden opgesplitst in kleinere woorden om het uit te spreken. Als u bijvoorbeeld **Xbox**wilt herkennen, spreekt u uit als **X-vak**. Met deze methode wordt de algehele nauw keurigheid niet verbeterd, maar kan de herkenning van deze tref woorden toenemen.

> [!NOTE]
> Deze techniek is op dit moment alleen beschikbaar voor sommige talen. Zie aanpassing voor uitspraak in [de tabel met spraak-naar-tekst](language-support.md) voor meer informatie.

## <a name="sources-by-scenario"></a>Bronnen op scenario

De volgende tabel geeft een overzicht van de scenario's voor spraak herkenning en een lijst met bron materialen die u kunt overwegen binnen de drie categorieën met trainings inhoud die hierboven worden vermeld.

| Scenario | Verwante tekst zinnen | Audio en Transcripten met menselijke labels | Nieuwe woorden met uitspraak |
|----------|------------------------|------------------------------|------------------------------|
| Callcenter             | Marketing documenten, website, product beoordelingen met betrekking tot Call Center-activiteit | Call Center-aanroepen, getranscribeerd door de mens | termen met een dubbel zinnige uitspraak (Zie Xbox hierboven) |
| Spraakassistent         | zinnen weer geven met alle combi Naties van opdrachten en entiteiten | Noteer de stem opdrachten op het apparaat en transcribeer naar tekst | namen (films, liedjes, producten) met unieke uitspraak |
| Dicteren               | geschreven invoer, zoals chat berichten of e-mails | vergelijkbaar met boven | vergelijkbaar met boven |
| Video closed captioning | TV-scripts, films, marketing inhoud, video overzichten | exacte transcripten van Video's | vergelijkbaar met boven |

## <a name="next-steps"></a>Volgende stappen

- [Uw model trainen](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Uw gegevens voorbereiden en testen](how-to-custom-speech-test-data.md)
- [Uw gegevens controleren](how-to-custom-speech-inspect-data.md)