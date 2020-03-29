---
title: 'Quickstart: Publiceer kennisbank, REST, C# - QnA Maker'
description: Deze C# REST-based quickstart publiceert uw kennisbank en creëert een eindpunt dat kan worden aangeroepen in uw applicatie of chatbot.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 0a2f64795d82928e33a10c7e9d162f2333cdf7bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851705"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Snelstart: Een knowledge base publiceren in QnA Maker met behulp van C#

In deze REST-quickstart wordt beschreven hoe u programmatisch uw KB (knowledge base ) kunt publiceren. Publishing pusht de nieuwste versie van de kennisbank naar een speciale Azure Cognitive Search-index en creëert een eindpunt dat kan worden aangeroepen in uw toepassing of chatbot.

In deze snelstart worden QnA Maker-API's aangeroepen:
* [Publiceren](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish): voor deze API zijn er geen gegevens in de hoofdtekst van de aanvraag nodig.

## <a name="prerequisites"></a>Vereisten

* De meest recente [**Visual Studio Community edition**](https://www.visualstudio.com/downloads/).
* U moet een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md)hebben. Als u uw sleutel en eindpunt (inclusief de naam van de bron) wilt ophalen, selecteert u **Quickstart** voor uw resource in de Azure-portal.
* Kb-id (QnA Maker Knowledge Base) `kbid` gevonden in de URL in de parameter querytekenreeks zoals hieronder weergegeven.

    ![Id voor knowledge base in QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Als u nog geen knowledge base hebt, kunt u een voorbeeldexemplaar maken om te gebruiken met deze snelstart: [Een nieuwe knowledge base maken](create-new-kb-csharp.md).

> [!NOTE]
> Het volledige oplossingsbestand(en) is beschikbaar in de [ **Azure-Samples/cognitive-services-qnamaker-csharp** GitHub repository.](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base)

## <a name="create-knowledge-base-project"></a>Een project met knowledge base maken

1. Open Visual Studio 2019 Community editie.
1. Maak een nieuw **console-app-project (.NET Core)** en geef een naam aan het project `QnaMakerQuickstart`. Accepteer de standaardwaarden voor de overige instellingen.

## <a name="add-required-dependencies"></a>Vereiste afhankelijkheden toevoegen

Boven in Program.cs vervangt u de enige using-instructie door de volgende regels. U voegt op deze manier de benodigde afhankelijkheden toe aan het project:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>Vereiste constanten toevoegen

Voeg in de klasse **Programma** de vereiste constanten toe om toegang te krijgen tot QnA Maker.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=8-34 "Add the required constants")]

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>De hoofdmethode toevoegen om de kennisbank te publiceren

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