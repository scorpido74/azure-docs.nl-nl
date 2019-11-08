---
title: 'Snelstartgids: Knowledge Base publiceren, REST, python-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Met deze python REST-gebaseerde Snelstartgids publiceert u uw Knowledge Base en maakt u een eind punt dat kan worden aangeroepen in uw toepassing of chat-bot.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 87913d4b1a6a01b91a74d7c4b0c41a66557b86f1
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794066"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>Snelstart: Een knowledge base in QnA Maker publiceren met behulp van Python

In deze op REST gebaseerde quickstart wordt beschreven hoe u programmatisch uw KB (knowledge base ) kunt publiceren. Publicatie duwt de nieuwste versie van de Knowledge Base naar een speciale Azure Cognitive Search-index en maakt een eind punt dat kan worden aangeroepen in uw toepassing of chat-bot.

Deze Snelstartgids roept QnA Maker REST Api's aan:
* [Publiceren](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - voor deze API zijn geen gegevens in de hoofdtekst van de aanvraag nodig.

## <a name="prerequisites"></a>Vereisten

* [Python 3.7](https://www.python.org/downloads/)
* U moet een [QnA Maker-service ](../How-To/set-up-qnamaker-service-azure.md) hebben. Als u de sleutel en het eind punt (inclusief de resource naam) wilt ophalen, selecteert u **Quick** start voor uw resource in het Azure Portal.
* Id voor knowledge base (KB) in QnA Maker gevonden in de URL in de parameter voor de kbid-queryreeks, zoals hieronder wordt weergegeven.

    ![Id voor knowledge base in QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Als u nog geen knowledge base hebt, kunt u een voorbeeldexemplaar maken om te gebruiken met deze snelstart: [Een nieuwe knowledge base maken](../how-to/create-knowledge-base.md).

> [!NOTE] 
> Het bestand of de bestanden van de volledige oplossing zijn beschikbaar in de GitHub-opslagplaats [**Azure-Samples/cognitive-services-qnamaker-python**](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-knowledge-base-python-file"></a>Een Python-bestand met knowledge base maken

Maak een bestand met de naam `publish-kb-3x.py`.

## <a name="add-the-required-dependencies"></a>De vereiste afhankelijkheden toevoegen

Voeg bovenaan `publish-kb-3x.py` de volgende regels toe om de nodige afhankelijkheden aan het project toe te voegen:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-required-constants"></a>Vereiste constanten toevoegen

Voeg de vereiste constanten toe voor toegang tot QnA Maker na de bovenstaande vereiste afhankelijkheden. Vervang de waarden door uw eigen waarden.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=5-15 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>POST-aanvraag toevoegen om knowledge base te publiceren

Voeg na de vereiste constanten de volgende code toe, waarmee een HTTP-aanvraag bij de QnA Maker-API wordt gedaan voor het publiceren van een knowledge base en het volgende antwoord wordt ontvangen:

[!code-python[Add a POST request to publish knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=17-26 "Add a POST request to publish knowledge base")]

De API-aanroep retourneert een 204-status voor een geslaagde publicatie zonder enige inhoud in de hoofdtekst van het antwoord. De code voegt inhoud toe voor 204-antwoorden.

Voor alle andere antwoorden wordt het antwoord ongewijzigd geretourneerd.

## <a name="build-and-run-the-program"></a>Het programma bouwen en uitvoeren

Voer de volgende opdracht op een opdrachtregel in om het programma uit te voeren. Hiermee wordt de aanvraag verzonden naar de QnA Maker-API om de knowledge base te publiceren. Vervolgens worden 204-berichten afgedrukt over geslaagde of mislukte publicaties.

```bash
python publish-kb-3x.py
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Volgende stappen

Nadat de knowledge base is gepubliceerd, moet u ervoor zorgen dat via de [eindpunt-URL een antwoord wordt gegenereerd](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [Naslaginformatie over REST-API voor QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)

[Overzicht van QnA Maker](../Overview/overview.md)
