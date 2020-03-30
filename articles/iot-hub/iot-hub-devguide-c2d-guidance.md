---
title: Azure IoT Hub-cloud-naar-apparaatopties | Microsoft Documenten
description: Handleiding voor ontwikkelaars - richtlijnen voor het gebruik van directe methoden, de gewenste eigenschappen van device twin of cloud-to-device-berichten voor communicatie tussen cloud en apparaat.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: a2ca9a167d50619ed2963b13515c0a772d712570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591265"
---
# <a name="cloud-to-device-communications-guidance"></a>Communicatierichtlijnen voor cloud-to-device

IoT Hub biedt drie opties voor apparaat-apps om functionaliteit bloot te stellen aan een back-end-app:

* [Directe communicatiemethoden](iot-hub-devguide-direct-methods.md) die onmiddellijke bevestiging van het resultaat vereisen. Directe methoden worden vaak gebruikt voor interactieve bediening van apparaten, zoals het inschakelen van een ventilator.

* [Twin's gewenste eigenschappen](iot-hub-devguide-device-twins.md) voor langlopende commando's bedoeld om het apparaat in een bepaalde gewenste toestand te brengen. Stel bijvoorbeeld het telemetrie-verzendinterval in op 30 minuten.

* [Cloud-to-device-berichten](iot-hub-devguide-messages-c2d.md) voor eenrichtingsmeldingen naar de apparaat-app.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Hier is een gedetailleerde vergelijking van de verschillende cloud-to-device communicatie-opties.

|  | Directe methoden | Twin's gewenste eigenschappen | Cloud-to-device berichten |
| ---- | ------- | ---------- | ---- |
| Scenario | Opdrachten die onmiddellijke bevestiging vereisen, zoals het inschakelen van een ventilator. | Langlopende opdrachten die bedoeld zijn om het apparaat in een bepaalde gewenste toestand te plaatsen. Stel bijvoorbeeld het telemetrie-verzendinterval in op 30 minuten. | One-way meldingen aan de apparaat-app. |
| Gegevensstroom | Twee richtingen. De apparaat-app kan meteen op de methode reageren. De back-end van de oplossing ontvangt de uitkomst contextueel aan de aanvraag. | One-way. De apparaat-app ontvangt een melding met de eigenschapswijziging. | One-way. De apparaat-app ontvangt het bericht
| Duurzaamheid | Losgekoppelde apparaten worden niet gecontacteerd. De back-end van de oplossing wordt gemeld dat het apparaat niet is aangesloten. | Eigenschapswaarden blijven behouden in de apparaattweeling. Apparaat zal het lezen bij de volgende heraansluiting. Eigenschapwaarden kunnen worden opgehaald met de [iot-hubquerytaal](iot-hub-devguide-query-language.md). | Berichten kunnen maximaal 48 uur worden bewaard door IoT Hub. |
| Doelen | Eén apparaat met **deviceId**of meerdere apparaten die [taken](iot-hub-devguide-jobs.md)gebruiken. | Eén apparaat met **deviceId**of meerdere apparaten die [taken](iot-hub-devguide-jobs.md)gebruiken. | Eén apparaat per **apparaatId**. |
| Grootte | Maximale directe methode payload grootte is 128 KB. | Maximale gewenste eigenschappen grootte is 32 KB. | Maximaal 64 KB-berichten. |
| Frequency | Hoog. Zie [IoT Hub-limieten voor](iot-hub-devguide-quotas-throttling.md)meer informatie. | Gemiddeld. Zie [IoT Hub-limieten voor](iot-hub-devguide-quotas-throttling.md)meer informatie. | Laag. Zie [IoT Hub-limieten voor](iot-hub-devguide-quotas-throttling.md)meer informatie. |
| Protocol | Beschikbaar via MQTT of AMQP. | Beschikbaar via MQTT of AMQP. | Beschikbaar op alle protocollen. Apparaat moet worden gepeild wanneer u HTTPS gebruikt. |

Meer informatie over het gebruik van directe methoden, gewenste eigenschappen en cloud-to-device-berichten in de volgende zelfstudies:

* [Directe methoden gebruiken](quickstart-control-device-node.md)
* [De gewenste eigenschappen gebruiken om apparaten te configureren](tutorial-device-twins.md) 
* [Cloud-naar-apparaat-berichten verzenden](iot-hub-node-node-c2d.md)
