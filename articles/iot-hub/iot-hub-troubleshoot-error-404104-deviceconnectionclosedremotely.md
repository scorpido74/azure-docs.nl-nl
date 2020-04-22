---
title: Azure IoT Hub-fout oplossen 404104 DeviceConnectionOp afstand afgesloten
description: Begrijpen hoe u fout 404104 DeviceConnection op afstand oplossen
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: c8cb91aa0c7ce1610320d4107db282d3c34407ba
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758722"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

In dit artikel worden de oorzaken en oplossingen beschreven voor fouten die op afstand zijn gedicht voor **404104 DeviceConnection.**

## <a name="symptoms"></a>Symptomen

### <a name="symptom-1"></a>Symptoom 1

Apparaten verbreken met een regelmatige interval (bijvoorbeeld elke 65 minuten) en u ziet **404104 DeviceConnectionClosedRemotely** in IoT Hub diagnostische logboeken. Soms ziet u ook **401003 IoTHubUnauthorized** en een succesvolle gebeurtenis voor apparaatverbindingen minder dan een minuut later.

### <a name="symptom-2"></a>Symptoom 2

Apparaten verbreken willekeurig de verbinding en u ziet **404104 DeviceConnectionClosedRemotely** in IoT Hub diagnostische logboeken.

### <a name="symptom-3"></a>Symptoom 3

Veel apparaten verbreken in één keer de verbinding, u ziet een dip in de [metrische verbonden apparaten](iot-hub-metrics.md)en er zijn meer **404104 DeviceConnectionClosedRemotely** en [500xxx Interne fouten](iot-hub-troubleshoot-error-500xxx-internal-errors.md) in diagnostische logboeken dan normaal.

## <a name="causes"></a>Oorzaken

### <a name="cause-1"></a>Oorzaak 1

Het [SAS-token dat wordt gebruikt om verbinding te maken met IoT Hub](iot-hub-devguide-security.md#security-tokens) is verlopen, waardoor IoT Hub het apparaat loskoppelt. De verbinding wordt hersteld wanneer het token wordt vernieuwd door het apparaat. Het [SAS-token verloopt](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication)bijvoorbeeld standaard elk uur voor C SDK, wat kan leiden tot regelmatige ontkoppeling.

Zie [401003 IoTHubUnauthorized cause voor](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1)meer informatie.

### <a name="cause-2"></a>Oorzaak 2

Enkele mogelijkheden zijn:

- Het apparaat verloor de onderliggende netwerkconnectiviteit langer dan de [MQTT in leven,](iot-hub-mqtt-support.md#default-keep-alive-timeout)wat resulteert in een externe idle time-out. De MQTT-instelling kan per apparaat verschillen.

- Het apparaat heeft een reset op TCP/IP-niveau verzonden, maar heeft geen toepassingsniveau verzonden. `MQTT DISCONNECT` In principe heeft het apparaat abrupt de onderliggende socketverbinding gesloten. Soms wordt dit probleem veroorzaakt door bugs in oudere versies van de Azure IoT SDK.

- De toepassing aan de zijkant van het apparaat is gecrasht.

### <a name="cause-3"></a>Oorzaak 3

IoT Hub ondervindt mogelijk een tijdelijk probleem. Zie [De interne serverfout van IoT Hub oorzaak](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause).

## <a name="solutions"></a>Oplossingen

### <a name="solution-1"></a>Oplossing 1

Zie [401003 IoTHubUnauthorized oplossing 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>Oplossing 2

- Zorg ervoor dat het apparaat een goede verbinding heeft met IoT Hub door [de verbinding te testen.](tutorial-connectivity.md) Als het netwerk onbetrouwbaar of intermitterend is, raden we niet aan om de waarde in leven te houden, omdat dit kan leiden tot detectie (bijvoorbeeld via Azure Monitor-waarschuwingen) die langer duren. 

- Gebruik de nieuwste versies van de [IoT SDKs.](iot-hub-devguide-sdks.md)

### <a name="solution-3"></a>Oplossing 3

Bekijk [oplossingen voor interne serverfouten van IoT Hub.](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution)

## <a name="next-steps"></a>Volgende stappen

We raden u aan Azure IoT-apparaat-SDK's te gebruiken om verbindingen betrouwbaar te beheren. Zie [Connectiviteit en betrouwbare berichten beheren met Azure IoT Hub-apparaat SDK's beheren voor](iot-hub-reliability-features-in-sdks.md) meer informatie.