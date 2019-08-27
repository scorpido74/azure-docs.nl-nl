---
title: De Taaldetectie container instantie controleren
titleSuffix: Azure Cognitive Services
description: Meer informatie over het controleren van het exemplaar van de Taaldetectie-container.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: 74d95d70438431495c9072e115854b1ccfa05bcb
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051907"
---
## <a name="verify-the-language-detection-container-instance"></a>De Taaldetectie container instantie controleren

1. Selecteer het tabblad **overzicht** en kopieer het IP-adres.
1. Open een nieuw browser tabblad en voer het IP-adres in. Voer `http://<IP-address>:5000 (http://55.55.55.55:5000`bijvoorbeeld in). De start pagina van de container wordt weer gegeven, waarin u kunt zien dat de container wordt uitgevoerd.

    ![De start pagina van de container weer geven om te controleren of deze wordt uitgevoerd](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selecteer de koppeling **Service-API-beschrijving** om naar de Swagger-pagina van de container te gaan.

1. Kies een van de **post** -api's en selecteer **try-out**. De para meters worden weer gegeven, met inbegrip van de volgende voorbeeld invoer:

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

1. Stel **showStats** in `true`op.

1. Selecteer **uitvoeren** om de sentiment van de tekst te bepalen.

    Het model dat in de container is verpakt, genereert een Score van 0 tot 1, waarbij 0 negatieve sentiment is en 1 positief sentiment is.

    Het JSON-antwoord dat wordt geretourneerd bevat sentiment voor de bijgewerkte tekst invoer:

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

We kunnen nu de documenten van de gegevens van de nettolading van de reactie aan de oorspronkelijke aanvraag Payload-documenten correleren `id`met hun overeenkomstige. Elk document wordt onafhankelijk behandeld met verschillende statistieken, zoals `characterCount` en `transactionCount`. Daarnaast heeft elk het resulterende document de `detectedLanguages` matrix met de `name`, `iso6391Name`en `score` voor elke gedetecteerde taal. Wanneer er meerdere talen worden gedetecteerd, `score` wordt de gebruikt om de meest waarschijnlijke taal te bepalen.