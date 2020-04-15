---
title: 'Zelfstudie: Patronen - LUIS'
description: Gebruik patronen om de intentie en entiteitsvoorspelling te vergroten en tegelijkertijd minder voorbeelduitingen in deze zelfstudie te geven. Het patroon wordt geleverd als een voorbeeld van een sjabloonutterance, dat syntaxis bevat om entiteiten en negeerbare tekst te identificeren.
ms.topic: tutorial
ms.date: 04/14/2020
ms.openlocfilehash: 826334fafd04a6357f529b1dc07408ff1c15ce5c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380775"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>Zelfstudie: Algemene indelingen voor patroonsjabloon-uitingen toevoegen om voorspellingen te verbeteren

Gebruik in deze zelfstudie patronen om de intentie en entiteitsvoorspelling te vergroten, waardoor u minder voorbeelduitingen geven. Het patroon is een sjabloonutterance die is toegewezen aan een intentie, die syntaxis bevat om entiteiten en negeerbare tekst te identificeren.

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

Als u sjabloonuitingen als een patroon toevoegt, u in het algemeen minder voorbeelduitingen aan een intentie toevoegen.

Een patroon wordt toegepast als een combinatie van tekstmatching en machine learning.  De sjabloonutterance in het patroon, samen met de voorbeelduitingen in de intentie, geeft LUIS een beter begrip van welke uitingen passen bij de intentie.

## <a name="import-example-app-and-clone-to-new-version"></a>Voorbeeld-app en kloon importeren naar nieuwe versie

Voer de volgende stappen uit:

1.  Download en sla het [JSON-bestand van](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json?raw=true)de app op.

