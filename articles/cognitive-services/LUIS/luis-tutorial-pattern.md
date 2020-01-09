---
title: 'Zelf studie: patronen-LUIS'
titleSuffix: Azure Cognitive Services
description: Gebruik patronen om de intentie en de voor spelling van de entiteit te verg Roten met minder voor beeld-uitingen in deze zelf studie. Het patroon wordt geleverd als een sjabloon utterance-voor beeld, inclusief de syntaxis voor het identificeren van entiteiten en het negeren van tekst.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: d52b2485436f0a9075dcc3f505806e46094340a3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75381695"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>Zelf studie: utterance-indelingen van algemene patroon Sjablonen toevoegen om voor spellingen te verbeteren

In deze zelf studie gebruikt u patronen om de intentie en de voor spellingen van de entiteit te verg Roten, zodat u minder voor beeld uitingen kunt opgeven. Het patroon is een sjabloon utterance die is toegewezen aan een intentie, met syntaxis voor het identificeren van entiteiten en het negeren van tekst.

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Een patroon maken
> * Verbeteringen van patroonvoorspellingen verifiëren
> * Tekst markeren als te negeren en nesten binnen het patroon
> * Testdeelvenster gebruiken om te controleren of het patroon is geslaagd

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>Uitingen in intentie en patroon

Er zijn twee soorten uitingen opgeslagen in de LUIS-app:

* Voor beeld van uitingen in het doel
* Sjabloon uitingen in het patroon

Door sjabloon uitingen toe te voegen als een patroon, kunt u minder voor beeld uitingen voor een intentie bieden.

Een patroon wordt toegepast als een combi natie van overeenkomende expressies en machine learning.  De sjabloon utterance, samen met het uitingen-voor beeld, geven LUIS een beter inzicht in wat uitingen het doel heeft.

## <a name="import-example-app-and-clone-to-new-version"></a>Voor beeld-App importeren en klonen naar nieuwe versie

Voer de volgende stappen uit:

1.  Down load en sla het [JSON-bestand](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json?raw=true)van de app op.

