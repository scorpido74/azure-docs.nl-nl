---
title: 'Quickstart: Antwoord uit knowledge base ophalen - REST, Python - QnA Maker'
description: Deze Python REST-quickstart begeleidt u bij het programmatisch ophalen van een antwoord uit een knowledge base.
ms.topic: quickstart
ms.date: 01/28/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCHANGE-20200128
ms.openlocfilehash: f439a492e2e63e3f99f80004b387d9cfc415e4b0
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842951"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-python"></a>Snelstartgids: Krijg antwoorden op een vraag van een kennis database met python

In deze quickstart wordt beschreven hoe u programmatisch een antwoord uit een gepubliceerde QnA Maker-knowledge base kunt ophalen. De Knowledge Base bevat vragen en antwoorden van [gegevens bronnen](../Concepts/knowledge-base.md) , zoals Veelgestelde vragen. De [vraag](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) wordt verzonden naar de QnA Maker-service. Het [antwoord](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) bevat het meest voorspelde antwoord.

[Referentie documentatie](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | voor [beeld](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/get-answer/get-answer-3x.py)

## <a name="prerequisites"></a>Vereisten

* [Python 3.6 of hoger](https://www.python.org/downloads/)
* [Visual Studio Code](https://code.visualstudio.com/)
* U moet een [QnA Maker-service ](../How-To/set-up-qnamaker-service-azure.md) hebben. Om uw sleutel op te halen, selecteert u in het Azure-dashboard voor uw QnA Maker-resource de optie **Sleutels** onder **Resourcebeheer**.
* Pagina Instellingen voor **Publiceren**. Als u nog geen knowledge base hebt gepubliceerd, moet u een lege knowledge base maken, een knowledge base importeren op de pagina **Instellingen** en de knowledge base publiceren. U kunt [deze eenvoudige knowledge base](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv) downloaden en gebruiken.

    De pagina Instellingen voor Publiceren bevat de waarde voor POST-route, Host en EndpointKey.

    ![Instellingen voor Publiceren](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-python-file"></a>Een Python-bestand maken

Open VSCode en maak een nieuw bestand met de naam `get-answer-3x.py`.

## <a name="add-the-required-dependencies"></a>De vereiste afhankelijkheden toevoegen

Voeg bovenaan het bestand `get-answer-3x.py` de vereiste afhankelijkheden toe aan het project:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=1-2 "Add the required dependencies")]

<!--TBD - reword this following paragraph -->

De host en route zijn anders dan ze worden weergegeven op de pagina **Publiceren**. Dit komt omdat de Python-bibliotheek geen routering in de host toestaat. De routering die op de pagina **Publiceren** wordt weergegeven als onderdeel van de host, is verplaatst naar de route.

## <a name="add-the-required-constants"></a>De vereiste constanten toevoegen

Voeg de vereiste constanten toe voor toegang tot QnA Maker. Deze waarden vindt u op de pagina **Publiceren** nadat u de knowledge base hebt gepubliceerd.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=5-25 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Een POST-aanvraag toevoegen om vragen te verzenden en antwoorden op te halen

Met de volgende code wordt een HTTPS-aanvraag naar de QnA Maker-API verzonden om de vraag naar de knowledge base te verzenden en het antwoord te ontvangen:

[!code-python[Add a POST request to send question to knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=27-48 "Add a POST request to send question to knowledge base")]

De waarde van de header van `Authorization` bevat de tekenreeks `EndpointKey`.

## <a name="run-the-program"></a>Het programma uitvoeren

Voer het programma uit vanaf de opdrachtregel. De aanvraag wordt automatisch verzonden naar de QnA Maker-API, waarna het antwoord wordt weergegeven in het consolevenster.

Het bestand uitvoeren:

```bash
python get-answer-3x.py
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

Meer informatie over de [aanvraag](../how-to/metadata-generateanswer-usage.md#generateanswer-request) en het [antwoord](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST API voor QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
