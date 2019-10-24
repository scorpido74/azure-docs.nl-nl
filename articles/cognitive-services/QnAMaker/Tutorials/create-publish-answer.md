---
title: 'Zelf studie: maken, publiceren, beantwoorden QnA Maker'
titleSuffix: Azure Cognitive Services
description: In deze REST-zelfstudie gaat u via een programma een kennisdatabase maken en publiceren, en vervolgens een vraag uit de knowledge base beantwoorden.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 10/12/2019
ms.author: diberry
ms.openlocfilehash: d13bce3c1cafd20b311aa882d3a32101c1833ba5
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555505"
---
# <a name="tutorial-using-c-create-knowledge-base-then-answer-question"></a>Zelfstudie: C# gebruiken, een knowledge base maken en vervolgens een vraag beantwoorden

In deze zelfstudie gaat u via een programma een KB (knowledge base) maken en publiceren, en vervolgens een klantvraag beantwoorden met de knowledge base. 

> [!div class="checklist"]
> * Een knowledge base maken 
> * Status voor maken controleren
> * De knowledge base trainen en publiceren
> * Eindpuntgegevens ophalen
> * Curl gebruiken om een query uit te voeren op de knowledge base


Deze Snelstartgids roept QnA Maker REST Api's aan:

* [Een KB (knowledge base) maken](https://go.microsoft.com/fwlink/?linkid=2092179)
* [Bewerkingsdetails ophalen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)
* [Details van knowledge base ophalen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/getdetails) 
* [Eindpunten van knowledge base ophalen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/getkeys)
* [Publiceren](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) 

## <a name="prerequisites"></a>Vereisten

* De meest recente [**Visual Studio Community edition**](https://www.visualstudio.com/downloads/).
* U moet een [QnA Maker-service ](../How-To/set-up-qnamaker-service-azure.md) hebben. Als u de sleutel en de resource naam wilt ophalen, selecteert u **Quick** start in het Azure portal voor uw QnA Maker resource. 

> [!NOTE] 
> Het bestand of de bestanden van de volledige oplossing zijn beschikbaar in de GitHub-opslagplaats [**Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="create-a-knowledge-base-project"></a>Een project met knowledge base maken

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>De vereiste afhankelijkheden toevoegen

Boven in Program.cs vervangt u de enige _using_-instructie door de volgende regels. U voegt op deze manier de benodigde afhankelijkheden toe aan het project:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-a-kbdetails-class"></a>Een KBDetails-klasse toevoegen
Voeg deze KBDetails-klasse toe binnen de naamruimtehaakjes. Met deze klasse kan de NewtonSoft-bibliotheek het JSON-antwoord deserialiseren in een C#-klasse.

[!code-csharp[Add a KBDetails class](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=15-26 "Add a KBDetails class")]

## <a name="add-the-required-constants"></a>De vereiste constanten toevoegen

Boven aan de klasse Program voegt u de volgende constanten toe voor toegang tot QnA Maker:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=30-57 "Add the required constants")]

## <a name="add-the-kb-definition"></a>De KB-definitie toevoegen

Voeg na de constanten de volgende KB-modeldefinitie toe:

[!code-csharp[Add the KB definition](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=59-85 "Add the KB definition")]

## <a name="add-supporting-functions-and-structures"></a>Ondersteunende functies en structuren toevoegen
Voeg het volgende codeblok toe in de klasse Program:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=87-123 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>Een POST-aanvraag voor het maken van de KB toevoegen

De volgende code maakt een HTTPS-aanvraag naar de QnA Maker-API om een KB bij te werken en ontvangt het antwoord:

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=124-141 "Add a POST request to create KB")]

Deze API-aanroep retourneert een JSON-antwoord dat de bewerkings-id bevat. Later maakt het programma gebruik van de bewerkings-id om te bepalen of de KB is gemaakt. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>GET-aanvraag toevoegen om de maakstatus te bepalen

Controleer de status van de bewerking voor maken.

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=142-151 "Add GET request to determine creation status")]

Deze API-aanroep retourneert een JSON-antwoord dat de bewerkingsstatus bevat: 

