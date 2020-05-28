---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 04/27/2020
ms.author: diberry
ms.openlocfilehash: d890fcb6a43b43e0be0df8e6f6ff0817bdd03115
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83998032"
---
In deze op krul gebaseerde Snelstartgids wordt u begeleid bij het verkrijgen van een antwoord uit uw Knowledge Base.

## <a name="prerequisites"></a>Vereisten

* Meest recente [**krul**](https://curl.haxx.se/).
* U moet beschikken over
    * Een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md)
    * Een getrainde en gepubliceerde kennis database met vragen en antwoorden, van de vorige [Snelstartgids](../Quickstarts/add-question-metadata-portal.md), geconfigureerd met meta gegevens en Chit Chat.

> [!NOTE]
> Wanneer u klaar bent voor het genereren van een antwoord op een vraag uit uw Knowledge Base, moet u uw kennis basis [trainen](../Quickstarts/create-publish-knowledge-base.md#save-and-train) en [publiceren](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) . Wanneer uw knowledge base is gepubliceerd, geeft de pagina **Publiceren** de instellingen van de HTTP-aanvraag weer voor het genereren van een antwoord. Op het tabblad **krul** worden de instellingen weer gegeven die nodig zijn om een antwoord te genereren vanuit het opdracht regel programma.

## <a name="use-metadata-to-filter-answer"></a>Meta gegevens gebruiken om antwoord te filteren

Gebruik de Knowledge Base van de vorige snel om een antwoord te zoeken op basis van meta gegevens.

1. Selecteer op de pagina **instellingen** van de Knowledge Base het tabblad **krul** om een voor beeld van een krul opdracht te zien die wordt gebruikt voor het genereren van een antwoord uit de Knowledge Base.
1. Kopieer de opdracht naar een Bewerk bare omgeving (zoals een tekst bestand), zodat u de opdracht kunt bewerken. Bewerk de waarde van de vraag als volgt zodat de meta gegevens van `service:qna_maker` worden gebruikt als een filter voor de QnA-paren.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    De vraag is slechts één woord, `size` waardoor een van de twee QnA-paren kan worden geretourneerd. De `strictFilters` matrix vertelt het antwoord om te beperken tot alleen de `qna_maker` antwoorden.

1. Het antwoord bevat alleen het antwoord dat voldoet aan de filter criteria. De volgende krul reactie is opgemaakt voor de Lees baarheid:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Als er een vraag-en antwoord paar is dat niet voldoet aan de zoek term maar wel aan het filter voldoet, wordt het niet geretourneerd. In plaats daarvan wordt het algemene antwoord `No good match found in KB.` geretourneerd.

## <a name="use-debug-query-property"></a>Eigenschap debug query gebruiken

Informatie over fout opsporing helpt u te begrijpen hoe het geretourneerde antwoord is vastgesteld. Hoewel het handig is, is het niet nodig. Als u een antwoord met foutopsporingsinformatie wilt genereren, voegt u de `debug` eigenschap toe:

```json
Debug: {Enable:true}
```

1. Bewerk de krul opdracht zodat u de eigenschap debug kunt gebruiken om meer informatie weer te geven.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'Debug':{'Enable':true}}"
    ```

1. Het antwoord bevat de relevante informatie over het antwoord. In de volgende JSON-uitvoer zijn een aantal fout opsporingsgegevens vervangen door het weglatings teken.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) to learn how to collaborate on a knowledge base.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>Test kennis basis gebruiken

Als u een antwoord wilt ontvangen van de test Knowledge Base, gebruikt u de `isTest` eigenschap Body.

De eigenschap is een Booleaanse waarde.

```json
isTest:true
```

De krul opdracht ziet er als volgt uit:

```bash
curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'IsTest':true}"
```

In het JSON-antwoord wordt hetzelfde schema gebruikt als voor de gepubliceerde Knowledge Base-query.

> [!NOTE]
> Als de test en de gepubliceerde kennis bases exact hetzelfde zijn, kan er nog een kleine variatie optreden omdat de test index wordt gedeeld tussen alle kennis grondslagen in de resource.

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Krul gebruiken om te zoeken naar een Chit-Chat-antwoord

1. Gebruik in de met krul geactiveerde Terminal een bot-instructie voor het beëindigen van de gebruiker, zoals `Thank you` de vraag. Er zijn geen andere eigenschappen om in te stellen.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'thank you'}"
    ```

1. Voer de opdracht krul uit en ontvang het JSON-antwoord, met inbegrip van de score en het antwoord.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    Omdat de vraag `Thank you` precies overeenkwam met een heen- en weergepraatvraag, is QnA Maker volledig zeker met de score van 100. QnA Maker heeft ook alle gerelateerde vragen geretourneerd, evenals de meta gegevens eigenschap met de Chit-Chat-meta gegevens code gegevens.

## <a name="use-threshold-and-default-answer"></a>Drempel en standaard antwoord gebruiken

U kunt een minimale drempel waarde voor het antwoord aanvragen. Als niet wordt voldaan aan de drempel waarde, wordt het standaard antwoord geretourneerd.

1. Voeg de `threshold` eigenschap toe om te vragen om een antwoord `size` met een drempel van 80% of hoger. De Knowledge Base mag dat antwoord niet vinden omdat de Score van de vraag 71% is. Het resultaat retourneert het standaard antwoord dat u hebt gegeven tijdens het maken van de Knowledge Base.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```

1. Voer de opdracht krul uit en ontvang het JSON-antwoord.

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker heeft een score geretourneerd van `0` , wat betekent dat u geen betrouw baarheid hebt. Ook wordt het standaard antwoord geretourneerd.

1. Wijzig de drempel waarde in 60% en vraag de query opnieuw aan:

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':60.00}"
    ```

    De geretourneerde JSON heeft het antwoord gevonden.

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```
