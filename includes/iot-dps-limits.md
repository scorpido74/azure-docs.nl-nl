---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: fb77682bce0d9faef8f9ac8197afd8751560d515
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224361"
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
