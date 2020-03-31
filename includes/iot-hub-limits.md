---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 34de38e91d47457d215c7ebf65d04ed2dbae5324
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74224391"
---
In de volgende tabel worden de limieten weergegeven die zijn gekoppeld aan de verschillende servicelagen S1, S2, S3 en F1. Zie [Azure IoT Hub-prijzen](https://azure.microsoft.com/pricing/details/iot-hub/)voor informatie over de kosten van elke *eenheid* in elke laag.

| Resource | S1 Standard | S2 Standard | S3 Standard | F1 Free |
| --- | --- | --- | --- | --- |
| Berichten/dag |400,000 |6,000,000 |300,000,000 |8,000 |
| Maximum aantal eenheden |200 |200 |10 |1 |

> [!NOTE]
> Als u verwacht meer dan 200 eenheden te gebruiken met een S1- of S2-tier-hub of 10-eenheden met een S3-tier hub, neemt u contact op met Microsoft Support.
> 
> 

In de volgende tabel worden de limieten weergegeven die van toepassing zijn op IoT Hub-bronnen.

| Resource | Limiet |
| --- | --- |
| Maximum aantal betaalde IoT-hubs per Azure-abonnement |100 |
| Maximum aantal gratis IoT-hubs per Azure-abonnement |1 |
| Maximum aantal tekens in een apparaat-id | 128 |
| Maximumaantal apparaat-id's<br/> dat in één aanroep wordt geretourneerd |1000 |
| Maximale bewaartermijn van IoT Hub-berichten voor apparaat-naar-cloud berichten |7 dagen |
| Maximale grootte van apparaat-naar-cloud berichten |256 kB |
| Maximale grootte van apparaat-naar-cloud batch |AMQP en HTTP: 256 KB voor de hele batch <br/>MQTT: 256 KB voor elk bericht |
| Maximum aantal berichten in apparaat-naar-cloud batch |500 |
| Maximale grootte van cloud-naar-apparaat bericht |64 kB |
| Maximale TTL voor cloud-naar-apparaat berichten |2 dagen |
| Maximumaantal leveringen voor cloud-naar-apparaat <br/> berichten |100 |
| Maximale wachtrijdiepte tussen cloud en apparaat per apparaat |50 |
| Maximumaantal leveringen voor feedbackberichten <br/> als antwoord op een cloud-naar-apparaat bericht |100 |
| Maximale TTL voor feedbackberichten <br/> als antwoord op een cloud-naar-apparaat bericht |2 dagen |
| [Maximale grootte van apparaatdubbel](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB voor tags sectie, en 32 KB voor gewenste en gerapporteerde eigenschappen secties elk |
| Maximale lengte van de dubbele tekenreekssleutel van het apparaat | 1 kB |
| Maximale lengte van de dubbele tekenreekswaarde van het apparaat | 4 kB |
| [Maximale diepte van object in apparaatdubbel](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Maximale grootte van de nettolading van directe methode | 128 kB |
| Maximale bewaartermijn van taakgeschiedenis | 30 dagen |
| Maximum aantal gelijktijdige taken | 10 (voor S3), 5 voor (S2), 1 (voor S1) |
| Maximaal aantal extra eindpunten | 10 (voor S1, S2 en S3) |
| Maximum aantal regels voor berichtroutering | 100 (voor S1, S2 en S3) |
| Maximaal aantal gelijktijdig verbonden apparaatstreams | 50 (alleen voor S1, S2, S3 en F1) |
| Maximale gegevensoverdracht van apparaatstromen | 300 MB per dag (alleen voor S1, S2, S3 en F1) |

> [!NOTE]
> Neem contact op met Microsoft Support als u meer dan 100 betaalde IoT-hubs in een Azure-abonnement nodig hebt.

> [!NOTE]
> Momenteel is het totale aantal apparaten plus modules dat kan worden geregistreerd op een enkele IoT-hub is beperkt tot 1.000.000. Als u deze limiet wilt verhogen, neemt u contact op met [Microsoft Support](https://azure.microsoft.com/support/options/).

IoT Hub geeft aanvragen waarwanneer de volgende quota worden overschreden.

| Vertragen | Waarde per hub |
| --- | --- |
| Registerbewerkingen voor identiteit <br/> (maken, ophalen, aanbieden, bijwerken en verwijderen), <br/> afzonderlijk of bulksgewijs importeren/exporteren |83.33/sec/unit (5.000/min/unit) (voor S3). <br/> 1.67/sec/unit (100/min/unit) (voor S1 en S2). |
| Apparaatverbindingen |6.000/sec/eenheid (voor S3), 120/sec/eenheid (voor S2), 12/sec/eenheid (voor S1). <br/>Minimaal 100 per seconde. |
| Apparaat-naar-cloud verzendt |6.000/sec/eenheid (voor S3), 120/sec/eenheid (voor S2), 12/sec/eenheid (voor S1). <br/>Minimaal 100 per seconde. |
| Cloud-naar-apparaat verzendt | 83.33/sec/unit (5.000/min/unit) (voor S3), 1.67/sec/unit (100/min/unit) (voor S1 en S2). |
| Cloud-naar-apparaat ontvangt |833.33/sec/unit (50.000/min/unit) (voor S3), 16.67/sec/unit (1.000/min/unit) (voor S1 en S2). |
| Bestandsuploadbewerkingen |83.33 file upload initiations/sec/unit (5.000/min/unit) (voor S3), 1.67 file upload initiations/sec/unit (100/min/unit) (voor S1 en S2). <br/> 10.000 SAS-URI's kunnen tegelijk worden uitgezocht voor een Azure Storage-account.<br/> Er kunnen 10 SAS URI's per apparaat tegelijk zijn uitgeschakeld. |
| Directe methoden | 24 MB/sec/unit (voor S3), 480 KB/sec/unit (voor S2), 160 KB/sec/unit (voor S1).<br/> Gebaseerd op 8-KB throttling meter grootte. |
| Apparaatdubbel leest | 500/sec/eenheid (voor S3), Maximum van 100/sec of 10/sec/eenheid (voor S2), 100/sec (voor S1) |
| Apparaatdubbel werkt bij | 250/sec/eenheid (voor S3), Maximum van 50/sec of 5/sec/eenheid (voor S2), 50/sec (voor S1) |
| Taakbewerkingen <br/> (maken, bijwerken, aanbieden en verwijderen) | 83.33/sec/eenheid (5.000/min/eenheid) (voor S3), 1.67/sec/eenheid (100/min/eenheid) (voor S2), 1.67/sec/eenheid (100/min/eenheid) (voor S1). |
| Doorvoer van taken per apparaat bewerkingen | 50/sec/eenheid (voor S3), maximaal 10/sec of 1/sec/eenheid (voor S2), 10/sec (voor S1). |
| Initiatiesnelheid voor apparaatstromen | 5 nieuwe streams/sec (alleen voor S1, S2, S3 en F1). |
