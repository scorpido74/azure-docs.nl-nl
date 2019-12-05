---
title: Woordgroepen lijsten-spraak service
titleSuffix: Azure Cognitive Services
description: Informatie over het leveren van de speech-service met een woordgroepen lijst met behulp van het `PhraseListGrammar`-object om de resultaten van spraak-naar-tekst herkenning te verbeteren.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: 052e02ef562da0637b6b5b9683120f0c397dbfd5
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805872"
---
# <a name="phrase-lists-for-speech-to-text"></a>Woordgroepen lijsten voor spraak naar tekst

Door de spraak service een lijst met zinsdelen te bieden, kunt u de nauw keurigheid van spraak herkenning verbeteren. Woordgroepen lijsten worden gebruikt voor het identificeren van bekende woord groepen in audio gegevens, zoals de naam van een persoon of een specifieke locatie.

Als u bijvoorbeeld een opdracht ' verplaatsen naar ' en een mogelijke bestemming van ' naar onder ' hebt, kunt u een vermelding van ' verplaatsen naar binnen ' toevoegen. Als u een woord groep toevoegt, wordt de kans groter dat wanneer de audio wordt herkend dat ' naar binnen verplaatsen ' wordt herkend.

Enkele woorden of volledige woord groepen kunnen worden toegevoegd aan een woordgroepen lijst. Tijdens de herkenning wordt een vermelding in een woordgroepen lijst gebruikt als de audio een exacte overeenkomst bevat. Op basis van het vorige voor beeld, als de woordgroepen lijst ' verplaatsen naar de andere ' bevat en de audio-vastgelegde geluiden vergelijkbaar genoeg zijn met ' verplaatsen naar ' en ' naar de andere locatie ', wordt het herkennings resultaat waarschijnlijk langer herkend als ' naar een andere locatie verplaatsen '.

>[!Note]
> Op dit moment ondersteunt een woordgroepen lijst alleen het Engels voor spraak naar tekst.

## <a name="how-to-use-phrase-lists"></a>Woordgroepen lijsten gebruiken

In de onderstaande voor beelden ziet u hoe u een woordgroepen lijst maakt met behulp van het `PhraseListGrammar`-object.

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

>[!Note]
> Het maximum aantal woordgroepen lijsten dat door de spraak service wordt gebruikt om te voldoen aan spraak is 1024 zinsdelen.

U kunt ook de zinsdelen wissen die zijn gekoppeld aan de `PhraseListGrammar` door Clear () aan te roepen.

```C++
phraselist->Clear();
```

```cs
phraseList.Clear();
```

```Python
phrase_list_grammar.clear()
```

```JavaScript
phraseListGrammar.clear();
```

```Java
phraseListGrammar.clear();
```

> [!NOTE]
> Wijzigingen in een `PhraseListGrammar`-object worden van kracht bij de volgende herkenning of na het opnieuw verbinden met de spraak service.

## <a name="next-steps"></a>Volgende stappen

* [Naslag documentatie voor Speech SDK](speech-sdk.md)
