---
title: Entiteiten toevoegen-LUIS
titleSuffix: Azure Cognitive Services
description: Maak entiteiten voor het extra heren van belang rijke gegevens van gebruikers uitingen in apps van Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 54c9d79c62052daeee76de5dffb1099dc7d75180
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467715"
---
# <a name="create-entities-without-utterances"></a>Entiteiten maken zonder uitingen

De entiteit vertegenwoordigt een woord of woord groep in de utterance die u wilt geëxtraheerden. Een entiteit vertegenwoordigt een klasse met inbegrip van een verzameling vergelijk bare objecten (plaatsen, dingen, personen, gebeurtenissen of concepten). Entiteiten beschrijven informatie die relevant is voor de bedoeling, en soms zijn ze essentieel voor uw app om de taak uit te voeren. U kunt entiteiten maken wanneer u een utterance toevoegt aan een intentie of op basis van (vóór of na) het toevoegen van een utterance aan een intentie.

U kunt entiteiten in uw LUIS-app toevoegen, bewerken of verwijderen via de **lijst entiteiten** op de pagina **entiteiten** . LUIS biedt twee hoofd typen entiteiten: [vooraf gemaakte entiteiten](luis-reference-prebuilt-entities.md)en uw eigen [aangepaste entiteiten](luis-concept-entity-types.md#types-of-entities).

Zodra een door de machine geleerde entiteit is gemaakt, moet u die entiteit markeren in alle voor beelden van de intenties in het utterance.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="add-prebuilt-entity"></a>

## <a name="add-a-prebuilt-entity-to-your-app"></a>Een vooraf samengestelde entiteit toevoegen aan uw app

Algemene vooraf gemaakte entiteiten die zijn toegevoegd aan een toepassing zijn *nummer* -en *datetimeV2*. 

1. In uw app selecteert u in de sectie **Build** de optie **entiteiten** in het linkerdeel venster.
 
1. Selecteer op de pagina **entiteiten** vooraf **gemaakte entiteiten toevoegen**.

1. Selecteer in het dialoog venster **vooraf gemaakte entiteiten toevoegen** de **standaard** -en **datetimeV2** -entiteiten. Selecteer vervolgens **Done**.

    ![Scherm afbeelding van het dialoog venster vooraf samengestelde entiteit toevoegen](./media/add-entities/list-of-prebuilt-entities.png)

<a name="add-simple-entities"></a>

## <a name="add-simple-entities-for-single-concepts"></a>Eenvoudige entiteiten voor enkele concepten toevoegen

In een eenvoudige entiteit wordt één concept beschreven. Gebruik de volgende procedure om een entiteit te maken die afdelings namen van het bedrijf extraheert, zoals *Human Resources* of *Operations*.   

1. Selecteer in uw app de sectie **Build** , selecteer **entiteiten** in het linkerdeel venster en selecteer vervolgens **nieuwe entiteit maken**.

1. In het pop-updialoogvenster typt u `Location` in het vak **entiteits naam** , selecteert u **eenvoudig** in de lijst **entiteits type** en selecteert u **gereed**.

    Zodra deze entiteit is gemaakt, gaat u naar alle intenties met voorbeeld uitingen die de entiteit bevatten. Selecteer de tekst in het voor beeld utterance en markeer de tekst als de entiteit. 

    Een [woordgroepen lijst](luis-concept-feature.md) wordt vaak gebruikt om het signaal van een eenvoudige entiteit te verhogen.

<a name="add-regular-expression-entities"></a>

## <a name="add-regular-expression-entities-for-highly-structured-concepts"></a>Reguliere expressie-entiteiten voor zeer gestructureerde concepten toevoegen

Een reguliere expressie-entiteit wordt gebruikt om gegevens uit de utterance op te halen op basis van een reguliere expressie die u opgeeft. 

1. Selecteer in uw app **entiteiten** in de linkernavigatiebalk en selecteer vervolgens **nieuwe entiteit maken**.

1. Voer in het pop-updialoogvenster `Human resources form name` in het vak **entiteits naam** , selecteer **reguliere expressie** in de lijst **entiteits type** , voer de reguliere expressie `hrf-[0-9]{6}`in en selecteer vervolgens **gereed**. 

    Deze reguliere expressie komt overeen met letterlijke tekens `hrf-`en 6 cijfers die een formulier nummer voor een HRM-formulier vertegenwoordigen.

<a name="add-composite-entities"></a>

## <a name="add-composite-entities-to-group-into-a-parent-child-relationship"></a>Samengestelde entiteiten toevoegen aan een groep in een relatie met een bovenliggend/onderliggend item

U kunt relaties tussen entiteiten van verschillende typen definiëren door een samengestelde entiteit te maken. In het volgende voor beeld bevat de entiteit een reguliere expressie en een vooraf gedefinieerde entiteit met de naam.  

In de `Send hrf-123456 to John Smith`utterance wordt de tekst `hrf-123456` gekoppeld aan een [reguliere expressie](#add-regular-expression-entities) van human resources en `John Smith` wordt geëxtraheerd met de vooraf gedefinieerde entiteits persoon. Elke entiteit maakt deel uit van een grotere bovenliggende entiteit. 

1. Selecteer in uw app **entiteiten** in de linkernavigatiebalk van de sectie **Build** en selecteer vervolgens **vooraf samengestelde entiteit toevoegen**.

1. Voeg de vooraf samengestelde entiteits **persoon**in. Zie voor instructies [add prebuiled entities](#add-prebuilt-entity). 

1. Selecteer **entiteiten** in het linkernavigatievenster en selecteer vervolgens **nieuwe entiteit maken**.

1. Voer in het pop-updialoogvenster `SendHrForm` in het vak **entiteits naam** in en selecteer vervolgens **samengesteld** in de lijst **entiteits type** .

1. Selecteer **kind toevoegen** om een nieuw onderliggend item toe te voegen.

1. Selecteer in **onderliggende #1**het entiteits **nummer** in de lijst.

1. Selecteer in **onderliggende #2**de naam van de entiteit **Human Resources-formulier** in de lijst. 

1. Selecteer **Done**.

<a name="add-pattern-any-entities"></a>

## <a name="add-patternany-entities-to-capture-free-form-entities"></a>Patroon toevoegen. entiteiten voor het vastleggen van vrije-vorm entiteiten

[Patroon. alle](luis-concept-entity-types.md) entiteiten zijn alleen geldig in [patronen](luis-how-to-model-intent-pattern.md), niet als doel. Dit type entiteit helpt LUIS het einde van entiteiten met een verschillende lengte en woord keuze te vinden. Omdat deze entiteit in een patroon wordt gebruikt, weet LUIS waar het einde van de entiteit zich in de utterance-sjabloon bevindt.

Als een app een `FindHumanResourcesForm` intentie heeft, kan de geëxtraheerde formulier titel de intentie voorspelling veroorzaken. Gebruik een patroon om te verduidelijken welke woorden in de titel van het formulier worden gebruikt. in een patroon. De LUIS-voor spelling begint met de utterance. Eerst wordt de utterance gecontroleerd en afgestemd op entiteiten, wanneer de entiteiten worden gevonden, wordt het patroon gecontroleerd en afgestemd. 

In de utterance-`Where is Request relocation from employee new to the company on the server?`is de titel van het formulier lastig, omdat het niet zo duidelijk is waar de titel eindigt en waar de rest van de utterance begint. Titels kunnen bestaan uit een wille keurige volg orde van woorden, waaronder één woord, complexe zinnen met interpunctie en nonsensicale volg orde van woorden. Met een patroon kunt u een entiteit maken waar de volledige en de exacte entiteit kunnen worden geëxtraheerd. Zodra de titel is gevonden, wordt de `FindHumanResourcesForm` intentie voor speld, omdat dat de bedoeling is voor het patroon.

1. Selecteer in de sectie **Build** de optie **entiteiten** in het linkerdeel venster en selecteer vervolgens **nieuwe entiteit maken**.

1. Voer in het dialoog venster **entiteit toevoegen** `HumanResourcesFormTitle` in het vak **entiteits naam** in en selecteer **patroon. elk** als **entiteits type**.

    Als u het patroon wilt gebruiken. een wille keurige entiteit, een patroon toevoegen op de pagina **patronen** , in de sectie de prestaties van de **app verbeteren** , met de juiste accolade syntaxis, zoals `Where is **{HumanResourcesFormTitle}** on the server?`.

    Als u merkt dat het patroon, wanneer dit een Pattern.any bevat, entiteiten onjuist extraheert, gebruikt u een [expliciete lijst](luis-concept-patterns.md#explicit-lists) om dit probleem te verhelpen. 

<a name="add-a-role-to-pattern-based-entity"></a>

## <a name="add-a-role-to-distinguish-different-contexts"></a>Een rol toevoegen om verschillende contexten te onderscheiden

Een rol is een benoemd subtype op basis van de context. Het is beschikbaar in alle entiteiten, inclusief vooraf ontwikkelde en niet-machines geleerde entiteiten. 

De syntaxis voor een rol is **`{Entityname:Rolename}`** waarbij de naam van de entiteit wordt gevolgd door een dubbele punt, en vervolgens de naam van de rol. Bijvoorbeeld `Move {personName} from {Location:Origin} to {Location:Destination}`.

1. Selecteer in de sectie **Build** de optie **entiteiten** in het linkerdeel venster.

1. Selecteer **Create new intent**. Voer de naam van `Location`in. Selecteer het type **eenvoudig** en selecteer **gereed**. 

1. Selecteer **entiteiten** in het linkerdeel venster en selecteer vervolgens de nieuwe entiteit **locatie** die in de vorige stap is gemaakt.

1. Voer in het tekstvak **rolnaam naam** de naam van de rol `Origin` in en voer in. Voeg een tweede Rolnaam van `Destination`toe. 

    ![Scherm afbeelding van de functie origin toevoegen aan de locatie-entiteit](./media/add-entities/roles-enter-role-name-text.png)

<a name="add-list-entities"></a>

## <a name="add-list-entities-for-exact-matches"></a>Lijst entiteiten toevoegen voor exacte overeenkomsten

Lijst entiteiten vertegenwoordigen een vaste, gesloten set verwante woorden. 

Voor een Human resources-app kunt u een lijst met alle afdelingen en eventuele synoniemen voor de afdelingen hebben. U hoeft niet alle waarden te weten wanneer u de entiteit maakt. U kunt meer toevoegen nadat u de werkelijke gebruikers uitingen hebt bekeken met synoniemen.

1. Selecteer in de sectie **Build** de optie **entiteiten** in het linkerdeel venster en selecteer vervolgens **nieuwe entiteit maken**.

1. In het dialoog venster **entiteit toevoegen** typt u `Department` in het vak **entiteits naam** en selecteert u **lijst** als **entiteits type**. Selecteer **Done**.
  
1. Op de pagina lijst entiteit kunt u genormaliseerde namen toevoegen. Voer in het tekstvak **waarden** een afdelings naam voor de lijst in, bijvoorbeeld `HumanResources` druk op ENTER op het toetsen bord. 

1. Rechts van de genormaliseerde waarde voert u synoniemen in, drukt u op ENTER op het toetsen bord na elk item.

1. Als u meer genormaliseerde items voor de lijst wilt, selecteert u **aanbevelen** om opties te bekijken in de [semantische woorden lijst](luis-glossary.md#semantic-dictionary).

    ![Scherm opname van het selecteren van een aanbevolen functie om opties te bekijken](./media/add-entities/hr-list-2.png)


1. Selecteer een item in de aanbevolen lijst om het toe te voegen als een genormaliseerde waarde of selecteer **Alles toevoegen** om alle items toe te voegen. 
    U kunt waarden importeren in een bestaande lijst entiteit met behulp van de volgende JSON-indeling:

    ```JSON
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

<a name="change-entity-type"></a>

## <a name="do-not-change-entity-type"></a>Entiteits type niet wijzigen

LUIS staat niet toe dat u het type van de entiteit wijzigt omdat deze niet weet wat moet worden toegevoegd of verwijderd om die entiteit te bouwen. Als u het type wilt wijzigen, is het beter om een nieuwe entiteit van het juiste type te maken met een iets andere naam. Nadat de entiteit is gemaakt, verwijdert u in elke utterance de oude naam van de entiteit met het label en voegt u de nieuwe entiteits naam toe. Als alle uitingen opnieuw zijn gelabeld, verwijdert u de oude entiteit. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-pattern-from-an-example-utterance"></a>Een patroon maken op basis van een voor beeld van een utterance

Zie [patroon toevoegen vanuit bestaande utterance op de pagina intentie of entiteit](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="train-your-app-after-changing-model-with-entities"></a>Uw app trainen na het wijzigen van het model met entiteiten

Nadat u entiteiten hebt toegevoegd, bewerkt of verwijderd, [traint](luis-how-to-train.md) en [publiceert](luis-how-to-publish-app.md) u uw app om uw wijzigingen te beïnvloeden op eindpunt query's. 

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over vooraf gemaakte entiteiten raadpleegt u het [tekst project recognizers-text](https://github.com/Microsoft/Recognizers-Text) . 

Zie voor meer informatie over hoe de entiteit wordt weer gegeven in het query-antwoord van het JSON-eind punt [gegevens extractie](luis-concept-data-extraction.md)

Nu u intenties, uitingen en entiteiten hebt toegevoegd, hebt u een eenvoudige LUIS-app. Meer informatie over het [trainen](luis-how-to-train.md), [testen](luis-interactive-test.md)en [publiceren](luis-how-to-publish-app.md) van uw app.
 
