---
title: 'Snelstartgids: ontvangst gegevens ophalen met behulp van python-formulier herkenner'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start gebruikt u de formulier Recognizer REST API met python om gegevens te extra heren uit installatie kopieën van verkoop ontvangsten.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: ef5c9e8d548e8acbcbdbe83f6e7c9965c798ad44
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931268"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>Snelstartgids: ontvangst gegevens ophalen met behulp van de formulier Recognizer REST API met python

In deze Quick Start gebruikt u de Azure Form Recognizer REST API met python om relevante informatie in verkoop ontvangsten te extra heren en te identificeren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten
Voor het volt ooien van deze Snelstartgids hebt u het volgende nodig:
- Toegang tot de preview-versie van beperkte toegang van de formulier herkenning. Als u toegang wilt krijgen tot de preview, vult u het formulier voor de [toegangs aanvraag voor de formulier herkenning](https://aka.ms/FormRecognizerRequestAccess) in en verzendt u dit.
- [Python](https://www.python.org/downloads/) geïnstalleerd (als u het voor beeld lokaal wilt uitvoeren).
- Een URL voor een afbeelding van een ontvangst. U kunt een [voorbeeld afbeelding](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) gebruiken voor deze Quick Start.

## <a name="create-a-form-recognizer-resource"></a>Een resource voor een formulier herkenning maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Een kwitantie analyseren

Als u wilt beginnen met het analyseren van een ontvangst, roept u de API voor het **analyseren** van de ontvangst aan met het python-script hieronder. Voordat u het script uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `<Endpoint>` door het eind punt dat u hebt verkregen met het formulier Recognizer-abonnement.
1. Vervang `<your receipt URL>` door het URL-adres van een kwitantie-installatie kopie.
1. Vervang `<subscription key>` door de abonnements sleutel die u uit de vorige stap hebt gekopieerd.

    ```python
    import http.client, urllib.request, urllib.parse, urllib.error, base64

    source = r"<your receipt URL>"
    body = {"url":source}
    body = json.dumps(body)

    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<subscription key>',
    }

    try:
        conn = http.client.HTTPSConnection('<Endpoint>')
        conn.request("POST", "/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze", body, headers)
        response = conn.getresponse()
        data = response.read()
        operationURL = "" + response.getheader("Operation-Location")
        print ("Operation-Location header: " + operationURL)
        conn.close()
    except Exception as e:
        print(e)
        exit()
    ```

1. Sla de code op in een bestand met de extensie. py. Bijvoorbeeld *Form-Recognizer-Receipts.py*.
1. Open een opdrachtpromptvenster.
1. Typ bij de prompt de opdracht `python` om het voorbeeld uit te voeren. Bijvoorbeeld `python form-recognizer-receipts.py`.

U ontvangt een `202 (Success)` antwoord met een **bewerkings locatie** header, die door het script wordt afgedrukt naar de-console. Deze header bevat een bewerkings-ID die u kunt gebruiken om de status van de bewerking op te vragen en de analyse resultaten op te halen. In de volgende voorbeeld waarde is de teken reeks na `operations/` de bewerkings-ID.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>De ontvangst resultaten ophalen

Nadat u de trans actie voor het analyseren van de **kwitantie** hebt aangeroepen, roept u de **Get-ontvangst resultaat** -API op om de status van de bewerking en de geëxtraheerde gegevens op te halen. Voeg de volgende code toe onder aan het python-script. Hiermee wordt de waarde van de bewerkings-ID geëxtraheerd en door gegeven aan een nieuwe API-aanroep. De bewerking is asynchroon, waardoor dit script de API regel matig aanroept totdat de resultaten beschikbaar zijn. We raden een interval van één seconde of meer aan.

```python
operationId = operationURL.split("operations/")[1]

conn = http.client.HTTPSConnection('<Endpoint>')
while True:
    try:
        conn.request("GET", f"/formrecognizer/v1.0-preview/prebuilt/receipt/operations/{operationId}", "", headers)
        responseString = conn.getresponse().read().decode('utf-8')
        responseDict = json.loads(responseString)
        conn.close()
        print(responseString)
        if 'status' in responseDict and responseDict['status'] not in ['NotStarted','Running']:
            break
        time.sleep(1)
    except Exception as e:
        print(e)
        exit()
```

1. Sla het script op.
1. Gebruik opnieuw de `python` opdracht om het voor beeld uit te voeren. Bijvoorbeeld `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>Het antwoord bekijken

Met het script worden reacties op de console afgedrukt totdat de analyse bewerking is voltooid. Vervolgens worden de geëxtraheerde tekst gegevens in JSON-indeling afgedrukt. Het veld `"recognitionResults"` bevat elke regel tekst die is geëxtraheerd uit de ontvangst en het veld `"understandingResults"` bevat sleutel/waarde-informatie voor de meest relevante onderdelen van de ontvangst.

Bekijk de volgende ontvangstbewijs afbeelding en de bijbehorende JSON-uitvoer. De uitvoer is inge kort voor de Lees baarheid.

![Een ontvangst bewijs van Contoso Store](../media/contoso-receipt.png)

```json
{
  "status": "Succeeded",
  "recognitionResults": [{
    "page": 1,
    "clockwiseOrientation": 0.36,
    "width": 1688,
    "height": 3000,
    "unit": "pixel",
    "lines": [{
      "boundingBox": [616, 291, 1050, 278, 1053, 384, 620, 397],
      "text": "Contoso",
      "words": [{
        "boundingBox": [619, 292, 1051, 284, 1052, 382, 620, 398],
        "text": "Contoso"
      }]
    }, {
      "boundingBox": [322, 588, 501, 600, 497, 655, 318, 643],
      "text": "Contoso",
      "words": [{
        "boundingBox": [330, 590, 501, 602, 499, 654, 326, 644],
        "text": "Contoso"
      }]
    },
    ...
    ]
  }],
  "understandingResults": [{
    "pages": [1],
    "fields": {
      "Subtotal": {
        "valueType": "numberValue",
        "value": 1098.99,
        "text": "1098.99",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/14/words/1"
        }]
      },
      "Total": {
        "valueType": "numberValue",
        "value": 1203.39,
        "text": "1203.39",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/18/words/1"
        }]
      },
      "Tax": {
        "valueType": "numberValue",
        "value": 104.4,
        "text": "$104.40",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/16/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/16/words/1"
        }]
      },
      "MerchantAddress": {
        "valueType": "stringValue",
        "value": "123 Main Street Redmond, WA 98052",
        "text": "123 Main Street Redmond, WA 98052",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/2/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/2"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/2"
        }]
      },
      "MerchantName": {
        "valueType": "stringValue",
        "value": "Contoso",
        "text": "Contoso",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/1/words/0"
        }]
      },
      "MerchantPhoneNumber": {
        "valueType": "stringValue",
        "value": null,
        "text": "123-456-7890",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/4/words/0"
        }]
      },
      "TransactionDate": {
        "valueType": "stringValue",
        "value": "2019-06-10",
        "text": "6/10/2019",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/0"
        }]
      },
      "TransactionTime": {
        "valueType": "stringValue",
        "value": "13:59:00",
        "text": "13:59",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/1"
        }]
      }
    }
  }]
}
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u de formulier Recognizer REST API met python gebruikt om een model te trainen en uit te voeren in een voorbeeld scenario. Raadpleeg vervolgens de referentie documentatie om de API voor het formulier Recognizer te verkennen.

> [!div class="nextstepaction"]
> [Documentatie over REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
