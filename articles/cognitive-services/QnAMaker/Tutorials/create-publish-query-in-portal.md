---
title: 'Zelf studie: maken, publiceren en beantwoorden QnA Maker'
description: Deze zelf studie laat zien hoe u een nieuwe Knowledge Base maakt met vragen en antwoorden van een open bare webgebaseerde Veelgestelde vragen. De Knowledge Base opslaan, trainen en publiceren. Nadat de Knowledge Base is gepubliceerd, kunt u een vraag verzenden en een antwoord met een krul opdracht ontvangen. Maak vervolgens een bot en test de bot met dezelfde vraag.
ms.topic: tutorial
ms.date: 01/28/2020
ms.openlocfilehash: 07a92b0f24aa95fc4a5188f54c7194fd342409fb
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844148"
---
# <a name="tutorial-from-the-qna-maker-portal-create-a-knowledge-base"></a>Zelf studie: een Knowledge Base maken vanuit de QnA Maker Portal

Maak een nieuwe Knowledge Base met vragen en antwoorden van een open bare webgebaseerde Veelgestelde vragen. De Knowledge Base opslaan, trainen en publiceren. Nadat de Knowledge Base is gepubliceerd, kunt u een vraag verzenden en een antwoord met een krul opdracht ontvangen. Maak vervolgens een bot en test de bot met dezelfde vraag.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een Knowledge Base in de QnA Maker Portal.
> * De Knowledge Base controleren, opslaan en trainen.
> * Publiceer de Knowledge Base.
> * Gebruik krul om de Knowledge Base te doorzoeken.
> * Maak een bot.


