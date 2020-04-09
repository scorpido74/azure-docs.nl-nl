---
title: De containerinstantie Taaldetectie verifiëren
titleSuffix: Azure Cognitive Services
description: Meer informatie over het verifiëren van de containerinstantie Taaldetectie.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: dc52586550f89ddae147d79458584331ed984eea
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876408"
---
### <a name="verify-the-language-detection-container-instance"></a>De containerinstantie Taaldetectie verifiëren

1. Selecteer het tabblad **Overzicht** en kopieer het IP-adres.
1. Open een nieuw browsertabblad en voer het IP-adres in. Voer bijvoorbeeld `http://<IP-address>:5000 (http://55.55.55.55:5000`in ). De startpagina van de container wordt weergegeven, zodat u weet dat de container wordt uitgevoerd.

    ![De startpagina van de container weergeven om te controleren of deze wordt uitgevoerd](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selecteer de koppeling **Service-API-beschrijving** om naar de Swagger-pagina van de container te gaan.

1. Kies een **POST** van de POST-API's en selecteer **Probeer het uit.** De parameters worden weergegeven, waaronder deze voorbeeldinvoer:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Stel **showStats** in op `true`.

1. Selecteer **Uitvoeren** om het gevoel van de tekst te bepalen.

    Het model dat is verpakt in de container genereert een score die varieert van 0 tot 1, waarbij 0 negatief sentiment is en 1 positief sentiment.

    De JSON-reactie die wordt geretourneerd, bevat het sentiment voor de bijgewerkte tekstinvoer:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

We kunnen nu correleren de documenten van json gegevens van de `id`reactie payload met de oorspronkelijke aanvraag payload documenten door hun overeenkomstige . Elk document wordt onafhankelijk behandeld met `characterCount` verschillende `transactionCount`statistieken, zoals en . Bovendien heeft elk resulterend `detectedLanguages` document de `name` `iso6391Name`array `score` met de , en voor elke gedetecteerde taal. Wanneer meerdere talen worden `score` gedetecteerd, wordt deze gebruikt om de meest waarschijnlijke taal te bepalen.