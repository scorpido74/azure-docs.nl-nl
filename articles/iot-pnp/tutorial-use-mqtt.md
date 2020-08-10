---
title: Een IoT Plug en Play-apparaatclient (preview) maken met MQTT | Microsoft Docs
description: Rechtstreeks het MQTT-protocol gebruiken om een IoT Plug en Play-apparaatclient (preview) te maken zonder de Azure IoT Device SDK's te gebruiken
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 56463b03fe633959585e14271050bcdaacb25663
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535178"
---
# <a name="use-mqtt-to-develop-an-iot-plug-and-play-preview-device-client"></a>Een IoT Plug en Play-apparaatclient (preview) ontwikkelen met MQTT

U moet een van de Azure IoT Device SDK's gebruiken om zo mogelijk uw IoT Plug en Play-apparaatclients te bouwen. In bepaalde scenario's, zoals wanneer u een apparaat met beperkt geheugen gebruikt, moet u echter mogelijk een MQTT-bibliotheek gebruiken om met uw IoT-hub te communiceren.

In het voorbeeld in deze zelfstudie wordt gebruikgemaakt van de MQTT-bibliotheek [Eclipse Mosquitto](http://mosquitto.org/) en Visual Studio. In de stappen in deze zelfstudie wordt ervan uitgegaan dat u Windows gebruikt op uw ontwikkelcomputer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie in Windows te voltooien, installeert u de volgende software in uw lokale Windows-omgeving:

* [Visual Studio (Community, Professional of Enterprise)](https://visualstudio.microsoft.com/downloads/): zorg ervoor dat u de workload **Desktop Development with C++** kiest tijdens het [installeren](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) van Visual Studio
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)
* [Azure IoT Explorer](howto-install-iot-explorer.md)

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de gedeelde toegangshandtekening op te halen waarmee het apparaat verbinding maakt met uw hub. Noteer deze tekenreeks voor later gebruik in deze zelfstudie:

```azurecli-interactive
az iot hub generate-sas-token -d <YourDeviceID> -n <YourIoTHubName>
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Gebruik de IoT-hub-verbindingsreeks om het hulpprogramma **Azure IoT Explorer** te configureren:

1. Start het hulpprogramma **Azure IoT Explorer**.
1. Plak op de pagina **Instellingen** de IoT-hub-verbindingsreeks in de instellingen voor **App-configuraties**.
1. Selecteer **Opslaan en verbinden**.
1. Het apparaat dat u eerder hebt toegevoegd, bevindt zich in de lijst met apparaten op de hoofdpagina.

## <a name="clone-sample-repo"></a>Voorbeeldopslagplaats klonen

Gebruik de volgende opdracht om de voorbeeldopslagplaats te klonen op de gewenste locatie op uw lokale computer:

```cmd
git clone https://github.com/Azure-Samples/IoTMQTTSample.git
```

## <a name="install-mqtt-library"></a>MQTT-bibliotheek installeren

Gebruik het hulpprogramma `vcpkg` om de Eclipse Mosquitto-bibliotheek te downloaden en te bouwen.

Gebruik de volgende opdracht om de opslagplaats **Vcpkg** te klonen op de gewenste locatie op uw lokale computer:

```cmd
git clone https://github.com/Microsoft/vcpkg.git
```

Gebruik de volgende opdrachten om de `vcpkg`-omgeving voor te bereiden. U hebt een opdrachtprompt met verhoogde bevoegdheid nodig wanneer u `vcpkg integrate install`uitvoert:

```cmd
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg integrate install
```

Gebruik de volgende opdracht om de Eclipse Mosquitto-bibliotheek te downloaden en te bouwen:

```cmd
.\vcpkg install mosquitto:x64-windows
```

## <a name="migrate-the-sample-to-iot-plug-and-play"></a>Het voorbeeld naar IoT Plug en Play migreren

### <a name="review-the-non-iot-plug-and-play-sample-code"></a>De niet-IoT Plug en Play-voorbeeldcode controleren

Werk de code bij met gegevens van uw IoT-hub en apparaat voordat u deze bouwt en uitvoert.

Als u de voorbeeldcode in Visual Studio wilt bekijken, opent u het oplossingsbestand *MQTTWin32.sln* in de map *IoTMQTTSample\src\Windows*.

Klik in **Solution Explorer** met de rechtermuisknop op het project **TelemetryMQTTWin32** en selecteer **Instellen als opstartproject**.

Open in het project **TelemetryMQTTWin32** het bronbestand **MQTT_Mosquitto.cpp**. Werk de definities van de verbindingsgegevens bij met de apparaatgegevens die u eerder hebt genoteerd. Vervang de tijdelijke tokentekenreeks voor:

* de id `IOTHUBNAME` door de naam van de IoT-hub die u hebt gemaakt.
* de id `DEVICEID` door de naam van het apparaat dat u hebt gemaakt.
* de id `PWD` door de waarde voor de Shared Access Signature die u hebt gegenereerd voor het apparaat.

Controleer of de code correct werkt door Azure IoT Explorer te starten en naar de telemetrie te luisteren.

Voer de toepassing uit (CTRL + F5). Na een paar seconden wordt er uitvoer weergegeven die er ongeveer als volgt uitziet:

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="Uitvoer van MQTT-voorbeeldtoepassing":::

In Azure IoT Explorer kunt u zien dat het apparaat geen IoT Plug en Play-apparaat is:

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="Niet-IoT Plug en Play-apparaat in Azure IoT Explorer":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>Van het apparaat een IoT Plug en Play-apparaat maken

IoT Plug en Play-apparaten moeten aan een aantal eenvoudige regels voldoen. Als een apparaat een model-id verzendt wanneer het verbinding maakt, wordt het een IoT Plug en Play-apparaat.

In dit voorbeeld voegt u een model-id** toe aan het MQTT-verbindingspakket. U geeft de model-id als queryreeks-parameter door in de `USERNAME` en wijzigt de `api-version` in `2020-05-31-preview`:

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-05-31-preview&model-id=dtmi:com:example:Thermostat;1"
#define PORT 8883
#define HOST IOTHUBNAME //".azure-devices.net"
#define TOPIC "devices/" DEVICEID "/messages/events/"
```

Herbouw het voorbeeld en voer het uit.

De apparaatdubbel bevat nu de model-id:

:::image type="content" source="media/tutorial-use-mqtt/model-id-iot-explorer.png" alt-text="De model-id in Azure IoT Explorer weergeven":::

U kunt nu navigeren naar het IoT Plug en Play-onderdeel:

:::image type="content" source="media/tutorial-use-mqtt/components-iot-explorer.png" alt-text="Onderdelen in Azure IoT Explorer weergeven":::

U kunt nu uw apparaatcode aanpassen voor het implementeren van de telemetrie, eigenschappen en opdrachten die in uw model zijn gedefinieerd. Voor een voorbeeldimplementatie van het thermostaatapparaat met behulp van de Mosquitto-bibliotheek, raadpleegt u [Using MQTT PnP with Azure IoTHub without the IoT SDK on Windows](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32) (MQTT PnP met Azure IoTHub gebruiken zonder de IoT SDK op Windows) op GitHub.

> [!NOTE]
> Een Shared Access Signature is standaard slechts 60 minuten geldig.

> [!NOTE]
>De client gebruikt het basis certificaatbestand `IoTHubRootCA_Baltimore.pem` om de identiteit van de IoT-hub waarmee verbinding wordt gemaakt te verifiëren.

### <a name="mqtt-topics"></a>MQTT-onderwerpen

De volgende definities zijn voor de MQTT-onderwerpen die het apparaat gebruikt om gegevens naar de IoT-hub te verzenden. Ga voor meer informatie naar [Communiceren met uw IoT-hub met het MQTT-protocol](../iot-hub/iot-hub-mqtt-support.md):

* `DEVICE_CAPABILITIES_MESSAGE` definieert het onderwerp dat het apparaat gebruikt voor het rapporteren van de interfaces die worden geïmplementeerd.
* `DEVICETWIN_PATCH_MESSAGE` definieert het onderwerp dat het apparaat gebruikt voor het rapporteren van eigenschapsupdates aan de IoT-hub.
* `DEVICE_TELEMETRY_MESSAGE` definieert het onderwerp dat het apparaat gebruikt om telemetriegegevens naar uw IoT-hub te verzenden.

Ga voor meer informatie over MQTT naar de GitHub-opslagplaats [MQTT Samples for Azure IoT](https://github.com/Azure-Samples/IoTMQTTSample/) (MQTT-voorbeelden voor Azure IoT).

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een MQTT-apparaatclient kunt wijzigen om de regels voor IoT Plug en Play te volgen. Voor meer informatie over IoT Plug and Play, bekijk:

> [!div class="nextstepaction"]
> [Architectuur](concepts-architecture.md)

Voor meer informatie over IoT Hub-ondersteuning voor het MQTT-protocol, gaat u naar:

> [!div class="nextstepaction"]
> [Communiceren met uw IoT-hub met het MQTT-protocol](../iot-hub/iot-hub-mqtt-support.md)
