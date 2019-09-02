---
title: 'Snelstartgids: Een knowledge base maken - REST, Java - QnA Maker'
titleSuffix: Azure Cognitive Services
description: In deze op Java REST gebaseerde snelstart wordt stapsgewijs uitgelegd hoe u, met behulp van een programma, een voorbeeldexemplaar van een knowledge base in QnA Maker kunt maken, dat wordt weergegeven op het Azure-dashboard van uw account voor de Cognitive Services-API.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: diberry
ms.openlocfilehash: 06e2a772bfad7b1964f813a6cb6266efe61c6ccf
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70206850"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Snelstartgids: Een knowledge base maken in QnA Maker met behulp van Java

In deze snelstart wordt beschreven hoe u programmatisch een voorbeeld van een QnA Maker-knowledge base kunt maken. Met QnA Maker worden automatisch vragen en antwoorden opgehaald uit semi-gestructureerde inhoud, zoals veelgestelde vragen, vanuit [gegevensbronnen](../Concepts/data-sources-supported.md). Het model voor de knowledge base wordt gedefinieerd in de JSON die in de hoofdtekst van de API-aanvraag wordt verzonden.

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-java-repo-note.md)]

## <a name="create-a-knowledge-base-file"></a>Een kennisdatabasebestand maken

Maak een bestand met de naam `CreateKB.java`

## <a name="add-the-required-dependencies"></a>De vereiste afhankelijkheden toevoegen

Voeg aan het begin van `CreateKB.java` de volgende regels toe om de nodige afhankelijkheden aan het project toe te voegen:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=1-5 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>De vereiste constanten toevoegen
Voeg na de bovenstaande vereiste afhankelijkheden de vereiste constanten toe aan de klasse `CreateKB` om toegang te krijgen tot QnA Maker. Vervang de waarde van de variabele `subscriptionKey` door uw eigen QnA Maker-sleutel. U hoeft de laatste accolade niet toe te voegen om de klasse te beëindigen; deze staat in het laatste codefragment aan het einde van deze snelstart.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=26-34 "Add the required constants")]


## <a name="add-the-kb-model-definition-classes"></a>De definitieklassen van het KB-model toevoegen
Voeg na de constanten de volgende klassen en functies toe in de klasse `CreateKB` om het modeldefinitieobject in JSON te serialiseren.

[!code-java[Add the KB model definition classes](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=36-80 "Add the KB model definition classes")]

## <a name="add-supporting-functions"></a>Ondersteunende functies toevoegen

Voeg daarna de volgende ondersteunende functies toe in de klasse `CreateKB`.

1. Voeg de volgende functie toe om JSON in een leesbare indeling weer te geven:

    [!code-java[Add the PrettyPrint function](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=82-87 "Add the KB model definition classes")]

2. Voeg de volgende klasse toe om het HTTP-antwoord te beheren:

    [!code-java[Add class to manage the HTTP response](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=89-97 "Add class to manage the HTTP response")]

3. Voeg de volgende methode toe om een POST-aanvraag te doen bij de QnA Maker-API's. De `Ocp-Apim-Subscription-Key` is de sleutel van de QnA Maker-service die wordt gebruikt voor verificatie.

    [!code-java[Add POST method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=99-121 "Add POST method")]

4. Voeg de volgende methode toe om een GET-aanvraag te doen bij de QnA Maker-API's.

    [!code-java[Add GET method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=123-137 "Add GET method")]

## <a name="add-a-method-to-create-the-kb"></a>Een methode toevoegen om de KB te maken
Voeg de volgende methode toe om de KB te maken door de Post-methode aan te roepen.

[!code-java[Add CreateKB method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=139-144 "Add CreateKB method")]

Deze API-aanroep retourneert een JSON-antwoord dat de bewerkings-id bevat. Gebruik de bewerkings-id om te bepalen of de KB is gemaakt.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-a-method-to-get-status"></a>Een methode toevoegen om de status op te halen
Voeg de volgende methode om de maakstatus te controleren.

[!code-java[Add GetStatus method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=146-150 "Add GetStatus method")]

Herhaal de aanroep totdat deze lukt of mislukt:

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-a-main-method"></a>Een hoofdmethode toevoegen
De hoofdmethode maakt de KB en vraagt vervolgens de status na. De bewerkings-ID wordt geretourneerd in de veld **locatie**van de post-antwoord header en vervolgens gebruikt als onderdeel van de route in de GET-aanvraag. De `while` lus wordt opnieuw geprobeerd om de status te herhalen als deze nog niet is voltooid.

[!code-java[Add main method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=152-191 "Add main method")]

## <a name="compile-and-run-the-program"></a>Het programma compileren en uitvoeren

1. Zorg ervoor dat de gson-bibliotheek zich in de directory `./libs` bevindt. Compileer het bestand `CreateKB.java` op de opdrachtregel:

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Voer de volgende opdracht op een opdrachtregel in om het programma uit te voeren. Hiermee wordt de aanvraag verzonden naar de QnA Maker-API om de KB te maken en vervolgens worden elke 30 seconden de resultaten gecontroleerd. Elk antwoord wordt weergegeven in het consolevenster.

    ```bash
    java -cp ",;libs/*" CreateKB
    ```

Zodra de knowledge base is gemaakt, kunt u deze weergeven in de QnA Maker-portal op de pagina [Mijn knowledge bases](https://www.qnamaker.ai/Home/MyServices).

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST API voor QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
