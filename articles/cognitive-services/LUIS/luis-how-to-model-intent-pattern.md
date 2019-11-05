---
title: Nauw keurigheid van patronen toevoegen-LUIS
titleSuffix: Azure Cognitive Services
description: Voeg patroon sjablonen toe om de nauw keurigheid van de voor spellingen te verbeteren in Language Understanding-toepassingen (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: cbd8ad73ff4a03790dd6b22d5ce33acf09a2b125
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491369"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Patronen toevoegen om de nauw keurigheid van de voor spelling te verbeteren
Nadat een LUIS-app eind punt uitingen ontvangt, gebruikt u een [patroon](luis-concept-patterns.md) om de nauw keurigheid van de voor spelling te verbeteren voor uitingen die een patroon onthult in de woord volgorde en woord keuze. Patronen gebruiken specifieke [syntaxis](luis-concept-patterns.md#pattern-syntax) om de locatie van: [entiteiten](luis-concept-entity-types.md), entiteits [rollen](luis-concept-roles.md)en optionele tekst aan te geven.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-template-utterance-to-create-pattern"></a>Een sjabloon utterance toevoegen om een patroon te maken
1. Open uw app door de naam ervan te selecteren op **mijn apps** pagina en vervolgens **patronen** te selecteren in het linkerdeel venster onder **app-prestaties verbeteren**.

    ![Scherm afbeelding van de lijst patronen](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Selecteer de juiste intentie voor het patroon. 

    ![Intentie selecteren](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. Typ in het tekstvak sjabloon de sjabloon utterance en selecteer ENTER. Wanneer u de naam van de entiteit wilt opgeven, gebruikt u de juiste syntaxis voor de patroon entiteit. Start de entiteits syntaxis met `{`. De lijst met entiteiten wordt weer gegeven. Selecteer de juiste entiteit en selecteer vervolgens ENTER. 

    ![Scherm afbeelding van de entiteit voor het patroon](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Als uw entiteit een [rol](luis-concept-roles.md)bevat, geeft u de rol op met één dubbele punt, `:`na de naam van de entiteit, zoals `{Location:Origin}`. De lijst met rollen voor de entiteiten wordt weer gegeven in een lijst. Selecteer de rol en selecteer vervolgens ENTER. 

    ![Scherm opname van entiteit met rol](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Nadat u de juiste entiteit hebt geselecteerd, voltooit u de invoer van het patroon en selecteert u vervolgens ENTER. Wanneer u klaar bent met het invoeren van patronen, [traint](luis-how-to-train.md) u uw app.

    ![Scherm opname van het ingevoerde patroon met beide typen entiteiten](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Uw app trainen na het wijzigen van het model met patronen
Nadat u een patroon hebt toegevoegd, bewerkt, verwijderd of opnieuw toewijst, [traint](luis-how-to-train.md) u uw app en [publiceert](luis-how-to-publish-app.md) u deze voor uw wijzigingen om eindpunt query's te beïnvloeden. 

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Contextuele werk balk gebruiken

Met de contextuele werk balk boven de lijst met patronen kunt u het volgende doen:

* Patronen zoeken
* Een patroon bewerken
* Afzonderlijk patroon toewijzen aan ander doel
* Verschillende patronen opnieuw toewijzen aan verschillende doel stellingen
* Delete-a-enkel patroon
* Verschillende patronen verwijderen
* Filter patroon lijst op entiteit
* Filter-pattern-list-by-intentie
* Entiteit of intentie filter verwijderen
* Patroon toevoegen vanuit bestaande utterance op de pagina intentie of entiteit

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [bouwen van een patroon](luis-tutorial-pattern.md) met een patroon. alle en rollen met een zelf studie.
* Meer informatie over het [trainen](luis-how-to-train.md) van uw app.
