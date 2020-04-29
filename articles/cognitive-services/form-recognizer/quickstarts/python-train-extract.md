---
title: 'Quick Start: een model trainen en formulier gegevens extra heren met behulp van de REST API met python-formulier Recognizer'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start gebruikt u de formulier Recognizer REST API met python voor het trainen van een model en het extra heren van gegevens uit formulieren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 66668f46595c22426984a02c489297e962d061d0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77118079"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-python"></a>Quick Start: een model voor een formulier herkenning trainen en formulier gegevens extra heren met behulp van de REST API met python

In deze Quick Start gebruikt u de Azure Form Recognizer REST API met python voor Train-en Score formulieren om sleutel-waardeparen en tabellen te extra heren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze Snelstartgids hebt u het volgende nodig:
- [Python](https://www.python.org/downloads/) geïnstalleerd (als u het voor beeld lokaal wilt uitvoeren).
- Een set van ten minste vijf vormen van hetzelfde type. U gebruikt deze gegevens om het model te trainen. Uw formulieren kunnen uit verschillende bestands typen bestaan, maar moeten van hetzelfde type zijn als het document. U kunt een voor [beeld](https://go.microsoft.com/fwlink/?linkid=2090451) van een gegevensset voor deze Quick Start gebruiken. Upload de trainings bestanden naar de hoofdmap van een BLOB storage-container in een Azure Storage-account.

## <a name="create-a-form-recognizer-resource"></a>Een resource voor een formulier herkenning maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Model van een formulier herkenning trainen

Eerst hebt u een set trainings gegevens nodig in een Azure Storage BLOB-container. U moet mini maal vijf ingevulde formulieren (PDF-documenten en/of afbeeldingen) van hetzelfde type/dezelfde structuur als uw belangrijkste invoer gegevens hebben. U kunt ook één leeg formulier met twee ingevulde formulieren gebruiken. De bestands naam van het lege formulier moet het woord ' empty ' bevatten. Zie [een trainings gegevensset voor een aangepast model bouwen](../build-training-data-set.md) voor tips en opties voor het samen stellen van uw trainings gegevens.

> [!NOTE]
> U kunt de functie met gelabelde gegevens hand matig een aantal of al uw trainings gegevens vooraf labelen. Dit is een complexere proces, maar resulteert in een beter getraind model. Zie de sectie [met labels trainen](../overview.md#train-with-labels) in het overzicht voor meer informatie.

Als u een formulier Recognizer-model wilt trainen met de documenten in uw Azure Blob-container, roept u de API voor het **[trainen van aangepaste modellen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** aan door de volgende python-code uit te voeren. Voordat u de code uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `<SAS URL>` door de URL voor Shared Access Signature (SAS) van de Azure Blob Storage-container. Als u de SAS-URL wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechter muisknop op uw container en selecteert u **gedeelde toegangs handtekening ophalen**. Zorg ervoor dat de machtigingen **lezen** en **lijst** zijn ingeschakeld en klik op **maken**. Kopieer vervolgens de waarde in de sectie **URL** . Het moet de volgende indeling hebben `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`:.
1. Vervang `<subscription key>` door de abonnements sleutel die u uit de vorige stap hebt gekopieerd.
1. Vervang `<endpoint>` door de URL van het eind punt voor de resource voor uw formulier herkenning.
1. Vervang `<Blob folder name>` door het pad naar de map in Blob Storage waar uw formulieren zich bevinden. Als uw formulieren zich in de hoofdmap van de container bevinden, laat u deze teken reeks leeg.

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
1. Sla de code op in een bestand met de extensie. py. Bijvoorbeeld *Form-Recognizer-Train.py*.
1. Open een opdrachtpromptvenster.
1. Typ bij de prompt de opdracht `python` om het voorbeeld uit te voeren. Bijvoorbeeld `python form-recognizer-train.py`.

## <a name="get-training-results"></a>Trainings resultaten ophalen

Nadat u de trein bewerking hebt gestart, gebruikt u de geretourneerde ID om de status van de bewerking op te halen. Voeg de volgende code toe onder aan het python-script. Dit maakt gebruik van de ID-waarde van de trainings oproep in een nieuwe API-oproep. De trainings bewerking is asynchroon, waardoor dit script de API regel matig aanroept totdat de trainings status is voltooid. We raden een interval van één seconde of meer aan.

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

Wanneer het trainings proces is voltooid, ontvangt u een `201 (Success)` antwoord met JSON-inhoud zoals het volgende:

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

Kopieer de `"modelId"` waarde voor gebruik in de volgende stappen.

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

Wanneer het proces is voltooid, ontvangt u een `200 (Success)` reactie met JSON-inhoud in de volgende indeling. Het antwoord is verkort voor eenvoud. De belangrijkste koppelingen en tabellen voor sleutel/waarde-paars `"pageResults"` bevinden zich in het knoop punt. Als u ook tekst extractie hebt opgegeven via de para meter *includeTextDetails* URL, worden in `"readResults"` het knoop punt de inhoud en posities van alle tekst in het document weer gegeven.

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

In deze Quick Start hebt u de formulier Recognizer REST API met python gebruikt om een model te trainen en uit te voeren in een voorbeeld scenario. Raadpleeg vervolgens de referentie documentatie om de API voor het formulier Recognizer te verkennen.

> [!div class="nextstepaction"]
> [Documentatie over REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
