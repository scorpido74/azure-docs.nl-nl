---
title: Richtlijnen voor transcripties met menselijk label - Spraakservice
titleSuffix: Azure Cognitive Services
description: Om de nauwkeurigheid van spraakherkenning te verbeteren, zoals wanneer woorden worden verwijderd of onjuist worden vervangen, u transcripties met het menselijk label gebruiken, samen met uw audiogegevens. Transcripties met menselijk label zijn woord voor woord, letterlijke transcripties van een audiobestand.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 81b4ffc8f77673e52bb78f891e3de618b67e0d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806059"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Transcripties met menselijk label maken

Als u de herkenningsnauwkeurigheid wilt verbeteren, met name problemen die worden veroorzaakt wanneer woorden worden verwijderd of onjuist worden vervangen, wilt u transcripties met het menselijk label gebruiken, samen met uw audiogegevens. Wat zijn transcripties met een menslabel? Dat is eenvoudig, het zijn woord-voor-woord, letterlijke transcripties van een audiobestand.

Een grote steekproef van transcriptiegegevens is nodig om de herkenning te verbeteren, we raden u aan om tussen de 10 en 1000 uur transcriptiegegevens te verstrekken. Op deze pagina bekijken we richtlijnen die zijn ontworpen om u te helpen transcripties van hoge kwaliteit te maken. Deze gids wordt opgesplitst door locale, met secties voor Amerikaans Engels, Mandarijn Chinees en Duits.

## <a name="us-english-en-us"></a>Amerikaans Engels (en-VS)

Transcripties met menselijk label voor Engelse audio moeten worden verstrekt als platte tekst, alleen met ASCII-tekens. Vermijd het gebruik van Latijnse-1- of Unicode-leestekens. Deze tekens worden vaak per ongeluk toegevoegd bij het kopiëren van tekst uit een tekstverwerkingstoepassing of het schrapen van gegevens van webpagina's. Als deze tekens aanwezig zijn, moet u deze bijwerken met de juiste ASCII-substitutie.

Enkele voorbeelden:

| Tekens om te vermijden | Substitution | Opmerkingen |
| ------------------- | ------------ | ----- |
| "Hallo wereld" | "Hallo wereld" | De openings- en sluitingstekens zijn vervangen door geschikte ASCII-tekens. |
| John's dag | John's dag | De apostrof is vervangen door het juiste ASCII-karakter. |
| het was goed - nee, het was geweldig! | het was goed--nee, het was geweldig! | De em dash werd vervangen door twee koppeltekens. |

### <a name="text-normalization-for-us-english"></a>Tekstnormalisatie voor Amerikaans Engels

Tekstnormalisatie is de transformatie van woorden in een consistente indeling die wordt gebruikt bij het trainen van een model. Sommige normalisatieregels worden automatisch toegepast op tekst, maar we raden u aan deze richtlijnen te gebruiken terwijl u uw transcriptiegegevens met het menselijk label voorbereidt:

- Schrijf afkortingen in woorden uit.
- Schrijf niet-standaard numerieke tekenreeksen in woorden (zoals boekhoudkundige termen).
- Niet-alfabetische tekens of gemengde alfanumerieke tekens moeten worden getranscribeerd zoals uitgesproken.
- Afkortingen die worden uitgesproken als woorden mogen niet worden bewerkt (zoals 'radar', 'laser', 'RAM' of 'NAVO').
- Schrijf afkortingen uit die worden uitgesproken als afzonderlijke letters met elke letter gescheiden door een spatie.

Hier zijn een paar voorbeelden van normalisatie die u moet uitvoeren op de transcriptie:

| Oorspronkelijke tekst               | Tekst na normalisatie              |
| --------------------------- | ------------------------------------- |
| Dr. Bruce Banner            | Doctor Bruce Banner                   |
| James Bond, 007             | James Bond, dubbel oh zeven           |
| Ke$ha                       | Kesha Kesha                                 |
| Hoe lang is de 2x4         | Hoe lang is de twee bij vier           |
| De vergadering gaat van 13.00-15.00 uur | De vergadering gaat van 1 tot 15 uur |
| Mijn bloedgroep is O+         | Mijn bloedgroep is O positief           |
| Water is H20                | Water is H 2 O                        |
| Speel OU812 door Van Halen     | Speel O U 8 12 door Van Halen           |
| UTF-8 met stuklijst              | U T F 8 met BOM                      |

De volgende normalisatieregels worden automatisch toegepast op transcripties:

- Gebruik kleine letters.
- Verwijder alle interpunctie, behalve apostrofs binnen woorden.
- Vouw getallen uit in woorden/gesproken vorm, zoals dollarbedragen.

Hier zijn een paar voorbeelden van normalisatie automatisch uitgevoerd op de transcriptie:

| Oorspronkelijke tekst                          | Tekst na normalisatie          |
| -------------------------------------- | --------------------------------- |
| "Heilige koe!" zei Batman.               | heilige koe zei batman              |
| "Wat?" Zei Batman's hulpje, Robin. | wat zei Batman's sidekick robin |
| Ga -em halen!                            | go get em                         |
| Ik ben dubbel-jointed                     | Ik ben dubbel gejointed                |
| 104 Elm Street                         | een oh vier Elm street            |
| Afstemmen op 102.7                          | afstemmen op een oh twee punt zeven    |
| Pi is ongeveer 3.14                       | pi is ongeveer drie punt een vier  |
| Het \$kost 3,14                        | het kost drie veertien           |

## <a name="mandarin-chinese-zh-cn"></a>Mandarijn Chinees (zh-CN)

