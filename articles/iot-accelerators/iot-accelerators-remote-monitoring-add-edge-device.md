---
title: Remote Monitoring-oplossing voegt Edge-apparaat toe - Azure | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u een IoT Edge-apparaat toevoegt aan een versneller met externe bewakingsoplossingen
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: 0a42763ff47cccfa506acbbbd95d20d41eb0827f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72965378"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Een IoT Edge-apparaat toevoegen aan uw versneller met externe bewakingsoplossing

Voer de volgende twee stappen uit om een [IoT Edge-apparaat](../iot-edge/about-iot-edge.md) toe te voegen aan uw oplossingsversneller:

1. Voeg het Edge-apparaat toe op de pagina **Device Explorer** in de webgebruikersinterface voor versneller op afstand.
1. Installeer de IoT Edge-runtime op uw Edge-apparaat.

## <a name="add-the-iot-edge-device"></a>Het IoT Edge-apparaat toevoegen

Als u een IoT Edge-apparaat wilt toevoegen aan de verbetering voor de externe bewakingsoplossing, gaat u naar de pagina **Device Explorer** in de webgebruikersinterface en klikt u op **+ Nieuw apparaat**.

Kies in het deelvenster **Nieuw apparaat** de optie **IoT Edge-apparaat**. Voor de overige instellingen kunt u de standaardwaarden gebruiken. Klik vervolgens op **Toepassen:**

![IoT Edge-apparaat toevoegen](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Alternatieve manieren om een IoT Edge-apparaat toe te voegen

Het is ook mogelijk om een IoT Edge-apparaat rechtstreeks te registreren met de IoT Hub-instantie in uw oplossingsversneller. U moet de naam van de IoT-hub in uw oplossingsversneller weten voordat u een van deze handleidingen volgt:

- [Een nieuw Azure IoT Edge-apparaat registreren vanuit de Azure-portal](../iot-edge/how-to-register-device.md#register-in-the-azure-portal)
- [Een nieuw Azure IoT Edge-apparaat registreren met Azure CLI](../iot-edge/how-to-register-device.md#register-with-the-azure-cli)
- [Een nieuw Azure IoT Edge-apparaat registreren vanuit Visual Studio Code](../iot-edge/how-to-register-device.md#register-with-visual-studio-code)

Wanneer u een apparaat rechtstreeks registreert met de IoT-hub in de versneller van de oplossing voor externe bewaking, wordt het weergegeven op de pagina **Device Explorer** in de webgebruikersinterface.

## <a name="install-the-iot-edge-runtime"></a>De runtime van IoT Edge installeren

Voordat u modules op uw Edge-apparaat implementeren, moet u de IoT Edge-runtime op het echte apparaat installeren. In de volgende handleidingen ziet u hoe u de runtime installeren op algemene apparaatplatforms:

- [De Azure IoT Edge-runtime op Linux installeren (x64)](../iot-edge/how-to-install-iot-edge-linux.md)
- [Azure IoT Edge-runtime op Linux installeren (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Azure IoT Edge-runtime installeren op Windows voor gebruik met Windows-containers](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [De Azure IoT Edge-runtime op Windows installeren voor gebruik met Linux-containers](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [De Runtime van IoT Edge installeren op Windows IoT Core](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>Volgende stappen

Nu u uw IoT Edge-apparaat hebt voorbereid, is de volgende stap het implementeren van modules. Zie [Een IoT Edge-pakket importeren in uw remote monitoring-oplossingsversneller](iot-accelerators-remote-monitoring-import-edge-package.md)
