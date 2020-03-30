---
title: Azure IoT Hub-communicatieprotocollen en -poorten | Microsoft Documenten
description: Ontwikkelaarshandleiding - beschrijft de ondersteunde communicatieprotocollen voor apparaat-naar-cloud- en cloud-naar-apparaatcommunicatie en de poortnummers die moeten worden geopend.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 6d1ab50e471c9c603c7886130375dc74e9b2a755
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284627"
---
# <a name="reference---choose-a-communication-protocol"></a>Referentie - kies een communicatieprotocol

Met IoT Hub kunnen voor apparaten de volgende protocollen worden gebruikt voor communicatie aan de zijde van het apparaat:

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT via WebSockets
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP via WebSockets
* HTTPS

Zie [Device-to-cloud communicatiebegeleiding en](iot-hub-devguide-d2c-guidance.md) communicatiebegeleiding tussen apparaten naar [de cloud](iot-hub-devguide-c2d-guidance.md)voor informatie over hoe deze protocollen specifieke IoT Hub-functies ondersteunen.

De volgende tabel bevat de aanbevelingen op hoog niveau voor uw protocolkeuze:

| Protocol | Wanneer u dit protocol moet kiezen |
| --- | --- |
| MQTT <br> MQTT via WebSocket |Gebruik op alle apparaten die niet nodig zijn om meerdere apparaten (elk met zijn eigen referenties per apparaat) via dezelfde TLS-verbinding aan te sluiten. |
| AMQP <br> AMQP via WebSocket |Gebruik op veld- en cloudgateways om te profiteren van het multiplexen van verbindingen op verschillende apparaten. |
| HTTPS |Gebruik voor apparaten die andere protocollen niet kunnen ondersteunen. |

Houd rekening met de volgende punten wanneer u uw protocol voor communicatie aan de apparaatzijde kiest:

* **Cloud-to-device patroon**. HTTPS heeft geen efficiënte manier om serverpush te implementeren. Als zodanig, wanneer u HTTPS gebruikt, peilen apparaten IoT Hub voor cloud-to-device-berichten. Deze aanpak is inefficiënt voor zowel het apparaat als de IoT Hub. Volgens de huidige HTTPS-richtlijnen moet elk apparaat elke 25 minuten of langer voor berichten worden gepeild. MQTT en AMQP ondersteunen server push bij het ontvangen van cloud-to-device berichten. Ze maken het mogelijk om berichten van IoT Hub naar het apparaat te pushen. Als de leveringslatentie een probleem is, zijn MQTT of AMQP de beste protocollen om te gebruiken. Voor zelden verbonden apparaten werkt HTTPS ook.

* **Veldgateways**. Wanneer u MQTT en HTTPS gebruikt, u niet meerdere apparaten (elk met zijn eigen referenties per apparaat) verbinden via dezelfde TLS-verbinding. Voor [veldgatewayscenario's](iot-hub-devguide-endpoints.md#field-gateways) waarvoor één TLS-verbinding tussen de veldgateway en IoT-hub voor elk verbonden apparaat vereist is, zijn deze protocollen suboptimaal.

* **Apparaten met een lage resource**. De MQTT- en HTTPS-bibliotheken hebben een kleinere voetafdruk dan de AMQP-bibliotheken. Als zodanig, als het apparaat beperkte resources heeft (bijvoorbeeld minder dan 1 MB RAM), zijn deze protocollen mogelijk de enige protocol-implementatie die beschikbaar is.

* **Netwerk traversal**. Het standaard AMQP-protocol maakt gebruik van poort 5671 en MQTT luistert op poort 8883. USe van deze poorten kan problemen veroorzaken in netwerken die zijn gesloten voor niet-HTTPS-protocollen. Gebruik MQTT via WebSockets, AMQP via WebSockets of HTTPS in dit scenario.

* **Laadvermogen grootte**. MQTT en AMQP zijn binaire protocollen, die resulteren in compactere payloads dan HTTPS.

> [!WARNING]
> Wanneer u HTTPS gebruikt, moet elk apparaat niet meer dan eens per 25 minuten voor cloud-to-device-berichten worden gepeild. In ontwikkeling kan elk apparaat vaker pollen, indien gewenst.

## <a name="port-numbers"></a>Poortnummers

Apparaten kunnen communiceren met IoT Hub in Azure met behulp van verschillende protocollen. Meestal wordt de keuze van het protocol bepaald door de specifieke vereisten van de oplossing. In de volgende tabel worden de uitgaande poorten weergegeven die open moeten zijn voor een apparaat om een specifiek protocol te kunnen gebruiken:

| Protocol | Poort |
| --- | --- |
| MQTT |8883 |
| MQTT via WebSockets |443 |
| AMQP |5671 |
| AMQP via WebSockets |443 |
| HTTPS |443 |

Zodra u een IoT-hub in een Azure-regio hebt gemaakt, behoudt de IoT-hub hetzelfde IP-adres voor de levensduur van die IoT-hub. Als Microsoft de IoT-hub echter naar een andere schaaleenheid verplaatst om de kwaliteit van de service te behouden, krijgt deze een nieuw IP-adres toegewezen.

## <a name="next-steps"></a>Volgende stappen

Zie [Communiceren met uw IoT-hub met het MQTT-protocol](iot-hub-mqtt-support.md)voor meer informatie over hoe IoT Hub het MQTT-protocol implementeert.
