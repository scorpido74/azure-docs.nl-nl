---
title: 'Quickstart: Antwoord uit knowledge base ophalen - REST, Java - QnA Maker'
description: Deze Java REST-quickstart begeleidt u bij het programmatisch ophalen van een antwoord uit een knowledge base.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 67f09b6d1e284cdf35825a2e584b372bd2adf70a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78851738"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Quick Start: antwoorden vinden op een vraag van een Knowledge Base met Java

In deze quickstart wordt beschreven hoe u programmatisch een antwoord uit een gepubliceerde QnA Maker-knowledge base kunt ophalen. De Knowledge Base bevat vragen en antwoorden van [gegevens bronnen](../Concepts/knowledge-base.md) , zoals Veelgestelde vragen. De [vraag](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) wordt verzonden naar de QnA Maker-service. Het [antwoord](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) bevat het meest voorspelde antwoord.

[Referentie documentatie](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [voorbeeld](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/get-answer/GetAnswer.java)

## <a name="prerequisites"></a>Vereisten

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* In dit voorbeeld wordt de Apache [HTTP-client](https://hc.apache.org/httpcomponents-client-ga/) uit HTTP Components gebruikt. U moet de volgende Apache HTTP-clientbibliotheken toevoegen aan uw project:
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* U moet een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md)hebben. Selecteer **Sleutels** onder **Resourcebeheer** in het Azure-dashboard voor de QnA Maker-resource om uw sleutel op te halen.
* Pagina Instellingen voor **Publiceren**. Als u nog geen knowledge base hebt gepubliceerd, moet u een lege knowledge base maken, een knowledge base importeren op de pagina **Instellingen** en de knowledge base publiceren. U kunt [deze eenvoudige knowledge base](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv) downloaden en gebruiken.

    De pagina Instellingen voor Publiceren bevat de waarde voor POST-route, Host en EndpointKey.

    ![Instellingen voor Publiceren](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-java-file"></a>Een Java-bestand maken

Open VSCode, maak een nieuw bestand met de naam `GetAnswer.java` en voeg de volgende klasse toe:

```Java
public class GetAnswer {

    public static void main(String[] args)
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>De vereiste afhankelijkheden toevoegen

In deze quickstart worden Apache-klassen voor HTTP-aanvragen gebruikt. Voeg boven de klasse GetAnswer, boven in het bestand `GetAnswer.java`, de vereiste afhankelijkheden toe aan het project:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=5-13 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>De vereiste constanten toevoegen

Voeg boven aan de klasse `GetAnswer.java` de vereiste constanten toe voor toegang tot QnA Maker. Deze waarden vindt u op de pagina **Publiceren** nadat u de knowledge base hebt gepubliceerd.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=26-42 "Add the required constants")]

## <a name="add-a-post-request-to-send-question"></a>Een POST-aanvraag voor het verzenden van een vraag toevoegen

Met de volgende code wordt een HTTPS-aanvraag naar de QnA Maker-API verzonden om de vraag naar de knowledge base te verzenden en het antwoord te ontvangen:

[!code-java[Add a POST request to send question to knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=44-72 "Add a POST request to send question to knowledge base")]

De waarde van de header van `Authorization` bevat de tekenreeks `EndpointKey`.

Meer informatie over de [aanvraag](../how-to/metadata-generateanswer-usage.md#generateanswer-request) en het [antwoord](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Het programma bouwen en uitvoeren

Compileer het programma en voer het uit vanaf de opdrachtregel. De aanvraag wordt automatisch verzonden naar de QnA Maker-API, waarna het antwoord wordt weergegeven in het consolevenster.

1. Het bestand compileren:

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. Het bestand uitvoeren:

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST-API voor QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)