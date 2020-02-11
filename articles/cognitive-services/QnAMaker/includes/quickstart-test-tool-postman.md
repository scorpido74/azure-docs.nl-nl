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
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2020
ms.locfileid: "77112344"
---
Deze snelstart op basis van Postman begeleidt u bij het ophalen van een antwoord uit een knowledge base.

## <a name="prerequisites"></a>Vereisten

* Meest recente [**Postman**](https://www.getpostman.com/).
* U moet beschikken over
    * Een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md)
    * Een getrainde en gepubliceerde [kennis database met vragen en antwoorden die](../Quickstarts/add-question-metadata-portal.md) zijn gebouwd op basis van de Quick start is geconfigureerd met meta gegevens en Chit Chat.

> [!NOTE]
> Wanneer u klaar bent voor het genereren van een antwoord op een vraag uit uw Knowledge Base, moet u uw kennis basis [trainen](../Quickstarts/create-publish-knowledge-base.md#save-and-train) en [publiceren](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) . Wanneer uw knowledge base is gepubliceerd, geeft de pagina **Publiceren** de instellingen van de HTTP-aanvraag weer voor het genereren van een antwoord. Het tabblad **postman** bevat de instellingen die nodig zijn voor het genereren van een antwoord.

## <a name="set-up-postman-for-requests"></a>Postman voor aanvragen instellen

Deze Snelstartgids maakt gebruik van dezelfde instellingen voor **de post van** postman en vervolgens wordt geconfigureerd voor de JSON van de hoofd tekst die naar de service wordt verzonden op basis van waarvoor u een query wilt uitvoeren.

Gebruik deze procedure voor het configureren van Postman en lees elke volgende sectie om de JSON van de hoofd tekst te configureren.

1. Selecteer op de pagina **instellingen** van de Knowledge Base het tabblad **postman** voor een overzicht van de configuratie die wordt gebruikt voor het genereren van een antwoord uit de Knowledge Base. Kopieer de volgende informatie om te gebruiken in postman.

    |Naam|Instelling|Doel en waarde|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Dit is de HTTP-methode en route voor de URL.|
    |`Host`|`https://diberry-qna-s0-s.azurewebsites.net/qnamaker`|Dit is de host van de URL. Voeg de host en post-waarden toe om de volledige generateAnswer-URL op te halen.|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|De waarde van de header voor om uw aanvraag in azure te autoriseren. |
    |`Content-type`|`application/json`|De waarde voor de header van uw inhoud.|
    ||`{"question":"<Your question>"}`|De hoofd tekst van de POST-aanvraag als een JSON-object. Deze waarde wordt in elk van de volgende secties gewijzigd, afhankelijk van de bedoeling van de query.|

1. Open postman en maak een nieuwe Basic **post** -aanvraag met uw gepubliceerde Knowledge Base-instellingen. Wijzig in de volgende secties de JSON POST Body om de query te wijzigen in uw Knowledge Base.

## <a name="use-metadata-to-filter-answer"></a>Meta gegevens gebruiken om antwoord te filteren

In een vorige Snelstartgids zijn meta gegevens toegevoegd aan twee QnA sets om onderscheid te maken tussen twee verschillende vragen. Voeg de meta gegevens toe aan de query om het filter te beperken tot alleen de relevante QnA-set.

1. Wijzig in postman alleen de query JSON door de eigenschap `strictFilters` toe te voegen met het combi natie van naam/waarde van `service:qna_maker`. De JSON van de hoofd tekst moet:

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

    De vraag is slechts één woord, `size`, waarmee een van de twee vraag-en antwoord sets kan worden geretourneerd. De `strictFilters` matrix vertelt u het antwoord om alleen de `qna_maker`-antwoorden te verminderen.

1. Het antwoord bevat alleen het antwoord dat voldoet aan de filter criteria.

    Het volgende antwoord is opgemaakt voor de Lees baarheid:

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

    Als er een vraag-en-antwoordset is die niet voldoet aan de zoek term maar wel aan het filter voldoet, wordt de set niet geretourneerd. In plaats daarvan wordt het algemene antwoord `No good match found in KB.` geretourneerd.

## <a name="use-debug-query-property"></a>Eigenschap debug query gebruiken

Informatie over fout opsporing helpt u te begrijpen hoe het geretourneerde antwoord is vastgesteld. Hoewel het handig is, is het niet nodig. Als u een antwoord met foutopsporingsinformatie wilt genereren, voegt u de eigenschap `debug`:

1. Wijzig in postman alleen de JSON van de hoofd tekst door de eigenschap `debug` toe te voegen. De JSON moet zijn:

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. Het antwoord bevat de relevante informatie over het antwoord. In de volgende JSON-uitvoer zijn bepaalde foutopsporingsinformatie-gegevens vervangen door weglatings tekens.

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

## <a name="use-test-knowledge-base"></a>Test kennis basis gebruiken

Als u een antwoord wilt ontvangen van de test Knowledge Base, gebruikt u de eigenschap `isTest` body.

Wijzig in postman alleen de JSON van de hoofd tekst door de eigenschap `isTest` toe te voegen. De JSON moet zijn:

```json
{
    'question':'size',
    'isTest': true
}
```

In het JSON-antwoord wordt hetzelfde schema gebruikt als voor de gepubliceerde Knowledge Base-query.

> [!NOTE]
> Als de test en de gepubliceerde kennis bases exact hetzelfde zijn, kan er nog een kleine variatie optreden omdat de test index wordt gedeeld tussen alle kennis grondslagen in de resource.

## <a name="query-for-a-chit-chat-answer"></a>Een query uitvoeren voor een Chit-Chat-antwoord

1. Wijzig in postman alleen de JSON van de hoofd tekst in een instructie voor het beëindigen van een conversatie van de gebruiker. De JSON moet zijn:

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

    Omdat de vraag `Thank you` precies overeenkwam met een heen- en weergepraatvraag, is QnA Maker volledig zeker met de score van 100. QnA Maker heeft ook alle gerelateerde vragen geretourneerd, evenals de meta gegevens eigenschap met de Chit-Chat-meta gegevens code gegevens.

## <a name="use-threshold-and-default-answer"></a>Drempel en standaard antwoord gebruiken

U kunt een minimale drempel waarde voor het antwoord aanvragen. Als niet wordt voldaan aan de drempel waarde, wordt het standaard antwoord geretourneerd.

1. Wijzig in postman alleen de JSON van de hoofd tekst in een instructie voor het beëindigen van een conversatie van de gebruiker. De JSON moet zijn:

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    De Knowledge Base mag dat antwoord niet vinden omdat de Score van de vraag 71% is en het standaard antwoord dat u hebt ingevoerd tijdens het maken van de Knowledge Base, in plaats daarvan wordt geretourneerd.

    Het geretourneerde JSON-antwoord, inclusief de score en het antwoord is:

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

    QnA Maker heeft een score geretourneerd van `0`, wat geen betrouw baarheid betekent. Ook wordt het standaard antwoord geretourneerd.

1. Wijzig de drempel waarde in 60% en vraag de query opnieuw aan:

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
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