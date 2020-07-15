---
title: 'Zelfstudie: Patronen - LUIS'
description: In deze zelfstudie gebruikt u patronen voor het verhogen van intentie- en entiteitsvoorspelling terwijl er minder voorbeelduitingen nodig zijn. Het patroon wordt als een voorbeeld van een sjabloonuiting, met de syntaxis voor het identificeren van entiteiten en te negeren tekst.
ms.topic: tutorial
ms.date: 07/06/2020
ms.openlocfilehash: 3ca8bb15d19b0fa0dd6b33d35a380c0b1b07abe0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86039497"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>Zelfstudie: Indelingen voor sjabloonuitingen met algemene patronen toevoegen om voorspellingen te verbeteren

Gebruik in deze zelfstudie patronen voor het verhogen van intentie- en entiteitsvoorspelling, zodat u minder voorbeelduitingen hoeft op te geven. Het patroon is een sjabloonuiting, toegewezen aan een intentie, die syntaxis bevat voor het identificeren van entiteiten en te negeren tekst.

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Een patroon maken
> * Verbeteringen van patroonvoorspellingen verifiëren
> * Tekst markeren als te negeren en nesten binnen het patroon
> * Testdeelvenster gebruiken om te controleren of het patroon is geslaagd

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>Uitingen in intentie en patroon

Er zijn twee soorten uitingen opgeslagen in de LUIS-app:

* Voorbeelduitingen in de intentie
* Sjabloonuitingen in het patroon

Door sjabloonuitingen toe te voegen als een patroon, hoeft u minder voorbeelduitingen voor een intentie op te geven.

Een patroon wordt toegepast als een combinatie van tekstvergelijking en machine learning.  Naast de intentie-uitingen geeft de sjabloonuiting in het patroon LUIS een beter idee van welke uitingen het best bij de intentie passen.

## <a name="import-example-app-and-clone-to-new-version"></a>Voorbeeld-app importeren en klonen naar nieuwe versie

Voer de volgende stappen uit:

