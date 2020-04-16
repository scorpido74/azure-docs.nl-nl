---
title: Woordgroeplijsten - Spraakservice
titleSuffix: Azure Cognitive Services
description: Meer informatie over het leveren van de `PhraseListGrammar` spraakservice met een woordgroeplijst met het object om de resultaten van spraak-naar-tekstherkenning te verbeteren.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5a21358edae4c61f35993770c22634da9ac83633
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401972"
---
# <a name="phrase-lists-for-speech-to-text"></a>Woordlijsten voor spraak-naar-tekst

Door de Spraakservice een lijst met zinnen te geven, u de nauwkeurigheid van spraakherkenning verbeteren. Woordgroeplijsten worden gebruikt om bekende zinnen in audiogegevens te identificeren, zoals de naam van een persoon of een specifieke locatie.

Als u bijvoorbeeld een opdracht 'Verplaatsen naar' en een mogelijke bestemming van 'Ward' hebt die kan worden gesproken, u een vermelding toevoegen van 'Verplaatsen naar afdeling'. Het toevoegen van een zin verhoogt de kans dat wanneer de audio wordt herkend, 'Naar Ward' wordt herkend in plaats van 'Naar toe gaan'.

Enkele woorden of volledige woordgroepen kunnen worden toegevoegd aan een woordgroeplijst. Tijdens de herkenning wordt een vermelding in een woordenlijst gebruikt als een exacte overeenkomst voor de hele zin in de audio wordt opgenomen als een afzonderlijke woordgroep. Als er geen exacte overeenkomst wordt gevonden met de woordgroep, wordt de herkenning niet ondersteund.

>[!Note]
> Op dit moment ondersteunt Woordgroeplijsten alleen Engels voor spraak-naar-tekst.

## <a name="how-to-use-phrase-lists"></a>Woordlijsten gebruiken

In de onderstaande voorbeelden is te `PhraseListGrammar` zien hoe u een woordgroeplijst maken met behulp van het object.

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

::: zone pivot="programming-language-more"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> Het maximum aantal woordgroeplijsten dat de spraakservice gebruikt om spraak te matchen, is 1024 zinnen.

U ook de zinnen `PhraseListGrammar` wissen die aan de door duidelijke aanroepen() zijn gekoppeld.

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

::: zone pivot="programming-language-more"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> Wijzigingen in `PhraseListGrammar` een object worden van kracht op de volgende herkenning of na een heraansluiting met de spraakservice.

## <a name="next-steps"></a>Volgende stappen

* [SpraakSDK-referentiedocumentatie](speech-sdk.md)
