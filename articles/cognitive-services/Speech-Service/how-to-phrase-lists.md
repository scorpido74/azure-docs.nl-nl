---
title: Woordgroepen lijsten-spraak service
titleSuffix: Azure Cognitive Services
description: Informatie over het leveren van de spraak service met een woordgroepen lijst `PhraseListGrammar` met behulp van het object om de resultaten van spraak-naar-tekst herkenning te verbeteren.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: dapine
zone_pivot_groups: programming-languages-speech-services-one-nomore-no-go
ms.openlocfilehash: d6418a863e8af75e4f2f063d9e6151e19a0e7e6e
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977580"
---
# <a name="phrase-lists-for-speech-to-text"></a>Woordgroepen lijsten voor spraak naar tekst

Door de spraak service een lijst met zinsdelen te bieden, kunt u de nauw keurigheid van spraak herkenning verbeteren. Woordgroepen lijsten worden gebruikt voor het identificeren van bekende woord groepen in audio gegevens, zoals de naam van een persoon of een specifieke locatie.

Als u bijvoorbeeld een opdracht ' verplaatsen naar ' en een mogelijke bestemming van ' naar onder ' hebt, kunt u een vermelding van ' verplaatsen naar binnen ' toevoegen. Als u een woord groep toevoegt, wordt de kans groter dat wanneer de audio wordt herkend dat ' naar binnen verplaatsen ' wordt herkend.

Enkele woorden of volledige woord groepen kunnen worden toegevoegd aan een woordgroepen lijst. Tijdens de herkenning wordt een vermelding in een woordgroepen lijst gebruikt als een exacte overeenkomst voor de hele woord groep wordt opgenomen in de audio als een afzonderlijke woord groep. Als een exacte overeenkomst met de zin niet wordt gevonden, wordt de herkenning niet ondersteund.

>[!Note]
> Op dit moment ondersteunt een woordgroepen lijst alleen het Engels voor spraak naar tekst.

## <a name="how-to-use-phrase-lists"></a>Woordgroepen lijsten gebruiken

In de onderstaande voor beelden ziet u hoe u een woordgroepen lijst `PhraseListGrammar` maakt met behulp van het object.

::: zone pivot="programming-language-csharp"

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> Het maximum aantal woordgroepen lijsten dat door de spraak service wordt gebruikt om te voldoen aan spraak is 1024 zinsdelen.

U kunt ook de zinsdelen wissen die zijn gekoppeld `PhraseListGrammar` aan de door het aanroepen van Clear ().

::: zone pivot="programming-language-csharp"

```cs
phraseList.Clear();
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
phraselist->Clear();
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
phraseListGrammar.clear();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar.clear()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> Wijzigingen in een `PhraseListGrammar` object worden van kracht bij de volgende herkenning of na het opnieuw verbinden met de spraak service.

## <a name="next-steps"></a>Volgende stappen

* [Naslag documentatie voor Speech SDK](speech-sdk.md)
