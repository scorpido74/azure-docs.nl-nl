---
title: Levenscyclus van kennisbank - QnA Maker
description: QnA Maker leert het beste in een iteratieve cyclus van modelwijzigingen, utterancevoorbeelden, publicatie en verzamelen van gegevens uit eindpuntquery's.
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 98fbd81baa717c981486f33cfb2b3a608cec27c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914949"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Knowledge base lifecycle in QnA Maker
QnA Maker leert het beste in een iteratieve cyclus van modelwijzigingen, utterancevoorbeelden, publicatie en verzamelen van gegevens uit eindpuntquery's.

![Ontwerpcyclus](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Het creëren van een QnA Maker kennisbank
QnA Maker knowledge base (KB) endpoint biedt een best-match antwoord op een gebruikersvraag op basis van de inhoud van de KB. Het maken van een kennisbank is een eenmalige actie voor het opzetten van een inhoudsopslagplaats met vragen, antwoorden en bijbehorende metagegevens. Een kennisbank kan worden gemaakt door bestaande inhoud te crawlen, zoals FAQ-pagina's, producthandleidingen of gestructureerde Q-A-paren. Leer hoe u [een kennisbank maken.](../quickstarts/create-publish-knowledge-base.md)

## <a name="testing-and-updating-the-knowledge-base"></a>Testen en bijwerken van de kennisbank

De kennisbank is klaar om te testen zodra deze is gevuld met inhoud, hetzij redactioneel of door automatische extractie. Interactieve tests kunnen worden uitgevoerd in de QnA Maker-portal via het **testpaneel** door algemene gebruikersquery's in te voeren en te controleren of de antwoorden zijn geretourneerd met de juiste respons en voldoende betrouwbaarheidsscore.

* **Om lage betrouwbaarheidsscores vast te stellen:** voeg alternatieve vragen toe.
* **Wanneer een query het [standaardantwoord](../How-to/change-default-answer.md)onjuist retourneert:** voeg nieuwe antwoorden toe aan de juiste vraag.

Deze strakke lus van test-update blijft totdat u tevreden bent met de resultaten. Leer hoe [u uw kennisbank testen.](../How-To/test-knowledge-base.md)

Voor grote KB's gebruikt u geautomatiseerdtesten `isTest` met de [generateAnswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) en de eigenschap body, die de `test` kennisbank opvraagt in plaats van de gepubliceerde kennisbank.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>De knowledge base publiceren
Zodra u klaar bent met het testen van de kennisbank, u deze publiceren. Publiceren duwt de nieuwste versie van de geteste kennisbank naar een speciale Azure Cognitive Search-index die de **gepubliceerde** kennisbank vertegenwoordigt. Hiermee wordt ook een eindpunt gemaakt dat kan worden aangeroepen in uw toepassing of chatbot.

Op deze manier hebben wijzigingen in de testversie van de kennisbank geen invloed op de gepubliceerde versie die mogelijk live is in een productietoepassing.

Elk van deze kennisbanken kan worden gericht voor het testen afzonderlijk. Met behulp van de API's u `isTest` de testversie van de kennisbank targeten met lichaamseigenschap in het genererenAntwoordgesprek.

Meer informatie over het [publiceren van uw kennisbank.](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)

## <a name="monitor-usage"></a>Gebruik bewaken
Als u de chatlogboeken van uw service wilt kunnen registreren, moet u Application Insights inschakelen wanneer u [uw QnA Maker-service maakt.](../How-To/set-up-qnamaker-service-azure.md)

U verschillende analyses van uw servicegebruik krijgen. Meer informatie over het gebruik van toepassingsinzichten om [analyses voor uw QnA Maker-service](../How-To/get-analytics-knowledge-base.md)te krijgen.

Op basis van wat u leert van uw analyses, maak de juiste [updates voor uw kennisbank.](../How-To/edit-knowledge-base.md)

## <a name="version-control-for-data-in-your-knowledge-base"></a>Versiebeheer voor gegevens in uw kennisbank

Versiebeheer voor gegevens wordt verstrekt via de import/exportfuncties op de pagina **Instellingen** in de QnA Maker-portal.

U een back-up maken van een `.tsv` kennisbank door de kennisbank te exporteren, in één of `.xls` ander formaat. Neem dit bestand op nadat u dit hebt geëxporteerd als onderdeel van uw reguliere broncontrole.

Wanneer u terug moet naar een specifieke versie, moet u dat bestand importeren uit uw lokale systeem. Een geëxporteerde kennisbank **mag** alleen worden gebruikt via import op de pagina **Instellingen.** Het kan niet worden gebruikt als een bestands- of URL-documentgegevensbron. Dit vervangt vragen en antwoorden die momenteel in de kennisbank staan bij de inhoud van het geïmporteerde bestand.

## <a name="test-and-production-knowledge-base"></a>Test- en productiekennisbank
Een kennisbank is de opslagplaats van vragen en antwoordsets die zijn gemaakt, onderhouden en gebruikt via QnA Maker. Elke QnA Maker-bron kan meerdere kennisbanken bevatten.

Een kennisbank heeft twee staten: *test* en *gepubliceerd*.

### <a name="test-knowledge-base"></a>Test kennisbank

De *testkennisbank* is de versie die momenteel is bewerkt, opgeslagen en getest op nauwkeurigheid en volledigheid van reacties. Wijzigingen in de testkennisbank hebben geen invloed op de eindgebruiker van uw toepassing of chatbot. De testkennisbank staat `test` bekend als in de HTTP-aanvraag. De `test` kennis is beschikbaar met het interactieve **testvenster van** de QnA Maker.

### <a name="production-knowledge-base"></a>De kennisbasis van de productie

De *gepubliceerde kennisbank* is de versie die wordt gebruikt in uw chatbot of toepassing. De actie van het publiceren van een kennisbank plaatst de inhoud van de testkennisbasis in de gepubliceerde versie van de kennisbank. Omdat de gepubliceerde kennisbank de versie is die de toepassing via het eindpunt gebruikt, moet u ervoor zorgen dat de inhoud correct en goed is getest. De gepubliceerde kennisbank `prod` staat bekend als in de HTTP-aanvraag.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Actieve leersuggesties](./active-learning-suggestions.md)