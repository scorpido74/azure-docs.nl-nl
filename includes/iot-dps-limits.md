---
author: rothja
ms.service: iot-dps
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: bbe928084a758335e5fa3b0531726c77a6d599b8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85838942"
---
In de volgende tabel staan de limieten die van toepassing zijn op Azure IoT Hub Device Provisioning Service-resources.

| Resource | Limiet |
| --- | --- |
| Maximaal aantal services voor apparaatinrichting per Azure-abonnement | 10 |
| Maximaal aantal inschrijvingen | 1.000.000 |
| Maximaal aantal registraties | 1.000.000 |
| Maximaal aantal inschrijvingsgroepen | 100 |
| Maximaal aantal CA's | 25 |
| Maximaal aantal gekoppelde IoT-hubs | 50 |
| Maximale berichtgrootte | 96 kB|

> [!NOTE]
> Neem contact op met [Microsoft Ondersteuning](https://azure.microsoft.com/support/options/) als u het aantal inschrijvingen en registraties voor uw inrichtingsservice wilt verhogen.

> [!NOTE]
> Verhoging van het maximale aantal CA's wordt niet ondersteund.

De service voor apparaatinrichting vertraagt aanvragen wanneer de volgende quota worden overschreden.

| Vertragen | Waarde per eenheid |
| --- | --- |
| Bewerkingen | 200/min/service |
| Apparaatregistraties | 200/min/service |
| Bewerking voor apparaatpolling | 5/10 sec/apparaat |
