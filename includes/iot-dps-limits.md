---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: a306a05899a644f2ee8ea738789b84ee98221531
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392268"
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
> Neem contact op met [Microsoft ondersteuning](https://azure.microsoft.com/support/options/)als u het aantal inschrijvingen en registraties voor uw inrichtings service wilt verhogen.

De Device Provisioning Service beperkt aanvragen wanneer de volgende quota worden overschreden.

| Vertragen | Waarde per eenheid |
| --- | --- |
| Operations | 200/min/service |
| Apparaatregistraties | 200/min/service |
| Polling bewerking voor apparaten | 5/10 sec/apparaat |
