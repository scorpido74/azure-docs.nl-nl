---
title: 'Snelstart: ontvangstgegevens extraheren met behulp van cURL - Formulierherkenning'
titleSuffix: Azure Cognitive Services
description: In deze quickstart gebruikt u de FORM Recognizer REST API met cURL om gegevens uit afbeeldingen van amerikaanse verkoopbonnen te extraheren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: e053222d3b79668c2f6044417e31e104ce0f4222
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118484"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>Snelstart: ontvangstgegevens extraheren met behulp van de API Voor formulierherkenningrest met cURL

In deze quickstart gebruikt u de Azure Form Recognizer REST API met cURL om relevante informatie in verkoopbevestigingen in de VS te extraheren en te identificeren.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om deze snelle start te voltooien, moet u het:
- [cURL](https://curl.haxx.se/windows/) geïnstalleerd.
- Een URL voor een afbeelding van een ontvangstbewijs. U een [voorbeeldafbeelding](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-allinone.jpg?raw=true) gebruiken voor deze quickstart.

## <a name="create-a-form-recognizer-resource"></a>Een bron voor formulierherkenning maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Een ontvangstbewijs analyseren

Als u wilt beginnen met het analyseren van een ontvangstbewijs, belt u de **[API Voor ontvangst analyseren](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeReceiptAsync)** met de onderstaande cURL-opdracht. Voer de opdracht aan voordat u de opdracht uitvoert:

1. Vervang `<Endpoint>` het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnement.
1. Vervang `<your receipt URL>` door het URL-adres van een ontvangstafbeelding.
1. Vervang `<subscription key>` de abonnementssleutel die u uit de vorige stap hebt gekopieerd.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0-preview/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"url\": \"<your receipt URL>\"}"
```

Je ontvangt een `202 (Success)` antwoord met de koptekst Am **Operation-Location.** De waarde van deze koptekst bevat een bewerkings-id die u gebruiken om de status van de asynchrone bewerking op te vragen en de resultaten op te halen. In het volgende voorbeeld `operations/` is de tekenreeks erna de bewerkings-id.

```console
https://cognitiveservice/formrecognizer/v2.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>De ontvangstresultaten opmaken

Nadat u de **API Voor ontvangstanalyse** hebt aangeroepen, roept u de API Voor het analyseren van **[ontvangstresultaten](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeReceiptResult)** aan om de status van de bewerking en de geëxtraheerde gegevens te verkrijgen. Voer de opdracht aan voordat u de opdracht uitvoert:

1. Vervang `<Endpoint>` het eindpunt dat u hebt verkregen door de abonnementssleutel Voor formulierherkenning. U het vinden op het tabblad Overzicht van **formulierherkenningsbronnen.**
1. Vervang `<operationId>` de bewerkings-id van de vorige stap.
1. Vervang `<subscription key>` door uw abonnementssleutel.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/prebuilt/receipt/analyzeResults/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Het antwoord bekijken

U ontvangt een `200 (Success)` antwoord met JSON-uitvoer. Het eerste `"status"`veld geeft de status van de bewerking aan. Als de bewerking is `"recognitionResults"` voltooid, bevat het veld elke regel tekst `"understandingResults"` die uit de ontvangst is geëxtraheerd en bevat het veld sleutel-/waarde-informatie voor de meest relevante delen van het ontvangstbewijs. Als de bewerking niet is `"status"` voltooid, `"running"` `"notStarted"`is de waarde van de API of , en moet u de API opnieuw aanroepen, handmatig of via een script. We raden een interval van één seconde of meer tussen gesprekken aan.

Zie de volgende ontvangstafbeelding en de bijbehorende JSON-uitvoer. De output is ingekort voor leesbaarheid.

![Een ontvangstbewijs van Contoso store](../media/contoso-allinone.jpg)

Het `"recognitionResults"` knooppunt bevat alle herkende tekst. Tekst wordt georganiseerd op pagina, vervolgens op regel, vervolgens op individuele woorden. Het `"understandingResults"` knooppunt bevat de ontvangstspecifieke waarden die het model heeft ontdekt. Hier vindt u nuttige sleutel-/waardeparen zoals de belasting, het totaal, het handelsadres, enzovoort.

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[ 
      { 
        "docType":"prebuilt:receipt",
        "pageRange":[ 
          1,
          1
        ],
        "fields":{ 
          "ReceiptType":{ 
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{ 
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[ 
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[ 
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{ 
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[ 
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{ 
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[ 
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{ 
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[ 
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{ 
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[ 
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[ 
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{ 
            "type":"array",
            "valueArray":[ 
              { 
                "type":"object",
                "valueObject":{ 
                  "Quantity":{ 
                    "type":"number",
                    "text":"1",
                    "boundingBox":[ 
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{ 
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[ 
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{ 
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[ 
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[ 
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{ 
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[ 
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[ 
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{ 
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[ 
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[ 
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{ 
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[ 
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[ 
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{ 
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[ 
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[ 
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de API Voor formulierherkenningrest met cURL gebruikt om de inhoud van een verkoopontvangstbewijs te extraheren. Zie vervolgens de referentiedocumentatie om de API voor formulierherkenning verder te verkennen.

> [!div class="nextstepaction"]
> [REST API-referentiedocumentatie](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeReceiptAsync)
