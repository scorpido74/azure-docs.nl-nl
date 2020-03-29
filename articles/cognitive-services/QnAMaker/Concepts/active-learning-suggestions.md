---
title: Actieve leersuggesties - QnA Maker
description: Met actieve leersuggesties u de kwaliteit van uw kennisbasis verbeteren door alternatieve vragen, op basis van gebruikersinzendingen, voor te stellen aan uw vraag- en antwoordpaar.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: af4f6b399bfd537b38ea741d03e59371ee81e588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053141"
---
# <a name="active-learning-suggestions"></a>Actieve leersuggesties

Met de functie _Actieve leersuggesties_ u de kwaliteit van uw kennisbank verbeteren door alternatieve vragen, op basis van gebruikersinzendingen, voor te stellen aan uw vraag- en antwoordpaar. U bekijkt deze suggesties en voegt deze toe aan bestaande vragen of wijst ze af.

Je kennisbank verandert niet automatisch. Om elke wijziging van kracht te laten worden, moet u de suggesties accepteren. Deze suggesties voegen vragen toe, maar wijzigen of verwijderen bestaande vragen niet.

## <a name="what-is-active-learning"></a>Wat is actief leren?

QnA Maker leert nieuwe vraagvariaties met impliciete en expliciete feedback.

* [Impliciete feedback](#how-qna-makers-implicit-feedback-works) - De ranker begrijpt wanneer een gebruikersvraag meerdere antwoorden heeft met scores die zeer dicht zijn en dit als feedback beschouwt. Je hoeft niets te doen om dit te laten gebeuren.
* [Expliciete feedback](#how-you-give-explicit-feedback-with-the-train-api) – Wanneer meerdere antwoorden met weinig variatie in scores worden geretourneerd uit de kennisbank, vraagt de clientapplicatie de gebruiker welke vraag de juiste vraag is. De expliciete feedback van de gebruiker wordt naar QnA Maker verzonden met de [Train API.](../How-to/improve-knowledge-base.md#train-api)

Beide methoden bieden de ranker vergelijkbare query's die zijn geclusterd.

## <a name="how-active-learning-works"></a>Hoe actief leren werkt

Actief leren wordt geactiveerd op basis van de scores van de top enkele antwoorden geretourneerd door QnA Maker. Als de scoreverschillen tussen QnA-sets die overeenkomen met de query binnen een klein bereik liggen, wordt de query beschouwd als een mogelijke suggestie (als een alternatieve vraag) voor elk van de mogelijke QnA-paren. Zodra u de voorgestelde vraag voor een specifiek QnA-paar accepteert, wordt deze afgewezen voor de andere paren. Je moet niet vergeten om op te slaan en te trainen, na het accepteren van suggesties.

Actief leren geeft de best mogelijke suggesties in gevallen waarin de eindpunten een redelijke hoeveelheid en verscheidenheid aan gebruiksquery's krijgen. Wanneer 5 of meer vergelijkbare query's worden geclusterd, stelt QnA Maker elke 30 minuten de gebruikersvragen voor aan de knowledge base designer om te accepteren of te weigeren. Alle suggesties worden geclusterd door gelijkenis en topsuggesties voor alternatieve vragen worden weergegeven op basis van de frequentie van de specifieke query's door eindgebruikers.

Zodra vragen worden voorgesteld in de QnA Maker-portal, moet u deze suggesties bekijken en accepteren of afwijzen. Er is geen API om suggesties te beheren.

## <a name="turn-on-active-learning"></a>Actief leren inschakelen

Standaard is actief leren **uitgeschakeld.**
Ga als een gebruik van actief leren:
* Je moet [actief leren inschakelen,](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions) zodat QnA Maker alternatieve vragen verzamelt voor je kennisbank.
* Als u de voorgestelde alternatieve vragen wilt bekijken, [gebruikt u Weergaveopties](../How-To/improve-knowledge-base.md#view-suggested-questions) op de pagina Bewerken.

## <a name="how-qna-makers-implicit-feedback-works"></a>Hoe de impliciete feedback van QnA Maker werkt

De impliciete feedback van QnA Maker maakt gebruik van een algoritme om de scorenabijheid te bepalen en doet vervolgens actieve leersuggesties. Het algoritme om de nabijheid te bepalen is geen eenvoudige berekening. De bereiken in het volgende voorbeeld zijn niet bedoeld om te worden vastgesteld, maar moeten worden gebruikt als een gids om de impact van het algoritme alleen te begrijpen.

Wanneer de score van een vraag zeer zelfverzekerd is, zoals 80%, is het bereik van scores dat in aanmerking komt voor actief leren breed, ongeveer binnen 10%. Naarmate de betrouwbaarheidsscore afneemt, zoals 40%, neemt ook het bereik van de scores af, ongeveer binnen 4%.

In de volgende JSON-reactie van een query op QnA Maker's generateAnswer zijn de scores voor A, B en C in de buurt en worden ze beschouwd als suggesties.

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA Maker weet niet welk antwoord het beste antwoord is. Gebruik de lijst met suggesties van de QnA Maker-portal om het beste antwoord te selecteren en opnieuw te trainen.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Hoe u expliciete feedback geeft met de Train API

QnA Maker heeft expliciete feedback nodig over welke van de antwoorden het beste antwoord was. Hoe het beste antwoord wordt bepaald is aan u en kan omvatten:

* Feedback van gebruikers, het selecteren van een van de antwoorden.
* Bedrijfslogica, zoals het bepalen van een acceptabel scorebereik.
* Een combinatie van zowel feedback van gebruikers als bedrijfslogica.

Gebruik de [Trein-API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train) om het juiste antwoord naar QnA Maker te sturen, nadat de gebruiker deze heeft geselecteerd.

## <a name="next-step"></a>Volgende stap

> [!div class="nextstepaction"]
> [De kennisbank opvragen](query-knowledge-base.md)