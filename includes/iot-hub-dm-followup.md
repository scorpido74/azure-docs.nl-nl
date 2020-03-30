---
title: bestand opnemen
description: bestand opnemen
services: iot-hub
ms.service: iot-hub
author: dominicbetts
ms.topic: include
ms.date: 02/17/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2514325c93f0f72aa979f871e86e465093479402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69558811"
---
## <a name="customize-and-extend-the-device-management-actions"></a>De acties voor apparaatbeheer aanpassen en uitbreiden

Uw IoT-oplossingen kunnen de gedefinieerde set apparaatbeheerpatronen uitbreiden of aangepaste patronen inschakelen met behulp van de primitieve primitieven van de apparaattweeling en de cloud-to-device-methode. Andere voorbeelden van acties voor apparaatbeheer zijn factory reset, firmware-update, software-update, energiebeheer, netwerk- en connectiviteitsbeheer en gegevensversleuteling.

## <a name="device-maintenance-windows"></a>Windows voor apparaatonderhoud

Doorgaans configureert u apparaten om acties uit te voeren op een tijdstip dat onderbrekingen en downtime minimaliseert. Apparaatonderhoudsvensters zijn een veelgebruikt patroon om het tijdstip te definiëren waarop een apparaat de configuratie moet bijwerken. Uw back-endoplossingen kunnen de gewenste eigenschappen van de apparaattweeling gebruiken om een beleid op uw apparaat te definiëren en te activeren dat een onderhoudsvenster mogelijk maakt. Wanneer een apparaat het onderhoudsvensterbeleid ontvangt, kan het de gerapporteerde eigenschap van de apparaattweeling gebruiken om de status van het beleid te rapporteren. De back-end-app kan vervolgens apparaatdubbele query's gebruiken om te getuigen van de naleving van apparaten en elk beleid.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een directe methode gebruikt om een externe reboot op een apparaat te activeren. U hebt de gerapporteerde eigenschappen gebruikt om de laatste herstarttijd vanaf het apparaat te melden en hebt de apparaattweeling opgevraagd om de laatste herstarttijd van het apparaat uit de cloud te ontdekken.

Zie [Een firmware-update uitvoeren](../articles/iot-hub/tutorial-firmware-update.md)om verder te gaan met IoT Hub- en apparaatbeheerpatronen, zoals firmware-update op afstand via de ether.

Zie Taken plannen [en uitzenden](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md)voor meer informatie over het uitbreiden van uw IoT-oplossing en het plannen van methodeoproepen op meerdere apparaten.