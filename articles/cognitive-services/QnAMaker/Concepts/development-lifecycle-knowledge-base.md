---
title: Levenscyclus van het knowledge base - QnA Maker
description: QnA Maker leert beste in een iteratief cyclus van wijzigingen in het gegevensmodel, utterance voorbeelden, publiceren en verzamelen van gegevens van eindpunt query's.
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 98fbd81baa717c981486f33cfb2b3a608cec27c7
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914949"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Levenscyclus van de Knowledge base in QnA Maker
QnA Maker leert beste in een iteratief cyclus van wijzigingen in het gegevensmodel, utterance voorbeelden, publiceren en verzamelen van gegevens van eindpunt query's.

![Ontwerpcyclus](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Het maken van een kennisdatabase QnA Maker
QnA Maker knowledge base (KB)-eindpunt biedt een best-none-match-antwoord op een gebruikersquery op basis van de inhoud van de KB. Het maken van een kennis database is een eenmalige actie voor het instellen van een inhouds opslagplaats met vragen, antwoorden en gekoppelde meta gegevens. Een kennisdatabase kunnen worden gemaakt met het verkennen van de bestaande inhoud zoals veelgestelde vragen over pagina's, producthandleidingen of gestructureerde Q-A-paren. Meer informatie over het [maken van een Knowledge Base](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testen en bijwerken van de knowledge base

De knowledge base is klaar voor testen wanneer het wordt gevuld met inhoud, redactioneel of via automatische extractie. Interactieve tests kunnen worden uitgevoerd in de QnA Maker portal via het deel venster **testen** door algemene gebruikers query's in te voeren en te controleren of de antwoorden die zijn geretourneerd met de juiste reactie en voldoende betrouw bare Score zijn.

* **Zo kunt u lage betrouwbaarheids scores herstellen**: alternatieve vragen toevoegen.
* **Wanneer een query niet de [standaard reactie](../How-to/change-default-answer.md)retourneert**: nieuwe antwoorden toevoegen aan de juiste vraag.

Deze lus van test-update gaat door totdat u tevreden met de resultaten bent. Meer informatie over het [testen van uw Knowledge Base](../How-To/test-knowledge-base.md).

Gebruik voor grote Kb's geautomatiseerde tests met de [generateAnswer-API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) en de eigenschap hoofd code van `isTest`, die de `test` Knowledge Base doorzoekt in plaats van de gepubliceerde kennis database.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>De knowledge base publiceren
Zodra u klaar bent met testen van de knowledge base, kunt u deze publiceert. Publiceren duwt de nieuwste versie van de geteste Knowledge Base naar een speciale Azure Cognitive Search-index die de **gepubliceerde** kennis database vertegenwoordigt. Hiermee wordt ook een eindpunt gemaakt dat kan worden aangeroepen in uw toepassing of chatbot.

Op deze manier alle wijzigingen aan de testversie van de knowledge base niet van invloed op de gepubliceerde versie die mogelijk live in een productietoepassing.

Elk van deze knowledge bases kan worden gericht voor het testen van afzonderlijk. Met behulp van de Api's kunt u de test versie van de Knowledge Base richten op `isTest` Body-eigenschap in de generateAnswer-aanroep.

Meer informatie over het [publiceren van uw Knowledge Base](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Gebruik bewaken
Als u de chat logboeken van uw service wilt kunnen registreren, moet u Application Insights inschakelen wanneer u [uw QnA Maker-service maakt](../How-To/set-up-qnamaker-service-azure.md).

U kunt verschillende analyse van uw gebruik van de service ophalen. Meer informatie over hoe u Application Insights kunt gebruiken om [analyses voor uw QnA Maker-service](../How-To/get-analytics-knowledge-base.md)op te halen.

Op basis van wat u leert van uw analyse, moet u [de juiste updates voor uw Knowledge Base](../How-To/edit-knowledge-base.md)maken.

## <a name="version-control-for-data-in-your-knowledge-base"></a>Versie beheer voor gegevens in uw Knowledge Base

Versie beheer voor gegevens wordt gegeven via de functies voor importeren/exporteren op de pagina **instellingen** van de QnA Maker Portal.

U kunt een back-up van een Knowledge Base maken door de Knowledge Base te exporteren in `.tsv` of `.xls` indeling. Als u dit bestand hebt geëxporteerd, neemt u dit op als onderdeel van de regel matige controle van broncode beheer.

Als u terug wilt gaan naar een specifieke versie, moet u het bestand importeren van het lokale systeem. Een geëxporteerde Knowledge Base **mag** alleen worden gebruikt via importeren op de pagina **instellingen** . Het kan niet worden gebruikt als een bestands-of URL-document gegevens bron. Hiermee worden vragen en antwoorden die momenteel in de Knowledge Base staan vervangen door de inhoud van het geïmporteerde bestand.

## <a name="test-and-production-knowledge-base"></a>Test-en productie Knowledge Base
Een kennis database is de opslag plaats van vragen en antwoorden sets die zijn gemaakt, onderhouden en worden gebruikt via QnA Maker. Elke QnA Maker resource kan meerdere kennis slagen bevatten.

Een kennis database heeft twee statussen: *testen* en *gepubliceerd*.

### <a name="test-knowledge-base"></a>Kennis basis testen

De *test kennis basis* is de versie die momenteel wordt bewerkt, opgeslagen en getest op nauw keurigheid en volledige reacties. Wijzigingen die zijn aangebracht in de test Knowledge Base, hebben geen invloed op de eind gebruiker van uw toepassing of chat-bot. De test kennis basis staat bekend als `test` in de HTTP-aanvraag. De `test` kennis is beschikbaar in het deel venster met de portal interactieve **test** van QnA Maker.

### <a name="production-knowledge-base"></a>Productie Knowledge Base

De *gepubliceerde kennis database* is de versie die wordt gebruikt in uw chat-bot of-toepassing. De actie voor het publiceren van een Knowledge Base heeft de inhoud van de test Knowledge Base in de gepubliceerde versie van de Knowledge Base. Omdat de gepubliceerde kennis basis de versie is die de toepassing via het eind punt gebruikt, moet u ervoor zorgen dat de inhoud juist en goed is getest. De gepubliceerde kennis basis staat bekend als `prod` in de HTTP-aanvraag.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Suggesties voor actieve trainingen](./active-learning-suggestions.md)