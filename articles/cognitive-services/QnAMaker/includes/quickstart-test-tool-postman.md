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
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 46947579ea72e2199af116442472eec330b38009
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77112344"
---
Deze snelstart op basis van Postman begeleidt u bij het ophalen van een antwoord uit een knowledge base.

## <a name="prerequisites"></a>Vereisten

* Meest recente [**Postman**](https://www.getpostman.com/).
* Je moet.
    * Een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md)
    * Een getrainde en gepubliceerde [kennisbank met vragen en antwoorden](../Quickstarts/add-question-metadata-portal.md) opgebouwd uit de quickstart is geconfigureerd met metadata en Chit chat.

> [!NOTE]
> Wanneer u klaar bent om een antwoord te genereren op een vraag uit uw kennisbank, moet u uw kennisbank [trainen](../Quickstarts/create-publish-knowledge-base.md#save-and-train) en [publiceren.](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) Wanneer uw knowledge base is gepubliceerd, geeft de pagina **Publiceren** de instellingen van de HTTP-aanvraag weer voor het genereren van een antwoord. Op het tabblad **Postbode** worden de instellingen weergegeven die nodig zijn om een antwoord te genereren.

## <a name="set-up-postman-for-requests"></a>Postbode instellen voor aanvragen

Deze quickstart maakt gebruik van dezelfde instellingen voor de Postman **POST-aanvraag** vervolgens configureert om POST lichaam JSON verzonden naar de service op basis van wat u probeert te vragen voor.

Gebruik deze procedure om Postman te configureren en lees vervolgens elke volgende sectie om de JSON van de POST-body te configureren.

1. Selecteer op de pagina **Instellingen van** de kennisbank het tabblad **Postman** om de configuratie te zien die wordt gebruikt om een antwoord uit de kennisbank te genereren. Kopieer de volgende informatie die u wilt gebruiken in Postman.

    |Name|Instelling|Doel en waarde|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Dit is de HTTP-methode en route voor de URL.|
    |`Host`|`https://diberry-qna-s0-s.azurewebsites.net/qnamaker`|Dit is de host van de URL. Geef de waarden Host en Post met elkaar om de volledige URL van generateanswer te krijgen.|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|De kopwaarde voor het autoriseren van uw aanvraag naar Azure. |
    |`Content-type`|`application/json`|De headerwaarde voor uw inhoud.|
    ||`{"question":"<Your question>"}`|De instantie van de POST aanvraag als een JSON object. Deze waarde verandert in elke volgende sectie, afhankelijk van wat de query moet doen.|

1. Open Postman en maak een nieuwe basis **POST-aanvraag** met uw gepubliceerde knowledge base-instellingen. Wijzig in de volgende secties de JSON van de POST-behuizing om de query te wijzigen in uw kennisbank.

## <a name="use-metadata-to-filter-answer"></a>Metagegevens gebruiken om antwoord te filteren

In een vorige quickstart werden metadata toegevoegd aan twee QnA-sets om onderscheid te maken tussen twee verschillende vragen. Voeg de metagegevens toe aan de query om het filter te beperken tot alleen de relevante QnA-set.

1. Wijzig in Postman alleen de queryJSON door de `strictFilters` eigenschap `service:qna_maker`toe te voegen met de naam/waardepaar van . Het lichaam JSON moet zijn:

    ```json
    {
        'question':'size',
        'strictFilters': [
            {
                'name':'service','value':'qna_maker'
            }
        ]
    }
    ```

    De vraag is slechts `size`een enkel woord, die kan terugkeren een van de twee vraag en antwoord sets. De `strictFilters` array vertelt de reactie `qna_maker` te verminderen tot alleen de antwoorden.

1. Het antwoord bevat alleen het antwoord dat voldoet aan de filtercriteria.

    Het volgende antwoord is opgemaakt voor leesbaarheid:

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

    Als er een vraag- en antwoordset is die niet aan de zoekterm voldeed, maar wel aan het filter voldeed, wordt deze niet geretourneerd. In plaats daarvan `No good match found in KB.` wordt het algemene antwoord geretourneerd.

## <a name="use-debug-query-property"></a>Eigenschap foutopsporingsquery gebruiken

Foutopsporingsgegevens helpen u te begrijpen hoe het geretourneerde antwoord is bepaald. Hoewel het nuttig is, is het niet nodig. Als u een antwoord wilt genereren `debug` met foutopsporingsgegevens, voegt u de eigenschap toe:

1. In Postman, verander alleen het lichaam `debug` JSON door het toevoegen van de eigenschap. De JSON moet zijn:

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. Het antwoord bevat de relevante informatie over het antwoord. In de volgende JSON-uitvoer zijn enkele foutopsporingsdetails vervangen door ellips.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) how to collaborate on a knowledge base.",
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

## <a name="use-test-knowledge-base"></a>Testkennisbank gebruiken

Als u een antwoord wilt krijgen van de `isTest` test kennisbank, gebruik maken van het lichaam eigendom.

In Postman, verander alleen het lichaam `isTest` JSON door het toevoegen van de eigenschap. De JSON moet zijn:

```json
{
    'question':'size',
    'isTest': true
}
```

Het JSON-antwoord gebruikt hetzelfde schema als de gepubliceerde knowledge base-query.

> [!NOTE]
> Als de test en gepubliceerde kennisbanken precies hetzelfde zijn, kan er nog steeds een lichte variatie zijn omdat de testindex wordt gedeeld tussen alle kennisbanken in de bron.

## <a name="query-for-a-chit-chat-answer"></a>Vraag voor een Chit-chat antwoord

1. Verander in Postman alleen de JSON van het lichaam in een gesprekseindeverklaring van de gebruiker. De JSON moet zijn:

    ```json
    {
        'question':'thank you'
    }
    ```

1. Het antwoord bevat de score en het antwoord.

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

    Omdat de vraag `Thank you` precies overeenkwam met een heen- en weergepraatvraag, is QnA Maker volledig zeker met de score van 100. QnA Maker heeft ook alle gerelateerde vragen teruggestuurd, evenals de metadata-eigenschap met de informatie over de chit-chat metadata-tag.

## <a name="use-threshold-and-default-answer"></a>Drempelwaarde en standaardantwoord gebruiken

U een minimumdrempel voor het antwoord aanvragen. Als de drempelwaarde niet wordt bereikt, wordt het standaardantwoord geretourneerd.

1. Verander in Postman alleen de JSON van het lichaam in een gesprekseindeverklaring van de gebruiker. De JSON moet zijn:

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    De kennisbank mag dat antwoord niet vinden omdat de score van de vraag 71% is, en in plaats daarvan het standaardantwoord teruggeven dat u gaf toen u de kennisbank maakte.

    De geretourneerde JSON reactie, inclusief de score en het antwoord is:

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

    QnA Maker keerde `0`een score van , wat betekent geen vertrouwen. Het gaf ook het standaardantwoord terug.

1. Wijzig de drempelwaarde in 60% en vraag de query opnieuw aan:

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
    ```

    De teruggekeerde JSON vond het antwoord.

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