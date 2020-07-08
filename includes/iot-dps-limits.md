---
author: rothja
ms.service: iot-dps
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: bbe928084a758335e5fa3b0531726c77a6d599b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85838942"
---
De volgende tabel bevat de limieten die van toepassing zijn op Azure IoT Hub Device Provisioning Service-resources.

| Resource | Limiet |
| --- | --- |
| Maximum aantal services voor het inrichten van apparaten per Azure-abonnement | 10 |
| Maximum aantal inschrijvingen | 1.000.000 |
| Maximum aantal registraties | 1.000.000 |
| Maximum aantal registratie groepen | 100 |
| Maximum aantal Ca's | 25 |
| Maximum aantal gekoppelde IoT-hubs | 50 |
| Maximale bericht grootte | 96 KB|

> [!NOTE]
> Neem contact op met [Microsoft ondersteuning](https://azure.microsoft.com/support/options/)als u het aantal inschrijvingen en registraties voor uw inrichtings service wilt verhogen.

> [!NOTE]
> Het verhogen van het maximum aantal Ca's wordt niet ondersteund.

De Device Provisioning Service beperkt aanvragen wanneer de volgende quota worden overschreden.

| Vertragen | Waarde per eenheid |
| --- | --- |
| Bewerkingen | 200/min/service |
| Apparaatregistraties | 200/min/service |
| Polling bewerking voor apparaten | 5/10 sec/apparaat |
