---
title: Verwijzing naar de syntaxis van patroon - LUIS
description: Maak entiteiten om belangrijke gegevens uit gebruikersuitingen te extraheren in LUIS-apps (Language Understanding). Geëxtraheerde gegevens worden gebruikt door de clienttoepassing.
ms.topic: reference
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: cc24667f43dfedc032f52c40fc5f8fe5c80bad70
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382150"
---
# <a name="pattern-syntax"></a>Patroonsyntaxis

Patroonsyntaxis is een sjabloon voor een utterance. De sjabloon moet woorden en entiteiten bevatten die u wilt overeenkomen, evenals woorden en [interpunctie](luis-reference-application-settings.md#punctuation-normalization) die u wilt negeren. Het is **geen** gewone uitdrukking.

> [!CAUTION]
> Patronen omvatten alleen machine-geleerde entiteit ouders, niet subcomponenten.

Entiteiten in patronen worden omgeven `{}`door krullende haakjes, . Patronen kunnen entiteiten en entiteiten met rollen omvatten. [Patroon.any](luis-concept-entity-types.md#patternany-entity) is een entiteit die alleen in patronen wordt gebruikt.

De syntaxis van de patroon ondersteunt de volgende syntaxis:

|Functie|Syntaxis|Nestniveau|Voorbeeld|
|--|--|--|--|
|Entiteit| {}- krullende haakjes|2|Waar is formulier {entity-name}?|
|optioneel|[] - vierkante haakjes<BR><BR>Er is een limiet van 3 op nestniveaus van een combinatie van optionele en |2|Het vraagteken is optioneel [?]|
|Groeperen|() - haakjes|2|is (a \| b)|
|of| \|- verticale balk (pijp)<br><br>Er is een limiet van 2 op de verticale balken (Of) in één groep |-|Waar is vorm ({form-name-short} &#x7c; {form-name-long} &#x7c; {form-number})|
|begin en/of einde van de uiting|^ - caret|-|^begin de uiting<br>de utterance is gedaan^<br>^strikte letterlijke overeenkomst van volledige uiting met {aantal} entiteit^|

## <a name="nesting-syntax-in-patterns"></a>Syntaxis in patronen nesten

De **optionele** syntaxis, met vierkante haakjes, kan twee niveaus worden genest. Bijvoorbeeld: `[[this]is] a new form`. In dit voorbeeld worden de volgende uitingen mogelijk:

|Genest optioneel utterance-voorbeeld|Uitleg|
|--|--|
|dit is een nieuwe vorm|komt overeen met alle woorden in patroon|
|is een nieuwe vorm|komt overeen met de buitenste optionele woorden en niet-optionele woorden in patroon|
|een nieuw formulier|komt alleen overeen met de vereiste woorden|

De **syntaxis van de groepering,** met haakjes, kan twee niveaus worden genest. Bijvoorbeeld: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Met deze functie kan een van de drie entiteiten worden gekoppeld.

Als Entiteit1 een locatie is met rollen zoals oorsprong (Seattle) en bestemming (Caïro) en entiteit 2 is een bekende bouwnaam van een lijstentiteit (RedWest-C), worden in de volgende uitingen dit patroon toegewezen:

|Voorbeeld van geneste groeperingsuiting|Uitleg|
|--|--|
|RedWest-C|komt overeen met de entiteit van de buitenste groepering|
|Seattle|komt overeen met een van de entiteiten van de binnenste groep|
|Cairo|komt overeen met een van de entiteiten van de binnenste groep|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Nestlimieten voor groepen met optionele syntaxis

Een combinatie van **groepering** met **optionele** syntaxis heeft een limiet van 3 nestniveaus.

|Toegestaan|Voorbeeld|
|--|--|
|Ja|( [ ( test1 &#x7c; test2 ) ] &#x7c; test3 )|
|Nee|( [ [ [ [ test1 ] &#x7c; test2 ) ] ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Nestlimieten voor groepen met of-ingsyntaxis

Een combinatie van **groepering** met **or-ing** syntaxis heeft een limiet van 2 verticale balken.

|Toegestaan|Voorbeeld|
|--|--|
|Ja|( test1 &#x7c; test2 &#x7c; ( test3 &#x7c; test4 ) )|
|Nee|( test1 &#x7c; test2 &#x7c; test3 &#x7c; ( test4 &#x7c; test5 ) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntaxis om een entiteit toe te voegen aan een patroonsjabloon
Als u een entiteit aan de patroonsjabloon wilt toevoegen, `Who does {Employee} manage?`omringt u de entiteitsnaam met krullende accolades, zoals .

|Patroon met entiteit|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntaxis om een entiteit en rol toe te voegen aan een patroonsjabloon
Een entiteitsrol wordt `{entity:role}` aangeduid als met de entiteitsnaam gevolgd door een dubbele punt en vervolgens de rolnaam. Als u een entiteit met een rol wilt toevoegen aan de patroonsjabloon, `Book a ticket from {Location:Origin} to {Location:Destination}`omringt u de naam en rolnaam van de entiteit met krullende accolades, zoals .

|Patroon met entiteitsrollen|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntaxis om een patroon toe te voegen aan patroonsjabloon
Met de entiteit Pattern.any u een entiteit met een verschillende lengte aan het patroon toevoegen. Zolang de patroonsjabloon wordt gevolgd, kan het patroon.elke lengte zijn.

Als u een **entiteit pattern.any** in de patroonsjabloon wilt toevoegen, omringt `How much does {Booktitle} cost and what format is it available in?`u de entiteit Pattern.any met de krullende accolades, zoals .

|Patroon met Patroon.elke entiteit|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Boektitels in het patroon|
|--|
|Hoeveel kost **stelen dit boek** kosten en welk formaat is het beschikbaar in?|
|Hoeveel vraagt de **kosten** en in welk formaat is het beschikbaar?|
|Hoeveel kost **The Curious Incident of the Dog in the Night-Time** en in welk formaat is het beschikbaar?|

De woorden van de titel van het boek zijn niet verwarrend voor LUIS, omdat LUIS weet waar de titel van het boek eindigt, op basis van het patroon.elke entiteit.

## <a name="explicit-lists"></a>Expliciete lijsten

maak een [expliciete lijst](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) via de ontwerp-API om de uitzondering toe te staan wanneer:

* Je patroon bevat een [patroon.](luis-concept-entity-types.md#patternany-entity)
* En dat patroon syntaxis maakt de mogelijkheid van een onjuiste entiteit extractie op basis van de uiting.

Stel dat u een patroon hebt dat `[]`zowel optionele syntaxis, als syntaxis van de entiteit bevat, `{}`gecombineerd op een manier om gegevens onjuist te extraheren.

Denk aan het patroon '[zoek] e-mail over {subject} [van {person}]'.

In de volgende uitingen worden het **onderwerp** en de **persoonsentiteit** correct en onjuist geëxtraheerd:

|Utterance|Entiteit|Juiste extractie|
|--|--|:--:|
|e-mail over honden van Chris|subject=honden<br>persoon=Chris|✔|
|e-mail over de man uit La Mancha|subject=de man<br>persoon=La Mancha|X|

In de vorige tabel moet `the man from La Mancha` het onderwerp (een boektitel) zijn, `from`maar omdat het onderwerp het optionele woord bevat, wordt de titel onjuist voorspeld.

Als u deze uitzondering op `the man from la mancha` het patroon wilt oplossen, voegt u als expliciete lijstovereenkomst toe voor de entiteit {subject} met behulp van de [ontwerp-API voor expliciete lijst](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxis om optionele tekst in een sjabloonutterance te markeren
De optionele tekst in de utterance markeren `[]`met de syntaxis van de reguliere expressie vierkant beugel. De optionele tekst kan alleen vierkante haakjes tot twee haakjes plaatsen.

|Patroon met optionele tekst|Betekenis|
|--|--|
|`[find] email about {subject} [from {person}]`|`find`en `from {person}` zijn optioneel|
|'Kunt u mij helpen[?]|Het leesteken is optioneel|

Leestekens (`?`, `!` `.`, ) moeten worden genegeerd en u moet ze negeren met behulp van de syntaxis van de vierkante haakjes in patronen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over patronen:

* [Patronen toevoegen](luis-how-to-model-intent-pattern.md)
* [Hoe patroon.een entiteit toe te voegen](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Patronen Concepten](luis-concept-patterns.md)

Begrijp hoe [sentiment](luis-reference-prebuilt-sentiment.md) wordt geretourneerd in de .json-reactie.