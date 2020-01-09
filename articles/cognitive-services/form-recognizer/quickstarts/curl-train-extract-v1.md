---
title: 'Quick Start: een model trainen en formulier gegevens extra heren met behulp van krul herkenning'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start gebruikt u de formulier Recognizer REST API met krul om een model te trainen en gegevens uit formulieren te extra heren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: pafarley
ms.openlocfilehash: 97e7341fa48bbd381bb193dcb1436e32564bb591
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473981"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Snelstartgids: een model voor een formulier herkenning trainen en formulier gegevens extra heren met behulp van de REST API met krul

In deze Quick Start gebruikt u de Azure Form Recognizer REST API met krul naar Train-en Score-formulieren om sleutel-waardeparen en tabellen te extra heren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!IMPORTANT]
> Deze Snelstartgids maakt gebruik van de API voor formulier herkenning v 1.0. Als uw abonnement zich in de `West US 2` of `West Europe` regio bevindt, moet u in plaats daarvan de v[2,0 API Quick](./curl-train-extract.md) start volgen.

## <a name="prerequisites"></a>Vereisten
Voor het volt ooien van deze Snelstartgids hebt u het volgende nodig:
- Toegang tot de preview-versie van beperkte toegang van de formulier herkenning. Als u toegang wilt krijgen tot de preview, vult u het formulier voor de [toegangs aanvraag voor de formulier herkenning](https://aka.ms/FormRecognizerRequestAccess) in en verzendt u dit.
- [krul](https://curl.haxx.se/windows/) geïnstalleerd.
- Een set van ten minste vijf vormen van hetzelfde type. U gebruikt deze gegevens om het model te trainen. U kunt een voor [beeld](https://go.microsoft.com/fwlink/?linkid=2090451) van een gegevensset voor deze Quick Start gebruiken. Upload de trainings bestanden naar de hoofdmap van een BLOB storage-container in een Azure Storage-account.

## <a name="create-a-form-recognizer-resource"></a>Een resource voor een formulier herkenning maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Model van een formulier herkenning trainen

Eerst hebt u een set trainings gegevens in een Azure Storage BLOB nodig. U moet mini maal vijf ingevulde formulieren (PDF-documenten en/of afbeeldingen) van hetzelfde type/dezelfde structuur als uw belangrijkste invoer gegevens hebben. U kunt ook één leeg formulier met twee ingevulde formulieren gebruiken. De bestands naam van het lege formulier moet het woord ' empty ' bevatten. Zie [een trainings gegevensset voor een aangepast model bouwen](../build-training-data-set.md) voor tips en opties voor het samen stellen van uw trainings gegevens.

Als u een formulier Recognizer-model wilt trainen met de documenten in uw Azure Blob-container, roept u de **Train** API aan door de volgende krul opdracht uit te voeren. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `<Endpoint>` door het eind punt dat u hebt verkregen met het formulier Recognizer-abonnement.
1. Vervang `<subscription key>` door de abonnements sleutel die u uit de vorige stap hebt gekopieerd.
1. Vervang `<SAS URL>` door de URL voor Shared Access Signature (SAS) van de Azure Blob Storage-container. Als u de SAS-URL wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechter muisknop op uw container en selecteert u **gedeelde toegangs handtekening ophalen**. Zorg ervoor dat de machtigingen **lezen** en **lijst** zijn ingeschakeld en klik op **maken**. Kopieer vervolgens de waarde in de sectie **URL** . Het moet de volgende indeling hebben: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/train" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

U ontvangt een `200 (Success)` antwoord met de volgende JSON-uitvoer:

```json
{
  "modelId": "59e2185e-ab80-4640-aebc-f3653442617b",
  "trainingDocuments": [
    {
      "documentName": "Invoice_1.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_2.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_3.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_4.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_5.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    }
  ],
  "errors": []
}
```

Let op de waarde van `"modelId"`. U hebt deze nodig in de volgende stappen.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Sleutel-waardeparen en tabellen uit formulieren extra heren

Vervolgens analyseert u een document en extraheert u sleutel-waardeparen en-tabellen. Roep het **model-analyse-** API aan door de krul opdracht uit te voeren die volgt. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `<Endpoint>` door het eind punt dat u hebt verkregen van het formulier Recognizer-abonnement.
1. Vervang `<modelID>` door de model-ID die u in de vorige sectie hebt ontvangen.
1. Vervang `<path to your form>` door het bestandspad van uw formulier (bijvoorbeeld C:\temp\file.PDF). Voor deze Quick Start kunt u de bestanden in de map **test** van de [set met voorbeeld gegevens](https://go.microsoft.com/fwlink/?linkid=2090451)gebruiken.
1. Vervang `<file type>` door het bestands type. Ondersteunde typen: `application/pdf`, `image/jpeg``image/png`.
1. Vervang `<subscription key>` door uw abonnementssleutel.


```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@\"<path to your form>\";type=<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Het antwoord bekijken

Er wordt een reactie van succes geretourneerd in JSON. Het vertegenwoordigt de sleutel-waardeparen en de tabellen die zijn geëxtraheerd uit het formulier:

```bash
{
  "status": "success",
  "pages": [
    {
      "number": 1,
      "height": 792,
      "width": 612,
      "clusterId": 0,
      "keyValuePairs": [
        {
          "key": [
            {
              "text": "Address:",
              "boundingBox": [
                57.4,
                683.1,
                100.5,
                683.1,
                100.5,
                673.7,
                57.4,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "1 Redmond way Suite",
              "boundingBox": [
                57.4,
                671.3,
                154.8,
                671.3,
                154.8,
                659.2,
                57.4,
                659.2
              ],
              "confidence": 0.86
            },
            {
              "text": "6000 Redmond, WA",
              "boundingBox": [
                57.4,
                657.1,
                146.9,
                657.1,
                146.9,
                645.5,
                57.4,
                645.5
              ],
              "confidence": 0.86
            },
            {
              "text": "99243",
              "boundingBox": [
                57.4,
                643.4,
                85,
                643.4,
                85,
                632.3,
                57.4,
                632.3
              ],
              "confidence": 0.86
            }
          ]
        },
        {
          "key": [
            {
              "text": "Invoice For:",
              "boundingBox": [
                316.1,
                683.1,
                368.2,
                683.1,
                368.2,
                673.7,
                316.1,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "Microsoft",
              "boundingBox": [
                374,
                687.9,
                418.8,
                687.9,
                418.8,
                673.7,
                374,
                673.7
              ],
              "confidence": 1
            },
            {
              "text": "1020 Enterprise Way",
              "boundingBox": [
                373.9,
                673.5,
                471.3,
                673.5,
                471.3,
                659.2,
                373.9,
                659.2
              ],
              "confidence": 1
            },
            {
              "text": "Sunnayvale, CA 87659",
              "boundingBox": [
                373.8,
                659,
                479.4,
                659,
                479.4,
                645.5,
                373.8,
                645.5
              ],
              "confidence": 1
            }
          ]
        }
      ],
      "tables": [
        {
          "id": "table_0",
          "columns": [
            {
              "header": [
                {
                  "text": "Invoice Number",
                  "boundingBox": [
                    38.5,
                    585.2,
                    113.4,
                    585.2,
                    113.4,
                    575.8,
                    38.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "34278587",
                    "boundingBox": [
                      38.5,
                      547.3,
                      82.8,
                      547.3,
                      82.8,
                      537,
                      38.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Date",
                  "boundingBox": [
                    139.7,
                    585.2,
                    198.5,
                    585.2,
                    198.5,
                    575.8,
                    139.7,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/18/2017",
                    "boundingBox": [
                      139.7,
                      546.8,
                      184,
                      546.8,
                      184,
                      537,
                      139.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Due Date",
                  "boundingBox": [
                    240.5,
                    585.2,
                    321,
                    585.2,
                    321,
                    575.8,
                    240.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/24/2017",
                    "boundingBox": [
                      240.5,
                      546.8,
                      284.8,
                      546.8,
                      284.8,
                      537,
                      240.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Charges",
                  "boundingBox": [
                    341.3,
                    585.2,
                    381.2,
                    585.2,
                    381.2,
                    575.8,
                    341.3,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "$56,651.49",
                    "boundingBox": [
                      387.6,
                      546.4,
                      437.5,
                      546.4,
                      437.5,
                      537,
                      387.6,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "VAT ID",
                  "boundingBox": [
                    442.1,
                    590,
                    474.8,
                    590,
                    474.8,
                    575.8,
                    442.1,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "PT",
                    "boundingBox": [
                      447.7,
                      550.6,
                      460.4,
                      550.6,
                      460.4,
                      537,
                      447.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            }
          ]
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u de formulier Recognizer REST API met krul gebruikt om een model te trainen en uit te voeren in een voorbeeld scenario. Raadpleeg vervolgens de referentie documentatie om de API voor het formulier Recognizer te verkennen.

> [!div class="nextstepaction"]
> [Documentatie over REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel)