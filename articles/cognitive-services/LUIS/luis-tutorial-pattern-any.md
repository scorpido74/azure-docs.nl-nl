---
title: 'Zelf studie: patroon. elke entiteit-LUIS'
titleSuffix: Azure Cognitive Services
description: Gebruik de entiteit pattern.any om gegevens te extraheren uit utterances die goed zijn opgemaakt en waar het einde van de gegevens eenvoudig kan worden verward met de resterende woorden van de utterance.
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 43ad6558b685d3959562be720f7494b3d2a7334e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499430"
---
# <a name="tutorial-extract-free-form-data-with-patternany-entity"></a>Zelf studie: vrije-vorm gegevens uitpakken met een patroon. elke entiteit

In deze zelfstudie gebruikt u de entiteit pattern.any om gegevens te extraheren uit utterances die goed zijn opgemaakt en waar het einde van de gegevens eenvoudig kan worden verward met de resterende woorden van de utterance. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Voorbeeld-app importeren
> * Voorbeeld van utterances toevoegen aan de bestaande entiteit
> * Pattern.any-entiteit maken
> * Patroon maken
> * Trainen
> * Nieuw patroon testen

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

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

## <a name="import-example-app"></a>Voorbeeld-app importeren

1. Download het [JSON-bestand van de app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-pattern-roles-HumanResources.json) en sla het op.

1. Importeer in de [Luis-Portal](https://www.luis.ai)op de pagina **mijn apps** de json in een nieuwe app.

1. Ga naar het gedeelte **Beheren**, open het tabblad **Versies**, kloon de versie en noem deze `patt-any`. Klonen is een uitstekende manier om te experimenteren met verschillende functies van LUIS zonder dat de oorspronkelijke versie wordt gewijzigd. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL.

## <a name="add-example-utterances"></a>Voorbeelden van utterances toevoegen 

1. Selecteer **Build** in de bovenste navigatiebalk en selecteer vervolgens **Intenties** in de linkernavigatiebalk.

1. Selecteer **FindForm** in de lijst met intenties.

1. Voeg enkele voorbeelden van utterances toe:

    |Voorbeeld van een utterance|
    |--|
    |Waar is het formulier **Wat te doen wanneer er brand uitbreekt in het lab** en wie moet het ondertekenen wanneer ik het heb gelezen?|
    |Waar is **Aanvraag voor relocatie van nieuwe medewerker in het bedrijf** op de server?|
    |Wie heeft "**Aanvragen voor gezondheid en welzijn op de hoofdcampus**" geschreven en wat is de meest recente versie?|
    |Ik ben op zoek naar het formulier met de naam "**Aanvraag voor verplaatsen van kantoor met inbegrip van fysieke activa**“. |

    Zonder een entiteit Pattern.any kan het lastig zijn voor LUIS om te begrijpen waar de titel van het formulier eindigt vanwege de vele variaties van de namen van formulieren.

## <a name="create-a-patternany-entity"></a>Een Pattern.any-entiteit maken
De entiteit Pattern.any extraheert entiteiten met verschillende lengten. Dit werkt alleen in een patroon omdat het patroon het begin en einde van de entiteit markeert.  

1. Selecteer **Entiteiten** in de linkernavigatiebalk.

1. Selecteer **Nieuwe entiteit maken**, voer de naam `FormName` in en selecteer **Pattern.any** als type. Selecteer **Done**. 

    U kunt de entiteit niet in een voor beeld van een uitingen labelen, omdat het een patroon is. any is alleen geldig in een patroon. 

    Als u wilt dat de opgehaalde gegevens ook andere entiteiten bevatten, zoals number of datetimeV2, moet u een samengestelde entiteit maken met daarin zowel de Pattern.any als number en datetimeV2.

## <a name="add-a-pattern-that-uses-the-patternany"></a>Een patroon toevoegen dat gebruikmaakt van de Pattern.any

1. Selecteer **Patronen** in de linkernavigatiebalk.

1. Selecteer de intentie **FindForm**.

1. Voer de volgende sjabloon-utterances in die gebruikmaken van de nieuwe entiteit:

    |Sjabloon-utterances|
    |--|
    |Waar is het formulier [“]{FormName}[“] en wie moet het ondertekenen wanneer ik het heb gelezen[?]|
    |Waar is [“]{FormName}[“] op de server [?]|
    |Wie heeft [“]{FormName}[“] geschreven en wat is de meest recente versie[?]|
    |Ik ben op zoek naar het formulier met de naam [“]{FormName}[“][.]|

    Als u rekening wilt houden met variaties voor het formulier, zoals enkele aanhalingstekens in plaats van dubbele aanhalingstekens of een punt in plaats van een vraagteken, moet u voor elke variatie een nieuw patroon maken.

## <a name="train-the-luis-app"></a>LUIS-app inleren

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Het nieuwe patroon voor extractie van vrije gegevens testen
1. Selecteer **Testen** in de bovenste balk om het testdeelvenster te openen. 

1. Voer de volgende utterance in: 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. Selecteer **Inspecteren** onder het resultaat om de testresultaten voor de entiteit en intentie te zien.

    De entiteit `FormName` is als eerste gevonden en vervolgens het patroon waarmee de intentie wordt bepaald. Als u een testresultaat hebt waar de entiteiten niet zijn gedetecteerd en daarom het patroon niet is gevonden, moet u meer voorbeeld- utterances toevoegen aan de intentie (niet het patroon).

1. Sluit het testdeelvenster met de knop **Testen** in de bovenste navigatiebalk.

## <a name="using-an-explicit-list"></a>Een expliciete lijst gebruiken

Als u merkt dat het patroon, wanneer dit een Pattern.any bevat, entiteiten onjuist extraheert, gebruikt u een [expliciete lijst](luis-concept-patterns.md#explicit-lists) om dit probleem te verhelpen.


## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u voorbeeld-utterances toegevoegd aan een bestaande intentie en vervolgens een nieuwe Pattern.any gemaakt voor de naam van het formulier. In de zelfstudie is vervolgens een patroon voor de bestaande intentie gemaakt met de nieuwe voorbeeld-utterances en entiteit. Interactieve testen hebben aangetoond dat het patroon en de bijbehorende intentie zijn voorspeld omdat de entiteit is gevonden. 

> [!div class="nextstepaction"]
> [Informatie over het gebruik van rollen met een patroon](luis-tutorial-pattern-roles.md)
