---
title: 'Snelstartgids: Knowledge Base publiceren, REST, C#-QnA Maker'
description: Met deze op C# REST gebaseerde Snelstartgids publiceert u uw Knowledge Base en maakt u een eind punt dat kan worden aangeroepen in uw toepassing of chat-bot.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-csharp
ms.topic: how-to
ms.openlocfilehash: ccad31c5a67eaec53ee9366961187bb1ed235283
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934154"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Snelstart: Een knowledge base publiceren in QnA Maker met behulp van C#

In deze REST-quickstart wordt beschreven hoe u programmatisch uw KB (knowledge base ) kunt publiceren. Publicatie duwt de nieuwste versie van de Knowledge Base naar een speciale Azure Cognitive Search-index en maakt een eind punt dat kan worden aangeroepen in uw toepassing of chat-bot.

In deze snelstart worden QnA Maker-API's aangeroepen:
* [Publiceren](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish): voor deze API zijn er geen gegevens in de hoofdtekst van de aanvraag nodig.

## <a name="prerequisites"></a>Vereisten

* De meest recente [**Visual Studio Community edition**](https://www.visualstudio.com/downloads/).
* U moet een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md)hebben. Als u de sleutel en het eind punt (inclusief de resource naam) wilt ophalen, selecteert u **Quick** start voor uw resource in het Azure Portal.
* QnA Maker Knowledge Base-ID (KB) gevonden in de URL in de `kbid` query teken reeks parameter, zoals hieronder wordt weer gegeven.

    ![Id voor knowledge base in QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Als u nog geen knowledge base hebt, kunt u een voorbeeldexemplaar maken om te gebruiken met deze snelstart: [Een nieuwe knowledge base maken](create-new-kb-csharp.md).

> [!NOTE]
> De volledige oplossings bestanden zijn beschikbaar via de [ **Azure-samples/cognitieve-Services-qnamaker-csharp github-** opslag plaats](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-knowledge-base-project"></a>Een project met knowledge base maken

1. Open Visual Studio 2019 Community Edition.
1. Maak een nieuw **console-app (.net core)-** project en noem het project `QnaMakerQuickstart` . Accepteer de standaardwaarden voor de overige instellingen.

## <a name="add-required-dependencies"></a>Vereiste afhankelijkheden toevoegen

Boven in Program.cs vervangt u de enige using-instructie door de volgende regels. U voegt op deze manier de benodigde afhankelijkheden toe aan het project:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>Vereiste constanten toevoegen

Voeg in de klasse **Program** de vereiste constanten toe om toegang te krijgen tot QnA Maker.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=8-34 "Add the required constants")]

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>De methode Main voor het publiceren van de Knowledge Base toevoegen

Voeg na de vereiste constanten de volgende code toe, waarmee een HTTP-aanvraag naar de QnA Maker-API wordt gedaan voor het publiceren van een knowledge base en het volgende antwoord wordt ontvangen:

[!code-csharp[Add HTTP Post request and response](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=36-56 "Add HTTP Post request and response")]

De API-aanroep retourneert een 204-status voor een geslaagde publicatie zonder enige inhoud in de hoofdtekst van het antwoord.

## <a name="build-and-run-the-program"></a>Het programma bouwen en uitvoeren

Bouw het programma en voer het uit. De aanvraag wordt automatisch verzonden naar de QnA Maker-API om de knowledge base te publiceren en vervolgens wordt het antwoord weergegeven in het consolevenster.

Zodra uw knowledge base is gepubliceerd, kunt u deze opvragen van het eindpunt met behulp van een clienttoepassing of chatbot.

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

Nadat de knowledge base is gepubliceerd, moet u ervoor zorgen dat via de [eindpunt-URL een antwoord wordt gegenereerd](./get-answer-from-knowledge-base-csharp.md).

> [!div class="nextstepaction"]
> [Naslaginformatie over REST-API voor QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)