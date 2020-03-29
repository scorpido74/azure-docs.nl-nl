---
title: Modbusprotocollen vertalen met gateways - Azure IoT Edge | Microsoft Documenten
description: Toestaan dat apparaten die Modbus TCP gebruiken, kunnen communiceren met Azure IoT Hub door een IoT-gatewayapparaat te maken
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 23fbbd87230ea0a0147dc9d90c77729f4d531e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511141"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Modbus TCP-apparaten verbinden via een IoT Edge-apparaatgateway

Als u IoT-apparaten die Modbus TCP- of RTU-protocollen gebruiken, wilt verbinden met een Azure IoT-hub, u een IoT Edge-apparaat als gateway gebruiken. Het gatewayapparaat leest de gegevens van uw Modbus-apparaten en geeft deze gegevens met behulp van een ondersteund protocol door aan de cloud.

![Modbus-apparaten maken verbinding met IoT Hub via IoT Edge-gateway](./media/deploy-modbus-gateway/diagram.png)

Dit artikel gaat over hoe u uw eigen containerinstallatiekopie maakt voor een Modbus-module (u kunt ook een kant- en-klaar voorbeeld gebruiken) en deze vervolgens implementeert op het IoT Edge-apparaat dat als uw gateway zal fungeren.

In dit artikel wordt ervan uitgegaan dat u het Modbus-protocol TCP gebruikt. Zie het [Azure IoT Edge Modbus-moduleproject](https://github.com/Azure/iot-edge-modbus) op GitHub voor meer informatie over het configureren van de module Modbus RTU.

## <a name="prerequisites"></a>Vereisten

* Een Azure IoT Edge-apparaat. Zie Azure IoT Edge implementeren op Windows of [Linux](quickstart-linux.md)voor een walkthrough over het instellen van een [azure iot edge.](quickstart.md)
* De verbindingsreeks van de primaire sleutel voor het IoT Edge-apparaat.
* Een fysiek of gesimuleerd Modbus-apparaat dat Modbus TCP ondersteunt. U moet het IPv4-adres weten.

## <a name="prepare-a-modbus-container"></a>Een Modbus-container voorbereiden

Als u de functionaliteit van de Modbus-gateway wilt testen, heeft Microsoft een voorbeeldmodule die u kunt gebruiken. U hebt toegang tot de module via de Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)of met de afbeelding URI. `mcr.microsoft.com/azureiotedge/modbus:1.0`

Als u uw eigen module wilt maken en aanpassen aan uw omgeving, is er een open-source [Azure IoT Edge Modbus-moduleproject](https://github.com/Azure/iot-edge-modbus) op GitHub. Volg de instructies in dit project om uw eigen containerinstallatiekopie te maken. Als u een containerafbeelding wilt maken, raadpleegt u [C#-modules ontwikkelen in Visual Studio](how-to-visual-studio-develop-csharp-module.md) of Modules ontwikkelen in Visual Studio [Code.](how-to-vs-code-develop-module.md) Deze artikelen geven instructies over het maken van nieuwe modules en het publiceren van containerafbeeldingen in een register.

## <a name="try-the-solution"></a>Probeer de oplossing

In deze sectie wordt de voorbeeldmodule Modbus van Microsoft geïmplementeerd op uw IoT Edge-apparaat.

1. Ga in de [Azure-portal](https://portal.azure.com/) naar uw IoT-hub.

2. Ga naar **IoT Edge** en klik op je IoT Edge-apparaat.

3. Selecteer **Modules instellen**.

4. Voeg in de sectie **IoT Edge Modules** de Modbus-module toe:

   1. Klik **op** de vervolgkeuzelijst Toevoegen en selecteer **Marketplace-module**.
   2. Zoek `Modbus` naar en selecteer de **Modbus TCP Module** van Microsoft.
   3. De module wordt automatisch geconfigureerd voor uw IoT-hub en wordt weergegeven in de lijst met IoT Edge-modules. De routes worden ook automatisch geconfigureerd. Selecteer **Controleren + maken**.
   4. Bekijk het implementatiemanifest en selecteer **Maken**.

5. Selecteer de Module `ModbusTCPModule`Modbus in de lijst en selecteer het tabblad **Tweeinstellingen module.** De vereiste JSON voor de gewenste eigenschappen van de module twin is automatisch gevuld.

6. Zoek naar de eigenschap **SlaveConnection** in de JSON en stel de waarde ervan in op het IPv4-adres van uw Modbus-apparaat.

7. Selecteer **Update**.

8. Selecteer **Controleren + maken,** controleer de implementatie en selecteer **Vervolgens Maken**.

9. Ga terug naar de detailpagina van het apparaat en selecteer **Vernieuwen**. U ziet de `ModbusTCPModule` nieuwe module samen met de Runtime van IoT Edge worden uitgevoerd.

## <a name="view-data"></a>Gegevens weergeven

Bekijk de gegevens die via de Modbus-module binnenkomen:

```cmd/sh
iotedge logs modbus
```

U ook de telemetrie bekijken die het apparaat verzendt met behulp van de [Azure IoT Hub-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (voorheen Azure IoT Toolkit-extensie).

## <a name="next-steps"></a>Volgende stappen

* Zie [Een IoT Edge-apparaat maken dat fungeert als een transparante gateway](./how-to-create-transparent-gateway.md)voor meer informatie over hoe IoT Edge-apparaten als gateway kunnen fungeren.
* Zie [Azure IoT Edge-modules begrijpen](iot-edge-modules.md)voor meer informatie over hoe IoT Edge-modules werken.
