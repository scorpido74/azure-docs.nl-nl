---
title: 'Snelstart: Train een model en vernieuw formuliergegevens met de REST API met Python - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: In deze quickstart gebruikt u de FORM Recognizer REST API met Python om een model te trainen en gegevens uit formulieren te extraheren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 66668f46595c22426984a02c489297e962d061d0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118079"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-python"></a>Snelstart: train een Form Recognizer-model en vernieuw formuliergegevens met behulp van de REST API met Python

In deze quickstart gebruikt u de Azure Form Recognizer REST API met Python om formulieren te trainen en te scoren om sleutelwaardeparen en -tabellen te extraheren.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om deze snelle start te voltooien, moet u het:
- [Python](https://www.python.org/downloads/) geïnstalleerd (als u het voorbeeld lokaal wilt uitvoeren).
- Een set van ten minste vijf vormen van hetzelfde type. U gebruikt deze gegevens om het model te trainen. Uw formulieren kunnen van verschillende bestandstypen zijn, maar moeten hetzelfde type document zijn. U een [voorbeeldgegevensset](https://go.microsoft.com/fwlink/?linkid=2090451) gebruiken voor deze quickstart. Upload de trainingsbestanden naar de hoofdmap van een blobopslagcontainer in een Azure Storage-account.

## <a name="create-a-form-recognizer-resource"></a>Een bron voor formulierherkenning maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Een formulierherkenningsmodel trainen

Eerst hebt u een set trainingsgegevens nodig in een Azure Storage blob-container. U moet minimaal vijf ingevulde formulieren (PDF-documenten en/of afbeeldingen) hebben van hetzelfde type/dezelfde structuur als uw belangrijkste invoergegevens. U ook één leeg formulier gebruiken met twee ingevulde formulieren. De bestandsnaam van het lege formulier moet het woord 'leeg' bevatten. Zie [Een trainingsgegevensset maken voor een aangepast model](../build-training-data-set.md) voor tips en opties voor het samenstellen van uw trainingsgegevens.

> [!NOTE]
> U de gelabelde gegevensfunctie gebruiken om vooraf een aantal of al uw trainingsgegevens handmatig te labelen. Dit is een complexer proces, maar resulteert in een beter opgeleid model. Zie het gedeelte [Trainen met labels](../overview.md#train-with-labels) in het overzicht voor meer informatie.

Als u een formulierherkenningsmodel wilt trainen met de documenten in uw Azure-blobcontainer, roept u de API **[voor aangepast model trainen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** aan door de volgende python-code uit te voeren. Breng de volgende wijzigingen aan voordat u de code uitvoert:

1. Vervang `<SAS URL>` de SAS-URL (Shared Access Signature) van de Azure Blob-opslagcontainer. Als u de SAS-URL wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechtermuisknop op uw container en selecteert **u Handtekening voor gedeelde toegang ophalen**. Controleer of de machtigingen **Lezen** en **Lijst** zijn ingeschakeld en klik op **Maken**. Kopieer vervolgens de waarde in de **sectie URL.** Het zou de `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`vorm moeten hebben: .
1. Vervang `<subscription key>` de abonnementssleutel die u uit de vorige stap hebt gekopieerd.
1. Vervang `<endpoint>` de URL van het eindpunt voor uw bron voor formulierherkenning.
1. Vervang `<Blob folder name>` het pad naar de map in blobopslag waar uw formulieren zich bevinden. Als uw formulieren aan de basis van uw container liggen, laat u deze tekenreeks leeg.

    ```python
    ########### Python Form Recognizer Labeled Async Train #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    post_url = endpoint + r"/formrecognizer/v2.0-preview/custom/models"
    source = r"<SAS URL>"
    prefix = "<Blob folder name>"
    includeSubFolders = False
    useLabelFile = False
    
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<subsription key>',
    }
    
    body =  {
        "source": source,
        "sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
        },
        "useLabelFile": useLabelFile
    }
    
    try:
        resp = post(url = post_url, json = body, headers = headers)
        if resp.status_code != 201:
            print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
            quit()
        print("POST model succeeded:\n%s" % resp.headers)
        get_url = resp.headers["location"]
    except Exception as e:
        print("POST model failed:\n%s" % str(e))
        quit() 
    ```
1. Sla de code op in een bestand met een .py-extensie. Bijvoorbeeld *form-recognizer-train.py*.
1. Open een opdrachtpromptvenster.
1. Typ bij de prompt de opdracht `python` om het voorbeeld uit te voeren. Bijvoorbeeld `python form-recognizer-train.py`.

## <a name="get-training-results"></a>Krijg trainingsresultaten

Nadat u de treinbewerking hebt gestart, gebruikt u de geretourneerde id om de status van de bewerking te krijgen. Voeg de volgende code toe aan de onderkant van uw Python-script. Hiermee wordt de ID-waarde van de trainingsaanroep in een nieuwe API-aanroep gebruikt. De trainingsbewerking is asynchroon, dus dit script roept de API regelmatig aan totdat de trainingsstatus is voltooid. We raden een interval van een seconde of meer aan.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = headers)
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
            quit()
        model_status = resp_json["modelInfo"]["status"]
        if model_status == "ready":
            print("Training succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if model_status == "invalid":
            print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
            quit()
        # Training still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET model failed:\n%s" % str(e)
        print(msg)
        quit()
print("Train operation did not complete within the allocated time.")
```

Wanneer het trainingsproces is voltooid, `201 (Success)` ontvangt u een antwoord met JSON-inhoud zoals:

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

Kopieer `"modelId"` de waarde voor gebruik in de volgende stappen.

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

Wanneer het proces is voltooid, `200 (Success)` ontvangt u een antwoord met JSON-inhoud in de volgende indeling. Het antwoord is ingekort voor eenvoud. De belangrijkste sleutel/waardepaarkoppelingen en `"pageResults"` -tabellen bevinden zich in het knooppunt. Als u ook de extractie met platte tekst hebt `"readResults"` opgegeven via de URL-parameter *includeTextDetails,* wordt in het knooppunt de inhoud en posities van alle tekst in het document weergegeven.

```bash
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

In deze quickstart hebt u de FORM Recognizer REST API met Python gebruikt om een model te trainen en uit te voeren in een voorbeeldscenario. Zie vervolgens de referentiedocumentatie om de API voor formulierherkenning verder te verkennen.

> [!div class="nextstepaction"]
> [REST API-referentiedocumentatie](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
