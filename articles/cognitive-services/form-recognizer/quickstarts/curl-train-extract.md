---
title: 'Snelstart: een model trainen en formuliergegevens extraheren met behulp van cURL - Formulierherkenning'
titleSuffix: Azure Cognitive Services
description: In deze quickstart gebruikt u de FORM Recognizer REST API met cURL om een model te trainen en gegevens uit formulieren te extraheren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 32756187852de0834afc1dc034d3f7419f0c8087
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118401"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Snelstart: train een Form Recognizer-model en vernieuw formuliergegevens met behulp van de REST API met cURL

In deze quickstart gebruikt u de Azure Form Recognizer REST API met cURL om formulieren te trainen en te scoren om sleutelwaardeparen en -tabellen te extraheren.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om deze snelle start te voltooien, moet u het:
- [cURL](https://curl.haxx.se/windows/) geïnstalleerd.
- Een set van ten minste zes vormen van hetzelfde type. Je gebruikt er vijf om het model te trainen, en dan test je het met het zesde formulier. Uw formulieren kunnen van verschillende bestandstypen zijn, maar moeten hetzelfde type document zijn. U een [voorbeeldgegevensset](https://go.microsoft.com/fwlink/?linkid=2090451) gebruiken voor deze quickstart. Upload de trainingsbestanden naar de hoofdmap van een blobopslagcontainer in een Azure Storage-account. U de testbestanden in een aparte map plaatsen.

## <a name="create-a-form-recognizer-resource"></a>Een bron voor formulierherkenning maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Een formulierherkenningsmodel trainen

Eerst hebt u een set trainingsgegevens nodig in een Azure Storage-blob. U moet minimaal vijf ingevulde formulieren (PDF-documenten en/of afbeeldingen) hebben van hetzelfde type/dezelfde structuur als uw belangrijkste invoergegevens. U ook één leeg formulier gebruiken met twee ingevulde formulieren. De bestandsnaam van het lege formulier moet het woord 'leeg' bevatten. Zie [Een trainingsgegevensset maken voor een aangepast model](../build-training-data-set.md) voor tips en opties voor het samenstellen van uw trainingsgegevens.

> [!NOTE]
> U de gelabelde gegevensfunctie gebruiken om vooraf een aantal of al uw trainingsgegevens handmatig te labelen. Dit is een complexer proces, maar resulteert in een beter opgeleid model. Bekijk het gedeelte [Trainen met labels](../overview.md#train-with-labels) in het overzicht voor meer informatie over deze functie.

Als u een formulierherkenningsmodel wilt trainen met de documenten in uw Azure-blobcontainer, roept u de API **[voor aangepast model trainen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** aan door de volgende cURL-opdracht uit te voeren. Voer de opdracht aan voordat u de opdracht uitvoert:

1. Vervang `<Endpoint>` het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnement.
1. Vervang `<subscription key>` de abonnementssleutel die u uit de vorige stap hebt gekopieerd.
1. Vervang `<SAS URL>` de SAS-URL (Shared Access Signature) van de Azure Blob-opslagcontainer. Als u de SAS-URL wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechtermuisknop op uw container en selecteert **u Handtekening voor gedeelde toegang ophalen**. Controleer of de machtigingen **Lezen** en **Lijst** zijn ingeschakeld en klik op **Maken**. Kopieer vervolgens de waarde in de **sectie URL.** Het zou de `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`vorm moeten hebben: .

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Je ontvangt een `201 (Success)` antwoord met een **koptekst Locatie.** De waarde van deze header is de ID van het nieuwe model dat wordt getraind. 

## <a name="get-training-results"></a>Krijg trainingsresultaten

Nadat u de treinbewerking hebt gestart, gebruikt u een nieuwe bewerking, **[Aangepast model krijgen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetCustomModel)** om de trainingsstatus te controleren. Geef de model-id door aan deze API-aanroep om de trainingsstatus te controleren:

1. Vervang `<Endpoint>` het eindpunt dat u hebt verkregen door de abonnementssleutel Voor formulierherkenning.
1. Vervangen `<subscription key>` door uw abonnementssleutel
1. Vervangen `<model ID>` door de model-id die u in de vorige stap hebt ontvangen

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Je ontvangt een `200 (Success)` reactie met een JSON-body in de volgende indeling. Let `"status"` op het veld. Dit heeft de `"ready"` waarde zodra de training is voltooid. Als het model nog niet klaar is met trainen, moet u de service opnieuw opvragen door de opdracht opnieuw uit te voeren. We raden een interval van één seconde of meer tussen gesprekken aan.

Het `"modelId"` veld bevat de id van het model dat u traint. Je hebt dit nodig voor de volgende stap.

```json
{ 
  "modelInfo":{ 
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{ 
    "trainingDocuments":[ 
      { 
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      }
    ],
    "errors":[ 

    ]
  },
  "keys":{ 
    "0":[ 
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Formulieren voor sleutelwaardeparen en tabellen analyseren

Vervolgens gebruikt u uw nieuw getrainde model om een document te analyseren en er sleutelwaardeparen en tabellen uit te halen. Roep de **[API voor formulier analyseren](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** aan door de volgende cURL-opdracht uit te voeren. Voer de opdracht aan voordat u de opdracht uitvoert:

1. Vervang `<Endpoint>` het eindpunt dat u hebt verkregen uit de abonnementssleutel Voor formulierherkenning. U het vinden op het tabblad Overzicht van **formulierherkenningsbronnen.**
1. Vervang `<model ID>` de model-id die u in de vorige sectie hebt ontvangen.
1. Vervang `<SAS URL>` een SAS-URL naar uw bestand in Azure-opslag. Volg de stappen in de sectie Training, maar in plaats van een SAS-URL voor de hele blobcontainer te krijgen, krijgt u er een voor het specifieke bestand dat u wilt analyseren.
1. Vervang `<subscription key>` door uw abonnementssleutel.

```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```

U ontvangt een `202 (Success)` antwoord met een koptekst **Operatie-Locatie.** De waarde van deze koptekst bevat een resultaten-id die u gebruikt om de resultaten van de bewerking Analyseren bij te houden. Sla deze resultaten-id op voor de volgende stap.

## <a name="get-the-analyze-results"></a>De resultaten analyseren

Gebruik de volgende API om de resultaten van de bewerking Analyseren op te vragen.

1. Vervang `<Endpoint>` het eindpunt dat u hebt verkregen uit de abonnementssleutel Voor formulierherkenning. U het vinden op het tabblad Overzicht van **formulierherkenningsbronnen.**
1. Vervang `<result ID>` de ID die u in de vorige sectie hebt ontvangen.
1. Vervang `<subscription key>` door uw abonnementssleutel.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Je ontvangt een `200 (Success)` reactie met een JSON-body in de volgende indeling. De output is ingekort voor eenvoud. Let `"status"` op het veld in de buurt van de bodem. Dit heeft de `"succeeded"` waarde wanneer de bewerking Analyseren is voltooid. Als de bewerking Analyseren nog niet is voltooid, moet u de service opnieuw opvragen door de opdracht opnieuw uit te voeren. We raden een interval van één seconde of meer tussen gesprekken aan.

De belangrijkste sleutel/waardepaarkoppelingen en `"pageResults"` -tabellen bevinden zich in het knooppunt. Als u ook de extractie met platte tekst hebt `"readResults"` opgegeven via de URL-parameter *includeTextDetails,* wordt in het knooppunt de inhoud en posities van alle tekst in het document weergegeven.

```json
{
  "analyzeResult":{ 
    "readResults":[ 
      { 
        "page":1,
        "width":8.5,
        "height":11.0,
        "angle":0,
        "unit":"inch",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              0.5278,
              1.0597,
              1.4569,
              1.0597,
              1.4569,
              1.4347,
              0.5278,
              1.4347
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  0.5278,
                  1.0597,
                  1.4569,
                  1.0597,
                  1.4569,
                  1.4347,
                  0.5278,
                  1.4347
                ]
              }
            ]
          },
          ...
          { 
            "text":"PT",
            "boundingBox":[ 
              6.2181,
              3.3528,
              6.3944,
              3.3528,
              6.3944,
              3.5417,
              6.2181,
              3.5417
            ],
            "words":[ 
              { 
                "text":"PT",
                "boundingBox":[ 
                  6.2181,
                  3.3528,
                  6.3944,
                  3.3528,
                  6.3944,
                  3.5417,
                  6.2181,
                  3.5417
                ]
              }
            ]
          }
        ]
      }
    ],
    "version":"2.0.0",
    "errors":[ 

    ],
    "documentResults":[ 

    ],
    "pageResults":[ 
      { 
        "page":1,
        "clusterId":1,
        "keyValuePairs":[ 
          { 
            "key":{ 
              "text":"Address:",
              "boundingBox":[ 
                0.7972,
                1.5125,
                1.3958,
                1.5125,
                1.3958,
                1.6431,
                0.7972,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/1/words/0"
              ]
            },
            "value":{ 
              "text":"1 Redmond way Suite 6000 Redmond, WA 99243",
              "boundingBox":[ 
                0.7972,
                1.6764,
                2.15,
                1.6764,
                2.15,
                2.2181,
                0.7972,
                2.2181
              ],
              "elements":[ 
                "#/readResults/0/lines/4/words/0",
                "#/readResults/0/lines/4/words/1",
                "#/readResults/0/lines/4/words/2",
                "#/readResults/0/lines/4/words/3",
                "#/readResults/0/lines/6/words/0",
                "#/readResults/0/lines/6/words/1",
                "#/readResults/0/lines/6/words/2",
                "#/readResults/0/lines/8/words/0"
              ]
            },
            "confidence":0.86
          },
          { 
            "key":{ 
              "text":"Invoice For:",
              "boundingBox":[ 
                4.3903,
                1.5125,
                5.1139,
                1.5125,
                5.1139,
                1.6431,
                4.3903,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/2/words/0",
                "#/readResults/0/lines/2/words/1"
              ]
            },
            "value":{ 
              "text":"Microsoft 1020 Enterprise Way Sunnayvale, CA 87659",
              "boundingBox":[ 
                5.1917,
                1.4458,
                6.6583,
                1.4458,
                6.6583,
                2.0347,
                5.1917,
                2.0347
              ],
              "elements":[ 
                "#/readResults/0/lines/3/words/0",
                "#/readResults/0/lines/5/words/0",
                "#/readResults/0/lines/5/words/1",
                "#/readResults/0/lines/5/words/2",
                "#/readResults/0/lines/7/words/0",
                "#/readResults/0/lines/7/words/1",
                "#/readResults/0/lines/7/words/2"
              ]
            },
            "confidence":0.86
          },
          ...
        ],
        "tables":[ 
          { 
            "caption":null,
            "rows":2,
            "columns":5,
            "cells":[ 
              { 
                "rowIndex":0,
                "colIndex":0,
                "header":true,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5347,
                  2.8722,
                  1.575,
                  2.8722,
                  1.575,
                  3.0028,
                  0.5347,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/9/words/0",
                  "#/readResults/0/lines/9/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":1,
                "header":true,
                "text":"Invoice Date",
                "boundingBox":[ 
                  1.9403,
                  2.8722,
                  2.7569,
                  2.8722,
                  2.7569,
                  3.0028,
                  1.9403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/10/words/0",
                  "#/readResults/0/lines/10/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":2,
                "header":true,
                "text":"Invoice Due Date",
                "boundingBox":[ 
                  3.3403,
                  2.8722,
                  4.4583,
                  2.8722,
                  4.4583,
                  3.0028,
                  3.3403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/11/words/0",
                  "#/readResults/0/lines/11/words/1",
                  "#/readResults/0/lines/11/words/2"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  },
  "lastUpdatedDateTime":"2019-10-07T19:32:18+00:00",
  "status":"succeeded",
  "createdDateTime":"2019-10-07T19:32:15+00:00"
}
```

## <a name="improve-results"></a>Resultaten verbeteren

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de API voor formulierherkenningsrest met cURL gebruikt om een model te trainen en uit te voeren in een voorbeeldscenario. Zie vervolgens de referentiedocumentatie om de API voor formulierherkenning verder te verkennen.

> [!div class="nextstepaction"]
> [REST API-referentiedocumentatie](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
