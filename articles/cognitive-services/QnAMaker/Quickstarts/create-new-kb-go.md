---
title: 'Snelstart: Knowledge base maken - REST, Go - QnA Maker'
description: In deze op REST gebaseerde snelstart wordt stapsgewijs uitgelegd hoe u, met behulp van een programma, een voorbeeldexemplaar van een knowledge base in QnA Maker kunt maken, dat wordt weergegeven op het Azure-dashboard van uw account voor de Cognitive Services-API.
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 221220345f4f3b7aff2a32c956d921f677ca0627
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78851914"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Snelstart: Een knowledge base maken in QnA Maker met behulp van Go

In deze snelstart wordt beschreven hoe u programmatisch een voorbeeld van een QnA Maker-knowledge base kunt maken. Met QnA Maker worden automatisch vragen en antwoorden opgehaald uit semi-gestructureerde inhoud, zoals veelgestelde vragen, vanuit [gegevensbronnen](../Concepts/knowledge-base.md). Het model voor de knowledge base wordt gedefinieerd in de JSON die in de hoofdtekst van de API-aanvraag wordt verzonden.

In deze snelstart worden QnA Maker-API's aangeroepen:
* [KB maken](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Bewerkingsdetails ophalen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Reference documentation](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | Voor[beeld](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go) van referentie documentatie

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

* [Go 1.10.1](https://golang.org/dl/)
* U moet een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md)hebben. Als u de sleutel en het eind punt (inclusief de resource naam) wilt ophalen, selecteert u **Quick** start voor uw resource in het Azure Portal.

## <a name="create-a-knowledge-base-go-file"></a>Een Go-bestand met knowledge base maken

Maak een bestand met de naam `create-new-knowledge-base.go`.

## <a name="add-the-required-dependencies"></a>De vereiste afhankelijkheden toevoegen

Voeg bovenaan `create-new-knowledge-base.go` de volgende regels toe om de nodige afhankelijkheden aan het project toe te voegen:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>De vereiste constanten toevoegen
Voeg na de bovenstaande vereiste afhankelijkheden de vereiste constanten toe voor toegang tot QnA Maker.

Stel de volgende waarden in:

* `<your-qna-maker-subscription-key>`-De **sleutel** is een teken reeks van 32 en is beschikbaar in de Azure Portal, op de QnA Maker-resource, op de pagina Quick Start. Dit is niet hetzelfde als de Voorspellings eindpunt sleutel.
* `{your-resource-name}`-De **resource naam** wordt gebruikt voor het maken van de URL voor het ontwerpen van eind punten voor het ontwerpen `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`, in de indeling van. Dit is niet dezelfde URL die wordt gebruikt om een query uit te zoeken op het Voorspellings eindpunt.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=13-20 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>De definitie van het KB-model toevoegen
Voeg na de constanten de volgende KB-modeldefinitie toe. Het model wordt na de definitie naar een tekenreeks geconverteerd.

[!code-go[Add the KB model definition](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=22-44 "Add the KB model definition")]

## <a name="add-supporting-structures-and-functions"></a>Ondersteunende functies en structuren toevoegen

Voeg daarna de volgende ondersteunende functies toe.

1. De structuur van een HTTP-aanvraag toevoegen:

    [!code-go[Add the structure for an HTTP request](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=46-49 "Add the structure for an HTTP request")]

2. Voeg de volgende methode toe voor het afhandelen van een POST-bewerking voor de QnA Maker-API's. In deze snelstart wordt POST gebruikt voor het verzenden van de KB-definitie naar QnA Maker.

    [!code-go[Add the POST method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=51-66 "Add the POST method")]

3. Voeg de volgende methode toe voor het afhandelen van een GET-bewerking voor de QnA Maker-API's. In deze snelstart wordt GET gebruikt om de status van de maakbewerking te controleren.

    [!code-go[Add the GET method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=68-83 "Add the GET method")]

## <a name="add-function-to-create-kb"></a>Functie toevoegen om KB te maken

Voeg de volgende functies toe voor het maken van een HTTP POST-aanvraag om de knowledge base te maken. De _create_ **bewerking-id** voor maken wordt geretourneerd in de veld **locatie**van de post-antwoord header en vervolgens gebruikt als onderdeel van de route in de GET-aanvraag. De `Ocp-Apim-Subscription-Key` is de sleutel van de QnA Maker-service die wordt gebruikt voor verificatie.

[!code-go[Add the create_kb method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=85-97 "Add the create_kb method")]

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

## <a name="add-function-to-get-status"></a>Een functie toevoegen om de status op te halen

Voeg de volgende functie toe om een HTTP GET-aanvraag te maken om de bewerkingsstatus te controleren. De `Ocp-Apim-Subscription-Key` is de sleutel van de QnA Maker-service die wordt gebruikt voor verificatie.

[!code-go[Add the check_status method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=99-108 "Add the check_status method")]

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
## <a name="add-main-function"></a>Een hoofdfunctie toevoegen

De volgende functie is de hoofdfunctie. Deze maakt de KB en voert herhalend controles uit voor de status. Omdat het maken van de KB enige tijd kan duren, moet u aanroepen herhalen om de status te controleren totdat de status geslaagd of mislukt is.

[!code-go[Add the main method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=110-140 "Add the main method")]


## <a name="compile-the-program"></a>Het programma compileren
Voer de volgende opdracht in voor het compileren van het bestand. De opdrachtprompt retourneert geen informatie voor een geslaagde build.

```bash
go build create-new-knowledge-base.go
```

## <a name="run-the-program"></a>Het programma uitvoeren

Voer de volgende opdracht op een opdrachtregel in om het programma uit te voeren. Hiermee wordt de aanvraag verzonden naar de QnA Maker-API om de KB te maken en vervolgens worden elke 30 seconden de resultaten gecontroleerd. Elk antwoord wordt weergegeven in het consolevenster.

```bash
go run create-new-knowledge-base
```

Zodra de knowledge base is gemaakt, kunt u deze weergeven in de QnA Maker-portal op de pagina [Mijn knowledge bases](https://www.qnamaker.ai/Home/MyServices).

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST-API voor QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)