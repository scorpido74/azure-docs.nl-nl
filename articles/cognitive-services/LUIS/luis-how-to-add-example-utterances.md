---
title: Voor beeld-uitingen-LUIS toevoegen
titleSuffix: Azure Cognitive Services
description: Voor beelden van uitingen zijn tekst voorbeelden van gebruikers vragen of-opdrachten. Als u Language Understanding wilt leren (LUIS), moet u bijvoorbeeld uitingen toevoegen aan een intentie.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: ed6321963422cf17c858f43593092f8ffa4a1119
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467744"
---
# <a name="add-an-entity-to-example-utterances"></a>Een entiteit toevoegen aan een voor beeld van uitingen 

Voor beelden van uitingen zijn tekst voorbeelden van gebruikers vragen of-opdrachten. Als u Language Understanding wilt leren (LUIS), moet u [bijvoorbeeld uitingen](luis-concept-utterance.md) toevoegen aan een [intentie](luis-concept-intent.md).


Normaal gesp roken voegt u eerst een voor beeld-utterance toe aan een intentie en vervolgens maakt u entiteiten en label uitingen op de pagina **intenties** . Als u eerst entiteiten wilt maken, raadpleegt u [entiteiten toevoegen](luis-how-to-add-entities.md).

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="marking-entities-in-example-utterances"></a>Entiteiten markeren in bijvoorbeeld uitingen

Wanneer u in het voor beeld utterance tekst selecteert om te markeren voor een entiteit, wordt een in-place pop-upmenu weer gegeven. Gebruik dit menu om een entiteit te maken of te selecteren. 

Bepaalde entiteits typen, zoals vooraf gemaakte entiteiten en reguliere expressie-entiteiten, kunnen niet worden gelabeld in het voor beeld utterance omdat ze automatisch worden gelabeld. 

## <a name="add-a-simple-entity"></a>Een eenvoudige entiteit toevoegen

In de volgende procedure maakt en labelt u een aangepaste entiteit in de volgende utterance op de pagina **intenties** :

```text
Are there any SQL server jobs?
```

