---
title: 'Snelstart: Knowledge base maken - REST, C# - QnA Maker'
description: In deze op C# REST gebaseerde snelstart wordt stapsgewijs uitgelegd hoe u met behulp van een programma een voorbeeldexemplaar van een knowledge base in QnA Maker kunt maken, dat wordt weergegeven op het Azure-dashboard van uw account voor de Cognitive Services-API.
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 78608d3acdfea07f21ccc42e8d530ff502fbb0b4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78851925"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c-with-rest"></a>Snelstartgids: een Knowledge Base maken in QnA Maker met behulp van C# met REST

In deze snelstart wordt beschreven hoe u programmatisch een voorbeeld van een QnA Maker-knowledge base kunt maken en publiceren. Met QnA Maker worden automatisch vragen en antwoorden opgehaald uit semi-gestructureerde inhoud, zoals veelgestelde vragen, vanuit [gegevensbronnen](../Concepts/knowledge-base.md). Het model voor de knowledge base wordt gedefinieerd in de JSON die in de hoofdtekst van de API-aanvraag wordt verzonden.

In deze snelstart worden QnA Maker-API's aangeroepen:
* [KB maken](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Bewerkingsdetails ophalen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Referentie documentatie](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [C#](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs) -voor beeld

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

* De huidige versie van [.net core](https://dotnet.microsoft.com/download/dotnet-core).
* U moet een [QnA Maker resource](../How-To/set-up-qnamaker-service-azure.md)hebben. Als u de sleutel en het eind punt (inclusief de resource naam) wilt ophalen, selecteert u **Quick** start voor uw resource in het Azure Portal.

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Maak een nieuwe .NET core-toepassing in uw voorkeurs editor of IDE.

In een console venster (zoals cmd, Power shell of bash) gebruikt u de `dotnet new` opdracht om een nieuwe console-app met de naam `qna-maker-quickstart`te maken. Met deze opdracht maakt u een eenvoudig ' Hallo wereld ' C#-project met één bron bestand: *Program.cs*.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Wijzig uw directory in de zojuist gemaakte app-map. U kunt de toepassing samen stellen met:

```dotnetcli
dotnet build
```

De build-uitvoer mag geen waarschuwingen of fouten bevatten.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="add-the-required-dependencies"></a>De vereiste afhankelijkheden toevoegen

Boven in Program.cs vervangt u de enige using-instructie door de volgende regels. U voegt op deze manier de benodigde afhankelijkheden toe aan het project:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>De vereiste constanten toevoegen

Voeg boven aan de programma klasse de vereiste constanten toe om toegang te krijgen tot QnA Maker.

Stel de volgende waarden in omgevings variabelen in:

* `QNA_MAKER_SUBSCRIPTION_KEY`-De **sleutel** is een teken reeks van 32 en is beschikbaar in de Azure Portal, op de QnA Maker-resource, op de pagina Quick Start. Dit is niet hetzelfde als de Voorspellings eindpunt sleutel.
* `QNA_MAKER_ENDPOINT`-Het **eind punt** is de URL voor het ontwerpen in de indeling van `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Dit is niet dezelfde URL die wordt gebruikt om een query uit te zoeken op het Voorspellings eindpunt.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=17-26 "Add the required constants")]

## <a name="add-the-kb-definition"></a>De KB-definitie toevoegen

Voeg na de constanten de volgende KB-definitie toe:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=28-58 "Add the knowledge base definition")]

## <a name="add-supporting-functions-and-structures"></a>Ondersteunende functies en structuren toevoegen
Voeg het volgende codeblok toe in de klasse Program:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=60-99 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>Een POST-aanvraag voor het maken van de KB toevoegen

De volgende code maakt een HTTPS-aanvraag naar de QnA Maker-API om een KB bij te werken en ontvangt het antwoord:

[!code-csharp[Add PostCreateKB to request via POST](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=145-165 "Add PostCreateKB to request via POST")]

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=101-122 "Add a POST request to create KB")]

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

## <a name="add-get-request-to-determine-creation-status"></a>GET-aanvraag toevoegen om de maakstatus te bepalen

Controleer de status van de bewerking.

[!code-csharp[Add GetStatus to request via GET](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=167-187 "Add GetStatus to request via GET")]

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=124-143 "Add GET request to determine creation status")]

Deze API-aanroep retourneert een JSON-antwoord dat de bewerkingsstatus bevat:

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

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

## <a name="add-createkb-method"></a>CreateKB-methode toevoegen

Met de volgende methode wordt de KB gemaakt en worden controles van de status herhaald.  De _create_ **bewerking-id** voor maken wordt geretourneerd in de veld **locatie**van de post-antwoord header en vervolgens gebruikt als onderdeel van de route in de GET-aanvraag. Omdat het maken van de KB enige tijd kan duren, moet u aanroepen herhalen om de status te controleren totdat de status geslaagd of mislukt is. Wanneer de bewerking is geslaagd, wordt de KB-id geretourneerd in **resourceLocation**.

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=189-254 "Add CreateKB method")]

## <a name="add-the-createkb-method-to-main"></a>De CreateKB-methode toevoegen aan Main

Wijzig de Main-methode om de CreateKB-methode aan te roepen:

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=256-265 "Add CreateKB method")]

## <a name="build-and-run-the-program"></a>Het programma bouwen en uitvoeren

Bouw het programma en voer het uit. Hiermee wordt de aanvraag automatisch verzonden naar de QnA Maker-API om de KB te maken en vervolgens worden elke 30 seconden de resultaten gecontroleerd. Elk antwoord wordt weergegeven in het consolevenster.

Zodra de knowledge base is gemaakt, kunt u deze weergeven in de QnA Maker-portal op de pagina [Mijn knowledge bases](https://www.qnamaker.ai/Home/MyServices).


[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST-API voor QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)