1.  Download het [JSON-bestand van de app](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true) en sla het op.

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Importeer de JSON in een nieuwe app in de [LUIS-portal](https://www.luis.ai). Selecteer op de pagina **Mijn apps** de optie **+ Nieuwe app voor conversatie** en selecteer **Importeren als JSON**. Selecteer het bestand dat u in de vorige stap hebt gedownload en noem de app `Patterns tutorial`.

## <a name="create-new-intents-and-their-utterances"></a>Nieuwe intenties en de bijbehorende utterances maken

De twee intenties vinden de manager of de directe ondergeschikten van de manager, op basis van de tekst van de uiting. Het probleem is dat de twee intenties iets anders _bedoelen_, maar dat de meeste woorden hetzelfde zijn. Alleen de woordvolgorde wijkt af. Om ervoor te zorgen dat de intentie correct wordt voorspeld, moet het een groot aantal voorbeelden hebben.

1. Selecteer **Build** op de navigatiebalk.

1. Selecteer op de pagina **Intenties** de optie **+ Maken** om een nieuwe intentie te maken.

1. Voer in het pop-updialoogvenster `OrgChart-Manager` in en selecteer vervolgens **Done**.

    ![Pop-upvenster om een nieuw bericht te maken](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. Voeg voorbeelden van utterances toe aan de intentie. Deze uitingen zijn niet _exact_ hetzelfde, maar hebben een patroon dat kan worden uitgepakt.

    |Voorbeelden van utterances|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

1. Selecteer **Intents** in de linkernavigatiebalk.

1. Selecteer **+ Maken** om een nieuwe intentie te maken. Voer in het pop-updialoogvenster `OrgChart-Reports` in en selecteer vervolgens **Done**.

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

1. Ga naar het einde van de URL in de adresbalk en vervang _YOUR_QUERY_HERE_ door: `Who is the boss of Jill Jones?`.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.326605469
                },
                "OrgChart-Reports": {
                    "score": 0.127583548
                },
                "EmployeeFeedback": {
                    "score": 0.0299124215
                },
                "MoveEmployee": {
                    "score": 0.01159851
                },
                "GetJobInformation": {
                    "score": 0.0104600191
                },
                "ApplyForJob": {
                    "score": 0.007508645
                },
                "Utilities.StartOver": {
                    "score": 0.00359402061
                },
                "Utilities.Stop": {
                    "score": 0.00336530479
                },
                "FindForm": {
                    "score": 0.002653719
                },
                "Utilities.Cancel": {
                    "score": 0.00263288687
                },
                "None": {
                    "score": 0.00238638581
                },
                "Utilities.Help": {
                    "score": 0.00226386427
                },
                "Utilities.Confirm": {
                    "score": 0.00211663754
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
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
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
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

De juiste beste intentie is voorspeld, `OrgChart-Manager`, maar de score is niet meer hoger dan 70% en niet ver genoeg boven de eerstvolgende hoogste intentie. Gebruik patronen om de score van de juiste intent een aanzienlijk hoger percentage te geven en ervoor te zorgen dat de score verder weg ligt van de eerstvolgende hogere score.

Laat dit tweede browservenster geopend. U hebt deze verderop in deze zelfstudie weer nodig.

## <a name="template-utterances"></a>Sjabloon-utterances
Vanwege de aard van het Human Resources-onderwerpdomein zijn er een paar algemene manieren om te vragen naar werknemersrelaties in organisaties. Bijvoorbeeld:

|Utterances|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Deze utterances liggen te dicht bij elkaar om de contextuele uniekheid van elk ervan te bepalen zonder _veel_ utterance-voorbeelden te moeten verstrekken. Door een patroon voor een intentie toe te voegen, leert LUIS algemene uitingspatronen voor een intentie te herkennen zonder dat veel voorbeelduitingen moeten worden verstrekt.

Dit zijn voorbeelden van sjabloon-utterances voor deze intent:

|Voorbeelden van sjabloon-utterances|betekenis van de syntaxis|
|--|--|
|`Who does {EmployeeListEntity} report to[?]`|uitwisselbaar `{EmployeeListEntity}`<br>`[?]` negeren|
|`Who reports to {EmployeeListEntity}[?]`|uitwisselbaar `{EmployeeListEntity}`<br>`[?]` negeren|

De syntaxis `{EmployeeListEntity}` markeert de locatie van de entiteit in de sjabloon-utterance en geeft aan om welke entiteit het gaat. De optionele syntaxis `[?]` markeert woorden of [leestekens](luis-reference-application-settings.md#punctuation-normalization) die optioneel zijn. LUIS kijkt naar de utterance, waarbij de optionele tekst tussen de haakjes wordt genegeerd.

Hoewel de syntaxis er uitziet als een reguliere expressie, is dit niet het geval. Alleen de accolades, `{}`, en de vierkant haakjes, `[]`, worden als syntaxis ondersteund. Ze kunnen tot maximaal twee niveaus worden genest.

Om een patroon te vergelijken met een uiting, worden _eerst_ de entiteiten binnen de uiting vergeleken met de entiteiten in de sjabloonuiting. Dit betekent dat de entiteiten voldoende voorbeelden in voorbeelduitingen moeten hebben met een hoge mate van voorspelling, voordat patronen met entiteiten slagen. De sjabloon helpt echter niet bij het voorspellen van entiteiten, alleen van intenties.

**Hoewel u voor patronen minder voorbeeld-utterances hoeft op te geven, komt het patroon niet overeen als de entiteiten niet worden gedetecteerd.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>De patronen voor de intentie OrgChart-Manager toevoegen

1. Selecteer **Build** in het bovenste menu.

1. Selecteer in het linkernavigatievenster, onder **Improve app performance**, de optie **Patterns**.

1. Selecteer de intent **OrgChart-Manager** en voer de volgende sjabloon-utterances in:

    |Sjabloon-utterances|
    |:--|
    |`Who is {EmployeeListEntity} the subordinate of[?]`|
    |`Who does {EmployeeListEntity} report to[?]`|
    |`Who is {EmployeeListEntity}['s] manager[?]`|
    |`Who does {EmployeeListEntity} directly report to[?]`|
    |`Who is {EmployeeListEntity}['s] supervisor[?]`|
    |`Who is the boss of {EmployeeListEntity}[?]`|

    Deze sjabloonuitingen bevatten de entiteit **EmployeeListEntity** met de notatie tussen accolades.

1. Terwijl de pagina Patronen nog is geopend, selecteert u de intentie **OrgChart-Reports** en voert u de volgende sjabloonuitingen in:

    |Sjabloon-utterances|
    |:--|
    |`Who are {EmployeeListEntity}['s] subordinates[?]`|
    |`Who reports to {EmployeeListEntity}[?]`|
    |`Who does {EmployeeListEntity} manage[?]`|
    |`Who are {EmployeeListEntity} direct reports[?]`|
    |`Who does {EmployeeListEntity} supervise[?]`|
    |`Who does {EmployeeListEntity} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Query uitvoeren op eindpunt wanneer patronen worden gebruikt

Nu de patronen aan de app zijn toegevoegd, kunt u de app trainen, publiceren en bevragen op het eindpunt van de voorspellingsruntime.

1. Selecteer **Trainen**. Wanneer de training is voltooid, selecteert u **Publiceren**, vervolgens de sleuf **Productie** en tot slot **Gereed**.

1. Wanneer de publicatie is voltooid, gaat u in de browser weer naar het tabblad van de eindpunt-URL.

1. Ga naar het einde van de URL in de adres balk en controleer of uw query nog steeds `Who is the boss of Jill Jones?` is en verzend vervolgens de URL voor een nieuwe voorspelling.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999999046
                },
                "OrgChart-Reports": {
                    "score": 3.237443E-05
                },
                "EmployeeFeedback": {
                    "score": 4.364242E-06
                },
                "GetJobInformation": {
                    "score": 1.616159E-06
                },
                "MoveEmployee": {
                    "score": 7.575752E-07
                },
                "ApplyForJob": {
                    "score": 5.234157E-07
                },
                "None": {
                    "score": 3.3E-09
                },
                "Utilities.StartOver": {
                    "score": 1.26E-09
                },
                "FindForm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Cancel": {
                    "score": 1.13636367E-09
                },
                "Utilities.Confirm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Help": {
                    "score": 1.13636367E-09
                },
                "Utilities.Stop": {
                    "score": 1.13636367E-09
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
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
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
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

De voorspelling van de intentie is nu aanzienlijk betrouwbaarder en de volgende hoogste score is zeer laag. Deze twee intenties zullen tijdens de training niet al te zeer veranderen.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Werken met optionele tekst en voorafgemaakte entiteiten

De vorige patroonsjabloon-utterances in deze zelfstudie hadden enkele voorbeelden van optionele tekst `'s`, zoals het gebruik van het vraagteken, `?`. Stel dat u huidige en toekomstige datums wilt toestaan in de tekst van de uiting.

Voorbeelden van utterances zijn:

|Intentie|Voorbeeld-utterances met optionele tekst en voorafgemaakte entiteiten|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

In elk van deze voorbeelden wordt gebruikgemaakt van een werkwoordsvorm, `was`, `is`, `will be`, evenals een datum, `March 3`, `now`, en `in a month`, die LUIS correct moet voorspellen. U ziet dat in de laatste twee voorbeelden in de tabel bijna dezelfde tekst wordt gebruikt, behalve `in` en `on`.

Voorbeeld van sjabloonuitingen die deze optionele informatie toestaan:

|Intentie|Voorbeeld-utterances met optionele tekst en voorafgemaakte entiteiten|
|:--|:--|
|OrgChart-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who is {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|


Dankzij het gebruik van de optionele syntaxis met vierkante haken, `[]`, kan deze optionele tekst eenvoudig worden toegevoegd aan de sjabloon-utterance en worden genest tot het tweede niveau met `[[]]`, en kunnen entiteiten of tekst worden opgenomen.


**Vraag: Waarom zijn alle letters `w`, als eerste letter in elke sjabloon-utterance, kleine letters? Moeten die niet naar keuze hoofdletters of kleine letters kunnen zijn?** De utterance die door de clienttoepassing naar het query-eindpunt wordt verzonden, wordt omgezet in kleine letters. De sjabloon-utterance kan uit hoofdletters of kleine letters bestaan en dat geldt ook voor de eindpunt-utterance. De vergelijking wordt altijd uitgevoerd na de conversie naar kleine letters.

**Vraag: Waarom maakt het vooraf gemaakte getal geen deel uit van de sjabloon-utterance als 3 maart wordt voorspeld als het getal `3` en als de datum `March 3`?** De sjabloon-utterance maakt contextueel gebruik van een datum, letterlijk zoals in `March 3`, of figuurlijk zoals `in a month`. Een datum kan een waarde bevatten, maar een getal hoeft niet per se als een datum te worden beschouwd. Gebruik altijd de entiteit die het beste het type aangeeft dat u wilt laten retourneren in de JSON-voorspellingsresultaten.

**Vraag: Hoe zit het met slecht geformuleerde utterances zoals `Who will {EmployeeListEntity}['s] manager be on March 3?`.** Grammaticaal verschillende werkwoordsvormen, zoals deze waarbij `will` en `be` van elkaar zijn gescheiden, moeten als een nieuwe sjabloon-utterance worden ingesteld. De bestaande sjabloon-utterance zal een dergelijke werkwoordsvorm niet herkennen. Het doel van de utterance is weliswaar niet veranderd, maar dat geldt wel voor de woordplaatsing in de utterance. Deze wijziging heeft gevolgen voor de voorspelling in LUIS. U kunt de werkwoordvervoegingen [groeperen en/of ](#use-the-or-operator-and-groups) om deze uitingen te combineren.

> [!CAUTION]
> **Houd er rekening mee dat als eerste entiteiten worden gevonden, waarna het patroon wordt vergeleken.**

### <a name="add-new-pattern-template-utterances"></a>Nieuwe patroonsjabloon-utterances toevoegen

1. Terwijl u zich nog steeds in de sectie **Patterns** van **Build** bevindt, voegt u verschillende nieuwe patroonsjabloon-utterances toe. Selecteer **OrgChart-Manager** in de vervolgkeuzelijst Intent en voer elk van de volgende sjabloon-utterances in:

    |Intentie|Voorbeeld-utterances met optionele tekst en voorafgemaakte entiteiten|
    |--|--|
    |OrgChart-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

2. Selecteer **Trainen** in de navigatiebalk om de app te trainen.

3. Wanneer de training is voltooid, selecteert u **Testen** bovenin het deelvenster om het testvenster te openen.

4. Voer verschillende test-utterances in om te controleren of het patroon overeenkomt en de intentiescore hoog genoeg is.

    Nadat u de eerste utterance hebt ingevoerd, selecteert u **Inspect** onder het resultaat zodat u alle voorspellingsresultaten kunt zien. Elke uiting moet de intentie **OrgChart-Manager** hebben en er moeten waarden voor de entiteiten `EmployeeListEntity` en `datetimeV2` worden uitgepakt.

    |Utterance|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

Voor al deze utterances zijn de daarin opgenomen entiteiten gevonden en daarom komen ze overeen met hetzelfde patroon en hebben ze een hoge voorspellingsscore. U hebt een aantal patronen toegevoegd die overeenkomen met een groot aantal verschillende uitingen. U hoefde geen voorbeelduitingen aan de intentie toe te voegen om de sjabloonuiting te laten werken in het patroon.

Dit gebruik van patronen leverde het volgende op:
* Hogere voorspellingsscores
* Met dezelfde voorbeelduitingen in de intentie
* Met slechts enkele goed geformuleerde sjabloonuitingen in het patroon

### <a name="use-the-or-operator-and-groups"></a>De operator OR en groepen gebruiken

Enkele van de vorige sjabloonuitingen lijken veel op elkaar. Gebruik de syntaxis **group** `()` en **OR** `|` om het aantal sjabloonuitingen te verminderen.

De volgende twee patronen kunnen in één patroon worden gecombineerd met behulp van de syntaxis group `()` en OR `|`.

|Intentie|Voorbeeld-utterances met optionele tekst en voorafgemaakte entiteiten|
|--|--|
|OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

De nieuwe sjabloonuiting is:

`who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`.

Hiervoor wordt een **group** rond de vereiste werkwoordvervoeging gebruikt en de optionele `in` en `on` met een **- of** -pipe ertussen.

> [!NOTE]
> Wanneer u het symbool _OR_, `|` (pipe) gebruikt, moet u erop letten dat u in de voorbeeldsjabloon een spatie voor en achter het pipe-symbool plaatst.

1. Selecteer op de pagina **Patronen** het filter **OrgChart-Manager**. Verfijn de lijst door te zoeken naar `manager`.

1. Behoud één versie van de sjabloonuiting (om in de volgende stap te bewerken) en verwijder de andere variaties.

1. Wijzig de sjabloonuiting in:

    `who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`

2. Selecteer **Trainen** in de navigatiebalk om de app te trainen.

3. Wanneer de training is voltooid, selecteert u **Testen** bovenin het deelvenster om het testvenster te openen.

    Gebruik het testvenster om versies van de uiting te testen:

    |Uitingen om in het testvenster in te voeren|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

Door meer patroonsyntaxis te gebruiken, vermindert u het aantal sjabloonuitingen dat u in uw app moet onderhouden, terwijl u nog steeds een hoge voorspellingsscore hebt.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>De begin- en eindankers van de uiting

De syntaxis van het patroon gebruikt een caret-teken, `^`, om het begin en eind van een uiting aan te geven. De begin- en eindankers van een uiting kunnen samen worden gebruikt om een zeer specifieke en mogelijk letterlijke uiting op te geven of afzonderlijk worden gebruikt om intenties op te geven.

## <a name="using-patternany-entity"></a>Pattern.any-entiteit gebruiken

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

### <a name="add-example-utterances-with-patternany"></a>Voorbeelduitingen toevoegen met Pattern.any

1. Selecteer **Build** in de bovenste navigatiebalk en selecteer vervolgens **Intenties** in de linkernavigatiebalk.

1. Selecteer **FindForm** in de lijst met intenties.

1. Voeg enkele voorbeelduitingen toe. De tekst die als een Pattern.any moet worden voorspeld is **vetgedrukt**. De naam van het formulier is moeilijk te onderscheiden van de andere woorden eromheen in de uiting. Het Pattern.any helpt bij het markeren van de grenzen van de entiteit.

    |Voorbeeld van een utterance|Formuliernaam|
    |--|--|
    |Waar is het formulier **Wat te doen wanneer er brand uitbreekt in het lab** en wie moet het ondertekenen wanneer ik het heb gelezen?|Wat te doen wanneer een brand in het lab uitbreekt
    |Waar is **Aanvraag voor relocatie van nieuwe medewerker in het bedrijf** op de server?|Aanvraag voor herstationering van nieuwe medewerker in het bedrijf|
    |Wie heeft "**Aanvragen voor gezondheid en welzijn op de hoofdcampus**" geschreven en wat is de meest recente versie?|Status- en wellnessaanvragen op de hoofdcampus|
    |Ik ben op zoek naar het formulier met de naam "**Aanvraag voor verplaatsen van kantoor met inbegrip van fysieke activa**“. |Aanvraag voor verplaatsen van kantoor, inclusief fysieke activa|

    Zonder een entiteit Pattern.any kan het lastig zijn voor LUIS om te begrijpen waar de titel van het formulier eindigt vanwege de vele variaties van de namen van formulieren.

### <a name="create-a-patternany-entity"></a>Een Pattern.any-entiteit maken
De entiteit Pattern.any extraheert entiteiten met verschillende lengten. Dit werkt alleen in een patroon omdat het patroon het begin en einde van de entiteit met syntaxis markeert.

1. Selecteer **Entiteiten** in de linkernavigatiebalk.

1. Selecteer **+ Maken**, voer de naam `FormName` in en selecteer **Pattern.any** als type. Selecteer **Maken**.

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

## <a name="what-did-this-tutorial-accomplish"></a>Wat hebt u met deze zelfstudie bereikt?

In deze zelfstudie zijn patronen toegevoegd om LUIS te helpen de intentie te voorspellen met een aanzienlijk hogere score zonder dat u meer voorbeelduitingen hebt moeten toevoegen. Door entiteiten en te negeren tekst te markeren kon LUIS het patroon toepassen op meer verschillende utterances.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen


> [!div class="nextstepaction"]
> [Informatie over het gebruik van rollen met een patroon](luis-tutorial-pattern.md)
