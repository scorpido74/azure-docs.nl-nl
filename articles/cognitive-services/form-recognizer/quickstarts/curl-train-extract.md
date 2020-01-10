---
title: 'Quick Start: een model trainen en formulier gegevens extra heren met behulp van krul herkenning'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start gebruikt u de formulier Recognizer REST API met krul om een model te trainen en gegevens uit formulieren te extra heren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/03/2019
ms.author: pafarley
ms.openlocfilehash: b95c5511b2f64414fcf165a4346dbb06b1f02435
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75833862"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Snelstartgids: een model voor een formulier herkenning trainen en formulier gegevens extra heren met behulp van de REST API met krul

In deze Quick Start gebruikt u de Azure Form Recognizer REST API met krul naar Train-en Score-formulieren om sleutel-waardeparen en tabellen te extra heren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!IMPORTANT]
> Deze Snelstartgids maakt gebruik van de API voor formulier herkenning v 2.0. Als uw abonnement zich niet in de `West US 2` of `West Europe` regio bevindt, moet u de v 1.0 API gebruiken. Volg in plaats daarvan de [Snelstartgids voor v 1.0](./curl-train-extract-v1.md) .

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze Snelstartgids hebt u het volgende nodig:
- Toegang tot de preview-versie van beperkte toegang van de formulier herkenning. Als u toegang wilt krijgen tot de preview, vult u het formulier voor de [toegangs aanvraag voor de formulier herkenning](https://aka.ms/FormRecognizerRequestAccess) in en verzendt u dit.
- [krul](https://curl.haxx.se/windows/) geïnstalleerd.
- Een set van ten minste zes soorten van hetzelfde type. U gebruikt vijf van deze om het model te trainen en vervolgens test u het met het zesde formulier. Uw formulieren kunnen uit verschillende bestands typen bestaan, maar moeten van hetzelfde type zijn als het document. U kunt een voor [beeld](https://go.microsoft.com/fwlink/?linkid=2090451) van een gegevensset voor deze Quick Start gebruiken. Upload de trainings bestanden naar de hoofdmap van een BLOB storage-container in een Azure Storage-account. U kunt de test bestanden in een afzonderlijke map plaatsen.

## <a name="create-a-form-recognizer-resource"></a>Een resource voor een formulier herkenning maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Model van een formulier herkenning trainen

Eerst hebt u een set trainings gegevens in een Azure Storage BLOB nodig. U moet mini maal vijf ingevulde formulieren (PDF-documenten en/of afbeeldingen) van hetzelfde type/dezelfde structuur als uw belangrijkste invoer gegevens hebben. U kunt ook één leeg formulier met twee ingevulde formulieren gebruiken. De bestands naam van het lege formulier moet het woord ' empty ' bevatten. Zie [een trainings gegevensset voor een aangepast model bouwen](../build-training-data-set.md) voor tips en opties voor het samen stellen van uw trainings gegevens.

> [!NOTE]
> U kunt de functie met gelabelde gegevens hand matig een aantal of al uw trainings gegevens vooraf labelen. Dit is een complexere proces, maar resulteert in een beter getraind model. Zie de sectie [met labels trainen](../overview.md#train-with-labels) in het overzicht voor meer informatie over deze functie.

Als u een formulier Recognizer-model wilt trainen met de documenten in uw Azure Blob-container, roept u de API voor het **[trainende aangepaste model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** aan door de volgende krul opdracht uit te voeren. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `<Endpoint>` door het eind punt dat u hebt verkregen met het formulier Recognizer-abonnement.
1. Vervang `<subscription key>` door de abonnements sleutel die u uit de vorige stap hebt gekopieerd.
1. Vervang `<SAS URL>` door de URL voor Shared Access Signature (SAS) van de Azure Blob Storage-container. Als u de SAS-URL wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechter muisknop op uw container en selecteert u **gedeelde toegangs handtekening ophalen**. Zorg ervoor dat de machtigingen **lezen** en **lijst** zijn ingeschakeld en klik op **maken**. Kopieer vervolgens de waarde in de sectie **URL** . Het moet de volgende indeling hebben: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

U ontvangt een `201 (Success)` antwoord met een **locatie** -header. De waarde van deze header is de ID van het nieuwe model dat wordt getraind. 

## <a name="get-training-results"></a>Trainings resultaten ophalen

Nadat u de trein bewerking hebt gestart, gebruikt u een nieuwe bewerking, **[haalt u aangepast model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetCustomModel)** op om de trainings status te controleren. Geef de model-ID door aan deze API-aanroep om de trainings status te controleren:

1. Vervang `<Endpoint>` door het eind punt dat u hebt verkregen met de abonnements sleutel van uw formulier herkenning.
1. `<subscription key>` vervangen door de sleutel van uw abonnement
1. Vervang `<model ID>` door de model-ID die u in de vorige stap hebt ontvangen

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

U ontvangt een `200 (Success)` antwoord met een JSON-hoofd tekst in de volgende indeling. Let op het veld `"status"`. Dit heeft de waarde `"ready"` zodra de training is voltooid. Als het model niet is getraind, moet u de service opnieuw uitvoeren door de opdracht opnieuw uit te voeren. We raden u aan een interval van één seconde of meer tussen aanroepen aan te bevelen.

Het `"modelId"` veld bevat de ID van het model dat u wilt trainen. Dit hebt u nodig voor de volgende stap.

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

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Formulieren analyseren voor sleutel-waardeparen en tabellen

Vervolgens gebruikt u uw pas getrainde model voor het analyseren van een document en het extra heren van sleutel-waardeparen en tabellen. Roep de **[analyse formulier](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** -API aan door de volgende krul opdracht uit te voeren. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `<Endpoint>` door het eind punt dat u hebt verkregen op basis van de abonnements sleutel van uw formulier herkenning. U vindt deze op het tabblad **overzicht** van resource Recognizer.
1. Vervang `<model ID>` door de model-ID die u in de vorige sectie hebt ontvangen.
1. Vervang `<SAS URL>` door een SAS-URL naar uw bestand in azure Storage. Volg de stappen in de sectie training, maar in plaats van een SAS-URL voor de hele BLOB-container op te halen, haalt u er een op voor het specifieke bestand dat u wilt analyseren.
1. Vervang `<subscription key>` door uw abonnementssleutel.

```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```

U ontvangt een `202 (Success)` antwoord met een **bewerkings locatie-** header. De waarde van deze header bevat een resultaten-ID die u gebruikt om de resultaten van de analyse bewerking bij te houden. Sla deze resultaat-ID op voor de volgende stap.

## <a name="get-the-analyze-results"></a>De resultaten analyseren

Gebruik de volgende API om de resultaten van de analyse bewerking op te vragen.

1. Vervang `<Endpoint>` door het eind punt dat u hebt verkregen op basis van de abonnements sleutel van uw formulier herkenning. U vindt deze op het tabblad **overzicht** van resource Recognizer.
1. Vervang `<result ID>` door de ID die u in de vorige sectie hebt ontvangen.
1. Vervang `<subscription key>` door uw abonnementssleutel.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

U ontvangt een `200 (Success)` antwoord met een JSON-hoofd tekst in de volgende indeling. De uitvoer is kort voor eenvoud. U ziet het veld `"status"` onderaan. Dit heeft de waarde `"succeeded"` wanneer de analyse bewerking is voltooid. Als de analyse bewerking nog niet is voltooid, moet u de service opnieuw uitvoeren door de opdracht opnieuw uit te voeren. We raden u aan een interval van één seconde of meer tussen aanroepen aan te bevelen.

De belangrijkste koppelingen en tabellen voor sleutel/waarde-paars bevinden zich in het knoop punt `"pageResults"`. Als u ook tekst extractie hebt opgegeven via de para meter *includeTextDetails* URL, worden in het knoop punt `"readResults"` de inhoud en posities van alle tekst in het document weer gegeven.

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

In deze Quick Start hebt u de formulier Recognizer REST API met krul gebruikt om een model te trainen en uit te voeren in een voorbeeld scenario. Raadpleeg vervolgens de referentie documentatie om de API voor het formulier Recognizer te verkennen.

> [!div class="nextstepaction"]
> [Documentatie over REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
