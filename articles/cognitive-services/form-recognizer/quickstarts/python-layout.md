---
title: 'Snelstartgids: tekst-en indelings informatie extra heren met behulp van python-formulier herkenner'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start gebruikt u de indeling REST API voor formulier herkenning met python om tekst-en tabel gegevens uit uw formulieren te lezen.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.custom: tracking-python
ms.openlocfilehash: 7600efef943f5e38b8187474e82e36590d576a17
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84610862"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>Snelstartgids: tekst-en indelings informatie extra heren met behulp van de formulier Recognizer REST API met python

In deze Quick Start gebruikt u de Azure Form Recognizer REST API met python om tekst indelings informatie en tabel gegevens op te halen uit formulier documenten.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze Snelstartgids hebt u het volgende nodig:
- [Python](https://www.python.org/downloads/) geïnstalleerd (als u het voor beeld lokaal wilt uitvoeren).
- Een formulier document. U kunt een installatie kopie downloaden uit de [set met voorbeeld gegevens](https://go.microsoft.com/fwlink/?linkid=2090451) voor deze Quick Start.

> [!NOTE]
> In deze Snelstartgids wordt een lokaal opgeslagen document gebruikt. Zie de [referentie documentatie](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)voor meer informatie over het gebruik van externe bestanden die worden geopend via URL.


## <a name="create-a-form-recognizer-resource"></a>Een resource voor een formulier herkenning maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>De indeling van het formulier analyseren

Om te beginnen met het analyseren van de lay-out roept u de **[indelings](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** -API analyseren op met het python-script hieronder. Voordat u het script uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang door `<Endpoint>` het eind punt dat u hebt verkregen met het formulier Recognizer-abonnement.
1. Vervang door `<path to your form>` het pad naar het lokale formulier document.
1. Vervang door `<subscription key>` de abonnements sleutel die u uit de vorige stap hebt gekopieerd.

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

1. Sla de code op in een bestand met de extensie. py. Bijvoorbeeld *Form-Recognizer-layout.py*.
1. Open een opdrachtpromptvenster.
1. Typ bij de prompt de opdracht `python` om het voorbeeld uit te voeren. Bijvoorbeeld `python form-recognizer-layout.py`.

U ontvangt een `202 (Success)` antwoord met een **bewerkings locatie** header, die door het script wordt afgedrukt naar de-console. Deze header bevat een bewerkings-ID die u kunt gebruiken om de status van de asynchrone bewerking op te vragen en de resultaten op te halen. In de volgende voorbeeld waarde is de teken reeks na `operations/` de bewerkings-id.

```console
https://cognitiveservice/formrecognizer/v2.0-preview/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-layout-results"></a>De resultaten van de indeling ophalen

Nadat u de **indelings** -API analyseren hebt aangeroepen, roept u de API voor het analyseren van de **[indeling ophalen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** aan om de status van de bewerking en de geëxtraheerde gegevens op te halen. Voeg de volgende code toe onder aan het python-script. Deze code maakt gebruik van de bewerkings-ID-waarde in een nieuwe API-aanroep. Met dit script wordt de API met regel matige tussen pozen aangeroepen totdat de resultaten beschikbaar zijn. We raden een interval van één seconde of meer aan.

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
1. Gebruik opnieuw de `python` opdracht om het voor beeld uit te voeren. Bijvoorbeeld `python form-recognizer-layout.py`.

### <a name="examine-the-response"></a>Het antwoord bekijken

Met het script worden antwoorden op de console afgedrukt totdat de **analyse-lay** -outbewerking is voltooid. Vervolgens worden de geëxtraheerde gegevens in JSON-indeling afgedrukt. Het `"readResults"` knoop punt bevat elke tekst regel met de betreffende plaatsing van de selectie vakjes op de pagina. `"pageResults"`In het veld wordt elk stuk tekst in tabellen weer gegeven, elk met de rij-kolom coördinaat.

Bekijk de volgende factuur afbeelding en de bijbehorende JSON-uitvoer. De uitvoer is kort voor eenvoud.

> [!div class="mx-imgBorder"]
> ![Contoso-factuur document met een tabel](../media/contoso-invoice.png)

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

In deze Quick Start hebt u de formulier Recognizer REST API met python gebruikt om de tekst indeling van een factuur uit te pakken. Raadpleeg vervolgens de referentie documentatie om de API voor het formulier Recognizer te verkennen.

> [!div class="nextstepaction"]
> [Documentatie over REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)
