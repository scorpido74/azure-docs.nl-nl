---
title: Oplossing voor externe controle edge-apparaat toevoegen-Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een IoT Edge apparaat toevoegt aan een oplossings versneller voor externe controle
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: 0a42763ff47cccfa506acbbbd95d20d41eb0827f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "72965378"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Een IoT Edge apparaat toevoegen aan de oplossings versneller voor externe controle

Voer de volgende twee stappen uit om een [IOT Edge](../iot-edge/about-iot-edge.md) apparaat toe te voegen aan uw oplossings versneller:

1. Voeg het apparaat aan de rand toe op de pagina **device Explorer** in de webgebruikersinterface van de oplossing voor externe bewaking.
1. Installeer de IoT Edge runtime op uw edge-apparaat.

## <a name="add-the-iot-edge-device"></a>Het IoT Edge apparaat toevoegen

Als u een IoT Edge-apparaat wilt toevoegen aan de verbetering voor de externe bewakingsoplossing, gaat u naar de pagina **Device Explorer** in de webgebruikersinterface en klikt u op **+ Nieuw apparaat**.

Kies **IOT edge apparaat**in het deel venster **nieuw apparaat** . Voor de overige instellingen kunt u de standaardwaarden gebruiken. Klik vervolgens op **Toepassen**:

![IoT Edge-apparaat toevoegen](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Alternatieve manieren om een IoT Edge apparaat toe te voegen

Het is ook mogelijk om een IoT Edge apparaat rechtstreeks te registreren bij het IoT Hub-exemplaar in uw oplossings versneller. U moet de naam van de IoT-hub in uw oplossings versneller kennen voordat u een van de volgende procedures voor hand leidingen volgt:

- [Een nieuw Azure IoT Edge apparaat registreren bij de Azure Portal](../iot-edge/how-to-register-device.md#register-in-the-azure-portal)
- [Een nieuw Azure IoT Edge-apparaat registreren bij Azure CLI](../iot-edge/how-to-register-device.md#register-with-the-azure-cli)
- [Een nieuw Azure IoT Edge-apparaat registreren bij Visual Studio code](../iot-edge/how-to-register-device.md#register-with-visual-studio-code)

Wanneer u een apparaat rechtstreeks registreert bij de IoT-hub in de oplossings versneller voor externe controle, wordt deze weer gegeven op de pagina **device Explorer** in de webgebruikersinterface.

## <a name="install-the-iot-edge-runtime"></a>De IoT Edge runtime installeren

Voordat u modules kunt implementeren op uw edge-apparaat, moet u de IoT Edge runtime op het echte apparaat installeren. In de volgende hand leidingen ziet u hoe u de runtime installeert op algemene platformen:

- [Installeer de Azure IoT Edge runtime op Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md)
- [Azure IoT Edge runtime installeren op Linux (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Azure IoT Edge runtime installeren op Windows voor gebruik met Windows-containers](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Installeer de Azure IoT Edge runtime op Windows voor gebruik met Linux-containers](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [Installeer de IoT Edge runtime op Windows IoT core](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>Volgende stappen

Nu u uw IoT Edge apparaat hebt voor bereid, is de volgende stap het implementeren van modules. Zie [een IOT Edge-pakket importeren in uw oplossings versneller voor externe controle](iot-accelerators-remote-monitoring-import-edge-package.md)
