---
title: Inzicht in Azure IoT Hub-berichten | Microsoft Documenten
description: Handleiding voor ontwikkelaars - device-to-cloud en cloud-to-device messaging met IoT Hub. Bevat informatie over berichtindelingen en ondersteunde communicatieprotocollen.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: f56332fa7f53c729ffaa28ea375f043d1b4a3678
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60626244"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Berichten van apparaat naar cloud en cloud-naar-apparaat verzenden met IoT Hub

IoT Hub zorgt voor bi-directionele communicatie met uw apparaten. Gebruik IoT Hub messaging om met uw apparaten te communiceren door berichten van uw apparaten naar uw oplossingen back-end te sturen en opdrachten van uw IoT-oplossingen back-end naar uw apparaten te verzenden. Meer informatie over de [berichtindeling van IoT Hub](iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Device-to-cloud-berichten verzenden naar IoT Hub

IoT Hub heeft een ingebouwd serviceeindpunt dat door back-endservices kan worden gebruikt om telemetrieberichten van uw apparaten te lezen. Dit eindpunt is compatibel met [Event Hubs](https://docs.microsoft.com/azure/event-hubs/) en u standaard IoT Hub SDK's gebruiken om te lezen vanaf [dit ingebouwde eindpunt.](iot-hub-devguide-messages-read-builtin.md)

IoT Hub ondersteunt ook [aangepaste eindpunten](iot-hub-devguide-endpoints.md#custom-endpoints) die door gebruikers kunnen worden gedefinieerd om apparaattelemetriegegevens en -gebeurtenissen naar Azure-services te verzenden met behulp van [berichtroutering.](iot-hub-devguide-messages-d2c.md)

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>Cloud-naar-device berichten verzenden vanuit IoT Hub

U berichten van cloud naar apparaat van de [back-end](iot-hub-devguide-messages-c2d.md) van de oplossing naar uw apparaten verzenden.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Kerneigenschappen van IoT Hub messaging-functionaliteit zijn de betrouwbaarheid en duurzaamheid van berichten. Deze eigenschappen maken veerkracht mogelijk voor intermitterende connectiviteit aan de apparaatzijde en om pieken in gebeurtenisverwerking aan de cloudzijde te laden. IoT Hub implementeert *ten minste eenmaal* leveringsgaranties voor zowel device-to-cloud als cloud-to-device messaging.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>Het juiste type IoT Hub-berichten kiezen

Gebruik device-to-cloudberichten voor het verzenden van telemetrie en waarschuwingen van tijdreeksen vanuit uw apparaat-app en berichten van cloud naar apparaat voor eenrichtingsmeldingen naar uw apparaat-app.

* Raadpleeg [communicatierichtlijnen voor apparaat tot cloud](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-d2c-guidance) om te kiezen tussen device-to-cloud-berichten, gerapporteerde eigenschappen of het uploaden van bestanden.

* Raadpleeg [communicatierichtlijnen voor de cloud naar het apparaat](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-c2d-guidance) om te kiezen tussen cloud-to-device-berichten, gewenste eigenschappen of directe methoden.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [routeren van IoT-hub-berichten](iot-hub-devguide-messages-d2c.md).

* Meer informatie over berichten over IoT [Hub-cloud naar apparaat.](iot-hub-devguide-messages-c2d.md)