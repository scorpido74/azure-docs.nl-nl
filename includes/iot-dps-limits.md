---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: e427a7b80b28f1cc70a02890152f9f2247a8bcd2
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "68360276"
---
De volgende tabel bevat de limieten die van toepassing zijn op Azure IoT Hub Device Provisioning Service-resources.

| Resource | Limiet |
| --- | --- |
| Maximum aantal services voor het inrichten van apparaten per Azure-abonnement | 10 |
| Maximum aantal inschrijvingen | 1\.000.000 |
| Maximum aantal registraties | 1\.000.000 |
| Maximum aantal registratie groepen | 100 |
| Maximum aantal Ca's | 25 |
| Maximum aantal gekoppelde IoT-hubs | 10 |
| Maximale bericht grootte | 96 KB|


> [!NOTE]
> Als u het aantal exemplaren in uw abonnement wilt verhogen, neemt u contact op met [Microsoft ondersteuning](https://azure.microsoft.com/support/options/).

> [!NOTE]
> Neem contact op met [Microsoft ondersteuning](https://azure.microsoft.com/support/options/)als u het aantal inschrijvingen en registraties voor uw inrichtings service wilt verhogen.

De Device Provisioning Service beperkt aanvragen wanneer de volgende quota worden overschreden.

| Vertragen | Waarde per eenheid |
| --- | --- |
| Bewerkingen | 200/min/service |
| Apparaatregistraties | 200/min/service |
| Polling bewerking voor apparaten | 5/10 sec/apparaat |
