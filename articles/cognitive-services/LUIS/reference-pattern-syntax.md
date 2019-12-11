---
title: Naslag informatie over patroon syntaxis-LUIS
titleSuffix: Azure Cognitive Services
description: Maak entiteiten voor het extra heren van belang rijke gegevens van gebruikers uitingen in apps van Language Understanding (LUIS). Geëxtraheerde gegevens worden gebruikt door de client toepassing.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: e1393b02948f2d86329263504d582fe78a474377
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974339"
---
# <a name="pattern-syntax"></a>Patroonsyntaxis

De syntaxis van het patroon is een sjabloon voor een utterance. De sjabloon moet bevatten woorden en entiteiten die u wilt vergelijken en woorden en leestekens die u wilt negeren. Het is **niet** een reguliere expressie.

> [!CAUTION]
> Patronen bestaan alleen uit door machines geleerde entiteits-Parents, geen subonderdelen.

Entiteiten in de patronen zijn omgeven door accolades, `{}`. Patronen kunnen bestaan uit entiteiten en entiteiten met functies. [Patroon. any](luis-concept-entity-types.md#patternany-entity) is een entiteit die alleen in patronen wordt gebruikt.

De syntaxis van het patroon ondersteunt de volgende syntaxis:

|Functie|Syntaxis|Niveau nesten|Voorbeeld|
|--|--|--|--|
|entiteit| {} accolades|2|Waar is formulier {entity-name}?|
|optioneel|[]-vier Kante haken<BR><BR>Er is een limiet van 3 op geneste niveaus van een combi natie van optioneel en groepering |2|Het vraag teken is optioneel [?]|
|shapes|()-haakjes|2|is (a \| b)|
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
|Ja|( [ ( test1 &#x7c; test2 ) ] &#x7c; test3 )|
|Nee|( [ ( [ test1 ] &#x7c; test2 ) ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limieten voor het nesten van groepen met de syntaxis

Een combi natie van **groepering** met **of-ING-** syntaxis heeft een limiet van 2 verticale balken.

|Toegestaan|Voorbeeld|
|--|--|
|Ja|( test1 &#x7c; test2 &#x7c; ( test3 &#x7c; test4 ) )|
|Nee|( test1 &#x7c; test2 &#x7c; test3 &#x7c; ( test4 &#x7c; test5 ) ) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntaxis voor een entiteit toevoegen aan een patroon-sjabloon
Als u wilt toevoegen een entiteit in de sjabloon voor het patroon, zoals de naam van de entiteit met behulp van accolades, rondom `Who does {Employee} manage?`.

|Patroon met entiteit|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntaxis voor het toevoegen van een entiteit en een rol aan een patroon-sjabloon
De entiteitsrol van een wordt aangeduid als `{entity:role}` met de naam van de entiteit gevolgd door een dubbele punt, gevolgd door de rolnaam. Als u wilt toevoegen een entiteit met een rol in de sjabloon voor het patroon, moet u de naam van de entiteit en de rolnaam met behulp van accolades, zoals `Book a ticket from {Location:Origin} to {Location:Destination}`.

|Patroon met behulp van entiteit|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntaxis voor het toevoegen van een pattern.any aan patroon sjabloon
De entiteit Pattern.any kunt u een entiteit van de verschillende lengten toevoegen aan het patroon. Als de sjabloon patroon wordt gevolgd, mag de pattern.any een willekeurige lengte.

Om toe te voegen een **Pattern.any** entiteit in de sjabloon patroon rondom de Pattern.any-entiteit met de accolades, zoals `How much does {Booktitle} cost and what format is it available in?`.

|Patroon met Pattern.any entiteit|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Titels in het patroon|
|--|
|Wat kost **stelen dit boek** kosten, en welke indeling is beschikbaar in?|
|Wat kost **vragen** kosten, en welke indeling is beschikbaar in?|
|Wat kost **de benieuwd Incident van de hond in de nacht-tijd** kosten, en welke indeling is beschikbaar in?|

De woorden van de boek titel zijn niet verwarrend voor LUIS, omdat LUIS weet waar de titel van het boek eindigt, op basis van het patroon. elke entiteit.

## <a name="explicit-lists"></a>Expliciete lijsten

Maak een [expliciete lijst](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) via de ontwerp-API om de uitzonde ring toe te staan wanneer:

* Uw patroon bevat een [patroon.](luis-concept-entity-types.md#patternany-entity)
* En met deze patroon syntaxis kan een onjuiste entiteits extractie worden uitgevoerd op basis van de utterance.

Stel bijvoorbeeld dat u hebt een patroon met beide optioneel syntaxis `[]`, en de syntaxis van de entiteit, `{}`, gecombineerde op een manier om gegevens te extraheren onjuist.

Houd rekening met het patroon [zoeken] e-mail over {subject} [van {persoon}].

In de volgende uitingen de **onderwerp** en **persoon** entiteit correct en niet correct worden opgehaald:

|Utterance|Entiteit|Juiste extractie|
|--|--|:--:|
|e-mail over honden van Chris|onderwerp = honden<br>persoon Chris =|✔|
|per e-mail over de man in de La Mancha|onderwerp = de man<br>persoon La Mancha =|X|

In de voor gaande tabel moet het onderwerp worden `the man from La Mancha` (een boek titel), maar omdat het onderwerp het optionele woord `from`bevat, is de titel onjuist voor speld.

U kunt met het oplossen van deze uitzondering op het patroon, toevoegen `the man from la mancha` als een lijst met expliciete-overeenkomst voor de {subject} entiteit met de [API ontwerpen voor expliciete lijst](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxis voor het markeren van optionele tekst in een sjabloon utterance
Optionele tekst in de utterance met behulp van de syntaxis van reguliere expressie vierkant haakje sluiten, markeert `[]`. De optionele tekst kunt vierkante haken maximaal twee vierkante haken nesten.

|Patroon met optionele tekst|Betekenis|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` en `from {person}` zijn optioneel|
|' Kan ik me helpen [?]|Het lees teken is optioneel|

Lees tekens (`?`, `!`, `.`) moeten worden genegeerd en u moet ze negeren met de syntaxis van de vier Kante haken in patronen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over patronen:

* [Patronen toevoegen](luis-how-to-model-intent-pattern.md)
* [Een patroon toevoegen. elke entiteit](luis-how-to-add-entities.md##add-a-patternany-entity)
* [Patronen concepten](luis-concept-patterns.md)

Begrijp hoe [sentiment](luis-reference-prebuilt-sentiment.md) wordt geretourneerd in het. json-antwoord.