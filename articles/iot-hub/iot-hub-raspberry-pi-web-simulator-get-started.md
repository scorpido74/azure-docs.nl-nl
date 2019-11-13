---
title: Verbinding maken tussen Raspberry Pi Web Simulator en Azure IoT Hub (node. js)
description: Verbind Raspberry Pi Web Simulator met Azure IoT Hub voor Raspberry PI om gegevens te verzenden naar de Azure-Cloud.
author: wesmc7777
manager: philmea
keywords: Raspberry Pi Simulator, Azure IOT Raspberry Pi, Raspberry Pi IOT hub, Raspberry Pi gegevens verzenden naar de Cloud, Raspberry Pi naar de Cloud
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: efbe41be6c923f3547df86fd6faeb56bff5e0802
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954535"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Verbinding maken tussen Raspberry Pi online Simulator en Azure IoT Hub (node. js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

In deze zelf studie begint u met het leren van de basis beginselen van het werken met Raspberry Pi online Simulator. U leert hoe u de Pi Simulator probleemloos verbindt met de Cloud met behulp van [Azure IOT hub](about-iot-hub.md).

Als u fysieke apparaten hebt, gaat u naar [Raspberry Pi Connect to Azure IOT hub](iot-hub-raspberry-pi-kit-node-get-started.md) om aan de slag te gaan.

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3-banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6-button-default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5-button-click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6-button-default.png';">
</div>

## <a name="what-you-do"></a>Wat u doet

* Meer informatie over de basis beginselen van Raspberry Pi online Simulator.

* Maak een IoT-hub.

* Registreer een apparaat voor pi in uw IoT-hub.

* Voer een voorbeeld toepassing op PI uit om gesimuleerde sensor gegevens naar uw IoT-hub te verzenden.

Verbind gesimuleerde Raspberry Pi met een IoT-hub die u maakt. Vervolgens voert u een voorbeeld toepassing uit met de Simulator om sensor gegevens te genereren. Ten slotte verzendt u de sensor gegevens naar uw IoT-hub.

## <a name="what-you-learn"></a>Wat u leert

* Een Azure IoT hub maken en uw nieuwe apparaat connection string ophalen. Als u geen Azure-account hebt, kunt u binnen een paar minuten [een gratis Azure-proef account maken](https://azure.microsoft.com/free/) .

* Werken met Raspberry Pi online Simulator.

* Sensor gegevens verzenden naar uw IoT-hub.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Overzicht van Raspberry Pi Web Simulator

Klik op de knop om Raspberry Pi online Simulator te starten.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Raspberry Pi Simulator starten</a>

De Web Simulator bevat drie gebieden.

1. Assembly-gebied-het standaard circuit is dat een pi verbinding maakt met een BME280-sensor en een LED. Het gebied is vergrendeld in preview-versie, zodat u niet kunt aanpassen.

2. Coderings gebied: een online code-editor waarmee u code kunt coderen met Raspberry pi. De standaard voorbeeld toepassing helpt bij het verzamelen van sensor gegevens van de BME280-sensor en verzendt deze naar uw Azure IoT Hub. De toepassing is volledig compatibel met echte Pi-apparaten. 

3. Geïntegreerd console venster: de uitvoer van de code wordt weer gegeven. Boven aan dit venster bevinden zich drie knoppen.

   * **Run** -Voer de toepassing uit in het gedeelte code ring.

   * **Reset** -het code gebied opnieuw instellen op de standaard voorbeeld toepassing.

   * **Vouwen/uitvouwen** : aan de rechter kant ziet u een knop waarmee u het console venster kunt vouwen of uitbreiden.

> [!NOTE]
> De Raspberry Pi Web Simulator is nu beschikbaar als preview-versie. We horen graag uw stem in de Gitter- [chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator). De bron code is openbaar op [github](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Overzicht van pi online Simulator](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Een voorbeeld toepassing uitvoeren op Pi Web Simulator

1. Zorg er in de code ring voor dat u werkt met de standaard voorbeeld toepassing. Vervang de tijdelijke aanduiding in regel 15 door de Azure IoT hub-apparaat connection string.
1. 
   ![De connection string van het apparaat vervangen](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Selecteer **uitvoeren** of typ `npm start` om de toepassing uit te voeren.

De volgende uitvoer wordt weer gegeven met de sensor gegevens en de berichten die worden verzonden naar uw IoT-hub ![uitvoer sensor gegevens die vanuit Raspberry Pi naar uw IoT hub worden verzonden](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Lees de berichten die door uw hub zijn ontvangen

Een manier om berichten te bewaken die door uw IoT-hub van het gesimuleerde apparaat worden ontvangen, is het gebruik van de Azure IoT-Hulpprogram Ma's voor Visual Studio code. Zie [Azure IOT-Hulpprogram ma's voor Visual Studio code gebruiken voor het verzenden en ontvangen van berichten tussen uw apparaat en IOT hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)voor meer informatie.

Ga verder met de volgende sectie voor meer manieren om gegevens te verwerken die door uw apparaat worden verzonden.

## <a name="next-steps"></a>Volgende stappen

U hebt een voorbeeld toepassing uitgevoerd voor het verzamelen van sensor gegevens en deze naar uw IoT-hub te verzenden.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
