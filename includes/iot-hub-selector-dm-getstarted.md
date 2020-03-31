---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 239ea2eb4d5bc8d326d5ca503a18b149252dc1be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69558695"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Back-end-apps kunnen Azure IoT Hub-primitieven, zoals [apparaattweeling-][lnk-devtwin] en [directe methoden,][lnk-c2dmethod]gebruiken om op afstand apparaatbeheeracties op apparaten te starten en te controleren. In deze zelfstudie ziet u hoe een back-end-app en een apparaat-app kunnen samenwerken om een herstart van een extern apparaat te starten en te controleren met behulp van IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Gebruik een directe methode om apparaatbeheeracties (zoals opnieuw opstarten, fabrieksreset en firmware-update) te starten vanuit een back-end-app in de cloud. Het apparaat is verantwoordelijk voor:

* De methodeaanvraag verwerken die vanuit IoT Hub wordt verzonden.

* Het initiëren van de bijbehorende apparaatspecifieke actie op het apparaat.

* Het verstrekken van statusupdates via *gerapporteerde eigenschappen* aan IoT Hub.

U een back-end-app in de cloud gebruiken om dubbele apparaatquery's uit te voeren om te rapporteren over de voortgang van uw apparaatbeheeracties.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
