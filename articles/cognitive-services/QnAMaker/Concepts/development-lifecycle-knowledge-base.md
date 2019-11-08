---
title: Levens cyclus van Knowledge Base-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker leert het beste in een iteratieve cyclus van model wijzigingen, utterance-voor beelden, publiceren en verzamelen van gegevens uit eindpunt query's.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 1cb5af13bdd309c762337e64ecde8538afc756b0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794848"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Levens cyclus van de Knowledge Base in QnA Maker
QnA Maker leert het beste in een iteratieve cyclus van model wijzigingen, utterance-voor beelden, publiceren en verzamelen van gegevens uit eindpunt query's. 

![Ontwerpcyclus](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Een QnA Maker Knowledge Base maken
QnA Maker Knowledge Base-eind punt (KB) biedt een beste antwoord op een gebruikers query op basis van de inhoud van de KB. Het maken van een kennis database is een eenmalige actie voor het instellen van een inhouds opslagplaats met vragen, antwoorden en gekoppelde meta gegevens. Een Knowledge Base kan worden gemaakt door vooraf bestaande inhoud te verkennen, zoals pagina's met veelgestelde vragen, product handleidingen of gestructureerde Q-A-paren. Meer informatie over het [maken van een Knowledge Base](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>De Knowledge Base testen en bijwerken

De Knowledge Base is klaar om te worden getest nadat deze is gevuld met inhoud, hetzij op basis van een hand leiding hetzij door automatische extractie. Interactieve tests kunnen worden uitgevoerd in de QnA Maker portal via het deel venster **testen** door algemene gebruikers query's in te voeren en te controleren of de antwoorden die zijn geretourneerd met de juiste reactie en voldoende betrouw bare Score zijn. 

* **Zo kunt u lage betrouwbaarheids scores herstellen**: alternatieve vragen toevoegen. 
* **Wanneer een query niet de [standaard reactie](confidence-score.md#change-default-answer)retourneert**: nieuwe antwoorden toevoegen aan de juiste vraag. 

Deze nauw keurige lus van test-update gaat door totdat u tevreden bent met de resultaten. Meer informatie over het [testen van uw Knowledge Base](../How-To/test-knowledge-base.md).

Gebruik voor grote Kb's geautomatiseerd testen met de [generateAnswer-API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) en de eigenschap body van `isTest` die de `test` Knowledge Base doorzoekt in plaats van de gepubliceerde kennis database. 

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>De knowledge base publiceren
Wanneer u klaar bent met het testen van de Knowledge Base, kunt u deze publiceren. Publiceren duwt de nieuwste versie van de geteste Knowledge Base naar een speciale Azure Cognitive Search-index die de **gepubliceerde** kennis database vertegenwoordigt. Hiermee wordt ook een eindpunt gemaakt dat kan worden aangeroepen in uw toepassing of chatbot.

Op deze manier zijn wijzigingen die worden aangebracht in de test versie van de Knowledge Base, niet van invloed op de gepubliceerde versie die mogelijk in een productie toepassing aanwezig is.

Elk van deze kennis grondslagen kan afzonderlijk worden getest. Met behulp van de Api's kunt u de test versie van de Knowledge Base richten op `isTest` Body-eigenschap in de generateAnswer-aanroep.

Meer informatie over het [publiceren van uw Knowledge Base](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Gebruik bewaken
Als u de chat logboeken van uw service wilt kunnen registreren, moet u Application Insights inschakelen wanneer u [uw QnA Maker-service maakt](../How-To/set-up-qnamaker-service-azure.md).

U kunt verschillende analyses van uw service gebruik krijgen. Meer informatie over hoe u Application Insights kunt gebruiken om [analyses voor uw QnA Maker-service](../How-To/get-analytics-knowledge-base.md)op te halen.

Op basis van wat u leert van uw analyse, moet u [de juiste updates voor uw Knowledge Base](../How-To/edit-knowledge-base.md)maken.

## <a name="version-control-of-a-knowledge-base"></a>Versie beheer van een Knowledge Base

Versie beheer wordt niet verschaft door QnA Maker. U moet uw Knowledge Base exporteren via de pagina **instellingen** en uw eigen methodologie en hulpprogram ma's gebruiken.

Het exporteren van de Knowledge Base naar de TSV-of XLS-indeling wordt voltooid vanaf de pagina **instellingen** . 

Als u terug wilt gaan naar een specifieke versie, moet u het bestand importeren van het lokale systeem. Importeer op de pagina **instellingen** het TSV-of XLS-bestand. Hiermee worden vragen en antwoorden die momenteel in de Knowledge Base staan vervangen door de inhoud van het geïmporteerde bestand.   

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Betrouwbaarheids Score](./confidence-score.md)