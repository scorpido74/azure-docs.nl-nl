---
title: 'Quickstart: Publiceer kennisbank, REST, Python - QnA Maker'
description: Deze Python REST-gebaseerde quickstart publiceert uw kennisbank en creëert een eindpunt dat kan worden aangeroepen in uw applicatie of chatbot.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 6f053ecbd58d3c2527c1b904437dcc4715c76af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851650"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>Snelstart: Een knowledge base in QnA Maker publiceren met behulp van Python

In deze REST-quickstart wordt beschreven hoe u programmatisch uw KB (knowledge base ) kunt publiceren. Publishing pusht de nieuwste versie van de kennisbank naar een speciale Azure Cognitive Search-index en creëert een eindpunt dat kan worden aangeroepen in uw toepassing of chatbot.

Deze quickstart roept QnA Maker REST API's:
* [Publiceren](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish): voor deze API zijn er geen gegevens in de hoofdtekst van de aanvraag nodig.

## <a name="prerequisites"></a>Vereisten

* [Python 3.7](https://www.python.org/downloads/)
* U moet een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md)hebben. Als u uw sleutel en eindpunt (inclusief de naam van de bron) wilt ophalen, selecteert u **Quickstart** voor uw resource in de Azure-portal.
* Kb-id (QnA Maker Knowledge Base) `kbid` gevonden in de URL in de parameter querytekenreeks zoals hieronder weergegeven.

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

Voeg na de bovenstaande vereiste afhankelijkheden de vereiste constanten toe voor toegang tot QnA Maker. Vervang de waarden door uw eigen waarden.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=5-15 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>POST-aanvraag toevoegen om knowledge base te publiceren

Voeg na de vereiste constanten de volgende code toe, waarmee een HTTP-aanvraag naar de QnA Maker-API wordt gedaan voor het publiceren van een knowledge base en het volgende antwoord wordt ontvangen:

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

Nadat de knowledge base is gepubliceerd, moet u ervoor zorgen dat via de [eindpunt-URL een antwoord wordt gegenereerd](./get-answer-from-knowledge-base-python.md).

> [!div class="nextstepaction"]
> [Naslaginformatie over REST-API voor QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)

[Overzicht van QnA Maker](../Overview/overview.md)