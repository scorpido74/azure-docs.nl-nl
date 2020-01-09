---
title: 'Snelstartgids: ontvangst gegevens ophalen met behulp van krul-Recognizer'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start gebruikt u de formulier Recognizer REST API met krul om gegevens te extra heren uit installatie kopieën van verkoop ontvangsten.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 12/18/2019
ms.author: pafarley
ms.openlocfilehash: 5dd459adec95eb764cf3458d1eceb455b36fd965
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473911"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>Snelstartgids: ontvangst gegevens ophalen met behulp van de formulier Recognizer REST API met krul

In deze Quick Start gebruikt u de Azure Form Recognizer REST API met krul om relevante informatie in verkoop ontvangsten te extra heren en te identificeren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!IMPORTANT]
> Deze Snelstartgids maakt gebruik van de API voor formulier herkenning v 1.0. Als uw abonnement zich in de `West US 2` of `West Europe` regio bevindt, moet u in plaats daarvan de v[2,0 API Quick](./python-train-extract.md) start volgen.

## <a name="prerequisites"></a>Vereisten
Voor het volt ooien van deze Snelstartgids hebt u het volgende nodig:
- Toegang tot de preview-versie van beperkte toegang van de formulier herkenning. Als u toegang wilt krijgen tot de preview, vult u het formulier voor de [toegangs aanvraag voor de formulier herkenning](https://aka.ms/FormRecognizerRequestAccess) in en verzendt u dit.
- [krul](https://curl.haxx.se/windows/) geïnstalleerd.
- Een URL voor een afbeelding van een ontvangst. U kunt een [voorbeeld afbeelding](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) gebruiken voor deze Quick Start.

## <a name="create-a-form-recognizer-resource"></a>Een resource voor een formulier herkenning maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Een kwitantie analyseren

Als u wilt beginnen met het analyseren van een ontvangst, roept u de API voor het **analyseren** van de ontvangst aan met behulp van de onderstaande krul opdracht. Voordat u de opdracht uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `<Endpoint>` door het eind punt dat u hebt verkregen met het formulier Recognizer-abonnement.
1. Vervang `<your receipt URL>` door het URL-adres van een kwitantie-installatie kopie.
1. Vervang `<subscription key>` door de abonnements sleutel die u uit de vorige stap hebt gekopieerd.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"url\": \"<your receipt URL>\"}"
```

U ontvangt een `202 (Success)` antwoord met een **bewerkings locatie** header. De waarde van deze header bevat een bewerkings-ID die u kunt gebruiken om de status van de bewerking op te vragen en de resultaten op te halen. In het volgende voor beeld is de teken reeks na `operations/` de bewerkings-ID.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>De ontvangst resultaten ophalen

Nadat u de trans actie voor het analyseren van de **kwitantie** hebt aangeroepen, roept u de **Get-ontvangst resultaat** -API op om de status van de bewerking en de geëxtraheerde gegevens op te halen.

1. Vervang `<operationId>` door de bewerkings-ID uit de vorige stap.
1. Vervang `<subscription key>` door uw abonnementssleutel.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/operations/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Het antwoord bekijken

U ontvangt een `200 (Success)` antwoord met JSON-uitvoer. Het eerste veld, `"status"`, geeft de status van de bewerking aan. Als de bewerking is voltooid, bevat het veld `"recognitionResults"` elke regel tekst die is geëxtraheerd uit de ontvangst, en het veld `"understandingResults"` bevat sleutel/waarde-informatie voor de meest relevante onderdelen van de ontvangst. Als de bewerking niet is voltooid, wordt de waarde van `"status"` `"Running"` of `"NotStarted"`. u moet de API opnieuw aanroepen, hetzij hand matig of via een script. We raden u aan een interval van één seconde of meer tussen aanroepen aan te bevelen.

Bekijk de volgende ontvangstbewijs afbeelding en de bijbehorende JSON-uitvoer. De uitvoer is inge kort voor de Lees baarheid.

![Een ontvangst bewijs van Contoso Store](../media/contoso-receipt.png)

Het knoop punt `"recognitionResults"` bevat alle herkende tekst. De tekst wordt geordend op pagina, vervolgens per regel en vervolgens op afzonderlijke woorden. Het knoop punt `"understandingResults"` bevat de ontvangstbewijs waarden die het model heeft gedetecteerd. Hier vindt u nuttige sleutel/waarde-paren zoals de belasting, het totaal, het bedrijfs adres, enzovoort.

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

In deze Quick Start hebt u de formulier Recognizer REST API met krul gebruikt om de inhoud van een verkoop ontvangst te extra heren. Raadpleeg vervolgens de referentie documentatie om de API voor het formulier Recognizer te verkennen.

> [!div class="nextstepaction"]
> [Documentatie over REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)