```JSON
{
  "operationState": "Running",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
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
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>CreateKB-methode toevoegen

In de volgende methode zijn de oproepen ingekapseld om de KB te maken en de status te controleren.  De **bewerkings-id** voor _maken_ wordt geretourneerd in het header-veld voor het POST-antwoord **Locatie**, en vervolgens gebruikt als onderdeel van de route in de GET-aanvraag. Omdat het maken van de KB enige tijd kan duren, moet u aanroepen herhalen om de status te controleren totdat de status geslaagd of mislukt is. Wanneer de bewerking is geslaagd, wordt de KB-id geretourneerd in **resourceLocation**. 

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=152-227 "Add GET request to determine creation status")]

## <a name="add-publish-method"></a>De methode Publish toevoegen

Nadat de Knowledge Base is gemaakt, publiceert u de KB. Wellicht verwachtte u een oproep naar een training-API. Dit is niet vereist met deze versie. 

De volgende code maakt een HTTPS-aanvraag naar de QnA Maker-API om een KB te publiceren en ontvangt het antwoord:

[!code-csharp[Add publish method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=228-259 "Add publish method")]

De API-aanroep retourneert een 204-status voor een geslaagde publicatie zonder enige inhoud in de hoofdtekst van het antwoord. Met deze quickstartcode wordt tekst voor 204-antwoorden toegevoegd, zodat u het resultaat kunt zien.

Voor alle andere antwoorden wordt het antwoord ongewijzigd geretourneerd.

## <a name="generating-an-answer"></a>Een antwoord genereren
Om toegang te krijgen tot de KB om een vraag te verzenden en het beste antwoord te ontvangen, heeft het programma de _resource naam_ nodig van de KB-informatie-API en de _primaire eindpunt sleutel_ van de API van het eind punt. Deze methoden bevinden zich in de volgende secties, samen met de methode voor het genereren van een antwoord. 

In de volgende tabel wordt getoond hoe de gegevens worden gebruikt om de URI samen te stellen:

|Antwoord-URI-sjabloon genereren|
|--|
|https://**your-resource name**. Azurewebsites.net/qnamaker/knowledgebases/**KBID**/generateAnswer|

Het _primaire eindpunt_ wordt doorgegeven als header om de aanvraag te verifiëren voor het genereren van een antwoord:

|Headernaam|Headerwaarde|
|--|--|
|Autorisatie|`Endpoint` + **primaire eindpunt**<br>Voorbeeld: `Endpoint xxxxxxx`<br>Let op de ruimte tussen de tekst van `Endpoint` en de waarde van het primaire eindpunt. 

De juiste JSON moet worden doorgegeven via de hoofdtekst van de aanvraag:

```JSON
{
    question: "What languages does QnA Maker support?"
}
```

## <a name="get-kb-details"></a>KB-details ophalen
Voeg de volgende methode toe om de KB-details op te halen. Deze details bevatten de resource naam van de KB, die `hostName` in de volgende JSON wordt genoemd. De resource naam is de naam van de QnA Maker resource die u hebt ingevoerd bij het maken van de QnA Maker resource. 

[!code-csharp[Get KB Details](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=260-273 "Add publish method")]

Met deze API-aanroep wordt een JSON-antwoord geretourneerd: 

```JSON
{
  "id": "ZZZ31e19-cba7-48d1-8594-5c4297ecc9c1",
  "hostName": "https://qnamaker-s0-s.azurewebsites.net",
  "lastAccessedTimestamp": "2018-10-11T18:10:05Z",
  "lastChangedTimestamp": "2018-10-11T18:09:37Z",
  "lastPublishedTimestamp": "2018-10-11T18:10:15Z",
  "name": "QnA Maker FAQ from quickstart",
  "userId": "AAAc3841df0b42cdb00f53a49d51a89c",
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

## <a name="get-kb-endpoints"></a>KB-eindpunten ophalen
Voeg de volgende methode toe om de primaire eindpunten van de QnA Maker op te halen. Deze eindpunten zijn niet gebonden aan de KB. Ze zijn geldig voor alle KB’s die zijn gekoppeld aan de QnA Maker-resourcesleutels uit de Azure-portal.  

[!code-csharp[Get KB Endpoints](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=274-289 "Get KB Endpoints")]

Met deze API-aanroep wordt een JSON-antwoord geretourneerd: 

```JSON
{
  "primaryEndpointKey": "AAAb5719-e2f7-4a33-937d-7a3b4736a1be",
  "secondaryEndpointKey": "BBBcba78-c1d2-4166-b98f-c77255aefaba",
  "installedVersion": "4.2.0",
  "lastStableVersion": "4.2.0"
}
```

## <a name="get-an-answer"></a>Een antwoord ophalen
Voeg de volgende methode toe om een antwoord te krijgen op de vraag van de gebruiker. 

[!code-csharp[Get Answer](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=290-315 "Get Answer")]

Als u het antwoord op alleen vragen wilt beperken, voegt u de eigenschap `[rankerType](Learn more about [rankerType](../concepts/best-practices.md#choosing-ranker-type).
)` toe aan de hoofd tekst, bijvoorbeeld: 

```csharp
request.Content = new StringContent("{question:'" + question + "', rankerType:'QuestionOnly'}", Encoding.UTF8, "application/json"); 
```

Met deze API-aanroep wordt een JSON-antwoord geretourneerd: 

```JSON
{
  "answers": [
    {
      "questions": [
        "Does QnA Maker support non-English languages?"
      ],
      "answer": "See more details about [supported languages](https://docs.microsoft.com/azure/cognitive-services/qnamaker/overview/languages-supported).\n\n\nIf you have content from multiple languages, be sure to create a separate service for each language.",
      "score": 82.19,
      "id": 11,
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    }
  ]
}
```

## <a name="main-method"></a>De methode Main
Met de methode Main worden de synchrone oproepen weergegeven voor het maken, publiceren en genereren van het antwoord. 

[!code-csharp[Main method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=316-337 "Main method")]

## <a name="build-and-run-the-program"></a>Het programma bouwen en uitvoeren

Bouw het programma en voer het uit. 

Zodra de knowledge base is gemaakt, kunt u deze weergeven in de QnA Maker-portal op de pagina [Mijn knowledge bases](https://www.qnamaker.ai/Home/MyServices). Zodra u weet hoe u de API voor het genereren van een antwoord gebruikt, kunt u de API gebruiken met elke taal of elk HTTP-aanvraagframework. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST-API voor QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
