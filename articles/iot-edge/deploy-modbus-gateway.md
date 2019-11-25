---
title: Translate modbus protocols with gateways - Azure IoT Edge | Microsoft Docs
description: Toestaan dat apparaten die Modbus TCP gebruiken, kunnen communiceren met Azure IoT Hub door een IoT-gatewayapparaat te maken
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: kgremban
ms.openlocfilehash: 649c7f620b83464d1bb56cf4b8191b0747105f01
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457222"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Connect Modbus TCP devices through an IoT Edge device gateway

Als u IoT-apparaten die gebruikmaken van Modbus TCP- of RTU-protocollen wilt verbinden met een Azure IoT-hub, gebruik dan een IoT Edge-apparaat als een gateway. Het gatewayapparaat leest de gegevens van uw Modbus-apparaten en geeft deze gegevens met behulp van een ondersteund protocol door aan de cloud.

![Modbus devices connect to IoT Hub through IoT Edge gateway](./media/deploy-modbus-gateway/diagram.png)

Dit artikel gaat over hoe u uw eigen containerinstallatiekopie maakt voor een Modbus-module (u kunt ook een kant- en-klaar voorbeeld gebruiken) en deze vervolgens implementeert op het IoT Edge-apparaat dat als uw gateway zal fungeren.

In dit artikel wordt ervan uitgegaan dat u het Modbus-protocol TCP gebruikt. For more information about how to configure the module to support Modbus RTU, see the [Azure IoT Edge Modbus module](https://github.com/Azure/iot-edge-modbus) project on GitHub.

## <a name="prerequisites"></a>Vereisten
* Een Azure IoT Edge-apparaat. For a walkthrough on how to set up one, see [Deploy Azure IoT Edge on Windows](quickstart.md) or [Linux](quickstart-linux.md).
* De verbindingsreeks van de primaire sleutel voor het IoT Edge-apparaat.
* Een fysiek of gesimuleerd Modbus-apparaat dat Modbus TCP ondersteunt.

## <a name="prepare-a-modbus-container"></a>Een Modbus-container voorbereiden

Als u de functionaliteit van de Modbus-gateway wilt testen, heeft Microsoft een voorbeeldmodule die u kunt gebruiken. You can access the module from the Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview), or with the image URI, **mcr.microsoft.com/azureiotedge/modbus:1.0**.

If you want to create your own module and customize it for your environment, there is an open-source [Azure IoT Edge Modbus module](https://github.com/Azure/iot-edge-modbus) project on GitHub. Volg de instructies in dit project om uw eigen containerinstallatiekopie te maken. To create a container image, refer to [Develop C# modules in Visual Studio](how-to-visual-studio-develop-csharp-module.md) or [Develop modules in Visual Studio Code](how-to-vs-code-develop-module.md). Those articles provide instructions on creating new modules and publishing container images to a registry.

## <a name="try-the-solution"></a>Try the solution

This section walks through deploying Microsoft's sample Modbus module to your IoT Edge device.

1. Ga in de [Azure-portal](https://portal.azure.com/) naar uw IoT-hub.

2. Go to **IoT Edge** and click on your IoT Edge device.

3. Selecteer **Modules instellen**.

4. Voeg als volgt de Modbus-module toe:

   1. Click **Add** and select **IoT Edge module**.

   2. Typ 'modbus' in het veld **Naam**.

   3. Voer in het veld **Installatiekopie** de URI in naar de installatiekopie van de voorbeeldcontainer: `mcr.microsoft.com/azureiotedge/modbus:1.0`.

   4. Schakel het selectievakje **Inschakelen** in om de gewenst eigenschappen van de moduledubbel bij te werken.

   5. Kopieer de volgende JSON naar het tekstvak. Wijzig de waarde van **SlaveConnection** in het IPv4-adres van uw Modbus-apparaat.

      ```JSON
      {
        "properties.desired":{
          "PublishInterval":"2000",
          "SlaveConfigs":{
            "Slave01":{
              "SlaveConnection":"<IPV4 address>","HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{
                "Op01":{
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"40001",
                  "Count":"2",
                  "DisplayName":"Voltage"
                }
              }
            }
          }
        }
      }
      ```

   6. Selecteer **Opslaan**.

5. Terug in de stap **Modules toevoegen** selecteert u **Volgende**.

7. In de stap **Routes opgeven** kopieert u de volgende JSON naar het tekstvak. Deze route verzendt alle berichten die door de Modbus-module worden verzameld naar IoT Hub. In this route, **modbusOutput** is the endpoint that Modbus module uses to output data and **$upstream** is a special destination that tells IoT Edge hub to send messages to IoT Hub.

   ```JSON
   {
     "routes": {
       "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
     }
   }
   ```

8. Selecteer **Next**.

9. Selecteer in de stap **Implementatie beoordelen** de optie **Verzenden**.

10. Ga terug naar de detailpagina van het apparaat en selecteer **Vernieuwen**. You should see the new **modbus** module running along with the IoT Edge runtime.

## <a name="view-data"></a>Gegevens weergeven
U bekijkt als volgt de gegevens die via de modbus-module lopen:
```cmd/sh
iotedge logs modbus
```

You can also view the telemetry the device is sending by using the [Azure IoT Hub Toolkit extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (formerly Azure IoT Toolkit extension).

## <a name="next-steps"></a>Volgende stappen

- To learn more about how IoT Edge devices can act as gateways, see [Create an IoT Edge device that acts as a transparent gateway](./how-to-create-transparent-gateway.md).
- For more information about how IoT Edge modules work, see [Understand Azure IoT Edge modules](iot-edge-modules.md).
