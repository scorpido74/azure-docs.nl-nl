---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 4bd890adcaa4982c52366faed0f2975729290360
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612043"
---
De volgende tabel bevat de limieten die van toepassing zijn op Azure IoT Hub Device Provisioning Service-resources.

| Bron | Limiet |
| --- | --- |
| Maximum aantal services voor het inrichten van apparaten per Azure-abonnement | 10 |
| Maximum aantal inschrijvingen | 1\.000.000 |
| Maximum aantal registraties | 1\.000.000 |
| Maximum aantal registratie groepen | 100 |
| Maximum aantal Ca's | 25 |
| Maximum aantal gekoppelde IoT-hubs | 50 |
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