1. Importeer de JSON in een nieuwe app in de [preview LUIS-portal](https://preview.luis.ai). Selecteer **op** de pagina Mijn apps de optie **+ Nieuwe app voor een gesprek**en selecteer Importeren als **JSON**. Selecteer het bestand dat u in de vorige stap hebt gedownload.

1. Selecteer in de sectie **Beheren** op het tabblad **Versies** de actieve versie en selecteer **Vervolgens Kloon**. Geef de gekloonde versie een naam. `patterns` Klonen is een uitstekende manier om te experimenteren met verschillende functies van LUIS zonder dat de oorspronkelijke versie wordt gewijzigd. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL.

## <a name="create-new-intents-and-their-utterances"></a>Nieuwe intenties en de bijbehorende utterances maken

De twee intenties vinden de manager of de directe rapporten van de manager, op basis van de utterancetekst. De moeilijkheid is dat de twee bedoelingen verschillende dingen _betekenen,_ maar de meeste woorden zijn hetzelfde. Alleen de woordvolgorde is anders. Om de intentie correct te kunnen voorspellen, zou het veel voorbeelden moeten hebben.

1. Selecteer **Bouwen** op de navigatiebalk.

1. Selecteer op de pagina **Intents** **+ Maken** om een nieuwe intentie te maken.

1. Voer in het pop-updialoogvenster `OrgChart-Manager` in en selecteer vervolgens **Done**.

    ![Pop-upvenster om een nieuw bericht te maken](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. Voeg voorbeelden van utterances toe aan de intentie. Deze uitingen zijn niet _precies_ gelijk, maar hebben wel een patroon dat kan worden geëxtraheerd.

    |Voorbeelden van utterances|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

    Maakt u zich geen zorgen als in de utterances van de intent de keyPhrase-entiteit is gemarkeerd in plaats van de werknemer-entiteit. Beide zijn in het deelvenster Test en bij het eindpunt juist voorspeld.

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

1. Ga naar het einde van de URL _YOUR_QUERY_HERE_ in `Who is the boss of Jill Jones?`de adresbalk en vervang YOUR_QUERY_HERE door: .

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

De scores van de twee topintents zijn dichtbij, maar de hoogste intentie is niet significant hoog (meer dan 60%) en is niet ver genoeg boven de score van de volgende intentie.

Omdat LUIS training is niet precies hetzelfde elke keer (er is een beetje variatie), deze top twee scores kunnen omkeren op de volgende training cyclus. Het resultaat is dat de verkeerde intent kan worden geretourneerd.

Gebruik patronen om de score van de juiste intent een aanzienlijk hoger percentage te geven en ervoor te zorgen dat de score verder weg ligt van de eerstvolgende hogere score.

Laat dit tweede browservenster geopend. U hebt het verderop in deze zelfstudie namelijk weer nodig.

## <a name="template-utterances"></a>Sjabloon-utterances
Vanwege de aard van het human resource onderwerp domein, zijn er een paar gemeenschappelijke manieren om te vragen over relaties met werknemers in organisaties. Bijvoorbeeld:

|Utterances|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Deze utterances liggen te dicht bij elkaar om de contextuele uniekheid van elk ervan te bepalen zonder veel utterance-voorbeelden te moeten verstrekken. Door een patroon voor een intent toe te voegen, leert LUIS algemene utterance-patronen voor een intent te herkennen zonder veel utterance-voorbeelden te moeten verstrekken.

Dit zijn voorbeelden van sjabloon-utterances voor deze intent:

|Voorbeelden van sjabloon-utterances|betekenis van de syntaxis|
|--|--|
|`Who does {Employee} report to[?]`|Verwisselbare`{Employee}`<br>Negeren`[?]`|
|`Who reports to {Employee}[?]`|Verwisselbare`{Employee}`<br>Negeren`[?]`|

De syntaxis `{Employee}` markeert de locatie van de entiteit in de sjabloon-utterance en geeft aan om welke entiteit het gaat. De optionele `[?]`syntaxis, markeert woorden of [interpunctie](luis-reference-application-settings.md#punctuation-normalization) die optioneel is. LUIS kijkt naar de utterance, waarbij de optionele tekst tussen de haakjes wordt genegeerd.

Hoewel de syntaxis eruit ziet als een gewone expressie, is het geen gewone expressie. Alleen de accolades, `{}`, en de vierkant haakjes, `[]`, worden als syntaxis ondersteund. Ze kunnen tot maximaal twee niveaus worden genest.

Als een patroon kan worden gekoppeld aan een _utterance,_ moeten eerst de entiteiten in de utterance overeenkomen met de entiteiten in de sjabloonutterance. Dit betekent dat de entiteiten voldoende voorbeelden moeten hebben in voorbeelduitingen met een hoge mate van voorspelling voordat patronen met entiteiten succesvol zijn. De sjabloon helpt echter niet bij het voorspellen van entiteiten, alleen van intenties.

**Hoewel u voor patronen minder voorbeeld-utterances hoeft op te geven, komt het patroon niet overeen als de entiteiten niet worden gedetecteerd.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>De patronen voor de intentie OrgChart-Manager toevoegen

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

    Deze sjabloonuitingen omvatten de entiteit **Werknemer** met de aantekening op krullende haakjes.

1. Terwijl u nog steeds op de pagina Patronen staat, selecteert u de intentie **OrgChart-Reports** en voert u de volgende sjabloonuitingen in:

    |Sjabloon-utterances|
    |:--|
    |`Who are {Employee}['s] subordinates[?]`|
    |`Who reports to {Employee}[?]`|
    |`Who does {Employee} manage[?]`|
    |`Who are {Employee} direct reports[?]`|
    |`Who does {Employee} supervise[?]`|
    |`Who does {Employee} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Query uitvoeren op eindpunt wanneer patronen worden gebruikt

Nu de patronen aan de app zijn toegevoegd, trainen, publiceren en query de app op de voorspelling runtime eindpunt.

1. Selecteer **Trein**. Nadat de training is voltooid, selecteert u **Publiceren** en selecteert u de **productiesleuf** en selecteert u **Gereed**.

1. Nadat het publiceren is voltooid, schakelt u browsertabbladen terug naar het tabblad URL van eindpunt.

1. Ga naar het einde van de URL in de adresbalk en vervang _YOUR_QUERY_HERE_ door:`Who is the boss of Jill Jones?`

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

De intentievoorspelling is nu aanzienlijk zelfverzekerder en de volgende hoogste intentiescore is aanzienlijk lager. Deze twee intenties zullen niet flip-flop tijdens de training.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Werken met optionele tekst en voorafgemaakte entiteiten

De vorige patroonsjabloon-utterances in deze zelfstudie hadden enkele voorbeelden van optionele tekst `'s`, zoals het gebruik van het vraagteken, `?`. Stel dat u huidige en toekomstige datums in de utterancetekst moet toestaan.

Voorbeelden van utterances zijn:

|Intentie|Voorbeeld-utterances met optionele tekst en voorafgemaakte entiteiten|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

In elk van deze voorbeelden wordt gebruikgemaakt van een werkwoordsvorm, `was`, `is`, `will be`, evenals een datum, `March 3`, `now`, en `in a month`, die LUIS correct moet voorspellen. De laatste twee voorbeelden in de tabel gebruiken bijna `in` `on`dezelfde tekst, behalve voor en .

Voorbeeldsjabloonuitingen die deze optionele informatie mogelijk maken:

|Intentie|Voorbeeld-utterances met optionele tekst en voorafgemaakte entiteiten|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


Dankzij het gebruik van de optionele syntaxis met vierkante haken, `[]`, kan deze optionele tekst eenvoudig worden toegevoegd aan de sjabloon-utterance en worden genest tot het tweede niveau met `[[]]`, en kunnen entiteiten of tekst worden opgenomen.


**Vraag: Waarom zijn `w` alle letters, de eerste letter in elke sjabloon uiting, kleine letters? Moeten ze niet optioneel boven- of ondergrens?** De utterance die door de clienttoepassing naar het query-eindpunt wordt verzonden, wordt omgezet in kleine letters. De sjabloon-utterance kan uit hoofdletters of kleine letters bestaan en dat geldt ook voor de eindpunt-utterance. De vergelijking wordt altijd uitgevoerd na de conversie naar kleine letters.

**Vraag: Waarom maakt het vooraf gemaakte getal geen deel uit van de sjabloon-utterance als 3 maart wordt voorspeld als het getal `3` en als de datum `March 3`?** De sjabloon-utterance maakt contextueel gebruik van een datum, letterlijk zoals in `March 3`, of figuurlijk zoals `in a month`. Een datum kan een waarde bevatten, maar een getal hoeft niet per se als een datum te worden beschouwd. Gebruik altijd de entiteit die het beste het type aangeeft dat u wilt laten retourneren in de JSON-voorspellingsresultaten.

**Vraag: Hoe zit het met slecht geformuleerde utterances zoals `Who will {Employee}['s] manager be on March 3?`.** Grammaticaal verschillende werkwoordsvormen, zoals deze waarbij `will` en `be` van elkaar zijn gescheiden, moeten als een nieuwe sjabloon-utterance worden ingesteld. De bestaande sjabloon-utterance zal een dergelijke werkwoordsvorm niet herkennen. Het doel van de utterance is weliswaar niet veranderd, maar dat geldt wel voor de woordplaatsing in de utterance. Deze wijziging heeft gevolgen voor de voorspelling in LUIS. U [groeperen en of](#use-the-or-operator-and-groups) de werkwoord-tijden om deze uitingen te combineren.

**Houd er rekening mee dat als eerste entiteiten worden gevonden, waarna het patroon wordt vergeleken.**

### <a name="add-new-pattern-template-utterances"></a>Nieuwe patroonsjabloon-utterances toevoegen

1. Terwijl u zich nog steeds in de sectie **Patterns** van **Build** bevindt, voegt u verschillende nieuwe patroonsjabloon-utterances toe. Selecteer **OrgChart-Manager** in de vervolgkeuzelijst Intent en voer elk van de volgende sjabloon-utterances in:

    |Intentie|Voorbeeld-utterances met optionele tekst en voorafgemaakte entiteiten|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Selecteer **Trainen** in de navigatiebalk om de app te trainen.

3. Nadat de training is voltooid, selecteert u **Testen** boven aan het paneel om het testpaneel te openen.

4. Voer verschillende test-utterances in om te controleren of het patroon overeenkomt en de intentiescore hoog genoeg is.

    Nadat u de eerste utterance hebt ingevoerd, selecteert u **Inspect** onder het resultaat zodat u alle voorspellingsresultaten kunt zien. Elke utterance moet de **intentie OrgChart-Manager** hebben en de waarden voor de entiteiten werknemer en datetimeV2 extraheren.

    |Utterance|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

Voor al deze utterances zijn de daarin opgenomen entiteiten gevonden en daarom komen ze overeen met hetzelfde patroon en hebben ze een hoge voorspellingsscore. U hebt een paar patronen toegevoegd die overeenkomen met veel variaties van uitingen. U hoefde geen voorbeelduitingen toe te voegen aan de intentie om de sjabloonutterance in het patroon te laten werken.

Dit gebruik van patronen voorzien:
* Hogere voorspellingsscores
* Met dezelfde voorbeelduitingen in de intentie
* Met slechts een paar goed geconstrueerde sjabloonuitingen in het patroon

### <a name="use-the-or-operator-and-groups"></a>De OPERATOR en groepen van de OK gebruiken

Verschillende van de vorige sjabloonuitingen zijn zeer dicht. Gebruik de **syntaxis groep** `()` en **OK** `|` om de sjabloonuitingen te verminderen.

De volgende 2 patronen kunnen worden gecombineerd `()` tot `|` één patroon met behulp van de groep en de OK-syntaxis.

|Intentie|Voorbeeld-utterances met optionele tekst en voorafgemaakte entiteiten|
|--|--|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

De nieuwe sjabloonutterance is:

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`.

Dit maakt gebruik van een **groep** `in` rond `on` de vereiste werkwoordtijd en de optionele en met een **of** pijp tussen hen.

1. Selecteer **op** de pagina Patronen het filter **OrgChart-Manager.** Verklein de lijst `manager`door te zoeken naar .

1. Eén versie van de sjabloonutterance behouden (in de volgende stap bewerken) en de andere varianten verwijderen.

1. Wijzig de sjabloon-utterance in: 

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`

2. Selecteer **Trainen** in de navigatiebalk om de app te trainen.

3. Nadat de training is voltooid, selecteert u **Testen** boven aan het paneel om het testpaneel te openen.

    Gebruik het deelvenster Testen om versies van de utterance te testen:

    |Uitingen die u wilt invoeren in het deelvenster Testen|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

Door meer patroonsyntaxis te gebruiken, vermindert u het aantal sjabloonuitingen dat u in uw app moet behouden, terwijl u nog steeds een hoge voorspellingsscore hebt.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>De begin- en eindankers van uitingen gebruiken

De syntaxis van het patroon biedt de `^`syntaxis van een caret begin- en einduitingsanker. De begin- en eindutteranceankers kunnen samen worden gebruikt om zeer specifieke en mogelijk letterlijke uitingen te targeten of afzonderlijk worden gebruikt om intents te targeten.

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

### <a name="add-example-utterances-with-patternany"></a>Voorbeelduitingen toevoegen met Pattern.any

1. Selecteer **Build** in de bovenste navigatiebalk en selecteer vervolgens **Intenties** in de linkernavigatiebalk.

1. Selecteer **FindForm** in de lijst met intenties.

1. Voeg enkele voorbeelduitingen toe. De tekst die moet worden voorspeld als een patroon.any is in **vette tekst**. De formuliernaam is moeilijk te bepalen aan de andere woorden eromheen in de utterance. Het patroon.any zal helpen door de grenzen van de entiteit te markeren.

    |Voorbeeld van een utterance|Formuliernaam|
    |--|--|
    |Waar is het formulier **Wat te doen wanneer er brand uitbreekt in het lab** en wie moet het ondertekenen wanneer ik het heb gelezen?|Wat te doen als er brand uitbreekt in het Lab
    |Waar is **Aanvraag voor relocatie van nieuwe medewerker in het bedrijf** op de server?|Verzoek verhuizing van werknemer nieuw bij het bedrijf|
    |Wie heeft "**Aanvragen voor gezondheid en welzijn op de hoofdcampus**" geschreven en wat is de meest recente versie?|Gezondheids- en wellnessverzoeken op de hoofdcampus|
    |Ik ben op zoek naar het formulier met de naam "**Aanvraag voor verplaatsen van kantoor met inbegrip van fysieke activa**“. |Office-verplaatsingsaanvraag inclusief fysieke elementen|

    Zonder een entiteit Pattern.any kan het lastig zijn voor LUIS om te begrijpen waar de titel van het formulier eindigt vanwege de vele variaties van de namen van formulieren.

### <a name="create-a-patternany-entity"></a>Een Pattern.any-entiteit maken
De entiteit Pattern.any extraheert entiteiten met verschillende lengten. Het werkt alleen in een patroon omdat het patroon het begin en einde van de entiteit markeert met syntaxis.

1. Selecteer **Entiteiten** in de linkernavigatiebalk.

1. Selecteer **+ Maken,** `FormName`voer de naam in en selecteer **Pattern.a.** als type. Selecteer **Maken**.

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

## <a name="what-did-this-tutorial-accomplish"></a>Wat heeft deze tutorial te bereiken?

Deze zelfstudie heeft patronen toegevoegd om LUIS te helpen de intentie te voorspellen met een aanzienlijk hogere score zonder meer voorbeelduitingen toe te voegen. Door entiteiten en te negeren tekst te markeren kon LUIS het patroon toepassen op meer verschillende utterances.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen


> [!div class="nextstepaction"]
> [Informatie over het gebruik van rollen met een patroon](luis-tutorial-pattern.md)
