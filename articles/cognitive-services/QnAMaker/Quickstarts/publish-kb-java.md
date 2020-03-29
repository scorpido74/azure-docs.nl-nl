---
title: 'Quickstart: Publiceer kennisbank, REST, Java - QnA Maker'
description: Deze Java REST-gebaseerde quickstart publiceert uw kennisbank en creëert een eindpunt dat kan worden aangeroepen in uw applicatie of chat bot.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 149d7963f29bf041cda75fffaac533e0a62ee7a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851683"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Snelstart: Een knowledge base publiceren in QnA Maker met behulp van Java

In deze REST-quickstart wordt beschreven hoe u programmatisch uw KB (knowledge base ) kunt publiceren. Publishing pusht de nieuwste versie van de kennisbank naar een speciale Azure Cognitive Search-index en creëert een eindpunt dat kan worden aangeroepen in uw toepassing of chatbot.

In deze snelstart worden QnA Maker-API's aangeroepen:
* [Publiceren](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish): voor deze API zijn er geen gegevens in de hoofdtekst van de aanvraag nodig.

## <a name="prerequisites"></a>Vereisten

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* In dit voorbeeld wordt de Apache [HTTP-client](https://hc.apache.org/httpcomponents-client-ga/) uit HTTP Components gebruikt. U moet de volgende Apache HTTP-clientbibliotheken toevoegen aan uw project:
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio-code](https://code.visualstudio.com/)
* U moet een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md)hebben. Als u uw sleutel en eindpunt (inclusief de naam van de bron) wilt ophalen, selecteert u **Quickstart** voor uw resource in de Azure-portal.
* Kb-id (QnA Maker Knowledge Base) `kbid` gevonden in de URL in de parameter querytekenreeks zoals hieronder weergegeven.

    ![Id voor knowledge base in QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Als u nog geen knowledge base hebt, kunt u een voorbeeldexemplaar maken om te gebruiken met deze snelstart: [Een nieuwe knowledge base maken](create-new-kb-csharp.md).

> [!NOTE]
> Het bestand of de bestanden van de volledige oplossing zijn beschikbaar in de GitHub-opslagplaats [**Azure-Samples/cognitive-services-qnamaker-java**](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-java-file"></a>Een Java-bestand maken

Open VSCode en maak een nieuw bestand met de naam `PublishKB.java`.

## <a name="add-the-required-dependencies"></a>De vereiste afhankelijkheden toevoegen

Voeg bovenaan `PublishKB.java`, boven de klasse, de volgende regels toe om de nodige afhankelijkheden aan het project toe te voegen:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>De klasse PublishKB maken met de methode Main

Voeg na de afhankelijkheden de volgende klasse toe:

```Go
public class PublishKB {

    public static void main(String[] args)
    {
    }
}
```

## <a name="add-required-constants"></a>Vereiste constanten toevoegen

Voeg in de methode **Main** de vereiste constanten toe voor toegang tot QnA Maker. Vervang de waarden door uw eigen waarden.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>POST-aanvraag toevoegen om knowledge base te publiceren

Voeg na de vereiste constanten de volgende code toe, waarmee een HTTP-aanvraag naar de QnA Maker-API wordt gedaan voor het publiceren van een knowledge base en het volgende antwoord wordt ontvangen:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

De API-aanroep retourneert een 204-status voor een geslaagde publicatie zonder enige inhoud in de hoofdtekst van het antwoord. De code voegt inhoud toe voor 204-antwoorden.

Voor alle andere antwoorden wordt het antwoord ongewijzigd geretourneerd.

## <a name="build-and-run-the-program"></a>Het programma bouwen en uitvoeren

Compileer het programma en voer het uit vanaf de opdrachtregel. De aanvraag wordt automatisch verzonden naar de QnA Maker-API, waarna het antwoord wordt weergegeven in het consolevenster.

1. Het bestand compileren:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Het bestand uitvoeren:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

Nadat de knowledge base is gepubliceerd, moet u ervoor zorgen dat via de [eindpunt-URL een antwoord wordt gegenereerd](./get-answer-from-knowledge-base-java.md).

> [!div class="nextstepaction"]
> [Naslaginformatie over REST-API voor QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)