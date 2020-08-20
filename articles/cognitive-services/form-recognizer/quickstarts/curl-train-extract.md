---
title: 'Quickstart: Een model trainen en formuliergegevens extraheren met behulp van cURL - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: In deze quickstart gebruikt u de Form Recognizer REST API met cURL om een model te trainen en gegevens te extraheren uit formulieren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: d0bf5da3633ce8f34e34cbdf0cee12a94c128d72
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88517962"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Quickstart: Een Form Recognizer-model trainen en formuliergegevens extraheren met behulp van de REST API met cURL

In deze Quick Start gebruikt u de REST API van Azure Form Recognizer met cURL om formulieren te trainen en scoren voor het extraheren van sleutel-waardeparen en tabellen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze quickstart hebt u het volgende nodig:
- [cURL](https://curl.haxx.se/windows/) moet zijn geïnstalleerd.
- Een set van minimaal zes formulieren van hetzelfde type. U gebruikt vijf ervan om het model te trainen en vervolgens test u het met het zesde formulier. Uw formulieren kunnen uit verschillende bestandstypen bestaan, maar moeten van hetzelfde type zijn als het document. U kunt voor deze quickstart een [set voorbeeldgegevens](https://go.microsoft.com/fwlink/?linkid=2090451) gebruiken. Upload de trainingsbestanden naar de hoofdmap van een Blob Storage-container in een Azure Storage-account met een standaardprestatielaag. U kunt de testbestanden in een afzonderlijke map plaatsen.

## <a name="create-a-form-recognizer-resource"></a>Een Form Recognizer-resource maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Een Form Recognizer-model trainen

Eerst hebt u een set trainingsgegevens in een Azure Storage-blob nodig. U moet minimaal vijf ingevulde formulieren (PDF-documenten en/of afbeeldingen) van hetzelfde type/dezelfde structuur als uw belangrijkste invoergegevens hebben. U kunt ook één leeg formulier met twee ingevulde formulieren gebruiken. De bestandsnaam van het lege formulier moet het woord empty bevatten. Zie [Een set met trainingsgegevens voor een aangepast model bouwen](../build-training-data-set.md) voor tips en opties voor het samenstellen van uw trainingsgegevens.

> [!NOTE]
> U kunt via de functie met gelabelde gegevens handmatig enkele of al uw trainingsgegevens vooraf labelen. Dit is een complexer proces, maar resulteert in een beter getraind model. Zie de sectie [Trainen met labels](../overview.md#train-with-labels) van het overzicht voor meer informatie over deze functie.

Als u een Form Recognizer-model wilt trainen met de documenten in uw Azure Blob-container, roept u de API **[Aangepast model trainen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** aan door de volgende cURL-opdracht uit te voeren. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `<Endpoint>` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnement.
1. Vervang `<subscription key>` door de abonnementssleutel die u uit de vorige stap hebt gekopieerd.
1. Vervang `<SAS URL>` door de URL van de SAS (Shared Access Signature) van de Azure Blob Storage-container. Als u de SAS-URL wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechtermuisknop op uw container en selecteert u **Handtekening voor gedeelde toegang ophalen**. Controleer of de machtigingen **Lezen** en **Lijst** zijn ingeschakeld en klik op **Maken**. Kopieer vervolgens de waarde in de sectie **URL**. Deze moet de notatie `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` hebben.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

U ontvangt een `201 (Success)` antwoord met een **Locatie**-header. De waarde van deze header is de id van het nieuwe model dat wordt getraind.

## <a name="get-training-results"></a>Trainingsresultaten ophalen

Nadat u de trainbewerking hebt gestart, gebruikt u een nieuwe bewerking **[Aangepast model ophalen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** om de trainingsstatus te controleren. Geef de model-id door aan deze API-aanroep om de trainingsstatus te controleren:

1. Vervang `<Endpoint>` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnementssleutel.
1. Vervang `<subscription key>` door uw abonnementssleutel.
1. Vervang `<model ID>` door de model-id in de vorige stap.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

U ontvangt een `200 (Success)`-antwoord met een JSON-hoofdtekst in de volgende indeling. Let op het veld `"status"`. Dit heeft de waarde `"ready"` zodra de training is voltooid. Als de training van het model niet is voltooid, moet u opnieuw een query uitvoeren op de service door de opdracht opnieuw uit te voeren. Een interval van één seconde of meer tussen oproepen wordt aanbevolen.

Het veld `"modelId"` bevat de id van het model dat u traint. U hebt deze nodig voor de volgende stap.

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

Vervolgens gebruikt u uw pas getrainde model voor het analyseren van een document en het extraheren van de sleutel-waardeparen en tabellen ervan. Roep de API **[Formulier analyseren](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** aan door de volgende cURL-opdracht uit te voeren. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `<Endpoint>` door het eindpunt dat u hebt verkregen van uw Form Recognizer-abonnementssleutel. U vindt deze op het tabblad **Overzicht** van uw Form Recognizer-resource.
1. Vervang `<model ID>` door de model-id die u in de vorige sectie hebt gekregen.
1. Vervang `<SAS URL>` door een SAS-URL naar uw bestand in Azure Storage. Volg de stappen in de sectie Training, maar in plaats van een SAS-URL voor de hele blobcontainer op te halen, haalt u er een op voor het specifieke bestand dat u wilt analyseren.
1. Vervang `<subscription key>` door uw abonnementssleutel.

```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```

U ontvangt een `202 (Success)`-antwoord met een **Bewerking-Locatie**-header. De waarde van deze header bevat een resultaten-id die u gebruikt om de resultaten van de analysebewerking bij te houden. Sla deze resultaten-id op voor de volgende stap.

## <a name="get-the-analyze-results"></a>De resultaten van de analyse ophalen

Gebruik de volgende API om de resultaten van de analysebewerking op te vragen.

1. Vervang `<Endpoint>` door het eindpunt dat u hebt verkregen van uw Form Recognizer-abonnementssleutel. U vindt deze op het tabblad **Overzicht** van uw Form Recognizer-resource.
1. Vervang `<result ID>` door de id die u in de vorige sectie hebt gekregen.
1. Vervang `<subscription key>` door uw abonnementssleutel.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

U ontvangt een `200 (Success)`-antwoord met een JSON-hoofdtekst in de volgende indeling. De uitvoer is voor het gemak ingekort. U ziet het veld `"status"` onderaan. Dit heeft de waarde `"succeeded"` wanneer de analysebewerking is voltooid. Als de bewerking Analyse niet is voltooid, moet u opnieuw een query uitvoeren op de service door de opdracht opnieuw uit te voeren. Een interval van één seconde of meer tussen oproepen wordt aanbevolen.

De hoofdkoppelingen en tabellen voor sleutel/waardeparen bevinden zich in het knooppunt `"pageResults"`. Als u ook extractie voor tekst zonder opmaak hebt opgegeven via de URL-parameter *includeTextDetails*, worden in het knooppunt `"readResults"` de inhoud en posities van alle tekst in het document weergegeven.

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

In deze quickstart hebt u de REST API van Form Recognizer met cURL gebruikt om een model te trainen en dit in een voorbeeldscenario uit te voeren. Raadpleeg hierna de referentiedocumentatie om de Form Recognizer API verder te verkennen.

> [!div class="nextstepaction"]
> [REST API-referentiedocumentatie](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