> [!NOTE]
> De programmaversie van deze zelfstudie is beschikbaar met een volledige oplossing in de GitHub-opslagplaats [**Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie moet u beschikken over een bestaande [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md).

## <a name="create-a-knowledge-base"></a>Een knowledge base maken

1. Aanmelden bij de [QnA Maker](https://www.qnamaker.ai)-portal.

1. Selecteer **Een knowledge base maken** in het menu bovenaan.

    > [!div class="mx-imgBorder"]
    > Scherm afbeelding van QnA Maker Portal ![](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Sla de eerste stap over, omdat u uw bestaande QnA Maker-service gaat gebruiken.

1. Selecteer de bestaande instellingen:

    |Instelling|Doel|
    |--|--|
    |Microsoft Azure Directory-ID|Deze ID is gekoppeld aan het account dat u gebruikt om u aan te melden bij de Azure Portal en de QnA Maker-Portal. |
    |Azure-abonnements-id|Het facturerings account waarin u de QnA Maker resource hebt gemaakt.|
    |Azure QnA Service|Uw bestaande QnA Maker-resource.|
    |Taal|De taal die wordt gebruikt voor alle Knowledge bases in de service. Maak geen talen in één resource. Dat de antwoord kwaliteit kan schaden.|

    > [!div class="mx-imgBorder"]
    > Scherm afbeelding van QnA Maker Portal ![](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. Voer de naam van uw Knowledge Base in `My Tutorial kb`.

    > [!div class="mx-imgBorder"]
    > Scherm afbeelding van QnA Maker Portal ![](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. Vul uw Knowledge Base in met de volgende instellingen:

    |Naam van instelling|Instellingswaarde|Doel|
    |--|--|--|
    |Meerdere Schakel extractie inschakelen|Geselecteerd|Hiermee kunt u opvolgings vragen.|
    |Standaard antwoord tekst|`No good answer was found.`|Retourneert wanneer er geen overeenkomst is met de vraag.|
    |URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |De inhoud van de veelgestelde vragen bij die URL zijn ingedeeld met een vraag gevolgd door een antwoord. QnA Maker kan deze indeling interpreteren om vragen en de bijbehorende antwoorden te extraheren.|
    |Bestand |_niet gebruikt in deze zelfstudie_|Hiermee worden bestanden voor vragen en antwoorden geüpload. |
    |De persoonlijkheid 'Heen- en weergepraat'|Weergave|Dit geeft een beschrijvende en onduidelijke [persoonlijkheid](../Concepts/best-practices.md#chit-chat) voor veelgestelde vragen en antwoorden. U kunt deze vragen en antwoorden later bewerken. |

    > [!div class="mx-imgBorder"]
    > Scherm afbeelding van QnA Maker Portal ![](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Selecteer **Uw KB maken** om het proces te voltooien.

    > [!div class="mx-imgBorder"]
    > Scherm afbeelding van QnA Maker Portal ![](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-save-and-train-the-knowledge-base"></a>De knowledge base controleren, opslaan en trainen

1. Controleer de vragen en antwoorden. De eerste pagina bestaat uit vragen en antwoorden uit de URL.

    > [!div class="mx-imgBorder"]
    > Scherm afbeelding van QnA Maker Portal ![](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. Selecteer de laatste pagina van de vragen en antwoorden vanaf de onderkant van de tabel. De pagina toont vragen en antwoorden van de persoonlijkheid 'Heen- en weergepraat'.

1. Selecteer in de werk balk boven de lijst met vragen en antwoorden het pictogram **weergave opties** en selecteer vervolgens **meta gegevens weer geven**. Hiermee worden de tags met metagegevens voor elke vraag en antwoord weergegeven. Voor de heen- en weergepraatvragen zijn de **editorial: chit-chat**-metagegevens al ingesteld. Deze meta gegevens worden geretourneerd naar de client toepassing, samen met het geselecteerde antwoord. De clienttoepassing, bijvoorbeeld een chatbot, kan deze gefilterde metagegevens gebruiken om extra verwerking of interactie met de gebruiker te bepalen.

    ![Scherm opname van QnA Maker Portal](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. Selecteer **Opslaan en trainen** in de bovenste menubalk.

## <a name="publish-to-get-knowledge-base-endpoints"></a>Publiceren om Knowledge Base-eind punten op te halen

Selecteer de knop **Publiceren** in het menu bovenaan. Selecteer **Publiceren** op de publicatiepagina.

![Scherm opname van QnA Maker Portal](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

Nadat de Knowledge Base is gepubliceerd, wordt het eind punt weer gegeven.

![Scherm opname van eindpunt instellingen](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Sluit deze **publicatie** pagina niet. U hebt deze later in de zelf studie nodig om een bot te maken.

## <a name="use-curl-to-query-for-an-faq-answer"></a>Krul gebruiken om een antwoord op een veelgestelde vragen te zoeken

1. Selecteer het tabblad **Curl**.

    ![Scherm afbeelding van het tabblad krul](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Kopieer de tekst van het tabblad **krul** en voer deze uit in een op krul ingeschakelde terminal of opdracht regel. De waarde van de autorisatie-header bevat de tekst `Endpoint`, met een spatie en vervolgens de sleutel.

1. Vervang `<Your question>` door `How large can my KB be?`. Dit komt dicht bij de vraag, `How large a knowledge base can I create?`, in de buurt, maar is niet precies hetzelfde. QnA Maker past natuurlijke taalverwerking toe om te bepalen of de twee vragen hetzelfde zijn.

1. Voer de opdracht krul uit en ontvang het JSON-antwoord, met inbegrip van de score en het antwoord.

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 42.81,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }

    ```

    QnA Maker is enigszins zeker met de score van 42.81%.

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Krul gebruiken om te zoeken naar een Chit-Chat-antwoord

1. Vervang in de met krul ingeschakelde Terminal `How large can my KB be?` door een bot Conversation-eind instructie van de gebruiker, zoals `Thank you`.

1. Voer de opdracht krul uit en ontvang het JSON-antwoord, met inbegrip van de score en het antwoord.

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }

    ```

    Omdat de vraag `Thank you` precies overeenkwam met een heen- en weergepraatvraag, is QnA Maker volledig zeker met de score van 100. QnA Maker heeft ook alle gerelateerde vragen geretourneerd, evenals de meta gegevens eigenschap met de Chit-Chat-meta gegevens code gegevens.

## <a name="use-curl-to-query-for-the-default-answer"></a>Krul gebruiken om het standaard antwoord op te vragen

Elke vraag die QnA Maker niet zeker weet hoe het standaard antwoord wordt ontvangen. Dit antwoord wordt geconfigureerd in Azure Portal.

1. Vervang `Thank you` door `x`in de krul geschakelde Terminal.

1. Voer de opdracht krul uit en ontvang het JSON-antwoord, met inbegrip van de score en het antwoord.

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```

    QnA Maker heeft een score geretourneerd van `0`, wat geen betrouw baarheid betekent. Ook wordt het standaard antwoord geretourneerd.

## <a name="create-a-knowledge-base-bot"></a>Een Knowledge Base-bot maken

Zie [een chat-bot maken met deze Knowledge Base](create-qna-bot.md)voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de bot van de Knowledge Base, verwijdert u de resource groep `my-tutorial-rg`om alle Azure-resources te verwijderen die in het bot-proces zijn gemaakt.

Wanneer u klaar bent met de Knowledge Base, selecteert u in de QnA Maker Portal **mijn Knowledge bases**. Vervolgens selecteert u de Knowledge Base, de **zelf studie KB**en selecteert u het pictogram verwijderen helemaal rechts in die rij.

## <a name="next-steps"></a>Volgende stappen

Zie [Ondersteunde gegevensbronnen](../Concepts/content-types.md) voor meer informatie over ondersteunde bestandsindelingen.

Lees meer over [persoonlijkheden](../Concepts/best-practices.md#chit-chat) voor heen- en weergepraat.

Zie [Geen overeenkomst gevonden](../Concepts/confidence-score.md#no-match-found) voor meer informatie over het standaardantwoord.

> [!div class="nextstepaction"]
> [Een chat-bot maken met deze kennis database](create-qna-bot.md)