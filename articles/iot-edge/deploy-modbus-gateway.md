---
title: Met gateways - Azure IoT Edge modbus-protocollen vertalen | Microsoft Docs
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
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Modbus TCP-apparaten verbinden via de gateway van een IoT Edge-apparaat

Als u IoT-apparaten die gebruikmaken van Modbus TCP- of RTU-protocollen wilt verbinden met een Azure IoT-hub, gebruik dan een IoT Edge-apparaat als een gateway. Het gatewayapparaat leest de gegevens van uw Modbus-apparaten en geeft deze gegevens met behulp van een ondersteund protocol door aan de cloud.

![Modbus-apparaten maken verbinding met IoT Hub via IoT Edge gateway](./media/deploy-modbus-gateway/diagram.png)

Dit artikel gaat over hoe u uw eigen containerinstallatiekopie maakt voor een Modbus-module (u kunt ook een kant- en-klaar voorbeeld gebruiken) en deze vervolgens implementeert op het IoT Edge-apparaat dat als uw gateway zal fungeren.

In dit artikel wordt ervan uitgegaan dat u het Modbus-protocol TCP gebruikt. Voor meer informatie over het configureren van de module ter ondersteuning van modbus RTU raadpleegt u het project [Modbus module Azure IOT Edge](https://github.com/Azure/iot-edge-modbus) op github.

## <a name="prerequisites"></a>Vereisten
* Een Azure IoT Edge-apparaat. Zie [Azure IOT Edge implementeren in Windows](quickstart.md) of [Linux](quickstart-linux.md)voor een overzicht van het instellen van een account.
* De verbindingsreeks van de primaire sleutel voor het IoT Edge-apparaat.
* Een fysiek of gesimuleerd Modbus-apparaat dat Modbus TCP ondersteunt.

## <a name="prepare-a-modbus-container"></a>Een Modbus-container voorbereiden

Als u de functionaliteit van de Modbus-gateway wilt testen, heeft Microsoft een voorbeeldmodule die u kunt gebruiken. U kunt de module openen vanuit Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)of met de afbeeldings-URI **MCR.Microsoft.com/azureiotedge/Modbus:1.0**.

Als u uw eigen module wilt maken en deze wilt aanpassen voor uw omgeving, is er een open-source [Azure IOT Edge Modbus-module](https://github.com/Azure/iot-edge-modbus) project op github. Volg de instructies in dit project om uw eigen containerinstallatiekopie te maken. Als u een container installatie kopie wilt maken, raadpleegt u [modules C# ontwikkelen in Visual Studio](how-to-visual-studio-develop-csharp-module.md) of [modules ontwikkelen in Visual Studio code](how-to-vs-code-develop-module.md). Deze artikelen bevatten instructies voor het maken van nieuwe modules en het publiceren van container installatie kopieën in een REGI ster.

## <a name="try-the-solution"></a>Probeer de oplossing

In deze sectie wordt uitgelegd hoe u de voor beeld-Modbus-module van micro soft implementeert op uw IoT Edge-apparaat.

1. Ga in de [Azure-portal](https://portal.azure.com/) naar uw IoT-hub.

2. Ga naar **IOT Edge** en klik op uw IOT edge apparaat.

3. Selecteer **Modules instellen**.

4. Voeg als volgt de Modbus-module toe:

   1. Klik op **toevoegen** en selecteer **IOT Edge module**.

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

7. In de stap **Routes opgeven** kopieert u de volgende JSON naar het tekstvak. Deze route verzendt alle berichten die door de Modbus-module worden verzameld naar IoT Hub. In deze route is **modbusOutput** het eind punt dat door de Modbus-module wordt gebruikt om gegevens uit te voeren en **$upstream** een speciaal doel is dat IOT Edge hub weet dat er berichten naar IOT hub worden verzonden.

   ```JSON
   {
     "routes": {
       "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
     }
   }
   ```

8. Selecteer **Volgende**.

9. Selecteer in de stap **Implementatie beoordelen** de optie **Verzenden**.

10. Ga terug naar de detailpagina van het apparaat en selecteer **Vernieuwen**. De nieuwe **Modbus** -module wordt samen met de IOT Edge runtime weer geven.

## <a name="view-data"></a>Gegevens weergeven
U bekijkt als volgt de gegevens die via de modbus-module lopen:
```cmd/sh
iotedge logs modbus
```

U kunt ook de telemetrie weer geven die het apparaat verzendt met behulp van de [azure IOT hub Toolkit-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (voorheen Azure IOT Toolkit-extensie).

## <a name="next-steps"></a>Volgende stappen

- Zie [een IOT edge apparaat maken dat fungeert als transparante gateway](./how-to-create-transparent-gateway.md)voor meer informatie over hoe IOT edge-apparaten kunnen fungeren als gateways.
- Zie [Azure IOT Edge modules begrijpen](iot-edge-modules.md)voor meer informatie over de werking van IOT Edge modules.
