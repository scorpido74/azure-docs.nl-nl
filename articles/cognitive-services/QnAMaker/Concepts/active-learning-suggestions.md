---
title: Suggesties voor actieve trainingen-QnA Maker
description: Met de suggesties voor actieve trainingen kunt u de kwaliteit van uw Knowledge Base verbeteren door alternatieve vragen te stellen op basis van de gebruikers inzendingen, naar uw vraag en antwoord paar.
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 56f3ab870e148c39912d4f1f5e6e7133a5df4a98
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921661"
---
# <a name="active-learning-suggestions"></a>Suggesties voor actieve trainingen

Met de functie voor het gebruik van _actieve trainingen_ kunt u de kwaliteit van uw Knowledge Base verbeteren door alternatieve vragen te stellen, op basis van de gebruikers inzendingen, naar uw vraag en antwoord paar. U kunt deze suggesties bekijken, ofwel toevoegen aan bestaande vragen of afwijzen.

Uw kennis database wordt niet automatisch gewijzigd. Als u een wijziging wilt door voeren, moet u de suggesties accepteren. Deze suggesties Voeg vragen toe, maar u kunt geen bestaande vragen wijzigen of verwijderen.

## <a name="what-is-active-learning"></a>Wat is actief leren?

QnA Maker leert nieuwe vraag varianten met impliciete en expliciete feedback.

* [Impliciete feedback](#how-qna-makers-implicit-feedback-works) : de rang schikking begrijpt wanneer een gebruikers vraag meerdere antwoorden heeft met scores die zeer dicht zijn en die als feedback beschouwt. U hoeft niets te doen om dit te doen.
* [Expliciete feedback](#how-you-give-explicit-feedback-with-the-train-api) : wanneer meerdere antwoorden met weinig variatie in scores worden geretourneerd door de Knowledge Base, vraagt de client toepassing aan de gebruiker welke vraag de juiste vraag is. De expliciete feedback van de gebruiker wordt verzonden naar QnA Maker met de [Train API](../How-to/improve-knowledge-base.md#train-api).

Beide methoden bieden de rang schikking van overeenkomende query's die geclusterd zijn.

## <a name="how-active-learning-works"></a>Werking van actief leren

Actief leren wordt geactiveerd op basis van de scores van de meeste antwoorden die door QnA Maker worden geretourneerd. Als de Score verschillen tussen QnA sets die overeenkomen met de query binnen een klein bereik liggen, wordt de query als een mogelijke suggestie (als een alternatieve vraag) beschouwd voor elk van de mogelijke QnA-paren. Zodra u de voorgestelde vraag voor een specifiek QnA-paar accepteert, wordt het voor de andere paren afgewezen. U moet onthouden om op te slaan en te trainen, nadat u suggesties hebt geaccepteerd.

Actief leren biedt de best mogelijke suggesties in gevallen waarin de eind punten een redelijk aantal en verschillende gebruiks query's verkrijgen. Wanneer vijf of meer soort gelijke query's worden geclusterd, wordt er om de 30 minuten geadviseerd op basis van de gebruikers vragen aan de Knowledge Base QnA Maker-ontwerp functie om te accepteren of te weigeren. Alle suggesties worden geclusterd op basis van de gelijkenis en de meeste suggesties voor alternatieve vragen worden weer gegeven, gebaseerd op de frequentie van de specifieke query's door eind gebruikers.

Zodra vragen worden voorgesteld in de QnA Maker Portal, moet u deze suggesties controleren en accepteren of afwijzen. Er is geen API om suggesties te beheren.

## <a name="how-qna-makers-implicit-feedback-works"></a>Hoe de impliciete feedback van QnA Maker werkt

De impliciete feedback van QnA Maker maakt gebruik van een algoritme om de Score nabijheid te bepalen en maakt vervolgens suggesties voor het actief leren. Het algoritme voor het bepalen van de nabijheid is geen eenvoudige berekening. De bereiken in het volgende voor beeld zijn niet bedoeld om te worden hersteld, maar moeten worden gebruikt als richt lijn om alleen de impact van het algoritme te begrijpen.

Wanneer de Score van een vraag zeer betrouwbaar is, zoals 80%, is het bereik van de scores die worden overwogen voor actief onderwijs breed, ongeveer 10%. Naarmate de betrouwbaarheids Score, zoals 40%, afneemt, wordt het bereik van de scores in ongeveer 4% verkleind.

In de volgende JSON-reactie van een query naar de generateAnswer van QnA Maker zijn de scores voor A, B en C in de buurt en worden ze als suggesties beschouwd.

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

QnA Maker weet niet welk antwoord het beste antwoord is. Gebruik de lijst met suggesties van de QnA Maker Portal om het beste antwoord en Train opnieuw te selecteren.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Expliciete feedback geven met de trein-API

QnA Maker expliciete feedback nodig hebt over welke van de antwoorden het beste antwoord is. Hoe het beste antwoord wordt bepaald, is aan u toe te voegen en kan het volgende omvatten:

* Feedback van gebruikers, selecteert u een van de antwoorden.
* Bedrijfs logica, zoals het bepalen van een acceptabel Score bereik.
* Een combi natie van feedback van gebruikers en bedrijfs logica.

Gebruik de [Train API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train) om het juiste antwoord naar QnA Maker te verzenden nadat de gebruiker dit heeft geselecteerd.

## <a name="next-step"></a>Volgende stap

> [!div class="nextstepaction"]
> [Query's uitvoeren op de Knowledge Base](query-knowledge-base.md)