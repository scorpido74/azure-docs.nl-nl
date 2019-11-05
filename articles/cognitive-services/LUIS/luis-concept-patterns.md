---
title: Help voor voor spelling van patronen-LUIS
titleSuffix: Azure Cognitive Services
description: Met een patroon kunt u een grotere nauwkeurigheid verkrijgen voor een intentie zonder te voorzien in veel meer uitingen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: diberry
ms.openlocfilehash: 4ca3a27a63f84eccb66b24d5046e2ae7d751387d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487556"
---
# <a name="patterns-improve-prediction-accuracy"></a>Nauw keurigheid van de voor spelling verbeteren
Patronen zijn ontworpen om de nauw keurigheid te verbeteren wanneer verschillende uitingen zeer vergelijkbaar zijn.  Met een patroon kunt u een grotere nauwkeurigheid verkrijgen voor een intentie zonder te voorzien in veel meer uitingen. 

## <a name="patterns-solve-low-intent-confidence"></a>Patronen voor minder betrouw baarheid oplossen
Overweeg een Human resources-app die rapporteert over het organigram in relatie tot een werk nemer. Op basis van de naam en relatie van een werk nemer LUIS worden de betrokken werk nemers geretourneerd. Overweeg een werk nemer, Tom, met de naam van een manager en een team van ondergeschikten met de naam Michael, Rebecca en Carl.

![Afbeelding van organigram](./media/luis-concept-patterns/org-chart.png)

|Utterances|Intentie voor speld|Intente Score|
|--|--|--|
|Wie is de ondergeschikte van Tom?|GetOrgChart|.30|
|Wie is de ondergeschikte van Tom?|GetOrgChart|.30|

Als een app tussen 10 en 20 uitingen met verschillende lengten van zinnen, een andere woord volgorde en zelfs andere woorden (synoniemen, ' beheer ', ' rapport ') heeft, kan LUIS een lage betrouwbaarheids Score retour neren. Maak een patroon om LUIS inzicht te krijgen in het belang van de woord volgorde. 

Met patronen worden de volgende situaties opgelost: 

* De intentie Score is laag
* De juiste intentie is niet de hoogste score, maar te dicht bij de bovenste Score. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Patronen zijn geen garantie van intentie
Patronen gebruiken een combi natie van voorspellings technologieën. Het instellen van een intentie voor een sjabloon utterance in een patroon is geen garantie voor de intentie voorspelling, maar is een sterk signaal. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>De door de machine gedetecteerde entiteits detectie worden niet door patronen verbeterd

Een patroon is hoofd zakelijk bedoeld om de voor spelling van intenties en rollen te helpen. Het patroon. elke entiteit wordt gebruikt om vrije-vorm entiteiten te extra heren. Hoewel patronen gebruikmaken van entiteiten, kan een patroon niet helpen bij het detecteren van een door de machine geleerde entiteit.  

Het is niet raadzaam om verbeterde entiteits voorspellingen te zien als u meerdere uitingen samenvouwt tot één patroon. Voor eenvoudige entiteiten die moeten worden gestart, moet u uitingen toevoegen of de lijst entiteiten gebruiken. anders wordt uw patroon niet geactiveerd.

## <a name="patterns-use-entity-roles"></a>Patronen gebruiken entiteits rollen
Als twee of meer entiteiten in een patroon contextuele gerelateerde zijn, gebruiken patronen entiteits [rollen](luis-concept-roles.md) om contextuele informatie over entiteiten te extra heren.  

## <a name="prediction-scores-with-and-without-patterns"></a>Voorspellings scores met en zonder patronen
Gezien voldoende voor beeld uitingen, kan LUIS de Voorspellings betrouwbaarheid zonder patronen verg Roten. Patronen verhogen de betrouwbaarheids Score zonder zoveel uitingen te bieden.  

## <a name="pattern-matching"></a>Patroon vergelijking
Er wordt een patroon vergeleken op basis van het detecteren van de entiteiten in het patroon, waarna de rest van de woorden en de woord volgorde van het patroon worden gevalideerd. Entiteiten zijn vereist in het patroon voor een patroon dat overeenkomt met. Het patroon wordt toegepast op token niveau, niet op het teken niveau. 

