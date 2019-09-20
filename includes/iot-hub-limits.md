---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 689322614e2cea4e59a5a573b72bfeb2aafda847
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71148398"
---
De volgende tabel bevat de limieten die zijn gekoppeld aan de verschillende service lagen S1, S2, S3 en F1. Zie [prijzen van Azure IOT hub](https://azure.microsoft.com/pricing/details/iot-hub/)voor meer informatie over de kosten van elke *eenheid* in elke laag.

| Resource | S1 Standard | S2 Standard | S3 Standard | F1 Free |
| --- | --- | --- | --- | --- |
| Berichten/dag |400,000 |6,000,000 |300,000,000 |8,000 |
| Maximum aantal eenheden |200 |200 |10 |1 |

> [!NOTE]
> Neem contact op met Microsoft Ondersteuning als u verwacht dat u meer dan 200 eenheden gebruikt met een S1-of S2-hub of 10 eenheden met een hub met S3-laag.
> 
> 

De volgende tabel bevat de limieten die van toepassing zijn op IoT Hub resources.

| Resource | Limiet |
| --- | --- |
| Maximum aantal betaalde IoT-hubs per Azure-abonnement |50 |
| Maximum aantal gratis IoT-hubs per Azure-abonnement |1 |
| Maximum aantal tekens in een apparaat-ID | 128 |
| Maximumaantal apparaat-id's<br/> dat in één aanroep wordt geretourneerd |1000 |
| Maximale bewaartermijn van IoT Hub-berichten voor apparaat-naar-cloud berichten |7 dagen |
| Maximale grootte van apparaat-naar-cloud berichten |256 kB |
| Maximale grootte van apparaat-naar-cloud batch |AMQP en HTTP: 256 KB voor de hele batch <br/>MQTT 256 KB voor elk bericht |
| Maximum aantal berichten in apparaat-naar-cloud batch |500 |
| Maximale grootte van cloud-naar-apparaat bericht |64 kB |
| Maximale TTL voor cloud-naar-apparaat berichten |2 dagen |
| Maximumaantal leveringen voor cloud-naar-apparaat <br/> berichten |100 |
| Maximale wachtrij diepte van Cloud naar apparaat per apparaat |50 |
| Maximumaantal leveringen voor feedbackberichten <br/> als antwoord op een cloud-naar-apparaat bericht |100 |
| Maximale TTL voor feedbackberichten <br/> als antwoord op een cloud-naar-apparaat bericht |2 dagen |
| [Maximale grootte van het dubbele apparaat](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) <br/> (tags, gerapporteerde eigenschappen en gewenste eigenschappen) | 8 kB |
| Maximale grootte van tekenreekswaarde van apparaatdubbel | 4 KB |
| [Maximale diepte van object in het dubbele apparaat](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 5 |
| Maximale grootte van de nettolading van directe methode | 128 KB |
| Maximale bewaartermijn van taakgeschiedenis | 30 dagen |
| Maximum aantal gelijktijdige taken | 10 (voor S3), 5 voor (S2), 1 (voor S1) |
| Maximaal aantal extra eindpunten | 10 (voor S1, S2 en S3) |
| Maximum aantal regels voor berichtroutering | 100 (voor S1, S2 en S3) |
| Maximum aantal gelijktijdig verbonden apparaat stromen | 50 (alleen voor S1, S2, S3 en F1) |
| Maximale gegevens overdracht van de apparaat stroom | 300 MB per dag (alleen voor S1, S2, S3 en F1) |

> [!NOTE]
> Als u meer dan 50 betaalde IoT-hubs in een Azure-abonnement nodig hebt, neemt u contact op met Microsoft Ondersteuning.

> [!NOTE]
> Op dit moment is het maximum aantal apparaten dat u kunt verbinden met één IoT-hub 1.000.000. Als u deze limiet wilt verhogen, neemt u contact op met [Microsoft ondersteuning](https://azure.microsoft.com/support/options/).

IoT Hub beperkt aanvragen wanneer de volgende quota worden overschreden.

| Vertragen | Waarde per hub |
| --- | --- |
| Registerbewerkingen voor identiteit <br/> (maken, ophalen, weer geven, bijwerken en verwijderen), <br/> afzonderlijk of bulksgewijs importeren/exporteren |83.33 per seconde per eenheid (5000/min/eenheid) (voor S3). <br/> 1.67 per seconde per eenheid (100/min/eenheid) (voor S1 en S2). |
| Apparaatverbindingen |6000 per seconde per eenheid (voor S3), 120 per seconde per eenheid (voor S2), 12 per seconde per eenheid (voor S1). <br/>Minimaal 100 per seconde. |
| Apparaat-naar-cloud verzendt |6000 per seconde per eenheid (voor S3), 120 per seconde per eenheid (voor S2), 12 per seconde per eenheid (voor S1). <br/>Minimaal 100 per seconde. |
| Cloud-naar-apparaat verzendt | 83.33 per seconde per eenheid (5000/min/eenheid) (voor S3), 1.67 per seconde per eenheid (100/min/eenheid) (voor S1 en S2). |
| Cloud-naar-apparaat ontvangt |833.33 per seconde per eenheid (50.000/min/eenheid) (voor S3), 16.67 per seconde per eenheid (1000/min/eenheid) (voor S1 en S2). |
| Bestandsuploadbewerkingen |83,33 bestand upload meldingen per seconde per eenheid (5000/min/eenheid) (voor S3), 1,67 bestand upload meldingen per seconde per eenheid (100 per minuut/eenheid) (voor S1 en S2). <br/> 10.000 SAS-Uri's kunnen voor een Azure Storage-account tegelijk worden uitgevoerd.<br/> Er kunnen 10 SAS URI's per apparaat tegelijk zijn uitgeschakeld. |
| Directe methoden | 24 MB per seconde per eenheid (voor S3), 480 KB per seconde per eenheid (voor S2), 160 KB per seconde per eenheid (voor S1).<br/> Op basis van de beperkings meter grootte van 8 KB. |
| Apparaatdubbel leest | 500 per seconde per eenheid (voor S3), Maxi maal 100 per seconde of 10 per seconde per eenheid (voor S2), 100 per seconde (voor S1) |
| Apparaatdubbel werkt bij | 250 per seconde per eenheid (voor S3), Maxi maal 50 per seconde of 5 per seconde per eenheid (voor S2), 50 per seconde (voor S1) |
| Taakbewerkingen <br/> (maken, bijwerken, weer geven en verwijderen) | 83.33 per seconde per eenheid (5000/min/eenheid) (voor S3), 1.67 per seconde per eenheid (100/min/eenheid) (voor S2), 1.67 per seconde per eenheid (100/min/eenheid) (voor S1). |
| Doorvoer van taken per apparaat bewerkingen | 50 per seconde per eenheid (voor S3), Maxi maal 10 per seconde of 1/sec per eenheid (voor S2), 10 per seconde (voor S1). |
| Start frequentie van Device stream | 5 nieuwe streams per seconde (alleen voor S1, S2, S3 en F1). |
