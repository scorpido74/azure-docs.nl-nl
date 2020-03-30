---
title: Raspberry Pi-websimulator verbinden met Azure IoT Hub (Node.js)
description: Sluit raspberry pi-websimulator aan op Azure IoT Hub voor Raspberry Pi om gegevens naar de Azure-cloud te verzenden.
author: wesmc7777
manager: philmea
keywords: raspberry pi simulator, azure iot raspberry pi, raspberry pi iot hub, raspberry pi send data to cloud, raspberry pi to cloud
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: efbe41be6c923f3547df86fd6faeb56bff5e0802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954535"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Raspberry Pi online simulator verbinden met Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

In deze tutorial, begin je met het leren van de basisprincipes van het werken met Raspberry Pi online simulator. Vervolgens leert u hoe u de Pi-simulator naadloos met de cloud verbinden met Azure [IoT Hub.](about-iot-hub.md)

Als u fysieke apparaten hebt, gaat u naar [Connect Raspberry Pi met Azure IoT Hub](iot-hub-raspberry-pi-kit-node-get-started.md) om aan de slag te gaan.

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

## <a name="what-you-do"></a>Wat je doet

* Leer de basisprincipes van raspberry pi online simulator.

* Maak een IoT-hub.

* Registreer een apparaat voor Pi in uw IoT-hub.

* Voer een voorbeeldtoepassing uit op Pi om gesimuleerde sensorgegevens naar uw IoT-hub te verzenden.

Verbind gesimuleerde Raspberry Pi met een IoT-hub die u maakt. Vervolgens voert u een voorbeeldtoepassing uit met de simulator om sensorgegevens te genereren. Ten slotte stuurt u de sensorgegevens naar uw IoT-hub.

## <a name="what-you-learn"></a>Wat u leert

* Een Azure IoT-hub maken en uw nieuwe tekenreeks voor apparaatverbinding krijgen. Als u geen Azure-account hebt, maakt u binnen enkele minuten [een gratis Azure-proefaccount.](https://azure.microsoft.com/free/)

* Hoe te werken met Raspberry Pi online simulator.

* Sensorgegevens verzenden naar uw IoT-hub.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Overzicht van Raspberry Pi websimulator

Klik op de knop om raspberry pi online simulator te starten.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Raspberry Pi-simulator starten</a>

Er zijn drie gebieden in de websimulator.

1. Assemblagegebied: bij een standaardcircuit wordt Pi verbonden met een BME280-sensor en een LED. Het gebied is in de preview-versie vergrendeld zodat er momenteel geen aanpassingen mogelijk zijn.

2. Coderingsgebied: een onlinecode-editor om met Raspberry Pi te coderen. De standaardvoorbeeldtoepassing helpt u sensorgegevens van de BME280-sensor te verzamelen en naar uw Azure-IoT-hub te verzenden. De toepassing is volledig compatibel met echte Pi-apparaten. 

3. Geïntegreerd consolevenster: hierin wordt de uitvoer van uw code weergegeven. Bovenin het venster bevinden zich drie knoppen.

   * **Uitvoering**: voer de toepassing uit in het coderingsgebied.

   * **Opnieuw instellen**: stel het coderingsgebied opnieuw in op de standaardvoorbeeldtoepassing.

   * **Samenvouwen/uitvouwen**: aan de rechterkant vindt u een knop waarmee u het consolevenster kunt samenvouwen/uitvouwen.

> [!NOTE]
> De Raspberry Pi websimulator is nu beschikbaar in preview-versie. We willen je stem graag horen in de [Gitter Chatroom.](https://gitter.im/Microsoft/raspberry-pi-web-simulator) De broncode is openbaar op [GitHub.](https://github.com/Azure-Samples/raspberry-pi-web-simulator)

![Overzicht van Pi online simulator](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Een voorbeeldtoepassing uitvoeren op Pi-websimulator

1. Controleer in het coderingsgedeelte of u werkt met de standaardvoorbeeldtoepassing. Vervang de tijdelijke aanduiding in regel 15 door de verbindingsreeks voor het Azure IoT Hub-apparaat.
1. 
   ![De apparaatverbindingsreeks ophalen](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Selecteer **Uitvoeren** `npm start` of typen om de toepassing uit te voeren.

U ziet de volgende uitvoer die de sensorgegevens en de berichten ![weergeeft die naar uw IoT-hub-uitvoer worden verzonden - sensorgegevens die van Raspberry Pi naar uw IoT-hub worden verzonden](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Lees de berichten die door uw hub zijn ontvangen

Een manier om berichten te controleren die door uw IoT-hub vanaf het gesimuleerde apparaat worden ontvangen, is door de Azure IoT-hulpprogramma's voor Visual Studio-code te gebruiken. Zie [Azure IoT-hulpprogramma's voor Visual Studio-code gebruiken om berichten tussen uw apparaat en IoT Hub te verzenden en te ontvangen.](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)

Ga door naar de volgende sectie voor meer manieren om gegevens te verwerken die door uw apparaat worden verzonden.

## <a name="next-steps"></a>Volgende stappen

U hebt een voorbeeldtoepassing uitgevoerd om sensorgegevens te verzamelen en naar uw IoT-hub te verzenden.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
