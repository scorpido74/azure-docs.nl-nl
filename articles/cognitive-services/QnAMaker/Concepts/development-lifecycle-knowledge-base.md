---
title: Levenscyclus van het knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker leert beste in een iteratief cyclus van wijzigingen in het gegevensmodel, utterance voorbeelden, publiceren en verzamelen van gegevens van eindpunt query's.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 52d4efaba25dcede3322586d05fceb4d166f51e3
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973811"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Levenscyclus van de Knowledge base in QnA Maker
QnA Maker leert beste in een iteratief cyclus van wijzigingen in het gegevensmodel, utterance voorbeelden, publiceren en verzamelen van gegevens van eindpunt query's. 

![Ontwerpcyclus](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Het maken van een kennisdatabase QnA Maker
QnA Maker knowledge base (KB)-eindpunt biedt een best-none-match-antwoord op een gebruikersquery op basis van de inhoud van de KB. Het maken van een kennisdatabase is een eenmalige actie voor het instellen van een opslagplaats voor inhoud van de vragen, antwoorden en gekoppelde metagegevens. Een kennisdatabase kunnen worden gemaakt met het verkennen van de bestaande inhoud zoals veelgestelde vragen over pagina's, producthandleidingen of gestructureerde Q-A-paren. Meer informatie over het [maken van een kennisdatabase](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testen en bijwerken van de knowledge base

De knowledge base is klaar voor testen wanneer het wordt gevuld met inhoud, redactioneel of via automatische extractie. Interactieve tests kunnen worden uitgevoerd in de QnA Maker portal via het deel venster **testen** door algemene gebruikers query's in te voeren en te controleren of de antwoorden die zijn geretourneerd met de juiste reactie en voldoende betrouw bare Score zijn. 

* **Zo kunt u lage betrouwbaarheids scores herstellen**: alternatieve vragen toevoegen. 
* **Wanneer een query niet de [standaard reactie](confidence-score.md#change-default-answer)retourneert**: nieuwe antwoorden toevoegen aan de juiste vraag. 

Deze lus van test-update gaat door totdat u tevreden met de resultaten bent. Meer informatie over het [uw knowledge base test](../How-To/test-knowledge-base.md).

Gebruik voor grote Kb's geautomatiseerde tests met de [generateAnswer-API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) en de eigenschap `isTest` die de `test` Knowledge Base doorzoekt in plaats van de gepubliceerde kennis database. 

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>De knowledge base publiceren
Zodra u klaar bent met testen van de knowledge base, kunt u deze publiceert. De nieuwste versie van de geteste knowledge base voor een specifieke Azure Search-index vertegenwoordigt pushes publiceren de **gepubliceerd** knowledge base. Hiermee wordt ook een eindpunt gemaakt dat kan worden aangeroepen in uw toepassing of chatbot.

Op deze manier alle wijzigingen aan de testversie van de knowledge base niet van invloed op de gepubliceerde versie die mogelijk live in een productietoepassing.

Elk van deze knowledge bases kan worden gericht voor het testen van afzonderlijk. Met behulp van de Api's kunt u de test versie van de Knowledge Base richten op de eigenschap `isTest` in de generateAnswer-aanroep.

Meer informatie over het [kennisbank publiceren](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Gebruik bewaken
Als u zich de chatlogs van uw service, moet u Application Insights inschakelen wanneer u [uw QnA Maker-service maken](../How-To/set-up-qnamaker-service-azure.md).

U kunt verschillende analyse van uw gebruik van de service ophalen. Meer informatie over hoe u application insights gebruiken om op te halen [analytics voor uw service QnA Maker](../How-To/get-analytics-knowledge-base.md).

Op basis van wat u van uw analyses leert, Controleer juiste [updates voor uw knowledge base](../How-To/edit-knowledge-base.md).

## <a name="version-control-of-a-knowledge-base"></a>Versie beheer van een Knowledge Base

Versie beheer wordt niet verschaft door QnA Maker. U moet uw Knowledge Base exporteren via de pagina **instellingen** en uw eigen methodologie en hulpprogram ma's gebruiken.

Het exporteren van de Knowledge Base naar de TSV-of XLS-indeling wordt voltooid vanaf de pagina **instellingen** . 

Als u terug wilt gaan naar een specifieke versie, moet u het bestand importeren van het lokale systeem. Importeer op de pagina **instellingen** het TSV-of XLS-bestand. Hiermee worden vragen en antwoorden die momenteel in de Knowledge Base staan vervangen door de inhoud van het geïmporteerde bestand.   

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Betrouwbaarheidsscore](./confidence-score.md)