Door mensen gelabelde transcripties voor Mandarijnse Chinese audio moeten UTF-8 zijn gecodeerd met een byte-order marker. Vermijd het gebruik van interpunctietekens met een halve breedte. Deze tekens kunnen per ongeluk worden opgenomen wanneer u de gegevens voorbereidt in een tekstverwerkingsprogramma of gegevens van webpagina's schraapt. Als deze tekens aanwezig zijn, moet u deze bijwerken met de juiste vervanging over de volle breedte.

Enkele voorbeelden:

| Tekens om te vermijden | Substitution   | Opmerkingen |
| ------------------- | -------------- | ----- |
| "???" | "???" | De openings- en sluitingstekens zijn vervangen door geschikte tekens. |
| ???? | ????| Het vraagteken is vervangen door een passend karakter. |

### <a name="text-normalization-for-mandarin-chinese"></a>Tekst normalisatie voor Mandarijn Chinees

Tekstnormalisatie is de transformatie van woorden in een consistente indeling die wordt gebruikt bij het trainen van een model. Sommige normalisatieregels worden automatisch toegepast op tekst, maar we raden u aan deze richtlijnen te gebruiken terwijl u uw transcriptiegegevens met het menselijk label voorbereidt:

- Schrijf afkortingen in woorden uit.
- Schrijf numerieke tekenreeksen in gesproken vorm uit.

Hier zijn een paar voorbeelden van normalisatie die u moet uitvoeren op de transcriptie:

| Oorspronkelijke tekst | Tekst na normalisatie |
| ------------- | ------------------------ |
| 21. | 我今年二十一 |
| 3 ...504 | 三号 楼 五 零 四 |

De volgende normalisatieregels worden automatisch toegepast op transcripties:

- Alle interpunctie verwijderen
- Getallen uitvouwen tot gesproken vorm
- Letters over de volle breedte converteren naar letters met een halve breedte
- Hoofdletters gebruiken voor alle Engelse woorden

Hier zijn een paar voorbeelden van normalisatie automatisch uitgevoerd op de transcriptie:

| Oorspronkelijke tekst | Tekst na normalisatie |
| ------------- | ------------------------ |
| 3.1415 | 三 点 一 四 一 五 |
| ¥ 3,5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992 | Het is een goede zaak. |
| ??? | ?? |
| 17:00 uur | 下午 五点 的 航班 |
| 21.00.00.00.00.00.00 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Duits (de-DE) en andere talen

Door de mens gelabelde transcripties voor Duitse audio (en andere niet-Engelse of Mandarijnse Chinese talen) moeten UTF-8 zijn gecodeerd met een byte-ordermarkering. Voor elk audiobestand moet één door de mens gelabelde transcriptie worden verstrekt.

### <a name="text-normalization-for-german"></a>Tekstnormalisatie voor Duits

Tekstnormalisatie is de transformatie van woorden in een consistente indeling die wordt gebruikt bij het trainen van een model. Sommige normalisatieregels worden automatisch toegepast op tekst, maar we raden u aan deze richtlijnen te gebruiken terwijl u uw transcriptiegegevens met het menselijk label voorbereidt:

- Schrijf decimalen als '' en niet ''.
- Schrijf tijdseparatoren als ':' en niet ''. (bijvoorbeeld: 12:00 Uhr).
- Afkortingen zoals 'ca'. worden niet vervangen. Wij raden u aan het volledige gesproken formulier te gebruiken.
- De vier belangrijkste wiskundige operatoren \*(+, -, en /) worden verwijderd. We raden u aan ze te vervangen door het geschreven formulier: 'plus', 'min', 'mal' en 'geteilt'.
- Vergelijkingsoperatoren worden verwijderd (=, < en >). We raden aan ze te vervangen door 'gleich', 'kleiner als' en 'grösser als'.
- Schrijf breuken, zoals 3/4, in geschreven vorm (bijvoorbeeld: "drei viertel" in plaats van 3/4).
- Vervang het "€" symbool door de geschreven vorm "Euro".

Hier zijn een paar voorbeelden van normalisatie die u moet uitvoeren op de transcriptie:

| Oorspronkelijke tekst    | Tekst na gebruikersnormalisatie | Tekst na systeemnormalisatie       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr              | es ist zwölf uhr drei und zwanzig uhr |
| {12.45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3 - 4        | 2 plus 3 min 4              | zwei plus drei min vier             |

De volgende normalisatieregels worden automatisch toegepast op transcripties:

- Kleine letters gebruiken voor alle tekst.
- Verwijder alle interpunctie, met inbegrip van verschillende soorten aanhalingstekens ("test", "test", "test", en «test» zijn OK).
- Gooi rijen met speciale tekens uit deze set weg: ¤ ¤ ¥ ¥ § © , ¬ ® ° ± ² μ × ÿ Ø¬¬.
- Vouw getallen uit naar gesproken vorm, inclusief dollar- of eurobedragen.
- Accepteer umlauts alleen voor a, o, en jij. Anderen zullen worden vervangen door "th" of worden weggegooid.

Hier zijn een paar voorbeelden van normalisatie automatisch uitgevoerd op de transcriptie:

| Oorspronkelijke tekst    | Tekst na normalisatie |
| ---------------- | ------------------------ |
| Frankfurter Ring | frankfurter ring         |
| ¡Eine Frage!     | eine frage               |
| wir, haben       | wir haben                |

## <a name="next-steps"></a>Volgende stappen

- [Uw gegevens voorbereiden en testen](how-to-custom-speech-test-data.md)
- [Uw gegevens inspecteren](how-to-custom-speech-inspect-data.md)
- [Uw gegevens evalueren](how-to-custom-speech-evaluate-data.md)
- [Uw model trainen](how-to-custom-speech-train-model.md)
- [Uw model implementeren](how-to-custom-speech-deploy-model.md)
