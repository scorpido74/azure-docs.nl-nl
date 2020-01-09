---
title: 'Snelstartgids: Knowledge Base, REST, go-QnA Maker publiceren'
titleSuffix: Azure Cognitive Services
description: Deze go op REST gebaseerde Snelstartgids publiceert uw Knowledge Base en maakt een eind punt dat kan worden aangeroepen in uw toepassing of chat-bot.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: c66f4f2b126b3b2b86ea55112d1bdc79b4bf4571
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447447"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Snelstart: Een knowledge base publiceren in QnA Maker met behulp van Go

In deze op REST gebaseerde quickstart wordt beschreven hoe u programmatisch uw KB (knowledge base ) kunt publiceren. Publicatie duwt de nieuwste versie van de Knowledge Base naar een speciale Azure Cognitive Search-index en maakt een eind punt dat kan worden aangeroepen in uw toepassing of chat-bot.

In deze snelstart worden QnA Maker-API's aangeroepen:
* [Publiceren](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish): voor deze API zijn er geen gegevens in de hoofdtekst van de aanvraag nodig.

## <a name="prerequisites"></a>Vereisten

* [Go 1.10.1](https://golang.org/dl/)
* U moet een [QnA Maker-service ](../How-To/set-up-qnamaker-service-azure.md) hebben. Als u de sleutel en het eind punt (inclusief de resource naam) wilt ophalen, selecteert u **Quick** start voor uw resource in het Azure Portal.

* QnA Maker Knowledge Base-ID (KB) gevonden in de URL in de query teken reeks parameter `kbid`, zoals hieronder wordt weer gegeven.

    ![Id voor knowledge base in QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Als u nog geen knowledge base hebt, kunt u een voorbeeldexemplaar maken om te gebruiken met deze snelstart: [Een nieuwe knowledge base maken](create-new-kb-csharp.md).

> [!NOTE]
> Het bestand of de bestanden van de volledige oplossing zijn beschikbaar in de GitHub-opslagplaats [**Azure-Samples/cognitive-services-qnamaker-go**](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-go-file"></a>Een Go-bestand maken

Open VSCode en maak een nieuw bestand met de naam `publish-kb.go`.

## <a name="add-the-required-dependencies"></a>De vereiste afhankelijkheden toevoegen

Voeg bovenaan `publish-kb.go` de volgende regels toe om de nodige afhankelijkheden aan het project toe te voegen:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=3-7 "Add the required dependencies")]

## <a name="create-the-main-function"></a>De hoofdfunctie toevoegen

Voeg na de vereiste afhankelijkheden de volgende klasse toe:

```Go
package main

func main() {

}
```

## <a name="add-required-constants"></a>Vereiste constanten toevoegen

Voeg in de **main**-functie


 de vereiste constanten toe voor toegang tot QnA Maker. Vervang de waarden door uw eigen waarden.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=16-20 "Add the required constants")]

## <a name="add-post-request-to-publish-kb"></a>POST-aanvraag voor het publiceren van KB toevoegen

Voeg na de vereiste constanten de volgende code toe, waarmee een HTTP-aanvraag naar de QnA Maker-API wordt gedaan voor het publiceren van een knowledge base en het volgende antwoord wordt ontvangen:

[!code-go[Add a POST request to publish KB](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to publish KB")]

De API-aanroep retourneert een 204-status voor een geslaagde publicatie zonder enige inhoud in de hoofdtekst van het antwoord. De code voegt inhoud toe voor 204-antwoorden.

Voor alle andere antwoorden wordt het antwoord ongewijzigd geretourneerd.

## <a name="build-and-run-the-program"></a>Het programma bouwen en uitvoeren

Voer de volgende opdracht in voor het compileren van het bestand. De opdrachtprompt retourneert geen informatie voor een geslaagde build.

```bash
go build publish-kb.go
```

Voer de volgende opdracht op een opdrachtregel in om het programma uit te voeren. Hiermee wordt de aanvraag verzonden naar de QnA Maker-API om de KB te publiceren. Vervolgens worden 204-berichten afgedrukt over geslaagde of mislukte publicaties.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

Nadat de knowledge base is gepubliceerd, moet u ervoor zorgen dat via de [eindpunt-URL een antwoord wordt gegenereerd](../Tutorials/create-publish-answer.md#generating-an-answer).

> [!div class="nextstepaction"]
> [Naslaginformatie over REST API voor QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