1. Selecteer `SQL server` in de utterance om deze als een eenvoudige entiteit te labelen. In de vervolg keuzelijst entiteit die wordt weer gegeven, kunt u een bestaande entiteit selecteren of een nieuwe entiteit toevoegen. Als u een nieuwe entiteit wilt toevoegen, typt u de naam ervan `Job` in het tekstvak en selecteert u **nieuwe entiteit maken**.

    ![Scherm opname van het invoeren van de naam van de entiteit](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > Bij het selecteren van woorden die als entiteiten moeten worden gelabeld:
    > * Voor één woord selecteert u dit. 
    > * Voor een set van twee of meer woorden selecteert u het eerste woord en vervolgens het laatste woord.

1. Controleer in het pop-upvenster **wat voor type entiteit wilt u maken?** de naam van de entiteit en selecteer het type **eenvoudig** entiteit en selecteer vervolgens **gereed**.

    Een [woordgroepen lijst](luis-concept-feature.md) wordt vaak gebruikt om het signaal van een eenvoudige entiteit te verhogen.

## <a name="add-a-list-entity"></a>Een lijst entiteit toevoegen

Lijst entiteiten vertegenwoordigen een set exacte tekst overeenkomsten met verwante woorden in uw systeem. 

Voor de afdelings lijst van een bedrijf kunt u genormaliseerde waarden hebben: `Accounting` en `Human Resources`. Elke genormaliseerde naam heeft synoniemen. Voor een afdeling kunnen deze synoniemen alle afkortingen, cijfers of slang van afdelingen bevatten. U hoeft niet alle waarden te weten wanneer u de entiteit maakt. U kunt meer toevoegen nadat u de werkelijke gebruikers uitingen hebt bekeken met synoniemen.

1. Selecteer in een voor beeld-utterance op de pagina **intenties** het woord of de woord groep die u in de nieuwe lijst wilt opnemen. Wanneer de vervolg keuzelijst entiteit wordt weer gegeven, voert u de naam voor de nieuwe entiteit lijst in het tekstvak boven in en selecteert u **nieuwe entiteit maken**.   

1. Geef in het vak **wat voor type entiteit wilt u maken?** een naam op voor de entiteit en selecteer **lijst** als type. Voeg synoniemen van dit lijst item toe en selecteer vervolgens **gereed**. 

    ![Scherm opname van het invoeren van de lijst entiteit synoniemen](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    U kunt meer lijst items of meer synoniemen van items toevoegen door andere uitingen te labelen of door de entiteit te bewerken vanuit de **entiteiten** in de linkernavigatiebalk. Als u de entiteiten [bewerkt](luis-how-to-add-entities.md#add-list-entities) , beschikt u over de opties voor het invoeren van aanvullende items met corresponderende synoniemen of het importeren van een lijst. 

## <a name="add-a-composite-entity"></a>Een samengestelde entiteit toevoegen

Samengestelde entiteiten worden gemaakt op basis van bestaande **entiteiten** om een bovenliggende entiteit te vormen. 

Ervan uitgaande dat de utterance, `Does John Smith work in Seattle?`, een samengestelde utterance, de entiteits gegevens van de naam van de werk nemer kan retour neren `John Smith`en de locatie `Seattle` in een samengestelde entiteit. De onderliggende entiteiten moeten al bestaan in de app en moeten zijn gemarkeerd in het voor beeld utterance voordat u de samengestelde entiteit maakt.

1. Als u de onderliggende entiteiten in een samengestelde entiteit wilt verpakken, selecteert u de **eerste** gelabelde entiteit (meest links) in het utterance voor de samengestelde entiteit. Er wordt een vervolg keuzelijst weer gegeven waarin de opties voor deze selectie worden weer gegeven.

1. Selecteer in de vervolg keuzelijst de optie **Terugloop in samengestelde entiteit** . 

1. Het laatste woord van de samengestelde entiteit selecteren (rechts). U ziet dat een groene lijn de samengestelde entiteit volgt. Dit is de visuele indicator voor een samengestelde entiteit en moet onder alle woorden in de samengestelde entiteit van de meest linkse onderliggende entiteit worden weer gegeven met de meest rechtse onderliggende entiteit.

1. Voer de naam van de samengestelde entiteit in de vervolg keuzelijst in.

    Wanneer u de entiteiten correct verpakt, bevindt een groene lijn zich onder de hele woord groep.

1. Valideer de details van de samengestelde entiteit in het pop-upvenster **welk type entiteit wilt u maken?** Selecteer vervolgens **gereed**.

    ![Scherm afbeelding van het pop-upvenster met entiteits Details](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. De samengestelde entiteit wordt weer gegeven met de blauwe hooglichten voor afzonderlijke entiteiten en een groene onderstreping voor de hele samengestelde entiteit. 

    ![Scherm afbeelding van de pagina Details van intenties, met samengestelde entiteit](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-entitys-role-to-utterance"></a>De rol van entiteit toevoegen aan utterance

Een rol is een benoemd subtype van een entiteit, bepaald door de context van de utterance. U kunt een entiteit in een utterance markeren als de entiteit of een rol binnen die entiteit selecteren. Elke entiteit kan rollen hebben met inbegrip van aangepaste entiteiten die door machines zijn geleerd (eenvoudige entiteiten en samengestelde entiteiten), zijn niet-geleerd op computers (vooraf gemaakte entiteiten, reguliere expressie-entiteiten, lijst entiteiten). 

Meer informatie [over hoe u een utterance markeert met entiteits rollen](tutorial-entity-roles.md) vanuit een hand leiding. 

## <a name="entity-status-predictions"></a>Voor spellingen van entiteits status

Wanneer u een nieuwe utterance in de LUIS-Portal invoert, kan de utterance de Voorspellings fouten van de entiteit bevatten. De Voorspellings fout is een verschil tussen hoe een entiteit wordt gelabeld in vergelijking met de manier waarop LUIS de entiteit heeft voor speld. 

Dit verschil wordt visueel weer gegeven in de LUIS-Portal met een rode onderstreping in de utterance. De rode onderstreping kan worden weer gegeven in de haakjes van de entiteit of buiten de haakjes. 

![Scherm opname van de voor spelling van de entiteits status](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Selecteer de woorden die rood worden onderstreept in het utterance. 

In het vak entiteit wordt de status van de **entiteit** weer gegeven met een rood uitroep teken als er sprake is van een Voorspellings verschil. Als u de status van de entiteit wilt zien met informatie over het verschil tussen gelabelde en voorspelde entiteiten, selecteert u **entiteits status** en selecteert u vervolgens het item aan de rechter kant.

![Scherm afbeelding van de selectie van entiteits status](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

De rode lijn kan worden weer gegeven op een van de volgende tijdstippen:

   * Wanneer een utterance wordt ingevoerd, maar voordat de entiteit is gelabeld
   * Wanneer het entiteit label wordt toegepast
   * Wanneer het entiteit label wordt verwijderd
   * Wanneer er meer dan één entiteit label voor die tekst wordt voor speld 

De volgende oplossingen helpen u bij het oplossen van discrepantie voor de entiteit voorspelling:

|Entiteit|Indicator voor visuele elementen|Voorspellings|Oplossing|
|--|--|--|--|
|Utterance is opgegeven, de entiteit heeft nog geen label.|rode onderstreping|De voor spelling is juist.|Label de entiteit met de voorspelde waarde.|
|Niet-gelabelde tekst|rode onderstreping|Onjuiste voor spelling|De huidige uitingen die gebruikmaken van deze onjuiste entiteit moeten worden gecontroleerd op alle intenties. De huidige uitingen hebben een LUIS dat deze tekst de voorspelde entiteit bevat.
|Goed gelabelde tekst|licht blauwe entiteit, rode onderstreping|Onjuiste voor spelling|Geef meer uitingen met de juiste entiteit op verschillende plaatsen en gebruik. De huidige uitingen zijn niet voldoende om LUIS te leren dat dit de entiteit is of vergelijk bare entiteiten worden weer gegeven in dezelfde context. Een vergelijk bare entiteit moet worden gecombineerd in één entiteit, zodat LUIS niet wordt verward. Een andere oplossing is het toevoegen van een woordgroepen lijst om de significantie van de woorden te verhogen. |
|Onjuist gelabelde tekst|licht blauwe entiteit, rode onderstreping|Juiste voor spelling| Geef meer uitingen met de juiste entiteit op verschillende plaatsen en gebruik. 

## <a name="other-actions"></a>Andere acties

U kunt acties op voorbeeld uitingen uitvoeren als een geselecteerde groep of als afzonderlijk item. Groepen van geselecteerde voorbeeld uitingen wijzigen het context menu boven de lijst. Afzonderlijke items kunnen zowel het context menu boven de lijst als het afzonderlijke contextuele beletsel teken aan het einde van elke utterance-rij gebruiken. 

### <a name="remove-entity-labels-from-utterances"></a>Labels van entiteiten verwijderen uit uitingen

U kunt door de machine geleerde entiteit labels verwijderen van een utterance op de pagina intenties. Als de entiteit niet door de computer is geleerd, kan deze niet worden verwijderd uit een utterance. Als u een entiteit die niet door een machine is geleerd, moet verwijderen uit de utterance, moet u de entiteit verwijderen uit de hele app. 

Als u een door de machine geleerde entiteits label wilt verwijderen uit een utterance, selecteert u de entiteit in de utterance. Selecteer vervolgens **label verwijderen** in de vervolg keuzelijst entiteit die wordt weer gegeven.

### <a name="add-a-prebuilt-entity-label"></a>Een vooraf gedefinieerde entiteit label toevoegen

Wanneer u de vooraf gemaakte entiteiten toevoegt aan uw LUIS-app, hoeft u uitingen met deze entiteiten niet te labelen. Zie [entiteiten toevoegen](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app)voor meer informatie over vooraf gemaakte entiteiten en hoe u deze kunt toevoegen.

### <a name="add-a-regular-expression-entity-label"></a>Een label voor een reguliere expressie-entiteit toevoegen

Als u de reguliere expressie-entiteiten toevoegt aan uw LUIS-app, hoeft u uitingen niet met deze entiteiten te labelen. Zie [entiteiten toevoegen](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts)voor meer informatie over de entiteiten van een reguliere expressie en hoe u deze kunt toevoegen.


### <a name="create-a-pattern-from-an-utterance"></a>Een patroon maken op basis van een utterance

Zie [patroon toevoegen vanuit bestaande utterance op de pagina intentie of entiteit](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-a-patternany-entity"></a>Een patroon toevoegen. elke entiteit

Als u het patroon toevoegt. alle entiteiten aan uw LUIS-app kunt u geen label uitingen met deze entiteiten. Ze zijn alleen geldig in patronen. Zie [entiteiten toevoegen](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities)voor meer informatie over het patroon. alle entiteiten en hoe u deze kunt toevoegen.

## <a name="train-your-app-after-changing-model-with-utterances"></a>Uw app trainen na het wijzigen van het model met uitingen

Nadat u uitingen hebt toegevoegd, bewerkt of verwijderd, moet u uw app [trainen](luis-how-to-train.md) en [publiceren](luis-how-to-publish-app.md) om uw wijzigingen door te geven aan eindpunt query's. 

## <a name="next-steps"></a>Volgende stappen

Nadat u uitingen in uw **intentie**hebt gelabeld, kunt u nu een [samengestelde entiteit](luis-how-to-add-entities.md)maken.
