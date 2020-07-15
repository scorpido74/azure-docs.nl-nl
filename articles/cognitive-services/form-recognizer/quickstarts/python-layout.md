---
title: 'Quickstart: Tekst- en indelingsgegevens extraheren met behulp van Python - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: In deze quickstart gebruikt u de Form Recognizer Layout REST API met Python om tekst- en tabelgegevens in uw formulieren te lezen.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.custom: tracking-python
ms.openlocfilehash: 62cbaec3e4eaffb9ff3b29e97516ed7e039b38ff
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86028114"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>Quickstart: Tekst- en indelingsgegevens extraheren met behulp van de Form Recognizer REST API en Python

In deze quickstart gebruikt u de Azure Form Recognizer REST API met Python om tekstindelingsgegevens en tabelgegevens op te halen uit formulierdocumenten.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze quickstart te voltooien:
- [Python](https://www.python.org/downloads/) moet zijn geïnstalleerd (als u het voorbeeld lokaal wilt uitvoeren).
- Een formulierdocument. U kunt een afbeelding downloaden uit de [set met voorbeeldgegevens](https://go.microsoft.com/fwlink/?linkid=2090451) voor deze quickstart.

> [!NOTE]
> In deze quickstart wordt een lokaal opgeslagen document gebruikt. Zie de [referentiedocumentatie](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync) voor meer informatie over het gebruik van externe bestanden die worden geopend via URL.


## <a name="create-a-form-recognizer-resource"></a>Een Form Recognizer-resource maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>De indeling van het formulier analyseren

Als u de indeling wilt analyseren, roept u de **[Analyze Layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** API op met behulp van het Python-script hieronder. Voordat u het script uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `<Endpoint>` door het eindpunt dat u hebt verkregen met uw Form Recognizer-abonnement.
1. Vervang `<path to your form>` door het pad naar het lokale formulierdocument.
1. Vervang `<subscription key>` door de abonnementssleutel die u uit de vorige stap hebt gekopieerd.

    ```python
    ########### Python Form Recognizer Async Layout #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.0/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': 'application/json',
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

1. Sla de code op in een bestand met de extensie .py. Bijvoorbeeld *form-recognizer-layout.py*.
1. Open een opdrachtpromptvenster.
1. Typ bij de prompt de opdracht `python` om het voorbeeld uit te voeren. Bijvoorbeeld `python form-recognizer-layout.py`.

U ontvangt een `202 (Success)`-antwoord met een **Operation-Location**-header, die het script naar de console afdrukt. Deze header bevat een bewerkings-id die u kunt gebruiken om query's uit te voeren op de status van de asynchrone bewerking en de resultaten op te halen. In de volgende voorbeeldwaarde is de tekenreeks na `operations/` de bewerkings-id.

```console
https://cognitiveservice/formrecognizer/v2.0/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-layout-results"></a>De indelingsresultaten ophalen

Nadat u de **Analyze Layout** API hebt aangeroepen, roept u de **[Get Analyze Layout Result](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** API aan om de status van de bewerking en de geëxtraheerde gegevens op te halen. Voeg onderaan uw Python-script de volgende code toe. Deze code maakt gebruik van de bewerkings-id-waarde in een nieuwe API-aanroep. Met dit script wordt de API met regelmatige tussenpozen aangeroepen totdat de resultaten beschikbaar zijn. We raden een interval van minimaal één seconde aan.

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
1. Gebruik opnieuw de `python`-opdracht om de sample uit te voeren. Bijvoorbeeld `python form-recognizer-layout.py`.

### <a name="examine-the-response"></a>Het antwoord bekijken

Met het script worden antwoorden naar de console afgedrukt totdat de **Analyze Layout**-bewerking is voltooid. Vervolgens worden de geëxtraheerde gegevens in JSON-indeling afgedrukt. Het knooppunt `"readResults"` bevat elke tekstregel met het bijbehorende begrenzingsvak op de pagina. In het veld `"pageResults"` wordt elk stuk tekst in tabellen weergegeven, elk stuk met een eigen rij-kolomcoördinaat.

Bekijk de volgende factuurafbeelding en de bijbehorende JSON-uitvoer. De uitvoer is voor het gemak ingekort.

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

In deze quickstart hebt u de Form Recognizer REST API met Python gebruikt om de tekstindeling van een factuur te extraheren. Raadpleeg hierna de referentiedocumentatie om de Form Recognizer API verder te verkennen.

> [!div class="nextstepaction"]
> [REST API-referentiedocumentatie](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)
