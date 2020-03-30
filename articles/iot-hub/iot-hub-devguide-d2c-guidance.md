---
title: Azure IoT Hub-opties voor device-to-cloud | Microsoft Documenten
description: Handleiding voor ontwikkelaars - handleiding en handleiding voor het gebruik van device-to-cloud-berichten, gerapporteerde eigenschappen of het uploaden van bestanden voor communicatie tussen cloud en apparaat.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 02dc1b55d85b7137a5c1f57999cc3b7e9b1efe29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591333"
---
# <a name="device-to-cloud-communications-guidance"></a>Richtlijnen voor communicatie tussen apparaten en cloud

Wanneer iot Hub informatie van de apparaat-app naar de back-end van de oplossing verzendt, worden drie opties beschreven:

* [Device-to-cloud berichten](iot-hub-devguide-messages-d2c.md) voor telemetrie en waarschuwingen uit tijdreeksen.

* [De gerapporteerde eigenschappen van Device Twin](iot-hub-devguide-device-twins.md) voor het rapporteren van informatie over de status van het apparaat, zoals beschikbare mogelijkheden, voorwaarden of de status van langlopende werkstromen. Bijvoorbeeld configuratie- en software-updates.

* [Bestandsuploads](iot-hub-devguide-file-upload.md) voor mediabestanden en grote telemetriebatches die worden geüpload door met tussenpozen verbonden apparaten of worden gecomprimeerd om bandbreedte te besparen.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Hier is een gedetailleerde vergelijking van de verschillende device-to-cloud communicatie-opties.

|  | Apparaat-naar-cloud-berichten | De gerapporteerde eigenschappen van apparaattweeling | Bestandsuploads |
| ---- | ------- | ---------- | ---- |
| Scenario | Telemetrietijdreeksen en waarschuwingen. Bijvoorbeeld, 256-KB sensor gegevens batches verzonden elke 5 minuten. | Beschikbare mogelijkheden en voorwaarden. Bijvoorbeeld de huidige apparaatconnectiviteitsmodus, zoals mobiel of WiFi. Het synchroniseren van langlopende workflows, zoals configuratie- en software-updates. | Mediabestanden. Grote (meestal gecomprimeerde) telemetriebatches. |
| Opslag en ophalen | Tijdelijk opgeslagen door IoT Hub, tot 7 dagen. Alleen sequentiële lezing. | Opgeslagen door IoT Hub in de apparaattweeling. Opvraagbaar met de [iot-hubquerytaal](iot-hub-devguide-query-language.md). | Opgeslagen in het door de gebruiker geleverde Azure Storage-account. |
| Grootte | Maximaal 256 KB-berichten. | De maximale gerapporteerde eigenschappengrootte is 32 KB. | Maximale bestandsgrootte ondersteund door Azure Blob Storage. |
| Frequency | Hoog. Zie [IoT Hub-limieten voor](iot-hub-devguide-quotas-throttling.md)meer informatie. | Gemiddeld. Zie [IoT Hub-limieten voor](iot-hub-devguide-quotas-throttling.md)meer informatie. | Laag. Zie [IoT Hub-limieten voor](iot-hub-devguide-quotas-throttling.md)meer informatie. |
| Protocol | Beschikbaar op alle protocollen. | Beschikbaar via MQTT of AMQP. | Beschikbaar bij het gebruik van een protocol, maar vereist HTTPS op het apparaat. |

Een toepassing moet mogelijk informatie verzenden als een telemetrietijdreeks of waarschuwing en deze beschikbaar maken in de apparaattweeling. In dit scenario u een van de volgende opties kiezen:

* De apparaat-app verzendt een apparaat-naar-cloud-bericht en rapporteert een eigenschapswijziging.
* De back-end van de oplossing kan de informatie opslaan in de tags van de apparaattweeling wanneer het bericht wordt ontvangen.

Aangezien device-to-cloud-berichten een veel hogere doorvoer mogelijk maken dan dubbele updates van het apparaat, is het soms wenselijk om te voorkomen dat de apparaattweeling wordt bijgewerkt voor elk apparaat-naar-cloud-bericht.
