---
title: Ontwerp kennis basis-QnA Maker
description: Een QnA Maker Knowledge Base bestaat uit een set vraag-en antwoord sets (QnA) en optionele meta gegevens die zijn gekoppeld aan elk QnA-paar.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb71de0f6a3e372d8c1c1fede67ba0c0354532ca
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844312"
---
# <a name="question-and-answer-set-concepts"></a>Concepten van vraag-en antwoord sets

Een Knowledge Base bestaat uit vraag-en antwoord sets (QnA).  Elke set heeft één antwoord en een set bevat alle informatie die aan het _antwoord_is gekoppeld. Een antwoord kan een Data Base-rij of een gegevens structuur-exemplaar in een keer lijken.

## <a name="question-and-answer-sets"></a>Vraag-en antwoord sets

De **vereiste** instellingen in een vraag-en-antwoord-set (QnA) zijn:

* een **vraag** : tekst van de gebruikers query, die wordt gebruikt om de machine trainingen van de gebruiker uit te QnA Maker, om af te stemmen met de vraag van gebruikers met andere woorden, maar hetzelfde antwoord
* het **antwoord: het antwoord van** de set is de reactie die wordt geretourneerd wanneer een gebruikers query overeenkomt met de bijbehorende vraag

Elke set wordt vertegenwoordigd door een **id**.

De **optionele** instellingen voor een set zijn onder andere:

* **Alternatieve vormen van de vraag** : dit helpt QnA Maker het juiste antwoord te retour neren voor een groter aantal vraag frasen
* **Meta gegevens**: meta gegevens zijn tags die zijn gekoppeld aan een QnA paar en worden weer gegeven als sleutel-waardeparen. Tags voor meta gegevens worden gebruikt voor het filteren van QnA-paren en het beperken van de set waarover overeenkomende query's worden uitgevoerd.
* **Vragen over meerdere schakelingen**, gebruikt om door te gaan met een gesprek op meerdere locaties

![QnA Maker knowledge bases](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Redactioneel in knowledge base toevoegen

Als u geen vooraf bestaande inhoud hebt om de Knowledge Base te vullen, kunt u QnA sets op de QnA Maker portal toevoegen. Meer informatie over het bijwerken van uw knowledge base [hier](../How-To/edit-knowledge-base.md).

## <a name="editing-your-knowledge-base-locally"></a>Uw kennis database lokaal bewerken

Zodra een Knowledge Base is gemaakt, wordt het aanbevolen dat u wijzigingen aanbrengt in de Knowledge Base-tekst in de [QnA Maker Portal](https://qnamaker.ai)in plaats van het exporteren en opnieuw importeren via lokale bestanden. Het is echter mogelijk dat u een kennis database lokaal moet bewerken.

Exporteer de Knowledge Base van de pagina **instellingen** en bewerk vervolgens de Knowledge Base met micro soft Excel. Als u ervoor kiest om een andere toepassing te gebruiken om het geëxporteerde bestand te bewerken, kan de toepassing syntaxis fouten veroorzaken omdat deze niet volledig TSV-compatibel is. De TSV-bestanden van micro soft Excel voeren doorgaans geen opmaak fouten uit.

Wanneer u klaar bent met uw bewerkingen, importeert u het TSV-bestand opnieuw vanaf de pagina **instellingen** . Hiermee wordt de huidige Knowledge Base volledig vervangen door de geïmporteerde kennis database.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Levens cyclus van de Knowledge Base in QnA Maker](./development-lifecycle-knowledge-base.md)