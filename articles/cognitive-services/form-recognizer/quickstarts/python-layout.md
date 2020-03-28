---
title: 'Snelstart: tekst- en lay-outgegevens extraheren met Python - Formulierherkenning'
titleSuffix: Azure Cognitive Services
description: In deze quickstart gebruikt u de API voor formulierherkenningsindeling met Python om tekst- en tabelgegevens uit uw formulieren te lezen.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 342ae7e42c85ad661c04ba4ebb6629673f4af4dc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77482273"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>Snelstart: tekst- en lay-outgegevens extraheren met de API Voor formulierherkenning rest met Python

In deze snelstart gebruikt u de Azure Form Recognizer REST API met Python om tekstlay-outgegevens en tabelgegevens uit formulierdocumenten te extraheren.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om deze snelle start te voltooien, moet u het:
- [Python](https://www.python.org/downloads/) geïnstalleerd (als u het voorbeeld lokaal wilt uitvoeren).
- Een formulierdocument. U een afbeelding downloaden uit de [voorbeeldgegevensset](https://go.microsoft.com/fwlink/?linkid=2090451) voor deze quickstart.

## <a name="create-a-form-recognizer-resource"></a>Een bron voor formulierherkenning maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>De formulierindeling analyseren

Als u wilt beginnen met het analyseren van de lay-out, roept u de API Voor het analyseren van **[lay-out](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** aan met behulp van het Python-script hieronder. Breng de volgende wijzigingen aan voordat u het script uitvoert:

1. Vervang `<Endpoint>` het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnement.
1. Vervang `<path to your form>` het pad naar uw lokale formulierdocument.
1. Vervang `<subscription key>` de abonnementssleutel die u uit de vorige stap hebt gekopieerd.

    ```python
    ########### Python Form Recognizer Async Layout #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Sla de code op in een bestand met een .py-extensie. bijvoorbeeld *form-recognizer-layout.py*.
1. Open een opdrachtpromptvenster.
1. Typ bij de prompt de opdracht `python` om het voorbeeld uit te voeren. Bijvoorbeeld `python form-recognizer-layout.py`.

U ontvangt een `202 (Success)` antwoord met een koptekst **Operatie-locatie,** die het script op de console afdrukt. Deze koptekst bevat een bewerkings-id die u gebruiken om de status van de asynchrone bewerking op te vragen en de resultaten op te halen. In het volgende voorbeeldwaarde `operations/` is de tekenreeks na de bewerkings-id.

```console
https://cognitiveservice/formrecognizer/v2.0-preview/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-layout-results"></a>De indelingsresultaten opmaken

Nadat u de **API Voor het analyseren van lay-out** hebt aangeroepen, roept u de API **[voor het analyseren van indelingsresultaten](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** aan om de status van de bewerking en de geëxtraheerde gegevens te krijgen. Voeg de volgende code toe aan de onderkant van uw Python-script. Deze code gebruikt de waarde van de bewerkings-id in een nieuwe API-aanroep. Dit script roept de API op regelmatige tijdstippen aan totdat de resultaten beschikbaar zijn. We raden een interval van een seconde of meer aan.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Layout results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Layout Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Layout Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Sla het script op.
1. Gebruik opnieuw `python` de opdracht om het voorbeeld uit te voeren. Bijvoorbeeld `python form-recognizer-layout.py`.

### <a name="examine-the-response"></a>Het antwoord bekijken

Het script drukt reacties op de console af totdat de bewerking **Indeling analyseren** is voltooid. Vervolgens worden de geëxtraheerde gegevens afgedrukt in JSON-indeling. Het `"readResults"` knooppunt bevat elke regel tekst met de respectievelijke plaatsing van het selectiekader op de pagina. Het `"pageResults"` veld toont elk stuk tekst in tabellen, elk met de rij-kolom coördinaat.

Zie de volgende factuurafbeelding en de bijbehorende JSON-uitvoer. De output is ingekort voor eenvoud.

> [!div class="mx-imgBorder"]
> ![Contoso-factuurdocument met een tabel](../media/contoso-invoice.png)

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-11-12T19:55:36Z",
  "lastUpdatedDateTime":"2019-11-12T19:55:43Z",
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
              0.5384,
              1.1583,
              1.4466,
              1.1583,
              1.4466,
              1.3534,
              0.5384,
              1.3534
            ],
            "text":"Contoso",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.5384,
                  1.1583,
                  1.4466,
                  1.1583,
                  1.4466,
                  1.3534,
                  0.5384,
                  1.3534
                ],
                "text":"Contoso",
                "confidence":1
              }
            ]
          },
          { 
            "language":"en",
            "boundingBox":[ 
              0.7994,
              1.5143,
              1.3836,
              1.5143,
              1.3836,
              1.6154,
              0.7994,
              1.6154
            ],
            "text":"Address:",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.7994,
                  1.5143,
                  1.3836,
                  1.5143,
                  1.3836,
                  1.6154,
                  0.7994,
                  1.6154
                ],
                "text":"Address:",
                "confidence":1
              }
            ]
          },
          ...
          { 
            "language":"en",
            "boundingBox":[ 
              6.2285,
              3.4114,
              6.3919,
              3.4114,
              6.3919,
              3.5119,
              6.2285,
              3.5119
            ],
            "text":"PT",
            "words":[ 
              { 
                "boundingBox":[ 
                  6.2285,
                  3.4114,
                  6.3919,
                  3.4114,
                  6.3919,
                  3.5119,
                  6.2285,
                  3.5119
                ],
                "text":"PT",
                "confidence":1
              }
            ]
          }
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
                  "#/readResults/0/lines/8/words/0",
                  "#/readResults/0/lines/8/words/1"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de FORM Recognizer REST API met Python gebruikt om de tekstindeling van een factuur te extraheren. Zie vervolgens de referentiedocumentatie om de API voor formulierherkenning verder te verkennen.

> [!div class="nextstepaction"]
> [REST API-referentiedocumentatie](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)
