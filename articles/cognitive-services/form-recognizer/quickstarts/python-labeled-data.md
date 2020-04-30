---
title: 'Snelstartgids: trainen met labels met behulp van de REST API en python-formulier herkenner'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de functie gelabelde gegevens van de formulier Recognizer met de REST API en python voor het trainen van een aangepast model.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 36ded3bd85cd7acdffbfe46b9e931a811994fa30
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81531097"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>Een model voor het herkennen van een formulier met labels trainen met behulp van REST API en python

In deze Quick Start gebruikt u de formulier Recognizer REST API met python voor het trainen van een aangepast model met hand matig gelabelde gegevens. Zie de sectie [met labels trainen](../overview.md#train-with-labels) in het overzicht voor meer informatie over deze functie.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze Snelstartgids hebt u het volgende nodig:
- [Python](https://www.python.org/downloads/) geïnstalleerd (als u het voor beeld lokaal wilt uitvoeren).
- Een set van ten minste zes soorten van hetzelfde type. U gebruikt deze gegevens om het model te trainen en een formulier te testen. U kunt een voor [beeld](https://go.microsoft.com/fwlink/?linkid=2090451) van een gegevensset voor deze Quick Start gebruiken. Upload de trainings bestanden naar de hoofdmap van een BLOB storage-container in een Azure Storage-account.

## <a name="create-a-form-recognizer-resource"></a>Een resource voor een formulier herkenning maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>Trainings gegevens instellen

Vervolgens moet u de vereiste invoer gegevens instellen. De functie voor gelabelde gegevens heeft speciale invoer vereisten dan die nodig zijn voor het trainen van een aangepast model. 

Zorg ervoor dat alle trainings documenten dezelfde indeling hebben. Als u formulieren in meerdere indelingen hebt, kunt u deze indelen in submappen op basis van de algemene indeling. Wanneer u traint, moet u de API naar een submap sturen.

Als u een model wilt trainen met behulp van gelabelde gegevens, hebt u de volgende bestanden nodig als invoer in de submap. Hieronder vindt u informatie over het maken van dit bestand.

* **Bron formulieren** : de formulieren waaruit gegevens moeten worden opgehaald. Ondersteunde typen zijn JPEG, PNG, PDF of TIFF.
* **OCR-indelings bestanden** : json-bestanden waarin de grootte en positie van alle Lees bare tekst in elk bron formulier worden beschreven. U gebruikt de indelings-API voor formulier herkenning om deze gegevens te genereren. 
* **Label bestanden** : json-bestanden met een beschrijving van gegevenslabels die een gebruiker hand matig heeft ingevoerd.

Al deze bestanden moeten dezelfde submap innemen en de volgende indeling hebben:

* input_file1. PDF 
* input_file1. PDF. OCR. json
* input_file1. PDF. labels. json 
* input_file2. PDF 
* input_file2. PDF. OCR. json
* input_file2. PDF. labels. json
* ...

> [!TIP]
> Wanneer u formulieren labelt met behulp van het voor [beeld labelen](./label-tool.md)van de formulier Recognizer, maakt het hulp programma automatisch dit label en OCR-indelings bestanden.

### <a name="create-the-ocr-output-files"></a>De OCR-uitvoer bestanden maken

U hebt OCR-resultaten bestanden nodig om de service te laten nadenken over de bijbehorende invoer bestanden voor gelabelde training. Volg de onderstaande stappen om OCR-resultaten voor een bepaald bron formulier te verkrijgen:

1. Roep de **[indelings](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** -API voor analyse op de container Lees indeling aan met het invoer bestand als onderdeel van de hoofd tekst van de aanvraag. Sla de ID op die is gevonden in de koptekst van de **bewerkings locatie** van de reactie.
1. Roep de resultaat-API voor het analyseren van de **[indeling ophalen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** aan met behulp van de bewerkings-id uit de vorige stap.
1. Haal het antwoord op en schrijf de inhoud naar een bestand. Voor elk bron formulier moet aan het bijbehorende OCR-bestand de oorspronkelijke bestands naam worden toegevoegd `.ocr.json`. De OCR JSON-uitvoer moet de volgende indeling hebben. Zie het [OCR-voorbeeld bestand](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json) voor een volledig voor beeld. 

    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```

### <a name="create-the-label-files"></a>De label bestanden maken

Label bestanden bevatten koppelingen naar sleutel waarden die een gebruiker hand matig heeft ingevoerd. Ze zijn nodig voor gelabelde gegevens training, maar niet elk bron bestand moet een bijbehorend label bestand hebben. Bron bestanden zonder labels worden behandeld als gewone trainings documenten. We raden vijf of meer gelabelde bestanden aan voor een betrouw bare training.

Wanneer u een label bestand maakt, kunt u optioneel regio's&mdash;op exacte posities van waarden in het document opgeven. Hiermee krijgt de training nog nauw keuriger. Regio's worden ingedeeld als een set van acht waarden die overeenkomen met vier X, Y-coördinaten: linksboven, rechtsboven, rechtsonder en linksonder. Coördinaat waarden liggen tussen nul en één, geschaald naar de afmetingen van de pagina.

Voor elk bron formulier moet de oorspronkelijke bestands naam worden toegevoegd aan `.labels.json`het bijbehorende label bestand. Het label bestand moet de volgende indeling hebben. Zie het [voorbeeld label bestand](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json) voor een volledig voor beeld.

```json
{
    "document": "Invoice_1.pdf",
    "labels": [
        {
            "label": "Provider",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Contoso",
                    "boundingBoxes": [
                        [
                            0.06334117647058823,
                            0.1053,
                            0.17018823529411767,
                            0.1053,
                            0.17018823529411767,
                            0.12303636363636362,
                            0.06334117647058823,
                            0.12303636363636362
                        ]
                    ]
                }
            ]
        },
        {
            "label": "For",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Microsoft",
                    "boundingBoxes": [
                        [
                            0.6122941176470589,
                            0.1374,
                            0.6841764705882353,
                            0.1374,
                            0.6841764705882353,
                            0.14682727272727272,
                            0.6122941176470589,
                            0.14682727272727272
                        ]
                    ]
                },
                {
                    "page": 1,
                    "text": "1020",
                    "boundingBoxes": [
                        [
                            0.6121882352941176,
                            0.156,
                            0.6462941176470588,
                            0.156,
                            0.6462941176470588,
                            0.1653181818181818,
                            0.6121882352941176,
                            0.1653181818181818
                        ]
                    ]
                },
                ...
```

> [!NOTE]
> U kunt slechts één label Toep assen op elk tekst element en elk label kan slechts eenmaal per pagina worden toegepast. U kunt momenteel geen label op meerdere pagina's Toep assen.


## <a name="train-a-model-using-labeled-data"></a>Een model trainen met behulp van gelabelde gegevens

Als u een model met gelabelde gegevens wilt trainen, roept u de API voor het **[trainen van aangepaste modellen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** aan door de volgende python-code uit te voeren. Voordat u de code uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `<Endpoint>` door de URL van het eind punt voor de resource voor uw formulier herkenning.
1. Vervang `<SAS URL>` door de URL voor Shared Access Signature (SAS) van de Azure Blob Storage-container. Als u de SAS-URL wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechter muisknop op uw container en selecteert u **gedeelde toegangs handtekening ophalen**. Zorg ervoor dat de machtigingen **lezen** en **lijst** zijn ingeschakeld en klik op **maken**. Kopieer vervolgens de waarde in de sectie **URL** . Het moet de volgende indeling hebben `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`:.
1. Vervang `<Blob folder name>` door de naam van de map in de BLOB-container waar de invoer gegevens zich bevinden. Als uw gegevens zich in de hoofdmap bevindt, laat u deze leeg en `"prefix"` verwijdert u het veld uit de hoofd tekst van de HTTP-aanvraag.

```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.0-preview/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
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

Wanneer het trainings proces is voltooid, ontvangt u een `201 (Success)` reactie met JSON-inhoud zoals de volgende. Het antwoord is verkort voor eenvoud.

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

Wanneer het proces is voltooid, ontvangt u een `202 (Success)` reactie met JSON-inhoud in de volgende indeling. Het antwoord is verkort voor eenvoud. De hoofd sleutel/waarde-koppelingen bevinden `"documentResults"` zich in het knoop punt. De lay-Outapi-resultaten (de inhoud en posities van alle tekst in het document) bevinden zich in het `"readResults"` knoop punt.

```json
{ 
    "analyzeResult":{ 
      "version":"2.0.0",
      "readResults":[ 
        { 
          "page":1,
          "language":"en",
          "angle":0,
          "width":8.5,
          "height":11,
          "unit":"inch",
          "lines":[ 
            { 
              "language":"en",
              "boundingBox":[ 
                0.5375,
                1.1349,
                2.6064,
                1.1349,
                2.6064,
                1.354,
                0.5375,
                1.354
              ],
              "text":"Contoso Suites",
              "words":[ 
                { 
                  "boundingBox":[ 
                    0.5375,
                    1.1402,
                    1.6595,
                    1.1402,
                    1.6595,
                    1.354,
                    0.5375,
                    1.354
                  ],
                  "text":"Contoso",
                  "confidence":1
                },
                { 
                  "boundingBox":[ 
                    1.758,
                    1.1349,
                    2.6064,
                    1.1349,
                    2.6064,
                    1.3534,
                    1.758,
                    1.3534
                  ],
                  "text":"Suites",
                  "confidence":1
                }
              ]
            },
            ...
          ]
        }
      ],
      "pageResults":[ 
        { 
          "page":1,
          "tables":[ 
            { 
              "rows":2,
              "columns":6,
              "cells":[ 
                { 
                  "rowIndex":0,
                  "columnIndex":0,
                  "text":"Invoice Number",
                  "boundingBox":[ 
                    0.5075,
                    2.8088,
                    1.9061,
                    2.8088,
                    1.9061,
                    3.3219,
                    0.5075,
                    3.3219
                  ],
                  "elements":[ 
                    "#/readResults/0/lines/7/words/0",
                    "#/readResults/0/lines/7/words/1"
                  ]
                },
                { 
                  "rowIndex":0,
                  "columnIndex":1,
                  "text":"Invoice Date",
                  "boundingBox":[ 
                    1.9061,
                    2.8088,
                    3.3074,
                    2.8088,
                    3.3074,
                    3.3219,
                    1.9061,
                    3.3219
                  ],
                  "elements":[ 
                    "#/readResults/0/lines/8/words/0",
                    "#/readResults/0/lines/8/words/1"
                  ]
                },
                ...        
              ]
            }
          ]
        }
      ],
      "documentResults":[ 
        { 
          "docType":"Analyze",
          "pageRange":[ 
            1,
            1
          ],
          "fields":{ 
            "total":{ 
              "type":"string",
              "valueString":"$22,123.24",
              "text":"$22,123.24",
              "boundingBox":[ 
                5.29,
                3.41,
                5.9750000000000009,
                3.41,
                5.9750000000000009,
                3.54,
                5.29,
                3.54
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/15/words/0"
                }
              ],
              "fieldName":"total"
            },
            "invoice #":{ 
              "type":"string",
              "valueString":"7689302",
              "text":"7689302",
              "boundingBox":[ 
                0.54,
                3.41,
                1.065,
                3.41,
                1.065,
                3.515,
                0.54,
                3.515
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/12/words/0"
                }
              ],
              "fieldName":"invoice #"
            },
            "vat":{ 
              "type":"string",
              "valueString":"QR",
              "text":"QR",
              "boundingBox":[ 
                6.2250000000000009,
                3.41,
                6.425,
                3.41,
                6.425,
                3.52,
                6.2250000000000009,
                3.52
              ],
              "page":1,
              "confidence":0.9839357733726502,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/16/words/0"
                }
              ],
              "fieldName":"vat"
            },
            ...
          }
        }
      ]
    },
    "status":"succeeded",
    "createdDateTime":"2019-11-12T21:26:19+00:00",
    "lastUpdatedDateTime":"2019-11-12T21:27:27.0488571+00:00"
}
```

## <a name="improve-results"></a>Resultaten verbeteren

Bekijk de `"confidence"` waarden voor elke sleutel/waarde resultaat onder het `"documentResults"` knoop punt. U moet ook de betrouwbaarheids scores in het `"readResults"` knoop punt bekijken die overeenkomen met de lay-outbewerking. Het vertrouwen van de lay-outresultaten heeft geen invloed op het vertrouwen van de resultaten van de extractie van sleutel/waarde, dus u moet beide controleren.
* Als de betrouwbaarheids scores voor de lay-outbewerking laag zijn, probeert u de kwaliteit van uw invoer documenten te verbeteren (Zie [invoer vereisten](../overview.md#input-requirements)).
* Als de betrouwbaarheids scores voor de extractie bewerking voor sleutel/waarde laag zijn, moet u ervoor zorgen dat de documenten die worden geanalyseerd van hetzelfde type zijn als documenten die worden gebruikt in de Trainingsset. Als de documenten in de Trainingsset variaties in de weer gave hebben, kunt u overwegen deze te splitsen in verschillende mappen en een model voor elke variatie te trainen.

### <a name="avoid-cluttered-labels"></a>Geen wirwar van labels

Wanneer u verschillende labels op dezelfde tekst regel toepast, kan de service deze labels soms samen voegen in één veld. U kunt bijvoorbeeld in een adres de plaats, de provincie en de post code als verschillende velden labelen, maar tijdens de voor spelling worden deze velden niet afzonderlijk herkend.

We begrijpen dat dit scenario essentieel is voor onze klanten en we werken eraan om dit in de toekomst te verbeteren. Momenteel raden we aan dat onze gebruikers meerdere overzichtelijke velden als één veld labelen en de voor waarden vervolgens scheiden in een naverwerking van de extractie resultaten.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u de formulier Recognizer REST API met python kunt gebruiken om een model te trainen met hand matig gelabelde gegevens. Bekijk vervolgens de [API-referentie documentatie](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) om de formulier Recognizer API te verkennen.