1. Importeer de JSON in een nieuwe app in de [Preview-Luis Portal](https://preview.luis.ai).

1. Ga naar het gedeelte **Beheren**, open het tabblad **Versies**, kloon de versie en noem deze `patterns`. Klonen is een uitstekende manier om te experimenteren met verschillende functies van LUIS zonder dat de oorspronkelijke versie wordt gewijzigd. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL.

## <a name="create-new-intents-and-their-utterances"></a>Nieuwe intenties en de bijbehorende utterances maken

1. Selecteer **samen stellen** op de navigatie balk.

1. Selecteer op de pagina **intenties** **+ maken** om een nieuwe intentie te maken.

1. Voer in het pop-updialoogvenster `OrgChart-Manager` in en selecteer vervolgens **Done**.

    ![Pop-upvenster om een nieuw bericht te maken](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. Voeg voorbeelden van utterances toe aan de intentie. Deze uitingen zijn niet _precies_ hetzelfde, maar hebben wel een patroon dat kan worden geëxtraheerd.

    |Voorbeelden van utterances|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

    Maakt u zich geen zorgen als in de utterances van de intent de keyPhrase-entiteit is gemarkeerd in plaats van de werknemer-entiteit. Beide zijn in het deelvenster Test en bij het eindpunt juist voorspeld.

1. Selecteer **Intents** in de linkernavigatiebalk.

1. Selecteer **+ maken** om een nieuwe intentie te maken. Voer in het pop-updialoogvenster `OrgChart-Reports` in en selecteer vervolgens **Done**.

1. Voeg voorbeelden van utterances toe aan de intentie.

    |Voorbeelden van utterances|
    |--|
    |`Who are John W. Smith's subordinates?`|
    |`Who reports to John W. Smith?`|
    |`Who does John W. Smith manage?`|
    |`Who are Jill Jones direct reports?`|
    |`Who does Jill Jones supervise?`|

### <a name="caution-about-example-utterance-quantity"></a>Waarschuwing over hoeveelheid voorbeeld-utterances

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

### <a name="train-the-app-before-testing-or-publishing"></a>De app trainen voordat u gaat testen of publiceren

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

### <a name="publish-the-app-to-query-from-the-endpoint"></a>De app publiceren om query's uit te voeren vanaf het eindpunt

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

### <a name="get-intent-and-entities-from-endpoint"></a>Intenties en entiteiten ophalen van eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ga naar het einde van de URL in het adres en voer `Who is the boss of Jill Jones?` in. De laatste query string-para meter is de utterance-`query`.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.313054234
                },
                "OrgChart-Reports": {
                    "score": 0.2462688
                },
                "EmployeeFeedback": {
                    "score": 0.0488328524
                },
                "GetJobInformation": {
                    "score": 0.0156933
                },
                "MoveEmployee": {
                    "score": 0.011265873
                },
                "Utilities.StartOver": {
                    "score": 0.003065792
                },
                "Utilities.Stop": {
                    "score": 0.00300148362
                },
                "Utilities.Cancel": {
                    "score": 0.00271081156
                },
                "None": {
                    "score": 0.00212835032
                },
                "ApplyForJob": {
                    "score": 0.0020669254
                },
                "Utilities.Confirm": {
                    "score": 0.00200891262
                },
                "FindForm": {
                    "score": 0.00194145238
                },
                "Utilities.Help": {
                    "score": 0.00182301877
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "Employee": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "Employee": [
                        {
                            "type": "Employee",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

Is deze query gelukt? Voor deze trainingscyclus is het gelukt. De scores van de twee belangrijkste doel einden zijn dicht, maar de hoogste intentie is niet aanzienlijk hoog (meer dan 60%) en niet veel genoeg boven de Score van de volgende intentie.

Omdat LUIS-training niet altijd precies hetzelfde is en er daarom kleine variaties kunnen ontstaan, kunnen deze twee scores bij de volgende trainingscyclus zijn omgekeerd. Het resultaat is dat de verkeerde intent kan worden geretourneerd.

Gebruik patronen om de score van de juiste intent een aanzienlijk hoger percentage te geven en ervoor te zorgen dat de score verder weg ligt van de eerstvolgende hogere score.

Laat dit tweede browservenster geopend. U hebt het verderop in deze zelfstudie namelijk weer nodig.

## <a name="template-utterances"></a>Sjabloon-utterances
Vanwege de aard van het Human Resources-domein zijn er een paar algemene manieren om te vragen naar werknemersrelaties in organisaties. Bijvoorbeeld:

|Utterances|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Deze utterances liggen te dicht bij elkaar om de contextuele uniekheid van elk ervan te bepalen zonder veel utterance-voorbeelden te moeten verstrekken. Door een patroon voor een intent toe te voegen, leert LUIS algemene utterance-patronen voor een intent te herkennen zonder veel utterance-voorbeelden te moeten verstrekken.

Dit zijn voorbeelden van sjabloon-utterances voor deze intent:

|Voorbeelden van sjabloon-utterances|betekenis van de syntaxis|
|--|--|
|`Who does {Employee} report to[?]`|verwisselbaar `{Employee}`<br>`[?]` negeren|
|`Who reports to {Employee}[?]`|verwisselbaar `{Employee}`<br>`[?]` negeren|

De syntaxis `{Employee}` markeert de locatie van de entiteit in de sjabloon-utterance en geeft aan om welke entiteit het gaat. De optionele syntaxis, `[?]`, markeert woorden of lees tekens die optioneel zijn. LUIS kijkt naar de utterance, waarbij de optionele tekst tussen de haakjes wordt genegeerd.

Hoewel de syntaxis eruitziet als een reguliere expressie, is het geen reguliere expressie. Alleen de accolades, `{}`, en de vierkant haakjes, `[]`, worden als syntaxis ondersteund. Ze kunnen tot maximaal twee niveaus worden genest.

Om ervoor te zorgen dat een patroon overeenkomt met een utterance, moeten eerst de entiteiten binnen de utterance worden vergeleken met de entiteiten in de sjabloon-utterance. Dit betekent dat de entiteiten voldoende voor beelden in voor beeld uitingen hebben met een hoge mate van voor spelling voordat patronen met entiteiten slagen. De sjabloon helpt echter niet bij het voorspellen van entiteiten, alleen van intenties.

**Hoewel u voor patronen minder voorbeeld-utterances hoeft op te geven, komt het patroon niet overeen als de entiteiten niet worden gedetecteerd.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>De patronen voor het organigram toevoegen-Manager intentie

1. Selecteer **Build** in het bovenste menu.

1. Selecteer in het linkernavigatievenster, onder **Improve app performance**, de optie **Patterns**.

1. Selecteer de intent **OrgChart-Manager** en voer de volgende sjabloon-utterances in:

    |Sjabloon-utterances|
    |:--|
    |`Who is {Employee} the subordinate of[?]`|
    |`Who does {Employee} report to[?]`|
    |`Who is {Employee}['s] manager[?]`|
    |`Who does {Employee} directly report to[?]`|
    |`Who is {Employee}['s] supervisor[?]`|
    |`Who is the boss of {Employee}[?]`|

1. Terwijl u nog steeds op de pagina patronen klikt, selecteert u het **organigram-rapporten** opzet en voert u de volgende sjabloon uitingen in:

    |Sjabloon-utterances|
    |:--|
    |`Who are {Employee}['s] subordinates[?]`|
    |`Who reports to {Employee}[?]`|
    |`Who does {Employee} manage[?]`|
    |`Who are {Employee} direct reports[?]`|
    |`Who does {Employee} supervise[?]`|
    |`Who does {Employee} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Query uitvoeren op eindpunt wanneer patronen worden gebruikt

Nu de patronen worden toegevoegd aan de app, Train, publiceert u de app op het moment van de voor spelling runtime.

1. **Trein**selecteren. Nadat de training is voltooid, selecteert u **publiceren** en selecteert u de **productie** sleuf en selecteert u vervolgens **gereed**.

1. Nadat het publiceren is voltooid, gaat u terug naar het tabblad eind punt-URL op de browser tabbladen.

1. Ga naar het einde van de URL in het adres en voer `Who is the boss of Jill Jones?` in als utterance. De laatste query string-para meter is de `query`.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999997854
                },
                "OrgChart-Reports": {
                    "score": 6.13748343E-05
                },
                "EmployeeFeedback": {
                    "score": 8.052567E-06
                },
                "GetJobInformation": {
                    "score": 1.18197136E-06
                },
                "MoveEmployee": {
                    "score": 7.65549657E-07
                },
                "None": {
                    "score": 3.975E-09
                },
                "Utilities.StartOver": {
                    "score": 1.53E-09
                },
                "Utilities.Confirm": {
                    "score": 1.38181822E-09
                },
                "Utilities.Help": {
                    "score": 1.38181822E-09
                },
                "Utilities.Stop": {
                    "score": 1.38181822E-09
                },
                "Utilities.Cancel": {
                    "score": 1.25833333E-09
                },
                "FindForm": {
                    "score": 1.15384613E-09
                },
                "ApplyForJob": {
                    "score": 5.26923061E-10
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "Employee": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "Employee": [
                        {
                            "type": "Employee",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

De intentie voorspelling is nu aanzienlijk meer vertrouwen en de Score van het volgende hoogste niveau is aanzienlijk lager. Deze twee intenties worden niet gespiegeld tijdens de training.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Werken met optionele tekst en voorafgemaakte entiteiten

De vorige patroonsjabloon-utterances in deze zelfstudie hadden enkele voorbeelden van optionele tekst `'s`, zoals het gebruik van het vraagteken, `?`. Stel dat u huidige en toekomstige datums wilt toestaan in de utterance-tekst.

Voorbeelden van utterances zijn:

|Intentie|Voorbeeld-utterances met optionele tekst en voorafgemaakte entiteiten|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

In elk van deze voorbeelden wordt gebruikgemaakt van een werkwoordsvorm, `was`, `is`, `will be`, evenals een datum, `March 3`, `now`, en `in a month`, die LUIS correct moet voorspellen. U ziet dat de laatste twee voor beelden in de tabel bijna dezelfde tekst gebruiken, met uitzonde ring van `in` en `on`.

Voorbeeld sjabloon uitingen die deze optionele informatie toestaan:

|Intentie|Voorbeeld-utterances met optionele tekst en voorafgemaakte entiteiten|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


Dankzij het gebruik van de optionele syntaxis met vierkante haken, `[]`, kan deze optionele tekst eenvoudig worden toegevoegd aan de sjabloon-utterance en worden genest tot het tweede niveau met `[[]]`, en kunnen entiteiten of tekst worden opgenomen.


**Vraag: Waarom zijn alle `w` letters, de eerste letter van elke sjabloon utterance, kleine letters? Is het niet zo dat ze ook in de bovenste of kleine letters worden gesteld?** De utterance die door de clienttoepassing naar het query-eindpunt wordt verzonden, wordt omgezet in kleine letters. De sjabloon-utterance kan uit hoofdletters of kleine letters bestaan en dat geldt ook voor de eindpunt-utterance. De vergelijking wordt altijd uitgevoerd na de conversie naar kleine letters.

**Vraag: Waarom maakt het vooraf gemaakte getal geen deel uit van de sjabloon-utterance als 3 maart wordt voorspeld als het getal `3` en als de datum `March 3`?** De sjabloon-utterance maakt contextueel gebruik van een datum, letterlijk zoals in `March 3`, of figuurlijk zoals `in a month`. Een datum kan een waarde bevatten, maar een getal hoeft niet per se als een datum te worden beschouwd. Gebruik altijd de entiteit die het beste het type aangeeft dat u wilt laten retourneren in de JSON-voorspellingsresultaten.

**Vraag: Hoe zit het met slecht geformuleerde utterances zoals `Who will {Employee}['s] manager be on March 3?`.** Grammaticaal verschillende werkwoordsvormen, zoals deze waarbij `will` en `be` van elkaar zijn gescheiden, moeten als een nieuwe sjabloon-utterance worden ingesteld. De bestaande sjabloon-utterance zal een dergelijke werkwoordsvorm niet herkennen. Het doel van de utterance is weliswaar niet veranderd, maar dat geldt wel voor de woordplaatsing in de utterance. Deze wijziging heeft gevolgen voor de voorspelling in LUIS. U kunt de [groep en of](#use-the-or-operator-and-groups) het werk woord tien tallen om deze uitingen te combi neren.

**Houd er rekening mee dat als eerste entiteiten worden gevonden, waarna het patroon wordt vergeleken.**

### <a name="edit-the-existing-pattern-template-utterance"></a>De bestaande patroonsjabloon-utterance bewerken

1. Selecteer in de LUIS-Portal de optie **Build** in het bovenste menu en selecteer vervolgens **patronen** in het menu links.

1. Zoek de bestaande sjabloon utterance, `Who is {Employee}['s] manager[?]`en selecteer het beletsel teken (***...***) aan de rechter kant en selecteer vervolgens **bewerken** in het pop-upmenu.

1. Wijzig de sjabloon-utterance in: `who is {Employee}['s] manager [[on]{datetimeV2}?]`

### <a name="add-new-pattern-template-utterances"></a>Nieuwe patroonsjabloon-utterances toevoegen

1. Terwijl u zich nog steeds in de sectie **Patterns** van **Build** bevindt, voegt u verschillende nieuwe patroonsjabloon-utterances toe. Selecteer **OrgChart-Manager** in de vervolgkeuzelijst Intent en voer elk van de volgende sjabloon-utterances in:

    |Intentie|Voorbeeld-utterances met optionele tekst en voorafgemaakte entiteiten|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Selecteer **trainen** in de navigatie balk om de app te trainen.

3. Nadat de training is voltooid, selecteert u **testen** boven aan het deel venster om het deel venster testen te openen.

4. Voer verschillende test-utterances in om te controleren of het patroon overeenkomt en de intentiescore hoog genoeg is.

    Nadat u de eerste utterance hebt ingevoerd, selecteert u **Inspect** onder het resultaat zodat u alle voorspellingsresultaten kunt zien. Elk utterance moet het **organigram-Manager** intentie hebben en de waarden voor de entiteiten van werk nemers en datetimeV2 ophalen.

    |Utterance|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

Voor al deze utterances zijn de daarin opgenomen entiteiten gevonden en daarom komen ze overeen met hetzelfde patroon en hebben ze een hoge voorspellingsscore. U hebt enkele patronen toegevoegd die overeenkomen met een groot aantal verschillende uitingen. U hoeft geen voor beeld-uitingen toe te voegen aan het doel om de sjabloon utterance te laten werken in het patroon.

Dit is het gebruik van patronen die zijn opgenomen:
* hogere scores voor de voor spelling
* met hetzelfde voor beeld uitingen in het doel
* met slechts een paar goed opgemaakte sjabloon uitingen in het patroon

### <a name="use-the-or-operator-and-groups"></a>De operator OR en groepen gebruiken

Enkele van de vorige sjabloon uitingens worden bijna gesloten. Gebruik de **groeps** `()` en **of** `|` syntaxis om de sjabloon uitingen te verminderen.

De volgende twee patronen kunnen in één patroon worden gecombineerd met behulp van de groeps `()` en of de `|` syntaxis.

|Intentie|Voorbeeld-utterances met optionele tekst en voorafgemaakte entiteiten|
|--|--|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

Het nieuwe sjabloon utterance is:

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`.

Dit maakt gebruik van een **groep** rond de vereiste term tien tallen en de optionele `in` en `on` met een **of** een sluis ertussen.

1. Selecteer op de pagina **patronen** het filter **organigram-Manager** . Verfijn de lijst door te zoeken naar `manager`.

1. Behoud één versie van de sjabloon utterance (om in de volgende stap te bewerken) en verwijder de andere variaties.

1. Wijzig de sjabloon utterance in:

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`

2. Selecteer **trainen** in de navigatie balk om de app te trainen.

3. Nadat de training is voltooid, selecteert u **testen** boven aan het deel venster om het deel venster testen te openen.

    Gebruik het test venster om versies van de utterance te testen:

    |Uitingen invoeren in het test venster|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

Door gebruik te maken van een meer patroon syntaxis kunt u het aantal uitingen dat u in uw app moet onderhouden, beperken, terwijl u nog steeds een hoge Voorspellings score hebt.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>De utterance begin-en eind ankerpunten gebruiken

De syntaxis van het patroon biedt het begin-en eind utterance-anker syntaxis van een caret-`^`. De begin-en eind utterance-ankers kunnen samen worden gebruikt om een zeer specifieke en mogelijk letterlijke utterance te bereiken of om afzonderlijk te worden gebruikt voor doel intentie.

## <a name="using-patternany-entity"></a>Pattern.any-entiteit gebruiken

Met de entiteit pattern.any kunt u vrije gegevens vinden waarbij het lastig is om uit de tekst van de entiteit het einde van de entiteit te bepalen op basis van de rest van de utterance.

Deze Human Resources-app helpt medewerkers bij het zoeken van bedrijfsformulieren.

|Utterance|
|--|
|Waar is **HRF-123456**?|
|Wie is de auteur van **HRF 123234**?|
|Is **HRF-456098** gepubliceerd in het Frans?|

Elk formulier heeft echter zowel een opgemaakte naam, gebruikt in de voorgaande tabel, als een beschrijvende naam, zoals `Request relocation from employee new to the company 2018 version 5`.

Utterances met de beschrijvende naam van het formulier zien er als volgt uit:

|Utterance|
|--|
|Waar is de **Aanvraag voor relocatie van nieuwe medewerker in het bedrijf 2018 versie 5**?|
|Wie heeft **Aanvraag voor relocatie van nieuwe medewerker in het bedrijf 2018 versie 5** geschreven?|
|Is **Aanvraag voor relocatie van nieuwe medewerker in het bedrijf 2018 versie 5** gepubliceerd in het Frans?|

De lengte varieert en er zijn woorden die verwarrend kunnen zijn voor LUIS om te bepalen waar de entiteit eindigt. Met behulp van een entiteit Pattern.any in een patroon, kunt u het begin en einde van de naam van het formulier opgeven, zodat de naam van het formulier correct worden geëxtraheerd door LUIS.

|Voorbeeld van sjabloon-utterance|
|--|
|Waar is {FormName}[?]|
|Wie heeft {FormName} geschreven[?]|
|Is {FormName} gepubliceerd in het Frans[?]|

### <a name="add-example-utterances-with-patternany"></a>Voeg bijvoorbeeld uitingen toe met een patroon.

1. Selecteer **Build** in de bovenste navigatiebalk en selecteer vervolgens **Intenties** in de linkernavigatiebalk.

1. Selecteer **FindForm** in de lijst met intenties.

1. Voeg enkele voorbeelden van utterances toe:

    |Voorbeeld van een utterance|Formulier naam|
    |--|--|
    |Waar is het formulier **Wat te doen wanneer er brand uitbreekt in het lab** en wie moet het ondertekenen wanneer ik het heb gelezen?|Wat te doen wanneer een brand wordt afgebroken in het lab
    |Waar is **Aanvraag voor relocatie van nieuwe medewerker in het bedrijf** op de server?|Nieuwe locatie aanvragen van een werk nemer die nieuw is voor het bedrijf|
    |Wie heeft "**Aanvragen voor gezondheid en welzijn op de hoofdcampus**" geschreven en wat is de meest recente versie?|Status-en Wellness-aanvragen op de hoofd vestiging|
    |Ik ben op zoek naar het formulier met de naam "**Aanvraag voor verplaatsen van kantoor met inbegrip van fysieke activa**“. |Office-aanvraag voor verplaatsen, inclusief fysieke activa|

    Zonder een entiteit Pattern.any kan het lastig zijn voor LUIS om te begrijpen waar de titel van het formulier eindigt vanwege de vele variaties van de namen van formulieren.

### <a name="create-a-patternany-entity"></a>Een Pattern.any-entiteit maken
De entiteit Pattern.any extraheert entiteiten met verschillende lengten. Het werkt alleen in een patroon omdat het patroon het begin en het einde van de entiteit markeert met de syntaxis.

1. Selecteer **Entiteiten** in de linkernavigatiebalk.

1. Selecteer **+ maken**, voer de naam in `FormName`en selecteer **patroon. elk** type. Selecteer **Maken**.

### <a name="add-a-pattern-that-uses-the-patternany"></a>Een patroon toevoegen dat gebruikmaakt van de Pattern.any

1. Selecteer **Patronen** in de linkernavigatiebalk.

1. Selecteer de intentie **FindForm**.

1. Voer de volgende sjabloon-utterances in die gebruikmaken van de nieuwe entiteit:

    |Sjabloon-utterances|
    |--|
    |`Where is the form ["]{FormName}["] and who needs to sign it after I read it[?]`|
    |`Where is ["]{FormName}["] on the server[?]`|
    |`Who authored ["]{FormName}["] and what is the most current version[?]`|
    |`I'm looking for the form named ["]{FormName}["][.]`|

1. Train de app.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Het nieuwe patroon voor extractie van vrije gegevens testen
1. Selecteer **Testen** in de bovenste balk om het testdeelvenster te openen.

1. Voer de volgende utterance in:

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. Selecteer **Inspecteren** onder het resultaat om de testresultaten voor de entiteit en intentie te zien.

    De entiteit `FormName` is als eerste gevonden en vervolgens het patroon waarmee de intentie wordt bepaald. Als u een testresultaat hebt waar de entiteiten niet zijn gedetecteerd en daarom het patroon niet is gevonden, moet u meer voorbeeld- utterances toevoegen aan de intentie (niet het patroon).

1. Sluit het testdeelvenster met de knop **Testen** in de bovenste navigatiebalk.

### <a name="using-an-explicit-list"></a>Een expliciete lijst gebruiken

Als u merkt dat het patroon, wanneer dit een Pattern.any bevat, entiteiten onjuist extraheert, gebruikt u een [expliciete lijst](reference-pattern-syntax.md#explicit-lists) om dit probleem te verhelpen.

## <a name="what-did-this-tutorial-accomplish"></a>Wat heeft deze zelf studie gedaan?

In deze zelf studie zijn patronen toegevoegd om LUIS te helpen voors pellen met een aanzienlijk hogere Score zonder dat u meer voorbeeld uitingen hoeft toe te voegen. Door entiteiten en te negeren tekst te markeren kon LUIS het patroon toepassen op meer verschillende utterances.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen


> [!div class="nextstepaction"]
> [Informatie over het gebruik van rollen met een patroon](luis-tutorial-pattern-roles.md)