## <a name="pattern-syntax"></a>Syntaxis van patroon
De syntaxis van het patroon is een sjabloon voor een utterance. De sjabloon moet woorden en entiteiten bevatten die u wilt laten overeenkomen, evenals woorden en interpunctie die u wilt negeren. Het is **geen** reguliere expressie. 

Entiteiten in patronen worden omgeven door accolades, `{}`. Patronen kunnen entiteiten en entiteiten met rollen bevatten. [Patroon. any](luis-concept-entity-types.md#patternany-entity) is een entiteit die alleen in patronen wordt gebruikt. 

De syntaxis van het patroon ondersteunt de volgende syntaxis:

|Functie|Syntaxis|Niveau nesten|Voorbeeld|
|--|--|--|--|
|Vennootschap| {} accolades|2|Waar is formulier {entity-name}?|
|Beschrijving|[]-vier Kante haken<BR><BR>Er is een limiet van 3 op geneste niveaus van een combi natie van optioneel en groepering |2|Het vraag teken is optioneel [?]|
|Shapes|()-haakjes|2|is (a \| b)|
|of| \|-verticale streep (pipe)<br><br>Er is een limiet van 2 op de verticale balken (of) in één groep |-|Waar is formulier ({Form-name-Short} &#x7c; {Form-name-Long} &#x7c; {Form-Number})| 
|begin en/of einde van utterance|^-caret|-|^ utterance starten<br>de utterance is voltooid.<br>^ strikte letterlijke overeenkomst van de volledige utterance met {Number} entiteit ^|

## <a name="nesting-syntax-in-patterns"></a>Syntaxis nesten in patronen

De **optionele** syntaxis met vier Kante haken kan twee niveaus genest zijn. Bijvoorbeeld: `[[this]is] a new form`. In dit voor beeld worden de volgende uitingen toegestaan: 

|Genest optioneel utterance-voor beeld|Uitleg|
|--|--|
|Dit is een nieuw formulier|komt overeen met alle woorden in het patroon|
|is een nieuw formulier|komt overeen met buitenste optionele woord en niet-optionele woorden in een patroon|
|een nieuw formulier|komt overeen met alleen de vereiste woorden|

De **groeperings** syntaxis met haakjes kan twee niveaus genest zijn. Bijvoorbeeld: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Met deze functie kunnen de drie entiteiten overeenkomen. 

Als Entity1 een locatie is met functies zoals origin (Seattle) en doel (Cairo) en entiteit 2 is een bekend gebouw naam van een lijst entiteit (RedWest-C), zou de volgende uitingen worden toegewezen aan dit patroon:

|Voor beeld van geneste groeperings utterance|Uitleg|
|--|--|
|RedWest-C|komt overeen met buitenste groeperings entiteit|
|Seattle|komt overeen met een van de binnenste groeperings entiteiten|
|Cairo|komt overeen met een van de binnenste groeperings entiteiten|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Limieten voor groepen met optionele syntaxis nesten

Een combi natie van **groepering** met **optionele** syntaxis heeft een limiet van 3 geneste niveaus.

|Toegestaan|Voorbeeld|
|--|--|
|Ja|([(test1 &#x7c; Test2)] &#x7c; Test3)|
|Nee|([([test1] &#x7c; Test2)] &#x7c; Test3)|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limieten voor het nesten van groepen met de syntaxis

Een combi natie van **groepering** met **of-ING-** syntaxis heeft een limiet van 2 verticale balken.

|Toegestaan|Voorbeeld|
|--|--|
|Ja|(test1 &#x7c; Test2 &#x7c; (Test3 &#x7c; test4))|
|Nee|(test1 &#x7c; Test2 &#x7c; Test3 &#x7c; (test4 &#x7c; test5)) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntaxis om een entiteit toe te voegen aan een patroon sjabloon
Als u een entiteit wilt toevoegen aan de patroon sjabloon, plaatst u de naam van de entiteit tussen accolades, zoals `Who does {Employee} manage?`. 

|Patroon met entiteit|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntaxis voor het toevoegen van een entiteit en rol aan een patroon sjabloon
De rol van een entiteit wordt aangeduid als `{entity:role}` met de naam van de entiteit, gevolgd door een dubbele punt, en vervolgens de naam van de rol. Als u een entiteit met een rol wilt toevoegen aan de patroon sjabloon, plaatst u de naam van de entiteit en de naam van de rol door accolades, zoals `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Patroon met entiteits rollen|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntaxis voor het toevoegen van een patroon. elke sjabloon voor een patroon
Het patroon. elke entiteit kunt u een entiteit van een lengte van variërend aan het patroon toevoegen. Zolang de patroon sjabloon wordt gevolgd, is het patroon. elk kan elk wille keurig lengte zijn. 

Als u een **patroon wilt toevoegen. een wille keurige** entiteit in de patroon sjabloon, rondom het patroon. elke entiteit met de accolades, zoals `How much does {Booktitle} cost and what format is it available in?`.  

|Patroon met patroon. elke entiteit|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Boek titels in het patroon|
|--|
|Wat zijn de kosten van **Dit boek** , maar wat is er beschikbaar in?|
|Wat zijn de kosten voor de **vraag** en welke indeling is er beschikbaar in?|
|Wat gebeurt er met **het nieuws gierige incidenten van de hond in de nacht** kosten en in welke indeling deze beschikbaar is?| 

De woorden van de boek titel zijn niet verwarrend voor LUIS, omdat LUIS weet waar de titel van het boek eindigt, op basis van het patroon. elke entiteit.

## <a name="explicit-lists"></a>Expliciete lijsten

Maak een [expliciete lijst](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) via de ontwerp-API om de uitzonde ring toe te staan wanneer:

* Uw patroon bevat een [patroon.](luis-concept-entity-types.md#patternany-entity)
* En met deze patroon syntaxis kan een onjuiste entiteits extractie worden uitgevoerd op basis van de utterance. 

Stel dat u een patroon hebt met zowel een optionele syntaxis, `[]`en de syntaxis van de entiteit, `{}`, gecombineerd op een manier om gegevens onjuist op te halen.

Overweeg het patroon [Find] e-mail over {subject} [van {person}].

In de volgende uitingen worden het **onderwerp** en de **persoons** entiteit op de juiste wijze en onjuist opgehaald:

|Utterance|Entiteit|Juiste extractie|
|--|--|:--:|
|e-mail over honden van Chris|onderwerp = honden<br>persoon = Chris|✔|
|e-mail over de man van La Mancha|onderwerp = de man<br>persoon = La Mancha|X|

In de voor gaande tabel moet het onderwerp worden `the man from La Mancha` (een boek titel), maar omdat het onderwerp het optionele woord `from`bevat, is de titel onjuist voor speld. 

Als u deze uitzonde ring op het patroon wilt herstellen, voegt u `the man from la mancha` toe als expliciet overeenkomende lijst voor de entiteit {subject} met behulp van de [API voor het maken van een expliciete lijst](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxis voor het markeren van optionele tekst in een sjabloon utterance
Markeer optionele tekst in de utterance met de reguliere expressie vier Kante haakjes syntaxis `[]`. Met de optionele tekst kunnen vier Kante haken Maxi maal twee haken worden genest.

|Patroon met optionele tekst|Betekenis|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` en `from {person}` zijn optioneel|
|' Kan ik me helpen [?]|Het lees teken is optioneel|

Lees tekens (`?`, `!`, `.`) moeten worden genegeerd en u moet ze negeren met de syntaxis van de vier Kante haken in patronen. 

## <a name="pattern-only-apps"></a>Alleen patroon-apps
U kunt een app bouwen met intenties die geen voor beeld uitingen hebben, zolang er voor elke intentie een patroon is. Het patroon mag voor een alleen-patroon app geen door machines geleerde entiteiten bevatten, omdat hiervoor bijvoorbeeld uitingen nodig zijn. 

## <a name="best-practices"></a>Aanbevolen procedures
Lees de [Aanbevolen procedures](luis-concept-best-practices.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het implementeren van patronen in deze zelf studie](luis-tutorial-pattern.md)
