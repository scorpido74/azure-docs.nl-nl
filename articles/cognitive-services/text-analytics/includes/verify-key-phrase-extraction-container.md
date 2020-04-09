---
title: De instantie Van de container sleutelwoordextractie controleren
titleSuffix: Azure Cognitive Services
description: Meer informatie over het verifiëren van de containerinstantie Sleutelzinextractie.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c177517ec18d7526b1cc09da74e35cb5434766d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876406"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>De instantie Van de container sleutelwoordextractie controleren

1. Selecteer het tabblad **Overzicht** en kopieer het IP-adres.
1. Open een nieuw browsertabblad en voer het IP-adres in. Voer bijvoorbeeld `http://<IP-address>:5000 (http://55.55.55.55:5000`in ). De startpagina van de container wordt weergegeven, zodat u weet dat de container wordt uitgevoerd.

    ![De startpagina van de container weergeven om te controleren of deze wordt uitgevoerd](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selecteer de koppeling **Service-API-beschrijving** om naar de Swagger-pagina van de container te gaan.

1. Kies een **POST** van de POST-API's en selecteer **Probeer het uit.** De parameters worden weergegeven, waaronder deze voorbeeldinvoer:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
        }
      ]
    }
    ```

1. Vervang de invoer door de volgende JSON-inhoud:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. Stel **showStats** in op `true`.

1. Selecteer **Uitvoeren** om het gevoel van de tekst te bepalen.

    Het model dat in de container is verpakt, genereert een score die varieert van 0 tot 1, waarbij 0 negatief is en 1 positief.

    De JSON-reactie die wordt geretourneerd, bevat het sentiment voor de bijgewerkte tekstinvoer:

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
          "statistics": {
            "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

We kunnen nu `id` het document van de JSON-gegevens van `id`de response payload correleren met het oorspronkelijke payloaddocument voor aanvragen. Het resulterende document `keyPhrases` heeft een array, die de lijst bevat met belangrijke zinnen die zijn geëxtraheerd uit het bijbehorende invoerdocument. Daarnaast zijn er verschillende statistieken, zoals `characterCount` en `transactionCount` voor elk resulterend document.