---
title: 'Snelstart: trainen met labels met de REST API en Python - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de functie Formulierherkenning met de functie Formulierherkenning met de REST API en Python om een aangepast model te trainen.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 5469c2512e133d17e4d18cebb64ab9e2a21b1f83
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77482314"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>Een formulierherkenningsmodel trainen met labels met REST API en Python

In deze quickstart gebruikt u de Form Recognizer REST API met Python om een aangepast model te trainen met handmatig gelabelde gegevens. Bekijk het gedeelte [Trainen met labels](../overview.md#train-with-labels) in het overzicht voor meer informatie over deze functie.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om deze snelle start te voltooien, moet u het:
- [Python](https://www.python.org/downloads/) geïnstalleerd (als u het voorbeeld lokaal wilt uitvoeren).
- Een set van ten minste zes vormen van hetzelfde type. U gebruikt deze gegevens om het model te trainen en een formulier te testen. U een [voorbeeldgegevensset](https://go.microsoft.com/fwlink/?linkid=2090451) gebruiken voor deze quickstart. Upload de trainingsbestanden naar de hoofdmap van een blobopslagcontainer in een Azure Storage-account.

## <a name="create-a-form-recognizer-resource"></a>Een bron voor formulierherkenning maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>Trainingsgegevens instellen

Vervolgens moet u de vereiste invoergegevens instellen. De gelabelde gegevensfunctie heeft speciale invoervereisten die verder gaan dan die welke nodig zijn om een aangepast model te trainen. 

Zorg ervoor dat alle trainingsdocumenten van hetzelfde formaat zijn. Als u formulieren in meerdere indelingen hebt, ordeert u deze in submappen op basis van een algemene indeling. Wanneer u traint, moet u de API naar een submap leiden.

Als u een model wilt trainen met gelabelde gegevens, hebt u de volgende bestanden nodig als invoer in de submap. Hieronder vindt u meer informatie over het maken van deze bestanden.

* **Bronformulieren** – de formulieren om gegevens uit te extraheren. Ondersteunde typen zijn JPEG, PNG, BMP, PDF of TIFF.
* **OCR-indelingsbestanden** - JSON-bestanden die de grootte en posities van alle leesbare tekst in elk bronformulier beschrijven. U gebruikt de API voor formulierindeling smeedinrichting om deze gegevens te genereren. 
* **Labelbestanden** - JSON-bestanden die gegevenslabels beschrijven die een gebruiker handmatig heeft ingevoerd.

Al deze bestanden moeten dezelfde submap innemen en in de volgende indeling zijn:

* input_file1.pdf 
* input_file1.pdf.ocr.json
* input_file1.pdf.labels.json 
* input_file2.pdf 
* input_file2.pdf.ocr.json
* input_file2.pdf.labels.json
* ...

> [!TIP]
> Wanneer u formulieren labelt met het [voorbeeldlabelingsgereedschap](./label-tool.md)Formulierherkenning, maakt het hulpprogramma deze label- en OCR-indelingsbestanden automatisch.

### <a name="create-the-ocr-output-files"></a>De OCR-uitvoerbestanden maken

U hebt OCR-resultaatbestanden nodig om de service rekening te kunnen houden met de bijbehorende invoerbestanden voor gelabelde training. Volg de onderstaande stappen om OCR-resultaten voor een bepaald bronformulier te verkrijgen:

1. Roep de **[API Voor lay-out analyseren](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** aan op de lees-indelingscontainer met het invoerbestand als onderdeel van de aanvraaginstantie. Sla de id op die is gevonden in de **koptekst Operation-Location** van het antwoord.
1. Roep de API **[voor het analysevan indelingsresultaat](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** aan met de bewerkings-id van de vorige stap.
1. Haal het antwoord op en schrijf de inhoud naar een bestand. Voor elk bronformulier moet het bijbehorende OCR-bestand `.ocr.json`de oorspronkelijke bestandsnaam hebben toegevoegd aan . De OCR JSON-uitvoer moet de volgende indeling hebben. Zie het [voorbeeld van het OCR-bestand](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json) voor een volledig voorbeeld. 

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

### <a name="create-the-label-files"></a>De labelbestanden maken

Labelbestanden bevatten associaties met sleutelwaarde die een gebruiker handmatig heeft ingevoerd. Ze zijn nodig voor gelabelde gegevenstraining, maar niet elk bronbestand hoeft een bijbehorend labelbestand te hebben. Bronbestanden zonder labels worden behandeld als gewone trainingsdocumenten. Wij raden vijf of meer gelabelde bestanden aan voor een betrouwbare training.

Wanneer u een labelbestand maakt, kunt&mdash;u optioneel gebieden exacte posities van waarden op het document opgeven. Dit geeft de training nog meer nauwkeurigheid. Regio's worden opgemaakt als een set van acht waarden die overeenkomen met vier X-, Y-coördinaten: linksboven, rechtsboven, rechtsonder en linksonder. De coördinatenwaarden liggen tussen nul en één, geschaald naar de afmetingen van de pagina.

Voor elk bronformulier moet de oorspronkelijke bestandsnaam bij `.labels.json`het bijbehorende labelbestand zijn toegevoegd aan . Het labelbestand moet de volgende indeling hebben. Zie het [voorbeeldlabelbestand](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json) voor een volledig voorbeeld.

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
> U slechts één label toepassen op elk tekstelement en elk label kan slechts één keer per pagina worden toegepast. U momenteel geen label toepassen op meerdere pagina's.


## <a name="train-a-model-using-labeled-data"></a>Een model trainen met gelabelde gegevens

Als u een model wilt trainen met gelabelde gegevens, roept u de **[API voor het aangepaste model train](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** aan door de volgende python-code uit te voeren. Breng de volgende wijzigingen aan voordat u de code uitvoert:

1. Vervang `<Endpoint>` de URL van het eindpunt voor uw bron voor formulierherkenning.
1. Vervang `<SAS URL>` de SAS-URL (Shared Access Signature) van de Azure Blob-opslagcontainer. Als u de SAS-URL wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechtermuisknop op uw container en selecteert **u Handtekening voor gedeelde toegang ophalen**. Controleer of de machtigingen **Lezen** en **Lijst** zijn ingeschakeld en klik op **Maken**. Kopieer vervolgens de waarde in de **sectie URL.** Het zou de `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`vorm moeten hebben: .
1. Vervang `<Blob folder name>` de mapnaam in de blobcontainer waar de invoergegevens zich bevinden. Of als uw gegevens bij de wortel staan, `"prefix"` laat u dit leeg en verwijdert u het veld uit de hoofdtekst van het HTTP-verzoek.

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

Wanneer het trainingsproces is voltooid, `201 (Success)` ontvangt u een antwoord met JSON-inhoud zoals de volgende. Het antwoord is ingekort voor eenvoud.

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

Wanneer het proces is voltooid, `202 (Success)` ontvangt u een antwoord met JSON-inhoud in de volgende indeling. Het antwoord is ingekort voor eenvoud. De belangrijkste sleutel/waardeverenigingen `"documentResults"` zijn in het knooppunt. De resultaten van de Layout API (de inhoud en posities `"readResults"` van alle tekst in het document) bevinden zich in het knooppunt.

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

Controleer `"confidence"` de waarden voor elk sleutel-/waarderesultaat onder het `"documentResults"` knooppunt. U moet ook kijken naar `"readResults"` de vertrouwensscores in het knooppunt, die overeenkomen met de indelingsbewerking. Het vertrouwen van de lay-outresultaten heeft geen invloed op het vertrouwen van de belangrijkste/waardeextractieresultaten, dus u moet beide controleren.
* Als de betrouwbaarheidsscores voor de indelingsbewerking laag zijn, probeert u de kwaliteit van uw invoerdocumenten te verbeteren (zie [Invoervereisten).](../overview.md#input-requirements)
* Als de betrouwbaarheidsscores voor de toets-/waardeextractiebewerking laag zijn, moet u ervoor zorgen dat de documenten die worden geanalyseerd van hetzelfde type zijn als documenten die in de trainingsset worden gebruikt. Als de documenten in de trainingsset variaties in uiterlijk hebben, u overwegen deze in verschillende mappen op te splitsen en één model voor elke variant op te zetten.

### <a name="avoid-cluttered-labels"></a>Voorkom rommelige labels

Soms wanneer u verschillende labels binnen dezelfde regel tekst toepast, kan de service deze labels samenvoegen tot één veld. In een adres u bijvoorbeeld de plaats, de status en de postcode als verschillende velden labelen, maar tijdens het voorspellen worden deze velden niet afzonderlijk herkend.

We begrijpen dat dit scenario essentieel is voor onze klanten en we werken eraan om dit in de toekomst te verbeteren. Momenteel raden we onze gebruikers aan om meerdere rommelige velden als één veld te labelen en vervolgens de termen te scheiden in een nabewerking van de extractieresultaten.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u de Form Recognizer REST API met Python gebruiken om een model te trainen met handmatig gelabelde gegevens. Zie vervolgens de [API-referentiedocumentatie](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) om de API voor formulierherkenning verder te verkennen.
